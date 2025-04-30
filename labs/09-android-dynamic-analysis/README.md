# Android Dynamic Analysis

Dynamic analysis of Android applications allows you to follow the concrete flow of an application.
And also add hooks to applications to be able to trace the use of variables, input and potentially exploit workflows.

Dynamic analysis generally requires access to the device in order to run the application.
For Android, you can use the [Android Emulator](https://developer.android.com/studio/run/emulator).
You can then start the application inside the emulator and use dynamic analysis tools on it.

A prime dynamic analysis tool for mobile applications is [Frida](https://frida.re/).
We will present it next.

## Task 0: Frida for Dynamic Analysis

Install and configure Frida for the Android emulator.
Follow [this tutorial](https://medium.com/@SecureWithMohit/getting-started-with-frida-setting-up-on-an-emulator-47980170d2b2).

Then follow this [Frida tutorial](https://medium.com/android-news/hacking-android-app-with-frida-a85516f4f8b7).

Follow [this tutorial](https://securityboat.net/getting-started-into-android-security-part-2/) in order to understand what are the ways of inspecting an APK file.
So you will apply them later during the lab.

## Task 1: Android Dynamic Analysis

The 9 flags from the [InjuredAndroid](https://github.com/B3nac/InjuredAndroid) APK come back.

Follow [this walkthrough](https://blog.syselement.com/tcm/courses/mapt/5-android-dynamic) and find the remaining flags from the previous lab.

## Task 2: DIVA Android

[DIVA](https://github.com/payatu/diva-android) (*Damn Insecure and Vulnerable App*) is an application intentionally designed to be insecure.
The aim of the app is to teach developers/QA/security professionals, flaws that are generally present in the apps due poor or insecure coding practices.

Use [this repository](https://github.com/0xArab/diva-apk-file).
Aim to solve all the 13 challenges.

Hints:

- https://medium.com/@thecyberprince/diva-beta-android-app-walkthrough-4b0f114aa74
- https://medium.com/@0x3adly/android-static-code-analysis-57a9827ab164
- https://freedium.cfd/https://infosecwriteups.com/hunting-for-bugs-in-android-app-fe9158a556d3

## Task 3: IntechCTF

Solve the [IntechCTF challenge](https://github.com/maulvialf/CTF-Challs-Archive/blob/main/2022/intechctf-2022/Android/flag/flag.apk).

Follow [this tutorial](https://maulvialf.medium.com/write-up-intechctf-android-challenge-1ec1b1822be0) and try to find what the flag is.

## Other Resources

- https://blog.syselement.com/tcm/courses/mapt/5-android-dynamic
- https://tryhackme.com/r/room/androidhacking101
