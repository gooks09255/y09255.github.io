---
title: 파일 시스템과 파티션2
author: 노아
date: 2024-07-28 20:47:00 +0800
categories: [Linux, 파일 시스템과 파티션2]
tags: [리눅스, 파일 시스템, 파티션2]
pin: true
math: true
mermaid: true

---
파일 시스템과 파티션2

# 목차

- 파티션

# 파티션
- 리눅스에서 파티션이라는 말을 들어보았을 것이다. Ubuntu나 CentOS 등 OS를 서버에 설치할때, 파티션 설정도 진행해주는 작업을 하게 되는데, 여기서 이 파티션이란 구체적으로 어떤 것일까? 이번 포스팅에서 이 파티션에 대해 개념을 정리하기로 한다.
- 물리 서버를 보면 하나 이상의 하드 디스크 장치들이 있는 걸 알 수 있을 것이다. 여기서 이 물리 하드 디스크 하나를 여러 개의 논리적인 디스크로 분할하는 것을 파티션이라고 한다. 즉, 하나의 물리적 하드 디스크를 논리적인 단위로 나누어 사용하여, 각각의 파티션이 독립적인 저장 공간으로 작동하게 하여 서로 다른 운영체제나 파일 시스템을 사용하도록 하는 것을 말한다.
- 그래서, 만약에 물리 하드 디스크가 여러 개라고 하면. 리눅스의 경우, 각 디스크를 /dev/ 디렉터리 아래에 /sda, /sdb 등의 디바이스 파일로써 나타내고. 그 디스크 각각에 파티션을 생성하여, sda1, sda2 ... 등으로 각각의 파티션을 나타내게 되는 것이다.
- 이때, 기본 파티션이 하나의 물리 하드디스크당 4개 밖에 생성이 안되는데 이러한 기본 파티션 수 제한을 극복하기 위해 확장 파티션이란 개념이 등장한다. 그렇다면 기본 파티션과 확장 파티션은 무엇일까?

- <b>파티션의 종류</b>
  - 기본 파티션
    - 운영체제를 설치할 수 있는 파티션으로 MBR(Master Boot Record) 방식에서는 최대 4개의 기본 파티션을 만들 수 있다.
    - 예시 : /dev/sda1, /dev/sda2
  - 확장 파티션
    - 위에서 언급한대로, 4개 밖에 생성 안되는 등의 기본 파티션 수 제한을 극복하기 위한 파티션으로, MBR 방식에서는 4개의 파티션 중 하나를 확장 파티션으로 지정할 수 있으며 그 안에 여러 개의 논리 파티션을 생성할 수 있다. 
    - 확장 파티션 자체는 데이터를 저장할 수 없으며, 논리 파티션을 통해 데이터를 저장한다. 
    - ** 여기서 여러 물리 하드 디스크를 하나로 합치는 RAID 기술이라는 개념과 확장 파티션을 혼동하면 안된다. RAID는 여러 물리적 디스크를 하나로 합쳐 사용하는 개념으로, 확장 파티션과는 엄연히 다른 개념이다. 주의 바란다.**
    - 예시 : /dev/sda3
  - 논리 파티션
    - 확장 파티션 내부에 존재하며, 데이터를 저장하는데 사용된다.
    - MBR 방식에서는 확장 파티션 내부에 원하는 만큼 논리 파티션을 생성할 수 있다.
    - 예시 : /dev/sda5, /dev/sda6 등 (논리 파티션의 경우 sda5부터 시작함)
  - 스왑 파티션
    - 스왑 파티션은 컴퓨터에서 메모리(RAM)이 부족할 때 데이터를 임시로 저장하기 위해 사용하는 디스크 공간. 이는 메모리 부족 문제를 해결하고 시스템이 계속해서 안정적으로 작동할 수 있도록 돕는다.
    - 이때, 사용하는 방식으로 스왑 파티션을 기본 파티션으로 설정하는 방식으로 설정할지, 스왑 파일을 사용하여 파티션 개수 제한과 무관하게 디스크 공간을 스왑 파일로 활용하는 방식으로 설정할지 등이 존재한다. 이에 대한 구체적인 내용은 후술하기로 한다.

