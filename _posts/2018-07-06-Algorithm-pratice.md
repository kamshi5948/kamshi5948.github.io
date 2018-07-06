---
layout: post
title: "알고리즘 문제풀이"
tags: [알고리즘]
comments: true
---

##### 블록게임

```
public static void main(String[] args) throws IOException {
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



##### 뉴스클러스터링

```
public static void main(String[] args) throws IOException {
    /*
    FRANCE  french  16384
    handshake   shake hands 65536
    aa1+aa2 AAAA12  43690
    E=M*C^2 e=m*c^2 65536

    {FR, RA, AN, NC, CE}, {FR, RE, EN, NC, CH}

    {FR, RA, AN, NC, CE, RE, EN, CH}
    */

    //String str1 = "FRANCE";
    //String str2 = "french";

    //String str1 = "handshake";
    //String str2 = "shake hands";

    //String str1 = "aa1+aa2";
    //String str2 = "AAAA12";

    String str1 = "E=M*C^2";
    String str2 = "e=m*c^2";

    String[] strArr1 = new String[str1.length() - 1];
    String[] strArr2 = new String[str2.length() - 1];
    int iIdx1 = 0;
    int iIdx2 = 0;

    String[] tmp = new String[100];
    String[] tmp1 = new String[100];
    int tmpIdx = 0;
    int tmpIdx1 = 0;

    // 그룹 만들기
    for(int i = 0; i < str1.length() - 1; i++) {
        if(str1.substring(i, i + 2).indexOf(" ") < 0 && Pattern.matches("^[a-zA-Z]*$", String.valueOf(str1.substring(i, i + 2)))) {
            strArr1[iIdx1++] = str1.substring(i, i + 2).toUpperCase();
            System.out.println(strArr1[iIdx1 - 1]);
        }
    }

    System.out.println("==============================");

    // 그룹 만들기
    for(int i = 0; i < str2.length() - 1; i++) {
        if(str2.substring(i, i + 2).indexOf(" ") < 0 && Pattern.matches("^[a-zA-Z]*$", String.valueOf(str2.substring(i, i + 2)))) {
            strArr2[iIdx2++] = str2.substring(i, i + 2).toUpperCase();
            System.out.println(strArr2[iIdx2 - 1]);
        }
    }

    System.out.println("iIdx1 = " + iIdx1);
    System.out.println("iIdx2 = " + iIdx2);

    for(int i = 0; i < iIdx1; i++) {
        for(int j = 0; j < iIdx2; j++) {
            if(strArr1[i].equals(strArr2[j])){
                /*boolean find = false;
                for(int k = 0; k < tmpIdx; k++) {
                    if(tmp[k].equals(strArr1[i])) {
                        find = true;
                    }
                }

                if(!find) tmp[tmpIdx++] = strArr1[i];*/

                tmp[tmpIdx++] = strArr1[i];
                break;
            }
        }
    }

    // 합집합 구하기
    for(int i = 0; i < iIdx1; i++) {
        tmp1[tmpIdx1++] = strArr1[i];
    }

    for(int i = 0; i < iIdx2; i++) {
        boolean find = false;
        for(int j = 0; j < iIdx1; j++) {
            if(strArr2[i].equals(strArr1[j])) {
                strArr1[j] = "";
                find = true;
                break;
            }
        }

        if(!find) tmp1[tmpIdx1++] = strArr2[i];
    }



    System.out.println("~~~~~~~~~~~~~~~~~~~~");

    for(int i = 0; i < tmpIdx; i++) {
        System.out.println(tmp[i]);
    }

    System.out.println("~~~~~~~~~~~~~~~~~~~~");

    for(int i = 0; i < tmpIdx1; i++) {
        System.out.println(tmp1[i]);
    }

    System.out.println("~~~~~~~~~~~~~~~~~~~~");

    System.out.println("tmpIdx = " + tmpIdx);
    System.out.println("tmpIdx1 = " + tmpIdx1);

    Double result = 0.0;
    if(tmpIdx == 0 && tmpIdx1 == 0) {
        result = 1.0;
    } else {
        result = Double.parseDouble(String.valueOf(tmpIdx)) / Double.parseDouble(String.valueOf(tmpIdx1));
    }

    System.out.println("result = " + Math.floor(result * 65536));
}
```


##### 비밀지도

```
public static void main(String[] args) throws IOException {
    /*
    n   5
    arr1    [9, 20, 28, 18, 11]
    arr2    [30, 1, 21, 17, 28]
    출력  ["#####","# # #", "### #", "# ##", "#####"]

    6
    arr1    [46, 33, 33 ,22, 31, 50]
    arr2    [27 ,56, 19, 14, 14, 10]
    출력  ["######", "### #", "## ##", " #### ", " #####", "### # "]
    */

    //int n = 5;
    //int[] arr1 = {9, 20, 28, 18, 11};
    //int[] arr2 = {30, 1, 21, 17, 28};
    int n = 6;
    int[] arr1 = {46, 33, 33 ,22, 31, 50};
    int[] arr2 = {27 ,56, 19, 14, 14, 10};


    String[][] tmp1 = new String[n][n];
    String[][] tmp2 = new String[n][n];
    String[][] tmp = new String[n][n];

    for(int i = 0; i < arr1.length; i++) {
        String temp = notation(arr1[i], 2);

        if(temp.length() < n) {
            while(temp.length() < n) {
                temp = "0" + temp;
            }
        }

        System.out.println(arr1[i] + " = " + temp);

        for(int j = 0; j < n; j++) {
            if("0".equals(String.valueOf(temp.charAt(j)))) {
                tmp1[i][j] = " ";
            } else if("1".equals(String.valueOf(temp.charAt(j)))) {
                tmp1[i][j] = "#";
            }
        }
    }

    for(int i = 0; i < arr2.length; i++) {
        String temp = notation(arr2[i], 2);

        if(temp.length() < n) {
            while(temp.length() < n) {
                temp = "0" + temp;
            }
        }

        System.out.println(arr2[i] + " = " + temp);

        for(int j = 0; j < n; j++) {
            if("0".equals(String.valueOf(temp.charAt(j)))) {
                tmp2[i][j] = " ";
            } else if("1".equals(String.valueOf(temp.charAt(j)))) {
                tmp2[i][j] = "#";
            }
        }
    }

    for(int i = 0; i < n; i++) {
        for(int j = 0; j < n; j++) {
            if("#".equals(tmp1[i][j]) || "#".equals(tmp2[i][j])){
                tmp[i][j] = "#";
            } else {
                tmp[i][j] = " ";
            }
        }
    }




    for(int i = 0; i < n; i++) {
        for(int j = 0; j < n; j++) {
            System.out.print(tmp[i][j]);
        }
        System.out.println("");
    }


    System.out.println("==================");
}



