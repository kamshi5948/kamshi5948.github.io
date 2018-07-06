---
layout: post
title: "알고리즘"
tags: [알고리즘]
comments: true
---

##### 패턴 정규식

```
//LTRIM(0제거)
String input = "0012";
String tmp = Pattern.compile("^[0]+").matcher(input).replaceAll("");

//LTRIM(공백제거)
Pattern.compile("^\\s+")

//RTRIM(공백제거)
Pattern.compile("\\s+$")

String ltrim = s.replaceAll("^\\s+","");
String rtrim = s.replaceAll("\\s+$","");

//정규식패턴
boolean i = Pattern.matches(pattern, input);

1) 숫자만 : ^[0-9]*$
2) 영문자만 : ^[a-zA-Z]*$
3) 한글만 : ^[가-힣]*$
4) 영어 & 숫자만 : ^[a-zA-Z0-9]*$
5) E-Mail : ^[a-zA-Z0-9]+@[a-zA-Z0-9]+$
6) 휴대폰 : ^01(?:0|1|[6-9]) - (?:\d{3}|\d{4}) - \d{4}$
7) 일반전화 : ^\d{2.3} - \d{3,4} - \d{4}$
8) 주민등록번호 : \d{6} \- [1-4]\d{6}
9) IP 주소 : ([0-9]{1,3}) \. ([0-9]{1,3}) \. ([0-9]{1,3}) \. ([0-9]{1,3})
```

##### 버블정렬(1차원)

```
public static int[] sort(int a[]) {
    int tmp;
    for (int i = 0; i < a.length - 1; i++) {
        for (int j = i + 1; j < a.length; j++) {
            if (a[i] > a[j]) {
                tmp = a[i];
                a[i] = a[j];
                a[j] = tmp;
            }
        }
    }

    return a;
}
```

##### 버블정렬(2차원)

```
public static String[][] arrSort(String a[][]) {
    String[] tmp;

    for (int i = 0; i < a.length - 1; i++) {
        for (int j = i + 1; j < a.length; j++) {
            if(Integer.parseInt(a[i][0]) > Integer.parseInt(a[j][0])) {
                tmp = a[i];
                a[i] = a[j];
                a[j] = tmp;
            } else if(Integer.parseInt(a[i][0]) == Integer.parseInt(a[j][0])) {
                if(a[i][1].compareTo(a[j][1]) > 0) {
                    tmp = a[i];
                    a[i] = a[j];
                    a[j] = tmp;
                }
            }
        }
    }

    return a;
}
```		

##### 진법변환

```
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
