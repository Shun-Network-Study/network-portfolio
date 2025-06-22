# 第4回②：NATの設定(応用)
- **実施日**: 2025/6/22
- **所要時間**: 約4時間

## 学習内容
- NAPTの設定
- 宛先NATの設定
- PPPoEサーバ・クライアントの設定
- 内部から外部への通信時に、IPアドレスがNAT変換されていることを確認
- 内部に存在していないIPアドレスへの通信時に、宛先NAT変換され、疎通可能なことを確認

```
(config)#dialer-list [番号] protocol ip permit………対象となるのはipパケットと指定
(config-if)#dialer-group [番号]…………………………………dialer-listを番号でインターフェースに紐づける
```
- Cisco Packet Tracerで、上記Dialerインターフェースの設定を行ったのですが、コマンド制限があり設定を実行できませんでした
- 高度なネットワーク設定のため「Cisco Modeling Labs」で検証を開始
- 無料版で5ノード限定のため、L2スイッチを使用しない以下のネットワーク構成に変更して検証を行いました

## 使用機器
- **ルータ**: Cisco IOL (IOS on Linux) ×2台
- **検証用PC** ×3台
- **クロスケーブル** ×4本

## 構成図
![構成図](/study04②/images/topology4②.png)

## 設定内容

### ルータ（GWRT）の設定
[ルータ（GWRT）の全ての設定ファイルを見る](/study04②/configs/router-config-GWRT.txt)

**PPPoEについて**
- PPPoEは、最も多く使われるインターネット接続方式であり、パスワード認証で正規のクライアントにのみIPアドレスが付与される
- PPPoEは、認証機能のあるPPPをイーサネットで使用できるようにした技術
- PPPoEは、サーバとクライアントで構成されており、プロバイダ側がPPPoEサーバになる
- PPPoEサーバがパスワードによるクライアントの認証を行い、認証に成功するとグローバルIPアドレスをクライアントへ払い出して接続を可能にする

**GWRTをPPPoEクライアントとするPPPoEの設定**
```
inserthostname-here>en
inserthostname-here#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
inserthostname-here(config)#hostname GWRT
GWRT(config)#enable password Cisco
 WARNING: Configured enable password CLI with weak encryption type 0 will be deprecated in future. Hence please migrate to enable secret CLI which accomplishes same functionality as enable password CLI and which also supports strong irreversible encryption type 9
GWRT(config)#
*Jun 22 08:14:08.633: %AAAA-4-CLI_DEPRECATED: WARNING: Configured enable password CLI with weak encryption type 0 will be deprecated in future. Hence please migrate to enable secret CLI which accomplishes same functionality as enable password CLI and which also supports strong irreversible encryption type 9
GWRT(config)#line vty 0 4
GWRT(config-line)# password Cisco
GWRT(config-line)# login
GWRT(config-line)#exit
GWRT(config)#interface E0/0
GWRT(config-if)#ip address 192.168.10.254 255.255.255.0
GWRT(config-if)# no shutdown
GWRT(config-if)#
*Jun 22 08:15:51.807: %LINK-5-UPDOWN: Interface Ethernet0/0, changed state to up
*Jun 22 08:15:52.807: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/0, changed state to up
GWRT(config-if)#exit
GWRT(config)#interface dialer 1 
GWRT(config-if)#ip address negotiated 
GWRT(config-if)# ip mtu 1454
GWRT(config-if)# encapsulation ppp
GWRT(config-if)# dialer pool 1
GWRT(config-if)# ppp authentication chap callin
GWRT(config-if)# ppp chap hostname test1@example.com
GWRT(config-if)# ppp chap password Cisco1
GWRT(config-if)# dialer-group 1
GWRT(config-if)# ip access-group EX in
GWRT(config-if)# ip inspect CBAC out
                      ^
% Invalid input detected at '^' marker.

GWRT(config-if)# ip inspect CBAC out
                      ^
% Invalid input detected at '^' marker.

GWRT(config-if)#ip inspect          
                     ^
% Invalid input detected at '^' marker.

GWRT(config-if)#ip ins?   
% Unrecognized command
GWRT(config-if)#ip in?
information-reply  

GWRT(config-if)#ip inspe
GWRT(config-if)#ip inspect ?
% Unrecognized command
GWRT(config-if)#ip inspect 
GWRT#
*Jun 22 08:18:16.519: %SYS-5-CONFIG_I: Configured from console by console
GWRT#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
GWRT(config)#ip inspect name CBAC icmp
                 ^
% Invalid input detected at '^' marker.

GWRT(config)#ip ins                   
GWRT(config)#ip ins?
% Unrecognized command
GWRT(config)#ip inspect na            
GWRT(config)#ip inspect name ?
% Unrecognized command
GWRT(config)#ip inspect name 
                 ^
% Invalid input detected at '^' marker.

GWRT(config)#interface dialer 1
GWRT(config-if)#no ip access-group EX in
GWRT(config-if)#exit
GWRT(config)#end
GWRT#show 
*Jun 22 08:24:29.276: %SYS-5-CONFIG_I: Configured from console by console
GWRT#show run
Building configuration...

Current configuration : 1124 bytes
!
! Last configuration change at 08:24:29 UTC Sun Jun 22 2025
!
version 17.15
service timestamps debug datetime msec
service timestamps log datetime msec
!
hostname GWRT
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
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
!         
!         
ip cef    
login on-success log
no ipv6 cef
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
!         
!         
memory free low-watermark processor 80093
!         
!         
spanning-tree mode rapid-pvst
!         
enable password Cisco
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
interface Ethernet0/0
 ip address 192.168.10.254 255.255.255.0
!         
interface Ethernet0/1
 no ip address
 shutdown 
!         
interface Ethernet0/2
 no ip address
 shutdown 
!         
interface Ethernet0/3
 no ip address
 shutdown 
!         
interface Dialer1
 ip address negotiated
 ip mtu 1454
 encapsulation ppp
 dialer pool 1
 dialer-group 1
 ppp authentication chap callin
 ppp chap hostname test1@example.com
 ppp chap password 0 Cisco1
!         
ip forward-protocol nd
          
GWRT#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
GWRT(config)#dialer-list 1 protocol ip permit
GWRT(config)#interface E0/1
GWRT(config-if)#pppoe enable
GWRT(config-if)# pppoe-client dial-pool-number 1
GWRT(config-if)# no shutdown enabling sss event trace

GWRT(config-if)# no shutdown 
GWRT(config-if)#
*Jun 22 08:28:12.636: %LINEPROTO-5-UPDOWN: Line protocol on Interface Virtual-Access1, changed state to up
*Jun 22 08:28:12.637: %LINK-5-UPDOWN: Interface Virtual-Access1, changed state to up
GWRT(config-if)#
*Jun 22 08:28:15.862: %LINK-5-UPDOWN: Interface Ethernet0/1, changed state to up
*Jun 22 08:28:16.863: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to up
GWRT(config-if)#exit
GWRT(config)#ip route 0.0.0.0 0.0.0.0 dialer1
GWRT(config)#end
```
- 外部からの通信が可能で、不正アクセス等のセキュリティ問題が発生するため、ルータにCBAC（Context-Based Access Control）というステートフルインスペクションの設定を行う必要がある
- ステートフルインスペクション → 内部から出て行った通信を確認し、それに対する戻りの通信を自動で判断し許可する機能
- CBACの設定を行ったが、TABキーや？キーを押しても反応が無かったため、Cisco Modeling Labsによるコマンド制限があり、設定を実行できませんでした
- セキュリティ強度が低い設定になりますが、拡張ACLを削除し（deny ip any any によってPing疎通確認ができなかったため）CBACの設定は行わずに検証を進めました

