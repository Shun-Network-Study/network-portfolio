# 第6回：VLANの設定(基本)
- **実施日**: 2025/6/26
- **所要時間**: 約4時間

## 学習内容
- Layer2でのVLANの設定
- Layer3でのVLANの設定
- 同じVLAN同士で通信が可能なことを確認
- VLAN間ルーティング設定後、異なるVLAN同士で通信が可能なことを確認
- DHCPサーバ（ルータ）・DHCPクライアント（PC）の設定

## 使用機器
- **L2スイッチ**: Catalyst 2960 × 2台
- **検証用PC** × 4台
- **LANケーブル** × 5本

## 構成図
![構成図](/study06/images/topology6_1.png)

## 設定内容

### スイッチ（SWA）の設定
[スイッチ（SWA）の全ての設定ファイルを見る](/study06/configs/switch-config-SWA.txt)

**ホスト名の設定**
```
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname SWA
```

**enableパスワードの設定**
```
SWA(config)#enable password Cisco
SWA(config)#end
SWA#
%SYS-5-CONFIG_I: Configured from console by console
```

**設定の確認**
```
SWA#show run
SWA#show running-config 
Building configuration...

Current configuration : 1101 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname SWA
!
enable password Cisco
!
!
!
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
```

**設定の保存**
```
SWA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
SWA#
```

### スイッチ（SWB）の設定
[スイッチ（SWB）の全ての設定ファイルを見る](/study06/configs/switch-config-SWB.txt)

**ホスト名の設定**
```
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname SWB
```

**enableパスワードの設定**
```
SWB(config)#enable password Cisco
SWB(config)#end
SWB#
%SYS-5-CONFIG_I: Configured from console by console
```

**設定の確認**
```
SWB#show run
SWB#show running-config 
Building configuration...

Current configuration : 1101 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname SWB
!
enable password Cisco
!
!
!
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
```

**設定の保存**
```
SWB#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
SWB#
```

### 検証①同じVLAN同士で通信が可能なことを確認する

**SWAにVLAN 10、20を作成**
```
SWA>en
Password: 
SWA#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SWA(config)#vlan 10
SWA(config-vlan)#exit
SWA(config)#vlan 20
SWA(config-vlan)#exit
```

**アクセスポートの設定**
```
SWA(config)#interface fastethernet 0/1
SWA(config-if)#switchport mode access
SWA(config-if)#switchport access vlan 10
SWA(config-if)#exit
SWA(config)#interface fastethernet 0/2
SWA(config-if)#switchport mode access
SWA(config-if)#switchport access vlan 20
SWA(config-if)#exit
```

**トランクポートの設定**
```
SWA(config)#interface fastethernet 0/24
SWA(config-if)#switchport mode trunk

SWA(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/24, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/24, changed state to up

SWA(config-if)#exit
SWA(config)#exit
SWA#
%SYS-5-CONFIG_I: Configured from console by console
```

**設定したVLANの確認**
```
SWA#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/3, Fa0/4, Fa0/5, Fa0/6
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Gig0/1, Gig0/2
10   VLAN0010                         active    Fa0/1
20   VLAN0020                         active    Fa0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
10   enet  100010     1500  -      -      -        -    -        0      0
20   enet  100020     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0   
1003 tr    101003     1500  -      -      -        -    -        0      0   
1004 fdnet 101004     1500  -      -      -        ieee -        0      0   
1005 trnet 101005     1500  -      -      -        ibm  -        0      0   

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------

Remote SPAN VLANs
------------------------------------------------------------------------------

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------
SWA#
```
- 左端のVLANの項目に、10と20があることを確認
- VLAN10がFa0/1、VLAN20がFa0/2のアクセスポートが表示されていることを確認
- トランクポートのFa0/24は表示されないことを確認
- #show vlan → アクセスポートは表示されるが、トランクポートは表示されない

**設定の保存**
```
SWA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
SWA#
```