- <b>파티션의 장점</b>
  - 이러한 파티션의 장점으로는 아래의 내용들을 들 수 있다.
    - 시스템의 안정성과 보안성을 높이며, 서로 다른 파일 시스템을 사용할 수 있다.
    - 특정 파티션의 데이터가 손상되더라도 다른 파티션에 영향을 주지 않아 데이터 안정성을 높인다.

- <b>fdisk</b>
  - 이러한 파티션을 생성, 삭제, 수정하는 등 리눅스 및 유닉스 운영체제에서 디스크 파티션을 관리하는 명령줄 도구로 fdisk를 사용한다. fdisk를 사용하기 위한 기본 형식은 아래와 같다.
  - ```fdisk [옵션] [장치명]```
  - 이에 따른 각 옵션들에 대한 자세한 내용은 아래와 같다.
    - m : 명령어 목록 표시
    - p : 현재 파티션 테이블 출력
    - n : 새 파티션 생성
      - p : 기본 파티션 생성
      - e : 확장 파티션 생성
      - l : 논리 파티션 생성
    - d : 기존 파티션 삭제
    - t : 파티션 유형 변경
    - w : 변경 사항 저장하고 fdisk 종료
    - q : 변경 사항 저장하지 않고 fdisk 종료
  - 새 파티션 생성하는 과정은 아래와 같다. 
  
  새 파티션 생성시 먼저, fdisk 명령어를 사용하여 특정 디스크를 열 수 있는데 예를 들어 /dev/sda를 사용하려면 아래와 같이 입력한다.
  ```
  sudo fdisk /dev/sda
  ```

그 다음 과정은 아래와 같다.
```
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-2097151, default 2048): 2048
Last sector, +sectors or +size{K,M,G,T,P} (2048-2097151, default 2097151): +500M
```

위의 명령은 새로운 기본 파티션을 생성하여 크기를 500MB로 설정한다. 그 다음에는 파티션 테이블을 저장하는 단계로, 파티션 변경 사항을 저장하고 종료하기 위해 다음 명령을 사용한다.

```Command (m for help): w```

그 다음 아래 명령어로 fdisk로 변경한 파티션을 확인한다(해당 명령어는 디스크의 모든 파티션 정보를 출력하는 명령어).

```sudo fdisk -l /dev/sda```


> **참조**<br>
>
> 확장 파티션과 논리 파티션이 어떻게 사용되는지에 대한 예시를 통해 확장 파티션 등의 내용에 대한 이해를 해보도록 하자. <br>
> 
> <b>예시 : MBR 디스크의 파티션 구성</b><br>
> - 가정<br>
>   - 하나의 물리적 하드 디스크가 있으며, 크기는 1TB이다.<br>
>   - 이 디스크에 두 개의 운영체제(Windows와 Linux)를 설치하고, 데이터를 관리하기 위해 여러 파티션을 생성한다.<br>
> - 파티션 구성<br>
>   - 1. 기본 파티션1(Primary Partition 1):
      - 용도 : Windows 운영체제 설치
      - 크기 : 200GB
      - 파일 시스템 : NTFS
  - 2. 기본 파티션(Primary Partition 2):
    - 용도 : Linux 운영체제 설치
    - 크기 : 100GB
    - 파일 시스템 : ext4
  - 3. 확장 파티션(Extended Partition):
    - 용도 : 논리 파티션을 포함하는 컨테이너
    - 크기 : 나머지 디스크 전체(700GB)
  - 4. 논리 파티션(Logical Partitions) 내 확장 파티션:
    - 논리 파티션1:
      - 용도 : 문서 저장
      - 크기 : 200GB
      - 파일 시스템 : NTFS
    - 논리 파티션2:
      - 용도 : 미디어 파일 저장(음악, 동영상 등)
      - 크기 : 300GB
      - 파일 시스템 : NTFS
    - 논리 파티션3:
      - 용도 : 백업 저장소
      - 크기 : 200GB
      - 파일 시스템 : ext4
    - 이에 따라 생성된 파티션 구성도는 아래와 같다.