public static String notation(int num, int notation) {
    String result = "";
    String[] tmpArr = new String[20];
    int idx = 0;
    while(num > 0){
        String temp = "";
        if(num % notation == 10) {
            temp = "A";
        } else if(num % notation == 11) {
            temp = "B";
        } else if(num % notation == 12) {
            temp = "C";
        } else if(num % notation == 13) {
            temp = "D";
        } else if(num % notation == 14) {
            temp = "E";
        } else if(num % notation == 15) {
            temp = "F";
        } else {
            temp = String.valueOf(num % notation);
        }

        tmpArr[idx++] = temp;
        num = num / notation;
    }

    for(int i = idx - 1; i >= 0; i--) {               
        result += tmpArr[i];
    }

    return result;
}
```

##### 자동완성

```
public static void main(String[] args) throws IOException {
    /*
    [“go”,”gone”,”guild”]   7
    [“abc”,”def”,”ghi”,”jklm”]  4
    [“word”,”war”,”warrior”,”world”]    15
    */
    /*
    g o
    g o n
    g u
    */
    //String[] auto = {"go", "gone", "guild"};
    //String[] auto = {"abc", "def", "ghi", "jklm"};
    String[] auto = {"word", "war", "warrior", "world"};

    int result = 0;
    for(int i = 0; i < auto.length; i++) {
        for(int j = 0; j < auto[i].length(); j++) {
            char curChar = auto[i].charAt(j);

            System.out.println("!!curChar = " + String.valueOf(curChar));
            System.out.println("!!j = " + j);

            boolean findFg = chk(auto, curChar, j);

            System.out.println("curChar = " + String.valueOf(curChar));
            System.out.println("findFg = " + findFg);

            if(!findFg) {
                result++;
                break;
            } else {
                result++;
            }


        }
    }
    System.out.println("==================");
    System.out.println("result = " + result);
}



