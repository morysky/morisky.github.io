---
layout: post
title:  "A suggestion using trieTree"
date:   2016-08-05 08:39:06 +0800
categories: algorithm
---

## 什么是Suggestion

Sug，即搜索建议，给定一组词典：

- lihai
- nihaoa
- test
- haoren

输入Query:`ha`，能够输出包含Query的所有词

## 实现方法->字典树(TrieTree)

> todo

## 关键代码

> 自己瞎写的，有那么个意思，C写的太烂

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include "myconf.h"

// error code
#define EXIT_SUCCESS 0
#define MEMORY_FAIL 1

#define DEBUG 0

#define MAX_BUF_SIZE 1024
#define MAX_DICT_SIZE 65535
#define MAX_RESULT_SIZE 1024

#define MSG_SIZE 100
#define MSG_SIGN '-'

struct _trieTreeNode {
    char val;
    struct _trieTreeNode* parrent;
    struct _trieTreeNode* child;
    struct _trieTreeNode* left;
    struct _trieTreeNode* right;
    char real_string[128];
};

typedef struct _trieTreeNode trieTreeNode;

void do_query(trieTreeNode* node, char* result_iter, int index, char** result_list, int* result_index);

/**
 * @brief Create trieTree node
 *
 * @param node
 * @param val
 *
 * @return 
 */
int create_trieTreeNode(trieTreeNode** node, char val) {
    *node = (trieTreeNode*) malloc(sizeof(trieTreeNode));

    if(NULL == *node) {
        return MEMORY_FAIL;
    }
    (*node)->val = val;
    (*node)->parrent = NULL;
    (*node)->child = NULL;
    (*node)->left = NULL;
    (*node)->right = NULL;
    return EXIT_SUCCESS;
}

int delete_trieTreeNode(trieTreeNode* node) {
    free(node);
    return EXIT_SUCCESS;
}

void query(trieTreeNode* root, char* string, char** result_list) {
    char* iter = string;
    trieTreeNode *cur = root->child;
    char *result_iter = (char*) malloc(MAX_BUF_SIZE);
    char *result_start = result_iter;
    memset(result_iter, '\0', MAX_BUF_SIZE);

    int index = 0;
    int result_index = 0;
    while(*iter != '\0') {
        while(cur) {
            if(cur->val == *iter) {
                // to deeper level
                *result_iter++ = *iter;
                index++;
                cur = cur->child;
                break;
            } else {
                cur = cur->right;
            }
        }
        iter++;
    }

    do_query(cur, result_start, index, result_list, &result_index);
    free(result_start);
    return;
}

void do_query(trieTreeNode* node, char* result_iter, int index, char** result_list, int* result_index) {
    if(!node) {
        return ;
    }
    trieTreeNode* cur = node;

    while(cur) {
        if('\0' == cur->val) {
            // reach end save result
            *(result_iter+index) = '\0';
            *(result_list+*result_index) = strdup(cur->real_string);
            *result_index = *result_index+1;
            if(DEBUG) {
                printf("test : %s\n", result_iter);
            }
        } else {
            *(result_iter+index) = cur->val;
            do_query(cur->child, result_iter, index+1, result_list, result_index);
            *(result_iter+index) = '\0';
        }
        cur = cur->right;
    }
    return;
}

int insert(trieTreeNode* root, char* string, const char* real_string) {
    char* iter = string;
    trieTreeNode* cur = NULL;
    trieTreeNode* parrent = root;
    trieTreeNode* add = NULL;
    trieTreeNode* tmp = NULL;

    int level = 0;
    while(*iter != '\0') {
        if(NULL == parrent->child) {
            if(DEBUG) {
                printf("insert at [new] level[%d] : %c\n", level, *iter);
            }
            // not exist, add
            create_trieTreeNode(&add, *iter);
            add->parrent = parrent;
            parrent->child = add;

            parrent = cur = add;
            iter++;
            level++;
            continue;
        }

        int found = 0;
        cur = parrent->child;
        trieTreeNode* last = NULL;
        do {
            last = cur;
            if(cur->val == *iter) {
                // find it
                found = 1;
                parrent = cur;
                break;
            } else {
                cur = cur->right;
            }
        } while(cur);

        if(0 == found) {
            if(DEBUG) {
                printf("insert at [same] level[%d] : %c\n", level, *iter);
            }
            create_trieTreeNode(&add, *iter);
            add->parrent = parrent;
            add->left = last;
            last->right = add;

            parrent = cur = add;
        }
        iter++;
        level++;
    }

    if(cur) {
        if(cur->child) {
            if(cur->child->val == '\0') {
            } else {
                if(DEBUG) {
                    printf("insert tag when do not have '\\0'\n");
                }

                // find the right one
                create_trieTreeNode(&tmp, '\0');
                strcpy(tmp->real_string, real_string);
                tmp->right = cur->child;
                cur->child->left = tmp;
                cur->child = tmp;
                tmp->parrent = cur;
            }
        } else {
            if(DEBUG) {
                printf("insert tag when cur->child null\n");
            }
            create_trieTreeNode(&tmp, '\0');
            strcpy(tmp->real_string, real_string);
            tmp->parrent = parrent;
            cur->child = tmp;
        }
    }
    return EXIT_SUCCESS;
}