**SWBにVLAN 10、20を作成**
```
SWB>en
Password: 
SWB#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SWB(config)#vlan 10
SWB(config-vlan)#exit
SWB(config)#vlan 20
SWB(config-vlan)#exit
```

**アクセスポートの設定**
```
SWB(config)#interface fastethernet 0/1
SWB(config-if)#switchport mode access
SWB(config-if)#switchport access vlan 10
SWB(config-if)#exit
SWB(config)#interface fastethernet 0/2
SWB(config-if)#switchport mode access
SWB(config-if)#switchport access vlan 20
SWB(config-if)#end
SWB#
%SYS-5-CONFIG_I: Configured from console by console
```

**トランクポートの設定**
```
SWB#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SWB(config)#interface fastethernet 0/24
SWB(config-if)#switchport mode trunk
SWB(config-if)#end
SWB#
%SYS-5-CONFIG_I: Configured from console by console
```

**設定したVLANの確認**
```
SWB#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/3, Fa0/4, Fa0/5, Fa0/6
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Gig0/1, Gig0/2
10   VLAN0010                         active    Fa0/1
20   VLAN0020                         active    Fa0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
10   enet  100010     1500  -      -      -        -    -        0      0
20   enet  100020     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0   
1003 tr    101003     1500  -      -      -        -    -        0      0   
1004 fdnet 101004     1500  -      -      -        ieee -        0      0   
1005 trnet 101005     1500  -      -      -        ibm  -        0      0   

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------

Remote SPAN VLANs
------------------------------------------------------------------------------

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------
SWB#
```
- 左端のVLANの項目に、10と20があることを確認
- VLAN10がFa0/1、VLAN20がFa0/2のアクセスポートが表示されていることを確認
- トランクポートのFa0/24は表示されないことを確認
- #show vlan → アクセスポートは表示されるが、トランクポートは表示されない

**設定の保存**
```
SWB#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
SWB#
```

### 検証用PC（PC A・PC B・PC C・PC D）の設定
#### 検証用PC（PC A・PC B・PC C・PC D）にIPアドレスを設定する
- PC A → IPアドレス: 192.168.10.1　サブネットマスク: 255.255.255.0
![検証用PC（PC A）にIPアドレスを設定する](/study06/images/課題6_1.png)

- PC B → IPアドレス: 192.168.20.1　サブネットマスク: 255.255.255.0
![検証用PC（PC B）にIPアドレスを設定する](/study06/images/課題6_2.png)

- PC C → IPアドレス: 192.168.10.2　サブネットマスク: 255.255.255.0
![検証用PC（PC C）にIPアドレスを設定する](/study06/images/課題6_3.png)

- PC D → IPアドレス: 192.168.20.2　サブネットマスク: 255.255.255.0
![検証用PC（PC D）にIPアドレスを設定する](/study06/images/課題6_4.png)

#### コマンドプロンプトで、検証用PC（PC A・PC B・PC C・PC D）の設定を確認
- ipconfigコマンドで、PC AのIPアドレスの確認
![PC AのIPアドレスの確認](/study06/images/課題6_5.png)

- ipconfigコマンドで、PC BのIPアドレスの確認
![PC BのIPアドレスの確認](/study06/images/課題6_6.png)

- ipconfigコマンドで、PC CのIPアドレスの確認
![PC CのIPアドレスの確認](/study06/images/課題6_7.png)

- ipconfigコマンドで、PC DのIPアドレスの確認
![PC DのIPアドレスの確認](/study06/images/課題6_8.png)

#### PC AからPC C（IPアドレス: 192.168.10.2）へPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC AからPC CへPingを使用して疎通確認](/study06/images/課題6_9.png)

#### PC BからPC D（IPアドレス: 192.168.20.2）へPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC BからPC DへPingを使用して疎通確認](/study06/images/課題6_10.png)

### 検証②異なるVLAN同士で通信ができないことを確認する