**設定の確認**
```
GWRT#show run
Building configuration...

Current configuration : 1240 bytes
!
! Last configuration change at 08:34:05 UTC Sun Jun 22 2025
!
version 17.15
service timestamps debug datetime msec
service timestamps log datetime msec
!
hostname GWRT
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
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
!         
!         
ip cef    
login on-success log
no ipv6 cef
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
!         
!         
memory free low-watermark processor 80093
!         
!         
spanning-tree mode rapid-pvst
!         
enable password Cisco
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
interface Ethernet0/0
 ip address 192.168.10.254 255.255.255.0
!         
interface Ethernet0/1
 no ip address
 pppoe enable group global
 pppoe-client dial-pool-number 1
!         
interface Ethernet0/2
 no ip address
 shutdown 
!         
interface Ethernet0/3
 no ip address
 shutdown 
!         
interface Dialer1
 ip address negotiated
 ip mtu 1454
 encapsulation ppp
 dialer pool 1
 dialer-group 1
 ppp authentication chap callin
 ppp chap hostname test1@example.com
 ppp chap password 0 Cisco1
!         
ip forward-protocol nd
!         
!         
ip http server
ip http secure-server
ip route 0.0.0.0 0.0.0.0 Dialer1
ip ssh bulk-mode 131072
no logging btrace
dialer-list 1 protocol ip permit
!         
!         
!         
control-plane
!         
!         
!         
line con 0
 logging synchronous
line aux 0
line vty 0 4
 password Cisco
 login    
 transport input ssh
!         
!         
!         
!         
end       
          
GWRT#
```
**設定の保存**
```
GWRT#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
GWRT#
GWRT#
```

### ルータ（GAIBURT）の設定
[ルータ（GAIBURT）の全ての設定ファイルを見る](/study04②/configs/router-config-GAIBURT.txt)