> - 파티션 구성도
```
|------------------------ 1TB Physical Disk -----------------------------|
|                    |            |              Extended                |
| Primary 1 (200GB)  | Primary 2  |--------------------------------------|
| Windows            | Linux      | Logical 1   | Logical 2  | Logical 3 |
| NTFS               | ext4       | 200GB       | 300GB      | 200GB     |
|                    | 100GB      | NTFS        | NTFS       | ext4      |
```

이렇게 생성되는 확장 파티션과 논리 파티션을 통해 알 수 있듯이, 확장 파티션이 생성되며, 논리 파티션은 MBR 디스크에서 기본 파티션 수의 제한을 극복하고 다양한 파일 시스템을 관리할 수 있도록 하는 것이다.

- <b>파티션 현황 확인 방법</b>
  - 이러한 파티션 현황을 확인하는 방법으로는 두가지 방식이 존재한다. 위에서 설명했듯이 fdisk를 통한 방법과 lsblk 명령어를 통한 방법인데, 각각에 대해 간단히 알아보고 넘어가기로 하자.
    - fdisk 명렁어
      - fdisk는 MBR 디스크에서 파티션 테이블을 관리하는 도구로, 파티션 정보를 확인할 때도 사용된다.
        - ```sudo fdisk -l```이란 명령어를 실행하면 아래와 같이 출력이 됨을 알 수 있다.

```
Disk /dev/sda: 1 TB, 1000000000000 bytes, 1953125000 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x12345678

Device     Boot Start        End    Sectors   Size Id Type
/dev/sda1  *     2048     409599     407552   199M 83 Linux
/dev/sda2        409600 1953124991 1952715392  931G 5 Extended
/dev/sda5        409648 1953124991 1952715344  931G 83 Linux
```

그러면 기본 파티션 정보와 확장 파티션 정보가 위 출력에서 보이는 바와 같이 확인할 수 있음을 알 수 있다. 그러면, lsblk는 어떨까?
  - lsblk 명령어
      - lsblk 명령어는 블록 디바이스 정보를 트리 구조로 시각적으로 보여준다.
        - lsblk 명령어 실행시 아래와 같이 출력이 됨을 알 수 있다.

```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 931.5G  0 disk 
├─sda1   8:1    0   200M  0 part /boot
├─sda2   8:2    0   931G  0 part 
│ └─sda5 8:5    0   931G  0 lvm  /
```

- <b>디스크와 장치명</b>
  - 리눅스에서는 하드 디스크와 기타 저장 장치를 /dev/sd* 형식으로 표시한다. IDE 인터페이스를 사용하는 디스크의 경우에는 /dev/hd*의 장치명을 사용하지만, SATA, SCSI, SAS 장치 모두에서는 /dev/sd* 형식으로 표시한다. 여기서 sda, sdb, sdc 등은 장치의 순서를 나타내며, 파티션은 sda1, sda2와 같은 형식으로 구분된다. 예를 들어 SATA 장치에서 첫번째 SATA 장치의 경우 /dev/sda로 나타내고, 두번째 SATA 장치는 /dev/sdb로 표시하는 원리이다. 
  - 그리고 이때, LVM 또는 RAID 등을 사용하는 경우, 장치와 파티션 표기 방식은 다를 수 있으며, /dev/mapper/ 또는 /dev/md* 와 같은 경로를 사용할 수 있다. 
  - 그렇다면, 여기서 의문이 있을 것이다. SATA와 SCSI 장치의 경우, /dev/sd* 형식으로 동일하게 표시되는데, 그 이유는 무엇이고 어떻게 구분할 수 있는가? 먼저 동일하게 표시되는 이유를 설명하자면, 리눅스에서는 저장 장치의 물리적 인터페이스(IDE, SATA, SCSI 등)를 추상화하여 동일한 장치명 규칙을 사용하는데 이 방식이 장치 드라이버가 다양한 인터페이스를 일관되게 처리할 수 있도록 돕기 때문이다. 그러나, 이렇게 동일하게 보일지라도 실제 하드웨어 인터페이스와 드라이버에 따라 적절하게 동작하기 때문에 염려할 필요는 없다. 
  - 그렇다면 어떻게 동일한 장치명을 구분할 수 있을까? 이에 대해서는 명령어 사용을 통해 구분할 수 있다. 이에 대한 내용은 아래와 같다.
    - lsblk 명령어 사용한 방법
      - lsblk는 위에서 설명했듯이, 시스템의 블록 장치 목록을 표시하는 것이라고 설명하였다. 이 명령어는 장치의 유형과 속성을 확인하는데 유용하며, ```lsblk -d -o NAME,TRAN``` 명령어를 사용하며, 출력시 아래와 같이 나온다. 아래에서 TRAN 열이 SATA인지 SAS인지 등을 보여주며, 이를 통해 구분할 수 있다고 볼 수 있다.