#### PC BからPC C（IPアドレス: 192.168.10.2）へPingを使用して疎通確認
- パケットを4回送信し、0回受信し、100%の損失を確認
- VLAN 10とVLAN 20の異なるVLAN同士では疎通できないことを確認
![PC BからPC CへPingを使用して疎通確認](/study06/images/課題6_11.png)

### 検証③VLAN間ルーティング設定後、異なるVLAN同士で通信が可能なことを確認する
- 異なるVLANで疎通ができないので、VLAN間ルーティングを設定

## 使用機器
- **ルータ**: Cisco 2911 × 2台　※Cisco 891FJを利用できなかったため、コマンド制限の少ないCisco 2911を使用
- **L2スイッチ**: Catalyst 2960 × 2台
- **検証用PC** × 4台
- **LANケーブル** × 7本

## 構成図
![構成図](/study06/images/topology6_2.png)

## 設定内容

### ルータ（RTA）の設定
[ルータ（RTA）の全ての設定ファイルを見る](/study06/configs/router-config-RTA.txt)

**ホスト名の設定**
```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname RTA
```

**enableパスワードの設定**
```
RTA(config)#enable password Cisco
```

**VTYパスワードの設定**
```
RTA(config)#line vty 0 4
RTA(config-line)#password Cisco
RTA(config-line)#login
RTA(config-line)#exit
```

**IPアドレスの設定**
```
RTA(config)#interface Gigabitethernet 0/1
RTA(config-if)#ip address 192.168.100.1 255.255.255.0
RTA(config-if)#no shutdown

RTA(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

RTA(config-if)#exit
```

**デフォルトルートの設定**
```
RTA(config)#ip route 0.0.0.0 0.0.0.0 192.168.100.2
```

**DHCPサーバの設定**
```
RTA(config)#service dhcp
RTA(config)#ip dhcp pool VLAN10
RTA(dhcp-config)#network 192.168.10.0 255.255.255.0
RTA(dhcp-config)#default-router 192.168.10.254
RTA(dhcp-config)#exit
RTA(config)#ip dhcp pool VLAN20
RTA(dhcp-config)#network 192.168.20.0 255.255.255.0
RTA(dhcp-config)#default-router 192.168.20.254
RTA(dhcp-config)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console
```

**設定の確認**
```
RTA#show run
RTA#show running-config 
Building configuration...

Current configuration : 955 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname RTA
!
!
!
enable password Cisco
!
!
!
ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.254
ip dhcp pool VLAN20
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.254
!
!
!
ip cef
no ipv6 cef
!
!
!
!
license udi pid CISCO2911/K9 sn FTX1524BLB5-
!
!
!
!
!
!
!
!
!
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface GigabitEthernet0/1
 ip address 192.168.100.1 255.255.255.0
 duplex auto
 speed auto
!
interface GigabitEthernet0/2
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
ip classless
ip route 0.0.0.0 0.0.0.0 192.168.100.2 
!
ip flow-export version 9
!
!
!
!
!
!
!
line con 0
!
line aux 0
!
line vty 0 4
 password Cisco
 login
!
!
!
end
```

**設定の保存**
```
RTA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTA#
```

### ルータ（RTB）の設定
[ルータ（RTB）の全ての設定ファイルを見る](/study06/configs/router-config-RTB.txt)

**ホスト名の設定**
```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname RTB
```

**enableパスワードの設定**
```
RTB(config)#enable password Cisco
```

**VTYパスワードの設定**
```
RTB(config)#line vty 0 4
RTB(config-line)#password Cisco
RTB(config-line)#login
RTB(config-line)#exit
```

**IPアドレスの設定**
```
RTB(config)#interface Gigabitethernet 0/1
RTB(config-if)#ip address 192.168.100.2 255.255.255.0
RTB(config-if)#no shutdown

RTB(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

RTB(config-if)#exit
```

**デフォルトルートの設定**
```
RTB(config)#ip route 0.0.0.0 0.0.0.0 192.168.100.1
```