**GAIBURTをPPPoEサーバとするPPPoEの設定**
```
inserthostname-here>en
inserthostname-here#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
inserthostname-here(config)#hostname GAIBURT
GAIBURT(config)#enable password Cisco
 WARNING: Configured enable password CLI with weak encryption type 0 will be deprecated in future. Hence please migrate to enable secret CLI which accomplishes same functionality as enable password CLI and which also supports strong irreversible encryption type 9
GAIBURT(config)#
*Jun 22 08:48:18.265: %AAAA-4-CLI_DEPRECATED: WARNING: Configured enable password CLI with weak encryption type 0 will be deprecated in future. Hence please migrate to enable secret CLI which accomplishes same functionality as enable password CLI and which also supports strong irreversible encryption type 9
GAIBURT(config)#line vty 0 4
GAIBURT(config-line)# password Cisco
GAIBURT(config-line)# login
GAIBURT(config-line)#exit
GAIBURT(config)#username test1@example.com password Cisco1
 WARNING: Command has been added to the configuration using a type 0 password. However, recommended to migrate to strong type-6 encryption
GAIBURT(config)#
*Jun 22 08:49:07.437: %AAA-6-USERNAME_CONFIGURATION: user with username: test1@example.com configured
GAIBURT(config)#
*Jun 22 08:49:07.437: %AAAA-4-CLI_DEPRECATED: WARNING: Command has been added to the configuration using a type 0 password. However, recommended to migrate to strong type-6 encryption
GAIBURT(config)#interface loopback 1
GAIBURT(config-if)#
*Jun 22 08:49:50.567: %LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback1, changed state to up
GAIBURT(config-if)#ip address 20.1.1.1 255.255.255.255
GAIBURT(config-if)#exit
GAIBURT(config)#ip local pool POOL1 200.1.1.1
GAIBURT(config)#interface Virtual-Template 1
GAIBURT(config-if)#
*Jun 22 08:50:52.776: %SYS-5-CONFIG_P: Configured programmatically by process VTEMPLATE Background Mgr from console as vty0
GAIBURT(config-if)#mtu 1454
GAIBURT(config-if)# ip unnumbered loopback 1
GAIBURT(config-if)# peer default ip address pool POOL1
GAIBURT(config-if)# ppp authentication chap
GAIBURT(config-if)#
*Jun 22 08:51:12.594: %SYS-5-CONFIG_P: Configured programmatically by process VTEMPLATE Background Mgr from console as vty0
*Jun 22 08:51:12.595: %SYS-5-CONFIG_P: Configured programmatically by process VTEMPLATE Background Mgr from console as vty0
*Jun 22 08:51:12.595: %SYS-5-CONFIG_P: Configured programmatically by process VTEMPLATE Background Mgr from console as console
*Jun 22 08:51:13.401: %SYS-5-CONFIG_P: Configured programmatically by process VTEMPLATE Background Mgr from console as console
*Jun 22 08:51:13.402: %SYS-5-CONFIG_P: Configured programmatically by process VTEMPLATE Background Mgr from console as console
GAIBURT(config-if)#
*Jun 22 08:51:13.402: %SYS-5-CONFIG_P: Configured programmatically by process VTEMPLATE Background Mgr from console as console
*Jun 22 08:51:13.402: %SYS-5-CONFIG_P: Configured programmatically by process VTEMPLATE Background Mgr from console as console
GAIBURT(config-if)#exit
GAIBURT(config)#bba-group pppoe PPPOE-GROUP1
GAIBURT(config-bba-group)#
*Jun 22 08:51:46.685: %LINEPROTO-5-UPDOWN: Line protocol on Interface Virtual-Access2, changed state to up
*Jun 22 08:51:46.685: %LINK-5-UPDOWN: Interface Virtual-Access2, changed state to up
GAIBURT(config-bba-group)#virtual-template 1
GAIBURT(config-bba-group)#exit
GAIBURT(config)#interface E0/1
GAIBURT(config-if)#pppoe enable group PPPOE-GROUP1
GAIBURT(config-if)# no shutdown
GAIBURT(config-if)#enabling sss event trace

GAIBURT(config-if)#
*Jun 22 08:52:54.658: %LINK-5-UPDOWN: Interface Ethernet0/1, changed state to up
*Jun 22 08:52:55.658: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to up
GAIBURT(config-if)#
*Jun 22 08:52:57.088: %SYS-5-CONFIG_P: Configured programmatically by process VTEMPLATE Background Mgr from console as console
GAIBURT(config-if)#exit
GAIBURT(config)#interface E0/2
GAIBURT(config-if)#ip address 8.8.8.254 255.255.255.0
GAIBURT(config-if)# no shutdown
GAIBURT(config-if)#
*Jun 22 08:58:59.952: %LINK-5-UPDOWN: Interface Ethernet0/2, changed state to up
*Jun 22 08:59:00.952: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/2, changed state to up
GAIBURT(config-if)#exit
GAIBURT(config)#ip route 0.0.0.0 0.0.0.0 200.1.1.1
GAIBURT(config)#interface E0/0
GAIBURT(config-if)#ip address 8.8.7.254 255.255.255.0
GAIBURT(config-if)#no shutdown
GAIBURT(config-if)#
*Jun 22 09:00:39.931: %LINK-5-UPDOWN: Interface Ethernet0/0, changed state to up
GAIBURT(config-if)#
*Jun 22 09:00:40.931: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/0, changed state to up
GAIBURT(config-if)#end
GAIBURT#
*Jun 22 09:01:26.612: %SYS-5-CONFIG_I: Configured from console by console
```
**設定の確認**
```
GAIBURT#show run
Building configuration...

Current configuration : 1299 bytes
!
! Last configuration change at 09:01:26 UTC Sun Jun 22 2025
!
version 17.15
service timestamps debug datetime msec
service timestamps log datetime msec
!
hostname GAIBURT
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
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
!         
!         
ip cef    
login on-success log
no ipv6 cef
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
!         
!         
memory free low-watermark processor 80093
!         
!         
spanning-tree mode rapid-pvst
!         
enable password Cisco
!         
username test1@example.com password 0 Cisco1
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
!         
!         
!         
!         
!         
!         
!         
!         
bba-group pppoe PPPOE-GROUP1
 virtual-template 1
!         
!         
!         
interface Loopback1
 ip address 20.1.1.1 255.255.255.255
!         
interface Ethernet0/0
 ip address 8.8.7.254 255.255.255.0
!         
interface Ethernet0/1
 no ip address
 pppoe enable group PPPOE-GROUP1
!         
interface Ethernet0/2
 ip address 8.8.8.254 255.255.255.0
!         
interface Ethernet0/3
 no ip address
 shutdown 
!         
interface Virtual-Template1
 mtu 1454 
 ip unnumbered Loopback1
 peer default ip address pool POOL1
 ppp authentication chap
!         
ip local pool POOL1 200.1.1.1
ip forward-protocol nd
!         
!         
ip http server
ip http secure-server
ip route 0.0.0.0 0.0.0.0 200.1.1.1
ip ssh bulk-mode 131072
no logging btrace
!         
!         
!         
control-plane
!         
!         
!         
line con 0
 logging synchronous
line aux 0
line vty 0 4
 password Cisco
 login    
 transport input ssh
!         
!         
!         
!         
end       
          
GAIBURT#
```

