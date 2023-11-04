# V1SCAN

V1SCAN is a tool for discovering 1-day vulnerabilities in the C/C++ target program (source code).
Principles and experimental results are discussed in our paper, which will be published in
32nd USENIX Security Symposium (Security 2023).

The current version is a prototype that can execute, but the source code is not well-organized. A later update might make the code clear.

## How to use

You can check the source code of V1SCAN, and our CVE and CPE datasets from Docker.
 
### 1. Execute V1SCAN-Docker
```
$ sudo docker run -it seunghoonwoo/v1scan_code:latest
# cd /home/V1SCAN
```
There are two testing projects in Docker: [Filament](https://github.com/google/filament) (v1.9.9) and [Turicreate](https://github.com/apple/turicreate) (v6.4.1).  
You can test them using the following commands:
```
# python3 Detector.py "./filament-1.9.9" Filament_OSSList.txt
# python3 Detector.py "./turicreate-6.4.1" Turicreate_OSSList.txt
```
- [4th, Nov 2023] Currently, I found a critical issue related to the dataset of V1SCAN-public: almost half of the CVE data disappeared during the refactoring phase. Therefore, when you test, you may get different results from the paper. Although it may take some time, I will work on supplementing the database. However, there is no problem with the code algorithm itself, so you can still run it and check the results.

### 2. Prepare the testing program (and its OSS components list)
Suppose we want to scan the Filament v1.9.9.  
We need to prepare the source code of the target program.
```
# git clone https://github.com/google/filament
# cd filament
# git checkout -f v1.9.9
```
Now "/home/V1SCAN/filament" is prepared.

Additionally, to use V1SCAN, the OSS component list of the target program is required.  
You can identify the components by using [CENTRIS](https://github.com/wooseunghoon/centris-public) or a manual examination.  

The name of the file containing the OSS list can be anything, but the contents should follow the format below.
```
// GitHub_team_name@@GitHub_project_name@#@
google@@googletest@#@
madler@@zlib@#@
...
```
Note that we consider only the OSS list provided by GitHub.   
For example, if OSS from https://github.com/madler/zlib is included in the target program, "madler@@zlib@#@" is added to the OSS list file.

### 3. Run Detector
```
# python3 Detector.py PATH_OF_TARGET_PROGRAM OSS_LIST_FILE
```
The vulnerability detection results will be printed.

### 4. How to use tools more effectively
To use V1SCAN more effectively, it is necessary to unify the OSS name of GitHub and the form provided by CPE because they are different in most cases (for example, Linux kernel v5.15 is referred as "torvalds/linux, v5.15" on GitHub and "linux/linux_kernel, 5.15" on CPE).

OSS and version names identified in our experiments were already manually mapped.   
The mapping for OSS names is stored in /home/V1SCAN/dataset/Git2CPE_OSS/git2cpe.txt.
```
// "GitHub_NAME":"CPE_NAME
{
 "torvalds@@linux": "linux@@linux_kernel",
 "gnutls@@gnutls": "gnu@@gnutls",
 ...
}
```

Version names for each OSS are mapped in /home/V1SCAN/dataset/Git2CPE_ver/.  
For example, the below file shows the contents of "/home/V1SCAN/dataset/Git2CPE_ver/linux@@linux_kernel.txt" 
```
// GitHub_VERSION@#@CPE_VERSION
v2.6.11@#@2.6.11
v2.6.12@#@2.6.12
...
```

In summary, to detect vulnerabilities of OSSs that are not currently included in V1SCAN's name mapping, this process should be further performed manually.


### FAQ
- If the code size of the target program is very large (e.g., FreeBSD), preprocessing may take a long time and consume a lot of disk space.
- Minor subprocess errors may occur during code analysis (negligible).

### About
This repository is authored and maintained by Seunghoon Woo.

For reporting bugs, you can submit an issue to [the GitHub repository](https://github.com/WOOSEUNGHOON/V1SCAN-public) or send me an email (<seunghoonwoo@korea.ac.kr>).