**DHCPサーバの設定**
```
RTB(config)#service dhcp
RTB(config)#ip dhcp pool VLAN30
RTB(dhcp-config)#network 192.168.30.0 255.255.255.0
RTB(dhcp-config)#default-router 192.168.30.254
RTB(dhcp-config)#exit
RTB(config)#ip dhcp pool VLAN40
RTB(dhcp-config)#network 192.168.40.0 255.255.255.0
RTB(dhcp-config)#default-router 192.168.40.254
RTB(dhcp-config)#end
RTB#
%SYS-5-CONFIG_I: Configured from console by console
```

**設定の確認**
```
RTB#show run
RTB#show running-config 
Building configuration...

Current configuration : 955 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname RTB
!
!
!
enable password Cisco
!
!
!
ip dhcp pool VLAN30
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.254
ip dhcp pool VLAN40
 network 192.168.40.0 255.255.255.0
 default-router 192.168.40.254
!
!
!
ip cef
no ipv6 cef
!
!
!
!
license udi pid CISCO2911/K9 sn FTX15246RH4-
!
!
!
!
!
!
!
!
!
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface GigabitEthernet0/1
 ip address 192.168.100.2 255.255.255.0
 duplex auto
 speed auto
!
interface GigabitEthernet0/2
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
ip classless
ip route 0.0.0.0 0.0.0.0 192.168.100.1 
!
ip flow-export version 9
!
!
!
!
!
!
!
line con 0
!
line aux 0
!
line vty 0 4
 password Cisco
 login
!
!
!
end
```

**設定の保存**
```
RTB#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTB#
```

### スイッチ（SWB）の設定

**SWBのアクセスポートの設定変更**
```
SWB>en
Password: 
SWB#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SWB(config)#interface fastethernet 0/1
SWB(config-if)#no switchport access vlan 10
SWB(config-if)#switchport access vlan 30
% Access VLAN does not exist. Creating vlan 30
SWB(config-if)#exit
SWB(config)#vlan 30
SWB(config-vlan)#exit
SWB(config)#vlan 40
SWB(config-vlan)#exit
SWB(config)#interface fastethernet 0/1
SWB(config-if)#switchport access vlan 30
SWB(config-if)#exit
SWB(config)#interface fastethernet 0/2
SWB(config-if)#no switchport access vlan 20
SWB(config-if)#switchport access vlan 40
SWB(config-if)#end
SWB#
%SYS-5-CONFIG_I: Configured from console by console

SWB#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/3, Fa0/4, Fa0/5, Fa0/6
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
10   VLAN0010                         active    
20   VLAN0020                         active    
30   VLAN0030                         active    Fa0/1
40   VLAN0040                         active    Fa0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
10   enet  100010     1500  -      -      -        -    -        0      0
20   enet  100020     1500  -      -      -        -    -        0      0
30   enet  100030     1500  -      -      -        -    -        0      0
40   enet  100040     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0   
1003 tr    101003     1500  -      -      -        -    -        0      0   
1004 fdnet 101004     1500  -      -      -        ieee -        0      0   
1005 trnet 101005     1500  -      -      -        ibm  -        0      0   

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------

Remote SPAN VLANs
------------------------------------------------------------------------------

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------
SWB#
```

**設定の保存**
```
SWB#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
SWB#
```

- VLANが存在しない場合は、先に(config)#vlan [VLAN番号]でVLANを作成してから、(config-if)#switchport access vlan [VLAN番号]で設定を行う必要がある
- アクセスポートの設定変更を行う場合、VLANが作成されているかどうか確認する必要がある

**RTAにサブインターフェースの設定**
```
RTA>en
Password: 
RTA#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTA(config)#int
RTA(config)#interface Gi
RTA(config)#interface GigabitEthernet 0/0
RTA(config-if)#no shutdown

RTA(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

RTA(config-if)#exit
RTA(config)#interface GigabitEthernet 0/0.10
RTA(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0.10, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.10, changed state to up

RTA(config-subif)#encapsulation dot1q 10
RTA(config-subif)#ip address 192.168.10.254 255.255.255.0
RTA(config-subif)#no shutdown
RTA(config-subif)#exit
RTA(config)#interface GigabitEthernet 0/0.20
RTA(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0.20, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.20, changed state to up

RTA(config-subif)#encapsulation dot1Q 20
RTA(config-subif)#ip address 192.168.20.254 255.255.255.0
RTA(config-subif)#no shutdown
RTA(config-subif)#exit
RTA(config)#exit
RTA#
%SYS-5-CONFIG_I: Configured from console by console
```