public static boolean chk(String[] auto, char curChar, int idx) {
    boolean result = false;
    int cnt = 0;
    for(int i = 0; i < auto.length; i++) {
        if(idx < auto[i].length()) {
            System.out.println("curChar!! = " + String.valueOf(curChar) + " , " + auto[i].charAt(idx));
            if(curChar == auto[i].charAt(idx)) {
                cnt++;
            }
        }
    }

    if(cnt > 1) {
        result = true;
    } else {
        result = false;
    }

    return result;
}
```

##### 바로그곡

```
public static void main(String[] args) throws IOException {
    //바로그곡
    /*
    m은 음 1개 이상 1439개 이하로 구성되어 있다.
    musicinfos는 100개 이하의 곡 정보를 담고 있는 배열로,
    각각의 곡 정보는 음익이 시작한 시각, 끝난 시각, 음악 제목, 악보 정보가 ‘,‘로 구분된 문자열이다.
    음악의 시작 시각과 끝난 시각은 24시간 HH:MM 형식이다.
    음악 제목은 ‘,’ 이외의 출력 가능한 문자로 표현된 길이 1 이상 64 이하의 문자열이다.
    악보 정보는 음 1개 이상 1439개 이하로 구성되어 있다.

    C, C#, D, D#, E, F, F#, G, G#, A, A#, B

    “ABCDEFG”   [“12:00,12:14,HELLO,CDEFGAB”, “13:00,13:05,WORLD,ABCDEF”]   “HELLO”
    “CC#BCC#BCC#BCC#B”  [“03:00,03:30,FOO,CC#B”, “04:00,04:08,BAR,CC#BCC#BCC#B”]    “FOO”
    “ABC”   [“12:00,12:14,HELLO,C#DEFGAB”, “13:00,13:05,WORLD,ABCDEF”]  “WORLD”
    */

    String m = "ABCDEFG";
    String[] musicinfos = {"12:00,12:14,HELLO,CDEFGAB", "13:00,13:05,WORLD,ABCDEF"};
    //String m = "CC#BCC#BCC#BCC#B";
    //String[] musicinfos = {"03:00,03:30,FOO,CC#B", "04:00,04:08,BAR,CC#BCC#BCC#B"};
    //String m = "ABC";
    //String[] musicinfos = {"12:00,12:14,HELLO,C#DEFGAB", "13:00,13:05,WORLD,ABCDEF"};

    System.out.println("m = " + m);

    String[][] music = new String[musicinfos.length][7];

    for(int i = 0; i < musicinfos.length; i++) {
        String[] tmp = musicinfos[i].split(",");
        String[] tmp1 = tmp[0].split(":");
        music[i][0] = tmp1[0];
        music[i][1] = tmp1[1];
        String[] tmp2 = tmp[1].split(":");
        music[i][2] = tmp2[0];
        music[i][3] = tmp2[1];

        music[i][4] = tmp[2];

        int hh = (Integer.parseInt(music[i][2]) - Integer.parseInt(music[i][0])) * 60;
        int mm = Integer.parseInt(music[i][3]) - Integer.parseInt(music[i][1]);
        int totMm = hh + mm;

        //System.out.println("totMm = " + totMm);
        String newM = "";

        for(int j = 0; j < totMm; j++) {
            //System.out.println("j = " + j + ", totMm = " + totMm);
            //System.out.println("jj % tmp[3].length()) + 1 = " + (j % tmp[3].length() + 1));
            if(j % tmp[3].length() < tmp[3].length() - 1 && "#".equals(String.valueOf(tmp[3].charAt((j % tmp[3].length()) + 1)))) {
                newM += tmp[3].charAt(j % tmp[3].length()) + "#";
                j++;
                totMm++;
            } else {
                newM += tmp[3].charAt(j % tmp[3].length());
            }
        }

        //System.out.println("newM = " + newM);

        music[i][5] = newM;
        music[i][6] = String.valueOf(hh + mm);
    }

    for(int i = 0; i < musicinfos.length; i++) {
        System.out.print(music[i][0] + " ");
        System.out.print(music[i][1] + " ");
        System.out.print(music[i][2] + " ");
        System.out.print(music[i][3] + " ");
        System.out.print(music[i][4] + " ");
        System.out.print(music[i][5] + " ");
        System.out.println(music[i][6]);
    }

    System.out.println("~~~~~~~~~~~~~~~~~~~~~~~~~~~~");

    String[][] find = new String[musicinfos.length][2];
    int findIdx = 0;

    for(int i = 0; i < music.length; i++) {
        if(music[i][5].indexOf(m) >= 0) {
            if(!"#".equals(music[i][5].substring(music[i][5].indexOf(m) + m.length(), music[i][5].indexOf(m) + m.length() + 1))){
                find[findIdx][0] = music[i][4];
                find[findIdx++][1] = music[i][6];
            }
        }
    }

    System.out.println("~~~~~~~~~~~~~~~~~~~~~~~~~~~~");

    if(findIdx > 1) {
        String[][] last = arrSort(find);
        System.out.println("last = " + last[0][0]);
    } else {
        System.out.println("last = " + find[0][0]);
    }


}