**設定の保存**
```
GAIBURT#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
GAIBURT#
```

**GWRTのダイアラインタフェースにグローバルIPアドレスが付与されていることを確認**
```
GWRT>en
Password: 
GWRT#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
GWRT(config)#exit
GWRT#
*Jun 22 09:12:52.857: %SYS-5-CONFIG_I: Configured from console by console
GWRT#show ip int brief
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            192.168.10.254  YES manual up                    up      
Ethernet0/1            unassigned      YES TFTP   up                    up      
Ethernet0/2            unassigned      YES TFTP   administratively down down    
Ethernet0/3            unassigned      YES TFTP   administratively down down    
Dialer1                200.1.1.1       YES IPCP   up                    up      
Virtual-Access1        unassigned      YES unset  up                    up      
Virtual-Access2        unassigned      YES manual up                    up      
GWRT#
GWRT#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, m - OMP
       n - NAT, Ni - NAT inside, No - NAT outside, Nd - NAT DIA
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       H - NHRP, G - NHRP registered, g - NHRP registration summary
       o - ODR, P - periodic downloaded static route, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR
       & - replicated local route overrides by connected

Gateway of last resort is 0.0.0.0 to network 0.0.0.0

S*    0.0.0.0/0 is directly connected, Dialer1
      20.0.0.0/32 is subnetted, 1 subnets
C        20.1.1.1 is directly connected, Dialer1
      192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.10.0/24 is directly connected, Ethernet0/0
L        192.168.10.254/32 is directly connected, Ethernet0/0
      200.1.1.0/32 is subnetted, 1 subnets
C        200.1.1.1 is directly connected, Dialer1
GWRT#
GWRT#
```
- インターフェースのIPアドレスやリンクアップ状態を確認し、C（直接接続）がPPPoEで払い出しているグローバルIPアドレスであること、インターフェースがdialer1であることを確認

### Proxyサーバ・WEBサーバ・DNSサーバの設定
#### Proxyサーバ・WEBサーバ・DNSサーバにIPアドレス・デフォルトゲートウェイを設定する
- Proxyサーバ → IPアドレス: 192.168.10.1　サブネットマスク: 255.255.255.0
- Proxyサーバのデフォルトゲートウェイ（GWRT E0/0） → IPアドレス: 192.168.10.254　サブネットマスク: 255.255.255.0
```
inserthostname-here:~$ sudo hostname Proxy_Server
Proxy_Server:~$ sudo ip addr add 192.168.10.1/24 dev eth0
Proxy_Server:~$ sudo ip link set eth0 up
Proxy_Server:~$ sudo ip route add default via 192.168.10.254
Proxy_Server:~$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 52:54:00:d3:ec:f3 brd ff:ff:ff:ff:ff:ff
    inet 192.168.10.1/24 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:fed3:ecf3/64 scope link 
       valid_lft forever preferred_lft forever
Proxy_Server:~$ ip route show
default via 192.168.10.254 dev eth0 
192.168.10.0/24 dev eth0 scope link  src 192.168.10.1 
Proxy_Server:~$ 
```
- WEBサーバ → IPアドレス: 8.8.7.7　サブネットマスク: 255.255.255.0
- WEBサーバのデフォルトゲートウェイ（GAIBURT E0/0） → IPアドレス: 8.8.7.254　サブネットマスク: 255.255.255.0
```
inserthostname-here:~$ sudo hostname Web_Server
Web_Server:~$ sudo ip addr add 8.8.7.7/24 dev eth0
Web_Server:~$ sudo ip link set eth0 up
Web_Server:~$ sudo ip route add default via 8.8.7.254
Web_Server:~$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 52:54:00:eb:da:ce brd ff:ff:ff:ff:ff:ff
    inet 8.8.7.7/24 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:feeb:dace/64 scope link 
       valid_lft forever preferred_lft forever
Web_Server:~$ ip route show
default via 8.8.7.254 dev eth0 
8.8.7.0/24 dev eth0 scope link  src 8.8.7.7 
Web_Server:~$ 
```
- DNSサーバ → IPアドレス: 8.8.8.8　サブネットマスク: 255.255.255.0
- DNSサーバのデフォルトゲートウェイ（GAIBURT E0/2） → IPアドレス: 8.8.8.254　サブネットマスク: 255.255.255.0
```
inserthostname-here:~$ sudo hostname Dns_Server
Dns_Server:~$ sudo ip addr add 8.8.8.8/24 dev eth0
Dns_Server:~$ sudo ip link set eth0 up
Dns_Server:~$ sudo ip route add default via 8.8.8.254
Dns_Server:~$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 52:54:00:26:cc:0d brd ff:ff:ff:ff:ff:ff
    inet 8.8.8.8/24 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:fe26:cc0d/64 scope link 
       valid_lft forever preferred_lft forever
Dns_Server:~$ ip route show
default via 8.8.8.254 dev eth0 
8.8.8.0/24 dev eth0 scope link  src 8.8.8.8 
Dns_Server:~$ 
```
**学習したLinuxコマンド**
- sudo hostname [ホスト名] → ホスト名を変更
- sudo ip addr add [IPアドレス/サブネットマスク（CIDR表記）] dev [インターフェース]
 - sudo ip link set [インターフェース] up → IPアドレスを設定