**設定したサブインターフェースの確認**
```
RTA#show running-config
Building configuration...

Current configuration : 1143 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname RTA
!
!
!
enable password Cisco
!
!
!
ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.254
ip dhcp pool VLAN20
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.254
!
!
!
ip cef
no ipv6 cef
!
!
!
!
license udi pid CISCO2911/K9 sn FTX1524BLB5-
!
!
!
!
!
!
!
!
!
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.254 255.255.255.0
!
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.254 255.255.255.0
!
interface GigabitEthernet0/1
 ip address 192.168.100.1 255.255.255.0
 duplex auto
 speed auto
!
interface GigabitEthernet0/2
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
ip classless
ip route 0.0.0.0 0.0.0.0 192.168.100.2 
!
ip flow-export version 9
!
!
!
!
!
!
!
line con 0
!
line aux 0
!
line vty 0 4
 password Cisco
 login
!
!
!
end
```
- GigabitEthernet0/0.10とGigabitEthernet0/0.20のサブインターフェースが作成されていることを確認
- GigabitEthernet0/0.10のIPアドレスが192.168.10.254であることを確認
- GigabitEthernet0/0.20のIPアドレスが192.168.20.254であることを確認

**物理ポート、サブインターフェースが有効化されていることを確認**
```
RTA#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     unassigned      YES unset  up                    up 
GigabitEthernet0/0.10  192.168.10.254  YES manual up                    up 
GigabitEthernet0/0.20  192.168.20.254  YES manual up                    up 
GigabitEthernet0/1     192.168.100.1   YES manual up                    up 
GigabitEthernet0/2     unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
RTA#
```
- GigabitEthernet0/0 の Status が up であることを確認
- GigabitEthernet0/0.10 の Status が up であることを確認
- GigabitEthernet0/0.20 の Status が up であることを確認

**設定の保存**
```
RTA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTA#
```

**RTBにサブインターフェースの設定**
```
RTB>en
Password: 
RTB#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTB(config)#int
RTB(config)#interface GigabitEthernet0/0
RTB(config-if)#no shutdown

RTB(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

RTB(config-if)#interface GigabitEthernet0/0.30
RTB(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0.30, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.30, changed state to up

RTB(config-subif)#encapsulation dot1q 30
RTB(config-subif)#ip address 192.168.30.254 255.255.255.0
RTB(config-subif)#no shutdown
RTB(config-subif)#exit
RTB(config)#interface GigabitEthernet0/0.40
RTB(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0.40, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.40, changed state to up

RTB(config-subif)#encapsulation dot1q 40
RTB(config-subif)#ip address 192.168.40.254 255.255.255.0
RTB(config-subif)#no shutdown
RTB(config-subif)#exit
RTB(config)#exit
RTB#
%SYS-5-CONFIG_I: Configured from console by console
```