public static String[][] arrSort(String a[][]) {
    String[] tmp;

    for (int i = 0; i < a.length - 1; i++) {
        for (int j = i + 1; j < a.length; j++) {
            if(Integer.parseInt(a[i][1]) < Integer.parseInt(a[j][1])) {
                tmp = a[i];
                a[i] = a[j];
                a[j] = tmp;
            }
        }
    }

    return a;
}
```

##### 압축

```
public static void main(String[] args) throws IOException {

    //압축
    /*
    K   A   11  27: KA
    A   K   1   28: AK
    KA  O   27  29: KAO
    O       15   
    */
    int[] result = new int[100];
    int resultIdx = 0;
    String[] dic = new String[100];
    int dicSize = 26;

    char ch = 'A';
    for(int i = 0; i < 100; i++) {
        dic[i] = String.valueOf(ch++);
        if(i >= 26) {
            dic[i] = "";
        }
    }


    //String msg = "KAKAO"; //  [11, 1, 27, 15]
    //String msg = "TOBEORNOTTOBEORTOBEORNOT"; //[20, 15, 2, 5, 15, 18, 14, 15, 20, 27, 29, 31, 36, 30, 32, 34]
    String msg = "ABABABABABABABAB"; //[1, 2, 27, 29, 28, 31, 30]
    String tmp = msg;

    while(tmp.length() > 0) {
        int idx = 0;
        int dicIdx = 0;
        System.out.println("tmp.length() = " + tmp.length());
        System.out.println("dicSize = " + dicSize);

        for(int i = 0; i < dicSize; i++) {
            System.out.print(dic[i] + " ");
            if(tmp.startsWith(dic[i])){

                if(dic[i].length() > idx) {
                    idx = dic[i].length();
                    dicIdx = i;
                }
            }
        }


        String cur = dic[dicIdx];
        String next = "";
        if(tmp.length() <= dic[dicIdx].length()) {
            next = "";
        } else {
            next = tmp.substring(dic[dicIdx].length(), dic[dicIdx].length() + 1);
        }


        dic[dicSize++] = cur + next;

        tmp = tmp.substring(dic[dicIdx].length());

        result[resultIdx++] = (dicIdx + 1);

        System.out.println("");
        System.out.println("dicIdx = " + (dicIdx + 1));
        System.out.println("cur = " + cur);
        System.out.println("next = " + next);
        System.out.println("dic[dicSize] = " + dic[dicSize - 1]);
        System.out.println("tmp = " + tmp);
        System.out.println("-------------------------");
    }

    System.out.println("======================================");

    for(int i = 0; i < resultIdx; i++) {
        System.out.print(result[i] + " ");
    }
}
```

##### 숫자말하기게임(진법)

```
public static void main(String[] args) throws IOException {

    //진법 n, 미리 구할 숫자의 갯수 t, 게임에 참가하는 인원 m, 튜브의 순서 p 가 주어진다.

    /*
    2 ≦ n ≦ 16
    0 ＜ t ≦ 1000
    2 ≦ m ≦ 100
    1 ≦ p ≦ m
    */

    /*int n = 2;
    int t = 4;
    int m = 2;
    int p = 1;*/
    int n = 16;
    int t = 16;
    int m = 2;
    int p = 2;

    int i = 0;
    String result = "";
    String last = "";

    while(result.length() < t * m) {
        if(i == 0) {
            result = "0";
        }

        String[] tmpArr = new String[20];
        int idx = 0;
        int tmp = i;
        while(tmp > 0){
            String temp = "";
            if(tmp % n == 10) {
                temp = "A";
            } else if(tmp % n == 11) {
                temp = "B";
            } else if(tmp % n == 12) {
                temp = "C";
            } else if(tmp % n == 13) {
                temp = "D";
            } else if(tmp % n == 14) {
                temp = "E";
            } else if(tmp % n == 15) {
                temp = "F";
            } else {
                temp = String.valueOf(tmp % n);
            }

            tmpArr[idx++] = temp;
            tmp = tmp / n;
        }

        for(int j = idx - 1; j >= 0; j--) {               
            if(result.length() >= t * m) break;
            result += tmpArr[j];
        }
        i++;
    }

    System.out.println("result = " + result);


    for(int j = 1; j < result.length() + 1; j++) {
        if(j % m == p && m != p) {
            last += result.charAt(j - 1);
        } else if(j % m == 0 && m == p) {
            last += result.charAt(j - 1);
        }
    }

    System.out.println("last = " + last);
    System.out.println("=======================");
    System.out.println(notation(10, 2));
}

public static String notation(int num, int notation) {
    String result = "";
    String[] tmpArr = new String[20];
    int idx = 0;
    while(num > 0){
        String temp = "";
        if(num % notation == 10) {
            temp = "A";
        } else if(num % notation == 11) {
            temp = "B";
        } else if(num % notation == 12) {
            temp = "C";
        } else if(num % notation == 13) {
            temp = "D";
        } else if(num % notation == 14) {
            temp = "E";
        } else if(num % notation == 15) {
            temp = "F";
        } else {
            temp = String.valueOf(num % notation);
        }

        tmpArr[idx++] = temp;
        num = num / notation;
    }

    for(int i = idx - 1; i >= 0; i--) {               
        result += tmpArr[i];
    }

    return result;
}
```
