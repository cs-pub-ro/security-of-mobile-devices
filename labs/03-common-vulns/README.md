# Replicating Common Vulnerabilities and Threats

Mobile systems and applications suffer from common vulnerabilities and threats, found on both Android and iOS platforms.
These may arise from programming errors from application programmers, from framework issues or platform poor design decisions.

OWASP published [OWASP Mobile Top 10 vulnerabilities for mobile applications](https://owasp.org/www-project-mobile-top-10/).
These are typically mishandling of data, APIs and insufficient verification by applications. Even if applications themselves are written in higher level more secure programming languages (compared to C).

## Setup

For analyzing application binaries, it is a good idea to have [Ghidra](https://ghidra-sre.org/) installed.
Follow the [instructions](https://ghidra-sre.org/InstallationGuide.html) to download Ghidra.

## Android

An index of older Android vulnerabilities are [here](https://androidvulnerabilities.org/index.html).

### Insecure Shop

For this task we aim to replicate the exploitation of a vulnerable app named [InsecureShop](https://www.insecureshopapp.com/).

Follow the instructions in the [Challenges page](https://docs.insecureshopapp.com/) or [this post](https://medium.com/@prnz_offl/exploring-common-vulnerabilities-in-android-insecure-logging-hardcoded-credentials-insecure-data-a3c9e0cb2611), collect all the vulnerabilities of the application and exploit them.

### Damn Vulnerable Bank

For this task we aim to replicate the exploitation of a vulnerable app named [Damn Vulnerable Bank](https://github.com/rewanthtammana/Damn-Vulnerable-Bank).

Follow the instructions from the [site](https://rewanthtammana.com/damn-vulnerable-bank/index.html) to collect all vulnerabilities of the application and exploit them.

### All Safe

For this task we aim to replicate the exploitation of a vulnerable app named [All Safe](https://github.com/t0thkr1s/allsafe).

Follow the instructions in the `README.md` to collect all vulnerabilities of the application and exploit them.

## iOS

### Damn Vulnerable iOS App

For this task we aim to replicate the exploitation of a vulnerable app conveniently titled [DVIA](https://github.com/prateek147/DVIA-v2).

Follow [this walkthrough](https://payatu.com/blog/n00bs-approach-solving-dvia-v2/) to collect all vulnerabilities of the application and exploit them.

### Oversecured Vulnerable iOS App

For this task we aim to replicate the exploitation of a vulnerable app conveniently titled [Oversecured Vulnerable iOS App](https://github.com/oversecured/OversecuredVulnerableiOSApp).

Follow [this walkthrough](https://medium.com/@raafat.abualazm96/oversecured-ios-app-walkthrough-deeplinks-part-1-2cc7e50e6378) to collect all vulnerabilities of the application and exploit them.

## Extra Info

Other Lists of Vulnerable Apps

- [OWASP iGoat](https://code.google.com/archive/p/owasp-igoat/)
- [UnSAFE Bank](https://github.com/lucideus-repo/UnSAFE_Bank)
- [MITRE: Vulnerable Mobile Apps](https://mitre.github.io/vulnerable-mobile-apps/)
- [100 Most Vulnerable Apps, Systems & Platforms to Practice Penetration Testing -2021](https://shamsher-khan-404.medium.com/100-most-vulnerable-apps-systems-platforms-to-practice-penetration-testing-2021-e76ca7235e74)
- [UnCrackable Mobile Apps (OWASP MASTG)](https://mas.owasp.org/crackmes/)
- [List of intentionally vulnerable Android apps](https://pentester.land/blog/list-of-intentionally-vulnerable-android-apps/)
- [Awesome Vulnerable Apps (Mobile Security)](https://github.com/vavkamil/awesome-vulnerable-apps?tab=readme-ov-file#Mobile-Security)
- [GitHub Vulnerable Android Apps](https://github.com/topics/vulnerable-android-apps)
- [ExploitMe Mobile Android Labs](https://securitycompass.github.io/AndroidLabs/)
- [ExploitMe Mobile iPhone Labs](https://securitycompass.github.io/iPhoneLabs/index.html)
- [7+3 Vulnerable Apps To Get Started With Android Penetration Testing](https://thelicato.medium.com/7-3-vulnerable-apps-to-get-started-with-android-penetration-testing-fd29c6279e6)
- [10 Vulnerable Android Applications for beginners to learn Android hacking](https://www.linkedin.com/pulse/10-vulnerable-android-applications-beginners-learn-hacking-anugrah-sr/)
