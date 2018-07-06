---
layout: post
title: "알고리즘 문제풀이"
tags: [알고리즘]
comments: true
---

##### 블록게임

```
public static void main(String[] args) throws IOException {

        //블록게임
        /*
        CCBDE
        AAADE
        AAABF
        CCBBF

        6   6   [“TTTANT”, “RRFACC”, “RRRFCC”, “TRRRAA”, “TTMMMF”, “TMMTTJ”]    15
        */

        //int m = 4;
        //int n = 5;
        //String[] input = {"CCBDE", "AAADE", "AAABF", "CCBBF"};

        int m = 6;
        int n = 6;
        String[] input = {"TTTANT", "RRFACC", "RRRFCC", "TRRRAA", "TTMMMF", "TMMTTJ"};

        String[][] board = new String[m][n];
        String[][] result = new String[m][n];
        boolean more = false;

        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                board[i][j] = String.valueOf(input[i].charAt(j));
                result[i][j] = String.valueOf(input[i].charAt(j));
                System.out.print(board[i][j] + " ");
            }
            System.out.println("");
        }

        System.out.println("===========================");

        do {    
            more = false;

            for(int i = 0; i < m - 1; i++) {
                for(int j = 0; j < n - 1; j++) {
                    if(!board[i][j].equals(" ") &&
                       board[i][j].equals(board[i][j + 1]) &&
                       board[i][j].equals(board[i + 1][j]) &&
                       board[i][j].equals(board[i + 1][j + 1])){
                        result[i][j] = " ";
                        result[i][j + 1] = " ";
                        result[i + 1][j] = " ";
                        result[i + 1][j + 1] = " ";
                        more = true;
                    }
                }
            }

            System.out.println("~~~~~~~~~~~~~~~~~~~~~~ 스퀘어 삭제");

            for(int i = 0; i < m; i++) {
                for(int j = 0; j < n; j++) {
                    System.out.print(result[i][j] + " ");
                }
                System.out.println("");
            }

            for(int k = 0; k < m; k++) {
                for(int i = 0; i < m - 1; i++) {
                    for(int j = 0; j < n; j++) {
                        if(" ".equals(result[i + 1][j])) {
                            result[i + 1][j] = result[i][j];
                            result[i][j] = " ";
                        }
                    }
                }
            }

            System.out.println("~~~~~~~~~~~~~~~~~~~~~~내리기");

            for(int i = 0; i < m; i++) {
                for(int j = 0; j < n; j++) {
                    System.out.print(result[i][j] + " ");
                }
                System.out.println("");
            }


            System.out.println("~~~~~~~~~~~~~~~~~~~~~~");

            //초기화
            board = result;

            /*board = new String[m][n];
            for(int i = 0; i < m; i++) {
                for(int j = 0; j < n; j++) {
                    board[i][j] = result[i][j];
                }
            }*/
        } while(more);


        System.out.println("~~~~~~~~~~~~~~~~~~~~~~");

        int last = 0;
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(result[i][j].equals(" ")){
                    last++;
                }
            }

        }
        System.out.println("last = " + last);
    }
```