```
NAME   TRAN
sda    sata
sdb    sas
```

  - lshw 명령어 사용한 방법
    - lshw 명령어는 시스템의 하드웨어 정보를 자세히 출력한다. 장치의 상세 정보를 포함하고 있으며, 장치 유형을 확인할 수 있다.
      - ```sudo lshw -class disk``` 명령어를 사용하며, 사용할 시 아래와 같이 출력이 나옴을 알 수 있다. 아래에서 description 필드에서 정보를 확인할 수 있다.

```
*-disk
     description: ATA Disk
     product: Samsung SSD 860
     ...
*-disk
     description: SAS Disk
     product: Seagate ST8000DM004
     ...
```

  - 이 밖에도, smartctl 명령어를 사용하는 방법과 dmesg 명령어를 사용하는 방법 등이 있으나 이 이상은 생략하기로 하고, 나중에 다시 정리하는 방향으로 진행하겠다. 또한, 이 과정에서 하드 디스크의 종류인 HDD, SSD와 장치와 시스템 간 연결을 해주는 역할인 SCSI나 IDE 같은 내용들도 나중에 다시 정리하는 방향으로 진행하고자 한다. 따로 알아보았으나, 해당 내용까지 서술하자니 내용이 보다 복잡해지고 깊게 파고들어야 할 거 같아서 이정도로 정리하는 것으로 하고, 스왑 파티션을 정리하면서 이번 포스팅을 마무리하겠다.

> **참조**<br>
> 스왑 파티션
> 
> 스왑 파티션(Swap Partition)은 Linux와 같은 운영 체제에서 메모리 부족을 보완하기 위해 사용되는 특별한 파티션이다. 스왑 공간은 시스템의 물리적 RAM이 부족할 때, 데이터를 일시적으로 저장하여 메모리 부족 문제를 완화하는데 도움을 준다.
> 이러한 스왑 파티션 설정 방법은 스왑 파티션 생성 및 설정하는 방법과 스왑 파일 생성 및 설정 두가지 방식이 있는데, 여기서는 스왑 파일 생성 및 설정을 통한 방법에 대해 정리하기로 한다.
> 
> 1. 스왑 파일 생성
```
sudo fallocate -l 2G /swapfile
```
> - 2GB 크기의 스왑 파일을 생성한다. 이때, fallocate 대신 dd 명령어를 사용할 수도 있다.
> 2. 스왑 파일 권한 설정
```
sudo chmod 600 /swapfile
```
> 3. 스왑 파일 포맷
```
sudo mkswap /swapfile
```
> 4. 스왑 파일 활성화
```
sudo swapon /swapfile
```
> 5. 스왑 파일 자동 마운트 설정
> - /etc/fstab 파일에 스왑 파일을 자동으로 마운트하도록 추가한다.
>   - 예시 : ```/swapfile  none  swap  sw  0  0```


