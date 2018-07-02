---
layout: post
title: "알고리즘"
tags: [알고리즘]
comments: true
---

##### 패턴 정규식
```
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
