---
layout: post
title:  "make git page with jekyll"
date:   2015-01-12
categories: unbuntu linux file system
---



## 리눅스 파일시스템과 하드 마운트하기 (Mount Hard drive to File System)

#### 하드 디스크 포맷 (Format your Hard disk)
```
Setting - disks
```

#### 마운트 할 디렉토리 설정 (Make directory to mount)
```
$ sudo mkdir /newHard
$ sudo chown userId:userGroup /경로(directory location)
$ sudo chmod 775 /newHard
//하위 폴더에도 같은 권한 주기
$ sudo chown -R
```

#### 외장 디스크(ext dist, usb) 마운트하기 (mount)

disks를 통해 현재 등록된 하드를 확인한다. 등록하고 싶은 하드 디바이스를 확인하다. ex) /dev/sda1 , Ext4..

```
$ sudo mount -t 파일시스템(ex4, utfs, vfat..) 하드 디바이스 마운트할 위치
$ sudo mount -t ext4 /dev/sd? /newHard
```

reference : http://blog.naver.com/pjfile/50116292104

#### 하드디스크 자동마운트하기
disks를 통해 현재 등록된 하드를 확인한다. 등록하고 싶은 하드 디바이스를 확인하다. ex) /dev/sda1 , Ext4..

*  현재 자동마운트 하려는 disk의 uuid를 기억한다.

```
$ ls -l /dev/disk/by-uuid
```
*  vi를 통해 자동마운트할 디스크 설정을 적어둔다.

```
$ sudo /etc/fstab
```
*  UUID = disk uid,  disk마운트 될 위치 파일, 파티션의 파일시스템 종류(ext4, ftfs, vfat, auto..) , 옵션 (부팅시 자동마운트 할 것인지, 제한된 사용자가 파일시스템을 마운트 할수 있는지..), Dump (0이 아닌값은 파일세스템이 백업 되어야 함, 0 은 백업이 없음을 말한다.), Pass(fsck옵션. 어떤 파일시스템이 체크되어야 하는지 순서 확인. 파일시스템이 체크되지 않을것을 의미 0)

```vi
UUID=1a8ee29e-6ba1-4521-877d-d70ebc39f606   /media/disk   ntfs   defaults   0   0
```
위의 설정을 fstab에 vi를 열어 추가하면 끝!!!!

ref : http://hyoungx.tistory.com/26