**設定したサブインターフェースの確認**
```
RTB#show running-config
Building configuration...

Current configuration : 1143 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname RTB
!
!
!
enable password Cisco
!
!
!
ip dhcp pool VLAN30
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.254
ip dhcp pool VLAN40
 network 192.168.40.0 255.255.255.0
 default-router 192.168.40.254
!
!
!
ip cef
no ipv6 cef
!
!
!
!
license udi pid CISCO2911/K9 sn FTX15246RH4-
!
!
!
!
!
!
!
!
!
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.254 255.255.255.0
!
interface GigabitEthernet0/0.40
 encapsulation dot1Q 40
 ip address 192.168.40.254 255.255.255.0
!
interface GigabitEthernet0/1
 ip address 192.168.100.2 255.255.255.0
 duplex auto
 speed auto
!
interface GigabitEthernet0/2
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
ip classless
ip route 0.0.0.0 0.0.0.0 192.168.100.1 
!
ip flow-export version 9
!
!
!
!
!
!
!
line con 0
!
line aux 0
!
line vty 0 4
 password Cisco
 login
!
!
!
end
```
- GigabitEthernet0/0.30とGigabitEthernet0/0.40のサブインターフェースが作成されていることを確認
- GigabitEthernet0/0.30のIPアドレスが192.168.30.254であることを確認
- GigabitEthernet0/0.40のIPアドレスが192.168.40.254であることを確認

**物理ポート、サブインターフェースが有効化されていることを確認**
```
RTB#
RTB#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     unassigned      YES unset  up                    up 
GigabitEthernet0/0.30  192.168.30.254  YES manual up                    up 
GigabitEthernet0/0.40  192.168.40.254  YES manual up                    up 
GigabitEthernet0/1     192.168.100.2   YES manual up                    up 
GigabitEthernet0/2     unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
RTB#
```
- GigabitEthernet0/0 の Status が up であることを確認
- GigabitEthernet0/0.30 の Status が up であることを確認
- GigabitEthernet0/0.40 の Status が up であることを確認

**設定の保存**
```
RTB#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTB#
```

### 検証用PC（PC A・PC B・PC C・PC D）の設定
#### 検証用PC（PC A・PC B・PC C・PC D）がIPアドレスを自動的に取得するため、デフォルトの設定の『Static』から『DHCP』に変更する
![検証用PC（PC A）『DHCP』に変更する](/study06/images/課題6_12.png)
![検証用PC（PC B）『DHCP』に変更する](/study06/images/課題6_13.png)
![検証用PC（PC C）『DHCP』に変更する](/study06/images/課題6_14.png)
![検証用PC（PC D）『DHCP』に変更する](/study06/images/課題6_15.png)

### 検証用PC（PC A・PC B・PC C・PC D）に対して払い出されたIPアドレス・デフォルトゲートウェイをコマンドプロンプトで確認
- ipconfigコマンドで、PC AのIPアドレス・デフォルトゲートウェイの確認
![PC AのIPアドレス・デフォルトゲートウェイの確認](/study06/images/課題6_16.png)
- PC A → IPアドレス: 192.168.10.1　サブネットマスク: 255.255.255.0
- DHCPサーバが指定したアドレス範囲「192.168.10.0/24」内からIPアドレスが正しく払い出されていることを確認
- デフォルトゲートウェイ「192.168.10.254」が払い出されていることを確認

![PC BのIPアドレス・デフォルトゲートウェイの確認](/study06/images/課題6_17.png)
- ipconfigコマンドで、PC BのIPアドレス・デフォルトゲートウェイの確認
- PC B → IPアドレス: 192.168.20.1　サブネットマスク: 255.255.255.0
- DHCPサーバが指定したアドレス範囲「192.168.20.0/24」内からIPアドレスが正しく払い出されていることを確認
- デフォルトゲートウェイ「192.168.20.254」が払い出されていることを確認

![PC CのIPアドレス・デフォルトゲートウェイの確認](/study06/images/課題6_18.png)
- ipconfigコマンドで、PC CのIPアドレス・デフォルトゲートウェイの確認
- PC C → IPアドレス: 192.168.30.1　サブネットマスク: 255.255.255.0
- DHCPサーバが指定したアドレス範囲「192.168.30.0/24」内からIPアドレスが正しく払い出されていることを確認
- デフォルトゲートウェイ「192.168.30.254」が払い出されていることを確認