- sudo ip route add default via [デフォルトゲートウェイのIPアドレス] → デフォルトゲートウェイを設定
- ip addr show → IPアドレスの確認
- ip route show → デフォルトゲートウェイの確認

### 検証①内部から外部への通信の際に、IPアドレスがNAT変換されていることを確認
**Pingを使用してProxyサーバーから、WEBサーバへ疎通可能であることを確認**
```
Proxy_Server:~$ ping 8.8.7.7
PING 8.8.7.7 (8.8.7.7): 56 data bytes
64 bytes from 8.8.7.7: seq=0 ttl=42 time=3.534 ms
64 bytes from 8.8.7.7: seq=1 ttl=42 time=1.950 ms
64 bytes from 8.8.7.7: seq=2 ttl=42 time=1.763 ms
64 bytes from 8.8.7.7: seq=3 ttl=42 time=1.720 ms
64 bytes from 8.8.7.7: seq=4 ttl=42 time=1.549 ms
64 bytes from 8.8.7.7: seq=5 ttl=42 time=2.548 ms
64 bytes from 8.8.7.7: seq=6 ttl=42 time=1.972 ms
64 bytes from 8.8.7.7: seq=7 ttl=42 time=2.228 ms
64 bytes from 8.8.7.7: seq=8 ttl=42 time=1.711 ms
64 bytes from 8.8.7.7: seq=9 ttl=42 time=1.487 ms
64 bytes from 8.8.7.7: seq=10 ttl=42 time=1.897 ms
^C
--- 8.8.7.7 ping statistics ---
11 packets transmitted, 11 packets received, 0% packet loss
round-trip min/avg/max = 1.487/2.032/3.534 ms
Proxy_Server:~$ 
```
- パケットを11回送信し、11回受信し、0%の損失を確認

**GWRTにNAPTの設定**
```
GWRT>en
Password: 
GWRT#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
GWRT(config)#interface Dialer1
GWRT(config-if)#ip nat outside
GWRT(config-if)#exit
GWRT(config)#interface E0/0
GWRT(config-if)#ip nat inside
GWRT(config-if)#exit                                       
GWRT(config)#access-list 1 permit 192.168.10.0 0.0.0.255
GWRT(config)#ip nat inside source list 1 interface Dialer1 overload
GWRT(config)#end
GWRT#
*Jun 22 10:18:29.020: %SYS-5-CONFIG_I: Configured from console by console
```

**設定したNAPTの確認**
```
GWRT#show ip nat translations
GWRT#
GWRT#show ip nat translations
GWRT#show ip nat tra
GWRT#show ip nat translations 
GWRT#
GWRT#show ip nat translations 
Pro Inside global      Inside local       Outside local      Outside global
icmp 200.1.1.1:1024    192.168.10.1:2     8.8.7.7:2          8.8.7.7:1024
GWRT#
```
- NAPTの設定後に#show ip nat translationsコマンドを入力しても、何も表示されなかった
- NAT変換を行う通信が発生していないためだと推測し、Pingを使用してProxyサーバーから、WEBサーバへ疎通確認
- Ping疎通確認後に#show ip nat translationsコマンドを入力したら、NATテーブルが表示された
- #show ip nat translations → Ping疎通確認後にNATテーブルが表示される、NAT変換を行う通信が発生していない場合NATテーブルが表示さない
- Inside global: 変換後のIPアドレス（200.1.1.1）であることを確認
- Inside local: 変換前のIPアドレス（192.168.10.1）であることを確認

**GWRTでデバックを有効化**
```
GWRT>en
Password: 
GWRT#debug ip nat
IP NAT debugging is on
GWRT#
```

**ProxyサーバからWEBサーバへ疎通確認**
```
Proxy_Server:~$ ping 8.8.7.7
PING 8.8.7.7 (8.8.7.7): 56 data bytes
64 bytes from 8.8.7.7: seq=0 ttl=42 time=1.843 ms
64 bytes from 8.8.7.7: seq=1 ttl=42 time=1.273 ms
64 bytes from 8.8.7.7: seq=2 ttl=42 time=1.831 ms
64 bytes from 8.8.7.7: seq=3 ttl=42 time=2.349 ms
64 bytes from 8.8.7.7: seq=4 ttl=42 time=1.925 ms
64 bytes from 8.8.7.7: seq=5 ttl=42 time=1.972 ms
^C
--- 8.8.7.7 ping statistics ---
6 packets transmitted, 6 packets received, 0% packet loss
round-trip min/avg/max = 1.273/1.865/2.349 ms
Proxy_Server:~$ 
```

