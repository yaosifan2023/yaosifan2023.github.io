---
title: java实现登入和注册的实现
date: 2024-01-06 11:56:34
tags: [Java, 学习笔记]
categories: Java
---

## java实现登入和注册 的实现

## 注册操作

设置循环可以一直进行注册

```java
public static void reGister() // 注册界面
   {
       String username =null;
       while (true){
           System.out.println("请输入你的用户名");
           username = scan.next();
           // 判断是否有一样的用户名
           int pos = 0 ;
           for(int i = 0 ;i < arr1.length ;i ++ ) {
               if (arr1[i] != null) {
                   if (arr1[i].equals(username)) {
                       pos = 1;
                       break;
                   }
               }
           }
           if (pos == 1){
               System.out.println("该用户名已经存在,请你重新输入!");
           }
           else break;
       }
       System.out.println("请输入你的密码");
       String password = scan.next();
       // 保存密码
       for(int i = 0 ; i < arr1.length ; i ++){
           if(arr1[i] == null){ // 如果为空就直接保存了
               arr1[i] = username;
               arr2[i] = password;
               break;  // 保存完结束
           }
       }
       System.out.println("注册成功!");
   }
```

## 登入

设置了三次登入可以退回界面

```java
public static void loGin()  // 登入
   {
       String username = null;
       String passward = null;
       int x=0;
       while (true){
           int pos = 0; // 标记是否正确,保证可以一直输入
           System.out.println("请输入登入的用户名");
           username = scan.next();
           System.out.println("请输入登入的密码");
           passward = scan.next();
           // 检测登入正确
           for(int i = 0 ; i < arr1.length ; i ++ ){
               if(arr1[i] != null) {
                   if (arr1[i].equals(username) && arr2[i].equals(passward)) {
                       pos = 1;
                       break;
                   }
               }
           }
           if (pos == 1){
               System.out.println("登入成功");
               break;
           }
           else {
               if(x<=2) {
                   System.out.println("登入失败,请重新输入你的用户名和密码");
                   x++;
               }
               if(x>=3){
                   System.out.println("你已经三次输入错误,你是否选择退出该界面:(yes or not)");
                   String v = scan.next();
                   if( v.equals("yes") || v.equals("YES")) // 输入yes 或者 YES 就退出
                       break;
               }
           }
       }
   }
```

### 完整代码

```java
import java.util.Scanner;

import static java.lang.System.exit;

public class reGister {
    static String[] arr1 = new String[15]; // 用于保存用户名
    static String[] arr2 = new String[15]; // 用于保存密码
    static Scanner scan = new Scanner(System.in);

    public static void main(String[] args) {
        arr1[0] = "彭挺";
        arr2[0] = "12345";
        while (true) {
            System.out.println("--------------欢迎使用简单的注册登入系统--------------");
            System.out.print("\t 1.查看操作");
            System.out.println("\t 2.注册操作");
            System.out.print("\t 3.登入操作");
            System.out.println("\t 4.退出系统");
            System.out.print("请输入你要进行的操作:");
            int pos = scan.nextInt();
            switch (pos) {
                case 1: {
                    looK();
                    break;
                }
                case 2: {
                    reGister();
                    break;
                }
                case 3: {
                    loGin();
                    break;
                }
                case 4: {
                    System.out.println("感谢你的使用,再见,欢迎下次使用!");
                    exit(0);
                    break;
                }
                default: {
                    System.out.println("你的输入有误,请重新输入");
                    break;
                }
            }
        }
    }
    public static void reGister() // 注册界面
    {
        String username =null;
        while (true){
            System.out.println("请输入你的用户名");
            username = scan.next();
            // 判断是否有一样的用户名
            int pos = 0 ;
            for(int i = 0 ;i < arr1.length ;i ++ ) {
                if (arr1[i] != null) {
                    if (arr1[i].equals(username)) {
                        pos = 1;
                        break;
                    }
                }
            }
            if (pos == 1){
                System.out.println("该用户名已经存在,请你重新输入!");
            }
            else break;
        }
        System.out.println("请输入你的密码");
        String password = scan.next();
        // 保存密码
        for(int i = 0 ; i < arr1.length ; i ++){
            if(arr1[i] == null){ // 如果为空就直接保存了
                arr1[i] = username;
                arr2[i] = password;
                break;  // 保存完结束
            }
        }
        System.out.println("注册成功!");
    }
    public static void loGin()  // 登入
    {
        String username = null;
        String passward = null;
        int x=0;
        while (true){
            int pos = 0; // 标记是否正确,保证可以一直输入
            System.out.println("请输入登入的用户名");
            username = scan.next();
            System.out.println("请输入登入的密码");
            passward = scan.next();
            // 检测登入正确
            for(int i = 0 ; i < arr1.length ; i ++ ){
                if(arr1[i] != null) {
                    if (arr1[i].equals(username) && arr2[i].equals(passward)) {
                        pos = 1;
                        break;
                    }
                }
            }
            if (pos == 1){
                System.out.println("登入成功");
                break;
            }
            else {
                if(x<=2) {
                    System.out.println("登入失败,请重新输入你的用户名和密码");
                    x++;
                }
                if(x>=3){
                    System.out.println("你已经三次输入错误,你是否选择退出该界面:(yes or not)");
                    String v = scan.next();
                    if( v.equals("yes") || v.equals("YES")) // 输入yes 或者 YES 就退出
                        break;
                }
            }
        }
    }
    public static void looK() // 查看
    {
        for(int i = 0 ; i < arr1.length; i++){
            if(arr1[i] != null )
            System.out.println("\t用户名:"+arr1[i]+"\n"+"\t密码:"+arr2[i]);
        }
    }
}

```

-   **