![PC DのIPアドレス・デフォルトゲートウェイの確認](/study06/images/課題6_19.png)
- ipconfigコマンドで、PC DのIPアドレス・デフォルトゲートウェイの確認
- PC D → IPアドレス: 192.168.40.1　サブネットマスク: 255.255.255.0
- DHCPサーバが指定したアドレス範囲「192.168.40.0/24」内からIPアドレスが正しく払い出されていることを確認
- デフォルトゲートウェイ「192.168.40.254」が払い出されていることを確認

### VLAN 10のPC AからVLAN 20のPC B（IPアドレス: 192.168.20.1）へPingを使用して異なるVLAN同士で疎通確認
- 1回目は、パケットを4回送信し、3回受信し、25%の損失を確認
- 2回目は、パケットを4回送信し、4回受信し、0%の損失を確認
- 相手機器に対してはじめて疎通確認を行ったため、ARPによるアドレス解決（IPアドレスから対応するMACアドレスを求める処理）を行っていたと考えられる
- 異なるVLAN同士で疎通が正常に行われることを確認
![PC AからPC BへPingを使用して疎通確認](/study06/images/課題6_20.png)

### VLAN 10のPC AからVLAN 30のPC C（IPアドレス: 192.168.30.1）へPingを使用して異なるVLAN同士で疎通確認
- 1回目は、パケットを4回送信し、2回受信し、50%の損失を確認
- 2回目は、パケットを4回送信し、4回受信し、0%の損失を確認
- 相手機器に対してはじめて疎通確認を行ったため、ARPによるアドレス解決（IPアドレスから対応するMACアドレスを求める処理）を行っていたと考えられる
- 異なるVLAN同士で疎通が正常に行われることを確認
![PC AからPC CへPingを使用して疎通確認](/study06/images/課題6_21.png)

### 学習成果・気付き

**技術的な学び**
- スイッチ同士の接続ポートはトランクポートに設定する
- 論理インターフェースを有効化するためには、物理インターフェースも有効化する必要がある
- 作成するサブインターフェースは、各PCのデフォルトゲートウェイになるため、IPアドレスを設定する
- VLANの数だけサブインターフェースが必要になる
- Layer2では、同じVLAN同士の通信は可能であるが、異なるVLAN同士では通信が不可
- 異なるVLANで通信を可能にするには、Layer3の機器でVLAN間ルーティングを設定する必要がある
- 論理インターフェースを作成することで、VLAN間ルーティングが可能となり、異なるVLAN同士で通信が可能になる
- 異なるVLAN同士の初回Ping実行時は、ARPによるアドレス解決のため、パケットロスが発生する場合がある

**トラブルシューティング**
- 作成したVLANはVLANコンフィギュレーションモード「(config-vlan)#」を終了したタイミングで作成が確定する
- exitやendコマンドでモードを終了するまでは、VLANが作成されていないので注意が必要

**設定コマンドの学び**
- #show vlan → アクセスポートは表示されるが、トランクポートは表示されない
- VLANが存在しない場合は、先に(config)#vlan [VLAN番号]でVLANを作成してから、(config-if)#switchport access vlan [VLAN番号]で設定を行う必要がある
- アクセスポートの設定変更を行う場合、VLANが作成されているかどうか確認する必要がある
- (config)#interface [インターフェース].[論理番号] → サブインターフェースの論理番号は、VLAN番号と一致させると管理しやすくなるため推奨される
- サブインターフェースの設定内容の確認コマンド → #show running-config・#show ip interface brief

**所感**
- VLANが単なる論理的な分割ではなく、実際のネットワーク運用において重要な役割を果たすことを実感
- 特に、同じ物理スイッチ上で異なるセグメントを作成し、セキュリティと効率性を両立させる仕組みの有効性に対する理解が深まった
- 設定ミスや設定順序の重要性を身をもって体験
- 特に、物理インターフェースの有効化を忘れがちになることや、VLANの作成タイミングなど、実際の現場で遭遇しそうな問題を事前に経験できたことは大きな収穫となった
- 実際の企業環境ではVLAN数がさらに多く、より複雑な構成になることが予想されるため、今後は、VTP・STP・VACLなど、より高度なVLAN技術についても学習予定