**Ping疎通確認後**
```
GWRT>en
Password: 
GWRT#debug ip nat
IP NAT debugging is on
GWRT#
*Jun 22 10:35:09.217: NAT: Entry assigned id 2
*Jun 22 10:35:09.217: NAT*: ICMP id=3->1024
*Jun 22 10:35:09.218: NAT*: s=192.168.10.1->200.1.1.1, d=8.8.7.7 [36045]
*Jun 22 10:35:09.219: NAT*: ICMP id=1024->3
*Jun 22 10:35:09.219: NAT*: s=8.8.7.7, d=200.1.1.1->192.168.10.1 [54515]
GWRT#
*Jun 22 10:35:10.218: NAT*: ICMP id=3->1024
*Jun 22 10:35:10.218: NAT*: s=192.168.10.1->200.1.1.1, d=8.8.7.7 [36065]
*Jun 22 10:35:10.218: NAT*: ICMP id=1024->3
*Jun 22 10:35:10.218: NAT*: s=8.8.7.7, d=200.1.1.1->192.168.10.1 [54553]
GWRT#
*Jun 22 10:35:11.218: NAT*: ICMP id=3->1024
*Jun 22 10:35:11.218: NAT*: s=192.168.10.1->200.1.1.1, d=8.8.7.7 [36124]
*Jun 22 10:35:11.219: NAT*: ICMP id=1024->3
*Jun 22 10:35:11.219: NAT*: s=8.8.7.7, d=200.1.1.1->192.168.10.1 [54560]
GWRT#
*Jun 22 10:35:12.218: NAT*: ICMP id=3->1024
*Jun 22 10:35:12.218: NAT*: s=192.168.10.1->200.1.1.1, d=8.8.7.7 [36164]
*Jun 22 10:35:12.220: NAT*: ICMP id=1024->3
*Jun 22 10:35:12.220: NAT*: s=8.8.7.7, d=200.1.1.1->192.168.10.1 [54642]
GWRT#
*Jun 22 10:35:13.219: NAT*: ICMP id=3->1024
*Jun 22 10:35:13.219: NAT*: s=192.168.10.1->200.1.1.1, d=8.8.7.7 [36180]
*Jun 22 10:35:13.220: NAT*: ICMP id=1024->3
*Jun 22 10:35:13.220: NAT*: s=8.8.7.7, d=200.1.1.1->192.168.10.1 [54741]
GWRT#
*Jun 22 10:35:14.219: NAT*: ICMP id=3->1024
*Jun 22 10:35:14.219: NAT*: s=192.168.10.1->200.1.1.1, d=8.8.7.7 [36232]
*Jun 22 10:35:14.221: NAT*: ICMP id=1024->3
*Jun 22 10:35:14.221: NAT*: s=8.8.7.7, d=200.1.1.1->192.168.10.1 [54760]
GWRT#
*Jun 22 10:36:14.477: NAT: expiring 200.1.1.1 (192.168.10.1) icmp 1024 (3)
*Jun 22 10:36:14.477: NAT: Freeing nat entry, id 2
GWRT#no debug ip nat
IP NAT debugging is off
GWRT#
GWRT#
```
```
s: 送信元IPアドレス
->: 変換後IPアドレス
d: 宛先IPアドレス
```
- #debug ip natコマンドの実行後、#no debug ip natコマンドで無効化
- プライベートIPアドレス（192.168.10.1）が、グローバルIPアドレス（200.1.1.1）にNAPT変換されていることを確認
- NAPTによって、1つのグローバルIPアドレスを使用し、多対1の通信ができるようになった
- debugの実行結果から、想定通りNAPTの設定が機能していることを確認

### 検証②内部に存在していないIPアドレスへの通信の際に、宛先NAT変換され、疎通が可能なことを確認
**GWRTに宛先NATの設定**
```
GWRT#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
GWRT(config)#ip nat outside source static 8.8.8.8 128.27.30.5
```

**設定したNATを確認**
```
GWRT(config)#show ip nat translations
               ^
% Invalid input detected at '^' marker.

GWRT(config)#end
GWRT#
*Jun 22 10:47:22.661: %SYS-5-CONFIG_I: Configured from console by console
GWRT#show ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
--- ---                ---                128.27.30.5        8.8.8.8
```
- Outside local → 変換前のIPアドレス（128.27.30.5）であることを確認
- Outside global → 変換後のIPアドレス（8.8.8.8）であることを確認

**GWRTでデバックを有効化**
```
GWRT#debug ip nat
IP NAT debugging is on
GWRT#
GWRT#
```

**ProxyサーバからIPアドレス128.27.30.5へ疎通確認**
```
Proxy_Server:~$ ping 128.27.30.5
PING 128.27.30.5 (128.27.30.5): 56 data bytes
64 bytes from 128.27.30.5: seq=0 ttl=42 time=2.454 ms
64 bytes from 128.27.30.5: seq=1 ttl=42 time=2.018 ms
64 bytes from 128.27.30.5: seq=2 ttl=42 time=2.047 ms
64 bytes from 128.27.30.5: seq=3 ttl=42 time=1.824 ms
64 bytes from 128.27.30.5: seq=4 ttl=42 time=1.776 ms
64 bytes from 128.27.30.5: seq=5 ttl=42 time=1.645 ms
64 bytes from 128.27.30.5: seq=6 ttl=42 time=1.969 ms
64 bytes from 128.27.30.5: seq=7 ttl=42 time=1.927 ms
64 bytes from 128.27.30.5: seq=8 ttl=42 time=1.599 ms
64 bytes from 128.27.30.5: seq=9 ttl=42 time=1.947 ms
64 bytes from 128.27.30.5: seq=10 ttl=42 time=1.868 ms
64 bytes from 128.27.30.5: seq=11 ttl=42 time=1.742 ms
^C
--- 128.27.30.5 ping statistics ---
12 packets transmitted, 12 packets received, 0% packet loss
round-trip min/avg/max = 1.599/1.901/2.454 ms
Proxy_Server:~$ 
```