void traverse_trietree(trieTreeNode* search) {
    if(!search) {
        return ;
    }

    trieTreeNode *cur = search;

    while(cur) {
        if('\0' == cur->val) {
        } else {
            printf("val : %c\n", cur->val);
        }
        traverse_trietree(cur->child);
        cur = cur->right;
    }
    return ;
}

void print_msg(const char* msg) {
    int msg_len = strlen(msg);
    int holder_len1, holder_len2;
    if(0 == (MSG_SIZE - msg_len)%2) {
        holder_len1 = holder_len2 = (MSG_SIZE-msg_len)/2;
    } else {
        holder_len1 = (MSG_SIZE-msg_len)/2;
        holder_len2 = holder_len1+1;
    }

    char* tmp = (char*)malloc(MAX_BUF_SIZE);

    memset(tmp, MSG_SIGN, holder_len1);
    tmp[holder_len1] = '\0';
    printf("%s", tmp);

    printf("%s", msg);

    memset(tmp, MSG_SIGN, holder_len2);
    tmp[holder_len2] = '\0';
    printf("%s\n", tmp);

    free(tmp);
}

int insert_all(trieTreeNode* root, const char* string) {
    if(DEBUG) {
        printf("all_dict_key : %s, len[%lu]\n", string, strlen(string));
    }
    int len = strlen(string);

    char* insert_string = (char*) malloc (MAX_BUF_SIZE);
    memset(insert_string, '\0', MAX_BUF_SIZE);

    for(int i=0; i<len; i++) {
        strncpy(insert_string, string+i, len-i);
        if(i > 0) {
            insert_string[len-i] = '\0';
        }
        if(DEBUG) {
            printf("dict : %s\n", insert_string);
        }

        insert(root, insert_string, string);
    }
    free(insert_string);
    return 0;
}

int main() {

    char* input = (char*) malloc(MAX_BUF_SIZE);
    char** result_list = (char**) malloc( MAX_RESULT_SIZE * sizeof(char*));
    char** result_list_iter = result_list;
    char buf[MAX_BUF_SIZE];
    memset(buf, '\0', MAX_BUF_SIZE);

    trieTreeNode* root;
    create_trieTreeNode(&root, '\0');

    char **conf_list = (char**) malloc (MAX_BUF_SIZE*sizeof(char*));
    memset(conf_list, NULL, MAX_BUF_SIZE*sizeof(char*));
    char** conf_start = conf_list;

    getConf("test", conf_list);

    while(*conf_list)
        insert_all(root, *conf_list++);
    free(conf_start);

    print_msg("load dict successfully");
    print_msg("please input query");

    while (scanf("%s",input) != EOF) {
        query(root, input, result_list);

        int dup_index[MAX_RESULT_SIZE];
        int tmp = 0;
        memset(&dup_index, -1, MAX_BUF_SIZE*sizeof(int));

        result_list_iter = result_list;
        if (*result_list_iter) {
            while (*result_list_iter) {
                tmp = (int)**result_list_iter-49;
                if(dup_index[tmp] > 0) {
                } else {

                    char* final_output = strstr(*result_list_iter, input);
                    if(final_output) {
                        int prefix = final_output-*result_list_iter-1;
                        int input_len = strlen(input);
                        int suffix = strlen(*result_list_iter)-prefix-input_len-1;

                        strcpy(buf, *result_list_iter+1);
                        buf[prefix] = '\0';
                        printf("\e[0;33mResult : \e[0m%s", buf);
                        printf("\e[1;31m\e[1m%s\e[0m", input);
                        strcpy(buf, *result_list_iter+prefix+input_len+1);
                        buf[strlen(*result_list_iter)] = '\0';
                        printf("%s\n", buf);
                        if(DEBUG) {
                            printf("%d - %d - %d\n", prefix, input_len, suffix);
                        }
                    }

                    dup_index[tmp] = 1;
                }
                result_list_iter++;
            }
        } else {
            printf("\e[0;35mNo result match with[%s]\e[0m\n", input);
        }

        result_list_iter = result_list;
        while(*result_list_iter) {
            // free strdup memory
            free(*result_list_iter);
            result_list_iter++;
        }

        memset(input, '\0', MAX_BUF_SIZE);
        memset(result_list, NULL, MAX_BUF_SIZE * sizeof(char*));
        print_msg("please input query");
    }
    free(input);
    free(result_list);

    // todo
    // destroy the tree

    return 0;
}
```

## 效果

![result](http://blog.morysky.win/assets/image/sug-pic0.png)
