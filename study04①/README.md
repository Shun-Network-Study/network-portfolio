# 第4回①：NATの設定(基本)
- **実施日**: 2025/6/18
- **所要時間**: 約3時間

## 学習内容
- スタティックNATの設定
- ダイナミックNATの設定
- 内部から外部への通信時に、意図したグローバルIPアドレスへ正常に変換されていることを確認
- 内部から外部への通信時に、プールの中のグローバルIPアドレスへ変換されていることを確認
- debugコマンドを使用したNAT動作のリアルタイム監視とログ分析

## 使用機器
- **ルータ**: Cisco 2911 ×1台　※Cisco 891FJを利用できなかったため、コマンド制限の少ないCisco 2911を使用
- **L2スイッチ**: Catalyst2960 ×2台
- **検証用PC** ×2台
- **LANケーブル** ×4本

## 構成図
![構成図](/study04①/images/topology4.png)

## 設定内容

### ルータ（RTA）の設定
[ルータ（RTA）の全ての設定ファイルを見る](/study04①/configs/router-config-RTA.txt)

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
RTA(config)#interface GigabitEthernet 0/0
RTA(config-if)#ip address 192.168.1.254 255.255.255.0
RTA(config-if)#no shutdown

RTA(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

RTA(config-if)#exit
RTA(config)#interface GigabitEthernet 0/1
RTA(config-if)#ip address 192.168.2.254 255.255.255.0
RTA(config-if)#no shutdown

RTA(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

RTA(config-if)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console

```

**設定の確認**
```
RTA#show run
Building configuration...

Current configuration : 756 bytes
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
!
!
!
ip cef
no ipv6 cef
!
!
!
!
license udi pid CISCO2911/K9 sn FTX15244S8Z-
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
 ip address 192.168.1.254 255.255.255.0
 duplex auto
 speed auto
!
interface GigabitEthernet0/1
 ip address 192.168.2.254 255.255.255.0
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


RTA#
```

**設定の保存**
```
RTA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTA#
RTA#
```

### スイッチ（SWA）の設定
[スイッチ（SWA）の全ての設定ファイルを見る](/study04①/configs/switch-config-SWA.txt)

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
interface FastEthernet0/6
!
interface FastEthernet0/7

```

**設定の保存**
```
SWA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
SWA#
SWA#
```

### スイッチ（SWB）の設定
[スイッチ（SWB）の全ての設定ファイルを見る](/study04①/configs/switch-config-SWB.txt)

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
!
interface FastEthernet0/7
!
interface FastEthernet0/8

SWB#
```

**設定の保存**
```
SWB#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
SWB#
SWB#
```

### 検証用PC（PC A・PC B）の設定
#### 検証用PC（PC A・PC B）にIPアドレス・デフォルトゲートウェイを設定する
- PC A → IPアドレス: 192.168.1.1　サブネットマスク: 255.255.255.0
- PC Aのデフォルトゲートウェイ（RTA GigabitEthernet 0/0） → IPアドレス: 192.168.1.254　サブネットマスク: 255.255.255.0
![検証用PC（PC A）にIPアドレス・デフォルトゲートウェイを設定する](/study04①/images/課題4_1.png)

- PC B → IPアドレス: 192.168.2.1　サブネットマスク: 255.255.255.0
- PC Bのデフォルトゲートウェイ（RTA GigabitEthernet 0/1） → IPアドレス: 192.168.2.254　サブネットマスク: 255.255.255.0
![検証用PC（PC B）にIPアドレス・デフォルトゲートウェイを設定する](/study04①/images/課題4_2.png)

### 検証用PC（PC A・PC B）の設定の確認
- ipconfigコマンドで、PC AのIPアドレス・デフォルトゲートウェイの確認
![PC AのIPアドレス・デフォルトゲートウェイの確認](/study04①/images/課題4_3.png)

- ipconfigコマンドで、PC BのIPアドレス・デフォルトゲートウェイの確認
![PC BのIPアドレス・デフォルトゲートウェイの確認](/study04①/images/課題4_4.png)

### PC AからPC BへPingを使用して疎通確認
- 1回目は、パケットを4回送信し、3回受信し、25%の損失を確認
- 2回目は、パケットを4回送信し、4回受信し、0%の損失を確認
- 相手機器に対してはじめて疎通確認を行ったため、ARPによるアドレス解決（IPアドレスから対応するMACアドレスを求める処理）を行っていたと考えられる
![PC AからPC BへPingを使用して疎通確認](/study04①/images/課題4_5.png)

### PC BからPC AへPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC BからPC AへPingを使用して疎通確認](/study04①/images/課題4_6.png)

### 検証①内部から外部への通信の際に、意図したグローバルIPアドレスへ変換されていることを確認する
**RTAのインターフェースへ、内側、外側の設定**
```
RTA#conf t
RTA(config)#
RTA(config)#int
RTA(config)#interface Gi
RTA(config)#interface GigabitEthernet 0/0
RTA(config-if)#ip nat inside
RTA(config-if)#exit
RTA(config)#int
RTA(config)#interface Gi
RTA(config)#interface GigabitEthernet 0/1
RTA(config-if)#ip nat outside
RTA(config-if)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console
```
- ip nat inside: NAT通信の内側のインターフェースに設定
- ip nat outside: NAT通信の外側のインターフェースに設定

**設定した内容の確認**
```
RTA#show running-config
Building configuration...

Current configuration : 839 bytes
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
!
!
!
ip cef
no ipv6 cef
!
!
!
!
license udi pid CISCO2911/K9 sn FTX15244S8Z-
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
 ip address 192.168.1.254 255.255.255.0
 ip nat inside
 duplex auto
 speed auto
!
interface GigabitEthernet0/1
 ip address 192.168.2.254 255.255.255.0
 ip nat outside
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
ip nat inside source static 192.168.1.1 11.10.10.1 
ip classless
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


RTA#
RTA#
RTA#
```
- GigabitEthernet 0/0が ip nat inside であることを確認
- GigabitEthernet 0/1が ip nat outside であることを確認

**スタティックNATの設定**
```
RTA#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTA(config)#ip nat inside source static 192.168.1.1 11.10.10.1
RTA(config)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console

```

**設定した内容の確認**
```
RTA#show ip nat translations
Pro  Inside global     Inside local       Outside local      Outside global
---  11.10.10.1        192.168.1.1        ---                ---

RTA#
RTA#
```
- Inside global: 変換後のグローバルIPアドレス(11.10.10.1)であることを確認
- Inside Local: 変換前のプライベートIPアドレス(192.168.1.1)であることを確認

**設定の保存**
```
RTA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTA#
RTA#
RTA#
```

**疎通確認前にルータでdebugコマンドを実行**
- debugコマンドは、ルータやスイッチの動作をリアルタイムにログとして出力するためのコマンド

```
RTA#debug ip nat
IP NAT debugging is on
RTA#
RTA#
```
- #debug ip natコマンド入力後、ログが何も表示されなかった

### PC AからPC BへPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC AからPC BへPingを使用して疎通確認](/study04①/images/課題4_7.png)

**Ping疎通確認後**
```
RTA#debug ip nat
IP NAT debugging is on
RTA#
RTA#
NAT: s=192.168.1.1->11.10.10.1, d=192.168.2.1 [17]

NAT*: s=192.168.2.1, d=11.10.10.1->192.168.1.1 [16]

NAT: s=192.168.1.1->11.10.10.1, d=192.168.2.1 [18]

NAT*: s=192.168.2.1, d=11.10.10.1->192.168.1.1 [17]

NAT: s=192.168.1.1->11.10.10.1, d=192.168.2.1 [19]

NAT*: s=192.168.2.1, d=11.10.10.1->192.168.1.1 [18]

NAT: s=192.168.1.1->11.10.10.1, d=192.168.2.1 [20]

NAT*: s=192.168.2.1, d=11.10.10.1->192.168.1.1 [19]

NAT: expiring 11.10.10.1 (192.168.1.1) icmp 13 (13)

NAT: expiring 11.10.10.1 (192.168.1.1) icmp 14 (14)

NAT: expiring 11.10.10.1 (192.168.1.1) icmp 15 (15)

NAT: expiring 11.10.10.1 (192.168.1.1) icmp 16 (16)

RTA#no debug ip nat
IP NAT debugging is off
RTA#
RTA#
RTA#
```
```
s: 送信元IPアドレス
->: 変換後IPアドレス
d: 宛先IPアドレス
```
- #debug ip natコマンドは、Ping疎通確認後にログが表示される
- PC AからPC Bにパケットが送信されたタイミングの1行目のログと、PC BからPC Aにパケットが返ってきた2行目のログを確認
- プライベートIPアドレス（192.168.1.1）がグローバルIPアドレス（11.10.10.1）に正常に変換されている
- スタティックNATの設定が正常に動作していることを確認
- また、#debug ip natコマンドの実行後は、#no debug ip natコマンドまたは#no debug allコマンドで無効化することが重要
- このコマンドは、NATに関する大量のログをリアルタイムで出力するため、ルータのCPU負荷が高くなり、操作に支障をきたす恐れがある

**課題には記載されていないログ【NAT: expiring】が出力されたため、Cisco公式サイト・CCNA白本・AIツール（ChatGPT・Claude）を用いて調査した。以下はその結果である。**
- このログは、一定時間通信が行われなかったことにより、NAT変換エントリが期限切れ（expiring）となり、自動的に削除されたことを示している
- NAT変換エントリとは、プライベートIPアドレスとグローバルIPアドレスの対応関係を管理する情報で、NATテーブルに記録される
- NAT変換エントリは、スタティックNATでは手動で設定するため恒久的だが、ダイナミックNATでは一定時間使用されないと期限切れ（expiring）となり、自動的に削除される
- ICMPプロトコル（ping通信）の場合、各ICMPエコー要求に対してNAT変換エントリが作成され、エコー応答の後は短時間で順次削除される
- これはルータがメモリリソースを効率的に管理するための正常な動作である

### 検証②内部から外部への通信の際に、プールの中のグローバルIPアドレスへ変換されていることを確認する

**検証の妨げとなるため、スタティックNATの設定を削除**
```
RTA>en
Password: 
RTA#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTA(config)#no ip nat inside source static 192.168.1.1 11.10.10.1
```

**NATプールの設定**
```
RTA(config)#ip nat pool cairn 11.10.10.1 11.10.10.254 netmask 255.255.255.0
```

**変換前のIPアドレスをACLで定義**
```
RTA(config)#access-list 1 permit 192.168.1.0 0.0.0.255
RTA(config)#interface GigabitEthernet 0/0
RTA(config-if)#ip access-group 1 in
```
- ダイナミックNATの場合、多：多でアドレス変換するため、プライベートIPアドレスを標準ACLで定義する必要がある
- 作成した標準ACLは、内側と定義したインターフェース（GigabitEthernet 0/0）にin方向で適用

**ダイナミックNATの設定**
```
RTA(config-if)#ip nat inside source list 1 pool cairn
RTA(config)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console

```

**設定の保存**
```
RTA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTA#
RTA#
```

**疎通確認前にルータでdebugコマンドを実行**
```
RTA#debug ip nat
IP NAT debugging is on
RTA#
RTA#
```

### PC AからPC BへPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC AからPC BへPingを使用して疎通確認](/study04①/images/課題4_8.png)

**Ping疎通確認後**
```
RTA#debug ip nat
IP NAT debugging is on
RTA#
RTA#
NAT: s=192.168.1.1->11.10.10.1, d=192.168.2.1 [21]

NAT*: s=192.168.2.1, d=11.10.10.1->192.168.1.1 [20]

NAT: s=192.168.1.1->11.10.10.1, d=192.168.2.1 [22]

NAT*: s=192.168.2.1, d=11.10.10.1->192.168.1.1 [21]

NAT: s=192.168.1.1->11.10.10.1, d=192.168.2.1 [23]

NAT*: s=192.168.2.1, d=11.10.10.1->192.168.1.1 [22]

NAT: s=192.168.1.1->11.10.10.1, d=192.168.2.1 [24]

NAT*: s=192.168.2.1, d=11.10.10.1->192.168.1.1 [23]

NAT: expiring 11.10.10.1 (192.168.1.1) icmp 17 (17)

NAT: expiring 11.10.10.1 (192.168.1.1) icmp 18 (18)

NAT: expiring 11.10.10.1 (192.168.1.1) icmp 19 (19)

NAT: expiring 11.10.10.1 (192.168.1.1) icmp 20 (20)

RTA#
RTA#no debug ip nat
IP NAT debugging is off
RTA#
RTA#
RTA#
```
```
s: 送信元IPアドレス
->: 変換後IPアドレス
d: 宛先IPアドレス
```
- プライベートIPアドレス（192.168.1.1）が、NATプールの範囲から払い出されたグローバルIPアドレス（11.10.10.1）に変換されていることを確認
- PC AからPC Bにパケットが送信されたタイミングの1行目のログと、PC BからPC Aにパケットが返ってきた2行目のログを確認
- NAT変換エントリの自動削除機能も正常に動作
- #debug ip natコマンドの実行後、#no debug ip natコマンドで無効化
- ダイナミックNATの設定が正常に動作していることを確認

### 学習成果・気付き

**技術的な学び**
- 【NAT: expiring】 → 一定時間通信が行われなかったことにより、NAT変換エントリが期限切れ（expiring）となり、自動的に削除されたことを示している、正常な動作であるログ
- NAT変換エントリ → プライベートIPアドレスとグローバルIPアドレスの対応関係を管理する情報で、NATテーブルに記録される
- スタティックNAT → 手動で設定するため恒久的にNAT変換エントリがNATテーブルに記録される
- ダイナミックNAT → NAT変換エントリが一定時間使用されないと期限切れ（expiring）となり、自動的に削除される
- ダイナミックNATの場合、多：多でアドレス変換するため、プライベートIPアドレスを標準ACLで定義する必要がある
- 作成した標準ACLは、内側と定義したインターフェースにin方向で適用
- NATを設定する際には、インターフェースへ内側（inside）、外側（outside）の設定をする
- ダイナミックNATは、変換後のグローバルIPアドレスをプールで指定する

**トラブルシューティング**
- #debug ip natコマンドの実行後は、#no debug ip natコマンドまたは#no debug allコマンドで無効化することが重要
- このコマンドは、NATに関する大量のログをリアルタイムで出力するため、ルータのCPU負荷が高くなり、操作に支障をきたす恐れがある
- NATプールの設定時、サブネットマスクの指定を忘れやすいため注意が必要
- ACLの適用方向（in/out）を間違えやすいため、設定後は必ず動作確認を実施

**設定コマンドの学び**
- #show running-config → NAT通信の内側・外側のインターフェースの確認
- #debug ip nat → コマンド入力後はログが何も表示されず、Ping疎通確認後にログが表示される

**所感**
- スタティックNATとダイナミックNATの違いを実機シミュレーターで確認することで、理論だけでは把握しづらい動作の違いを体感できた
- debugコマンドによるリアルタイム監視は、NATの動作原理を視覚的に理解する上で非常に有効だった
- 本番環境では、debugコマンドの使用はCPU負荷の観点から慎重に行う必要があることを学んだ