**Ping疎通確認後**
```
GWRT#debug ip nat
IP NAT debugging is on
GWRT#
GWRT#
*Jun 22 10:50:45.793: NAT: Entry assigned id 4
*Jun 22 10:50:45.793: NAT*: ICMP id=4->1024
*Jun 22 10:50:45.793: NAT*: s=192.168.10.1->200.1.1.1, d=128.27.30.5 [8101]
*Jun 22 10:50:45.793: NAT*: s=200.1.1.1, d=128.27.30.5->8.8.8.8 [8101]
*Jun 22 10:50:45.795: NAT*: ICMP id=1024->4
*Jun 22 10:50:45.795: NAT*: s=8.8.8.8->128.27.30.5, d=200.1.1.1 [35940]
*Jun 22 10:50:45.795: NAT*: s=128.27.30.5, d=200.1.1.1->192.168.10.1 [35940]
GWRT#
*Jun 22 10:50:46.793: NAT*: ICMP id=4->1024
*Jun 22 10:50:46.793: NAT*: s=192.168.10.1->200.1.1.1, d=128.27.30.5 [8123]
*Jun 22 10:50:46.793: NAT*: s=200.1.1.1, d=128.27.30.5->8.8.8.8 [8123]
*Jun 22 10:50:46.795: NAT*: ICMP id=1024->4
*Jun 22 10:50:46.795: NAT*: s=8.8.8.8->128.27.30.5, d=200.1.1.1 [36009]
*Jun 22 10:50:46.795: NAT*: s=128.27.30.5, d=200.1.1.1->192.168.10.1 [36009]
GWRT#
*Jun 22 10:50:47.794: NAT*: ICMP id=4->1024
*Jun 22 10:50:47.794: NAT*: s=192.168.10.1->200.1.1.1, d=128.27.30.5 [8147]
*Jun 22 10:50:47.794: NAT*: s=200.1.1.1, d=128.27.30.5->8.8.8.8 [8147]
*Jun 22 10:50:47.795: NAT*: ICMP id=1024->4
*Jun 22 10:50:47.795: NAT*: s=8.8.8.8->128.27.30.5, d=200.1.1.1 [36051]
*Jun 22 10:50:47.795: NAT*: s=128.27.30.5, d=200.1.1.1->192.168.10.1 [36051]
GWRT#
*Jun 22 10:50:48.794: NAT*: ICMP id=4->1024
*Jun 22 10:50:48.794: NAT*: s=192.168.10.1->200.1.1.1, d=128.27.30.5 [8190]
*Jun 22 10:50:48.794: NAT*: s=200.1.1.1, d=128.27.30.5->8.8.8.8 [8190]
*Jun 22 10:50:48.795: NAT*: ICMP id=1024->4
*Jun 22 10:50:48.795: NAT*: s=8.8.8.8->128.27.30.5, d=200.1.1.1 [36082]
*Jun 22 10:50:48.795: NAT*: s=128.27.30.5, d=200.1.1.1->192.168.10.1 [36082]
GWRT#
*Jun 22 10:50:49.795: NAT*: ICMP id=4->1024
*Jun 22 10:50:49.795: NAT*: s=192.168.10.1->200.1.1.1, d=128.27.30.5 [8267]
*Jun 22 10:50:49.795: NAT*: s=200.1.1.1, d=128.27.30.5->8.8.8.8 [8267]
*Jun 22 10:50:49.796: NAT*: ICMP id=1024->4
*Jun 22 10:50:49.796: NAT*: s=8.8.8.8->128.27.30.5, d=200.1.1.1 [36110]
*Jun 22 10:50:49.796: NAT*: s=128.27.30.5, d=200.1.1.1->192.168.10.1 [36110]
*Jun 22 10:50:50.795: NAT*: ICMP id=4->1024
*Jun 22 10:50:50.795: NAT*: s=192.168.10.1->200.1.1.1, d=128.27.30.5 [8360]
*Jun 22 10:50:50.795: NAT*: s=200.1.1.1, d=128.27.30.5->8.8.8.8 [8360]
GWRT#
*Jun 22 10:50:50.796: NAT*: ICMP id=1024->4
*Jun 22 10:50:50.796: NAT*: s=8.8.8.8->128.27.30.5, d=200.1.1.1 [36129]
*Jun 22 10:50:50.796: NAT*: s=128.27.30.5, d=200.1.1.1->192.168.10.1 [36129]
*Jun 22 10:50:51.795: NAT*: ICMP id=4->1024
*Jun 22 10:50:51.795: NAT*: s=192.168.10.1->200.1.1.1, d=128.27.30.5 [8421]
*Jun 22 10:50:51.795: NAT*: s=200.1.1.1, d=128.27.30.5->8.8.8.8 [8421]
GWRT#
*Jun 22 10:50:51.797: NAT*: ICMP id=1024->4
*Jun 22 10:50:51.797: NAT*: s=8.8.8.8->128.27.30.5, d=200.1.1.1 [36212]
*Jun 22 10:50:51.797: NAT*: s=128.27.30.5, d=200.1.1.1->192.168.10.1 [36212]
*Jun 22 10:50:52.796: NAT*: ICMP id=4->1024
*Jun 22 10:50:52.796: NAT*: s=192.168.10.1->200.1.1.1, d=128.27.30.5 [8517]
*Jun 22 10:50:52.796: NAT*: s=200.1.1.1, d=128.27.30.5->8.8.8.8 [8517]
*Jun 22 10:50:52.797: NAT*: ICMP id=1024->4
*Jun 22 10:50:52.797: NAT*: s=8.8.8.8->128.27.30.5, d=200.1.1.1 [36291]
*Jun 22 10:50:52.797: NAT*: s=128.27.30.5, d=200.1.1.1->192.168.10.1 [36291]
GWRT#
*Jun 22 10:50:53.796: NAT*: ICMP id=4->1024
*Jun 22 10:50:53.796: NAT*: s=192.168.10.1->200.1.1.1, d=128.27.30.5 [8529]
*Jun 22 10:50:53.796: NAT*: s=200.1.1.1, d=128.27.30.5->8.8.8.8 [8529]
*Jun 22 10:50:53.797: NAT*: ICMP id=1024->4
*Jun 22 10:50:53.797: NAT*: s=8.8.8.8->128.27.30.5, d=200.1.1.1 [36331]
*Jun 22 10:50:53.797: NAT*: s=128.27.30.5, d=200.1.1.1->192.168.10.1 [36331]
GWRT#
*Jun 22 10:50:54.797: NAT*: ICMP id=4->1024
*Jun 22 10:50:54.797: NAT*: s=192.168.10.1->200.1.1.1, d=128.27.30.5 [8621]
*Jun 22 10:50:54.797: NAT*: s=200.1.1.1, d=128.27.30.5->8.8.8.8 [8621]
*Jun 22 10:50:54.798: NAT*: ICMP id=1024->4
*Jun 22 10:50:54.798: NAT*: s=8.8.8.8->128.27.30.5, d=200.1.1.1 [36412]
*Jun 22 10:50:54.798: NAT*: s=128.27.30.5, d=200.1.1.1->192.168.10.1 [36412]
GWRT#
*Jun 22 10:50:55.797: NAT*: ICMP id=4->1024
*Jun 22 10:50:55.797: NAT*: s=192.168.10.1->200.1.1.1, d=128.27.30.5 [8646]
*Jun 22 10:50:55.797: NAT*: s=200.1.1.1, d=128.27.30.5->8.8.8.8 [8646]
*Jun 22 10:50:55.798: NAT*: ICMP id=1024->4
*Jun 22 10:50:55.798: NAT*: s=8.8.8.8->128.27.30.5, d=200.1.1.1 [36440]
*Jun 22 10:50:55.798: NAT*: s=128.27.30.5, d=200.1.1.1->192.168.10.1 [36440]
*Jun 22 10:50:56.797: NAT*: ICMP id=4->1024
*Jun 22 10:50:56.797: NAT*: s=192.168.10.1->200.1.1.1, d=128.27.30.5 [8739]
*Jun 22 10:50:56.797: NAT*: s=200.1.1.1, d=128.27.30.5->8.8.8.8 [8739]
GWRT#
*Jun 22 10:50:56.798: NAT*: ICMP id=1024->4
*Jun 22 10:50:56.798: NAT*: s=8.8.8.8->128.27.30.5, d=200.1.1.1 [36468]
*Jun 22 10:50:56.798: NAT*: s=128.27.30.5, d=200.1.1.1->192.168.10.1 [36468]
GWRT#
*Jun 22 10:51:57.298: NAT: expiring 200.1.1.1 (192.168.10.1) icmp 1024 (4)
*Jun 22 10:51:57.298: NAT: Freeing nat entry, id 4
GWRT#no debug ip nat
IP NAT debugging is off
GWRT#
```
```
s: 送信元IPアドレス
->: 変換後IPアドレス
d: 宛先IPアドレス
```
- #debug ip natコマンドの実行後、#no debug ip natコマンドで無効化
- プライベートIPアドレス（192.168.10.1）が、グローバルIPアドレス（200.1.1.1）にNAPT変換されていることを確認
- debugの実行結果から、想定通りNAPTの設定が機能していることを確認

宛先NATにより、本来存在しないお客さん先の実環境を想定したDNSサーバのIPアドレスへの疎通が確認できました
これにより、導入機器のDNSサーバーの設定を変えることなく、本番環境の参照先のまま構築試験を行うことができます

↑宛先NATの文章が上手くまとまらなかったので、上手くまとめて欲しいです
↑なぜ128.27.30.5のIPアドレスを使用するのか分からなかった、任意のIPアドレスという認識は正しいのか？

### 学習成果・気付き

**技術的な学び**
- PPPoEは、最も多く使われるインターネット接続方式であり、パスワード認証で正規のクライアントにのみIPアドレスが付与される
- PPPoEは、認証機能のあるPPPをイーサネットで使用できるようにした技術
- PPPoEは、サーバとクライアントで構成されており、プロバイダ側がPPPoEサーバになる
- PPPoEサーバがパスワードによるクライアントの認証を行い、認証に成功するとグローバルIPアドレスをクライアントへ払い出して接続を可能にする
- ルータの複数のインターフェースに同一ネットワークのIPアドレス（〈例〉E0/0 → IPアドレス: 8.8.8.254/24・E0/2 → IPアドレス: 8.8.8.253/24は設定不可）を設定することができない
- ルータのインターフェースには異なるネットワークのIPアドレス（〈例〉E0/0 → IPアドレス: 8.8.8.254/24・E0/2 → IPアドレス: 8.8.7.254/24は設定可能）を設定する必要がある

**トラブルシューティング**
- 

**設定コマンドの学び**
- 

**所感**
- 初めてLinuxコマンドを使用し、ホスト名変更・IPアドレス・デフォルトゲートウェイの設定をAIツール（ChatGPT・Claude）を活用しながら行ったが、Linuxの仕組みが想像以上に複雑だった
- ネットワークエンジニアの現場でもLinuxコマンドを使用する場面が多いらしいので、引き続き学習を進めなければならないと実感した
