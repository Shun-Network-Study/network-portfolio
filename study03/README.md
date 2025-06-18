# 第3回：アクセスリスト(ACL)の設定
- **実施日**: 2025/6/17
- **所要時間**: 約3.5時間

## 学習内容
- 標準ACL・拡張ACLの設定
- インターフェースへの適用
- 標準ACL設定後、Pingを使用してPC AからPC Bへの通信制御を確認
- 拡張ACL設定後、異なるネットワークのルータへのTelnet接続が制御されることを確認

## 使用機器
- **ルータ**: Cisco 2911 ×2台　※Cisco 891FJを利用できなかったため、コマンド制限の少ないCisco 2911を使用
- **L2スイッチ**: Catalyst2960 ×2台
- **検証用PC** ×2台
- **LANケーブル** ×5本

## 構成図
![構成図](/study03/images/topology3.png)

## 設定内容

### ルータ（RTA）の設定
[ルータ（RTA）の全ての設定ファイルを見る](/study03/configs/router-config-RTA.txt)

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
RTA(config)#int
RTA(config)#interface Gi
RTA(config)#interface GigabitEthernet 0/0
RTA(config-if)#ip address 192.168.1.254 255.255.255.0
RTA(config-if)#no shutdown

RTA(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

RTA(config-if)#exit
RTA(config)#inter
RTA(config)#interface Gi
RTA(config)#interface GigabitEthernet 0/1
RTA(config-if)#ip address 192.168.3.1 255.255.255.0
RTA(config-if)#no shutdown

RTA(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

RTA(config-if)#exit
```

**スタティックルートの設定**
```
RTA(config)#ip route 192.168.2.0 255.255.255.0 192.168.3.2
RTA(config)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console

```

**設定の確認**
```
RTA#show run
Building configuration...

Current configuration : 802 bytes
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
license udi pid CISCO2911/K9 sn FTX152410QQ-
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
 ip address 192.168.3.1 255.255.255.0
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
ip route 192.168.2.0 255.255.255.0 192.168.3.2 
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

### ルータ（RTB）の設定
[ルータ（RTB）の全ての設定ファイルを見る](/study03/configs/router-config-RTB.txt)

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
RTB(config)#int
RTB(config)#interface Gi
RTB(config)#interface GigabitEthernet 0/0
RTB(config-if)#ip address 192.168.2.254 255.255.255.0
RTB(config-if)#no shutdown

RTB(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

RTB(config-if)#exit
RTB(config)#inter
RTB(config)#interface Gi
RTB(config)#interface GigabitEthernet 0/1
RTB(config-if)#ip address 192.168.3.2 255.255.255.0
RTB(config-if)#no shutdown

RTB(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

RTB(config-if)#exit
```

**スタティックルートの設定**
```
RTB(config)#ip route 192.168.1.0 255.255.255.0 192.168.3.1
RTB(config)#end
RTB#
%SYS-5-CONFIG_I: Configured from console by console

```

**設定の確認**
```
RTB#show run
Building configuration...

Current configuration : 802 bytes
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
!
!
!
ip cef
no ipv6 cef
!
!
!
!
license udi pid CISCO2911/K9 sn FTX1524H50D-
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
 ip address 192.168.2.254 255.255.255.0
 duplex auto
 speed auto
!
interface GigabitEthernet0/1
 ip address 192.168.3.2 255.255.255.0
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
ip route 192.168.1.0 255.255.255.0 192.168.3.1 
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
RTB#
```

### スイッチ（SWA）の設定
[スイッチ（SWA）の全ての設定ファイルを見る](/study03/configs/switch-config-SWA.txt)

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
!
interface FastEthernet0/8
!
interface FastEthernet0/9

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
[スイッチ（SWB）の全ての設定ファイルを見る](/study03/configs/switch-config-SWB.txt)

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
!
interface FastEthernet0/9

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
- PC Aのデフォルトゲートウェイ（RTA） → IPアドレス: 192.168.1.254 サブネットマスク: 255.255.255.0
![検証用PC（PC A）にIPアドレス・デフォルトゲートウェイを設定する](/study03/images/課題3_1.png)

- PC B → IPアドレス: 192.168.2.1　サブネットマスク: 255.255.255.0
- PC Bのデフォルトゲートウェイ（RTB） → IPアドレス: 192.168.2.254 サブネットマスク: 255.255.255.0
![検証用PC（PC B）にIPアドレス・デフォルトゲートウェイを設定する](/study03/images/課題3_2.png)

### 検証用PC（PC A・PC B）の設定の確認
- ipconfigコマンドで、PC AのIPアドレス・デフォルトゲートウェイの確認
![PC AのIPアドレス・デフォルトゲートウェイの確認](/study03/images/課題3_3.png)

- ipconfigコマンドで、PC BのIPアドレス・デフォルトゲートウェイの確認
![PC BのIPアドレス・デフォルトゲートウェイの確認](/study03/images/課題3_4.png)

### PC AからPC BへPingを使用して疎通確認
- 1回目は、パケットを4回送信し、2回受信し、50%の損失を確認
- 2回目は、パケットを4回送信し、4回受信し、0%の損失を確認
- 相手機器に対してはじめて疎通確認を行ったため、ARPによるアドレス解決（IPアドレスから対応するMACアドレスを求める処理）を行っていたと考えられる
![PC AからRTAへPingを使用して疎通確認](/study03/images/課題3_5.png)

### PC BからPC AへPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC BからRTBへPingを使用して疎通確認](/study03/images/課題3_6.png)

### 検証①標準アクセスリストにて、PC AからPC Bへの通信が制御されることを確認する
- 標準ACLは、宛先に近いインターフェースへ設定するため、RTBへ設定する

**RTBに標準ACLの作成**
```
RTB>en
Password: 
RTB#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTB(config)#access-list 1 deny 192.168.1.1 0.0.0.0
RTB(config)#access-list 1 permit any
RTB(config)#end
RTB#
%SYS-5-CONFIG_I: Configured from console by console

```

**作成したACLの確認**
```
RTB#show access-lists
Standard IP access list 1
    10 deny host 192.168.1.1
    20 permit any

RTB#
```
- Standard IP access-lists 1 の1行目が 192.168.1.1 を deny していることを確認
- Standard IP access-lists 1 の2行目が any を permit していることを確認

**標準ACLのインターフェースへの適用**
```
RTB#inter
RTB#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTB(config)#inter
RTB(config)#interface Gi
RTB(config)#interface GigabitEthernet 0/0
RTB(config-if)#ip access-group 1 out
RTB(config-if)#end
RTB#
%SYS-5-CONFIG_I: Configured from console by console

```

**設定した内容（ACLの番号と適用方向）の確認**
```
RTB#show ip inter
RTB#show ip interface Gi
RTB#show ip interface GigabitEthernet 0/0
GigabitEthernet0/0 is up, line protocol is up (connected)
  Internet address is 192.168.2.254/24
  Broadcast address is 255.255.255.255
  Address determined by setup command
  MTU is 1500 bytes
  Helper address is not set
  Directed broadcast forwarding is disabled
  Outgoing access list is 1
  Inbound  access list is not set
  Proxy ARP is enabled
  Security level is default
  Split horizon is enabled
  ICMP redirects are always sent
  ICMP unreachables are always sent
  ICMP mask replies are never sent
  IP fast switching is disabled
  IP fast switching on the same interface is disabled
  IP Flow switching is disabled
  IP Fast switching turbo vector
  IP multicast fast switching is disabled
  IP multicast distributed fast switching is disabled
  Router Discovery is disabled
  IP output packet accounting is disabled
  IP access violation accounting is disabled

RTB#

```
- Outgoing access list is 1 が設定されていることを確認

**設定の保存**
```
RTB#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTB#
```

### PC AからPC BへPingを使用して疎通確認
- パケットを4回送信し、0回受信し、100%の損失を確認
- PC Aからの通信は全て拒否しているので、疎通不可な状態を確認
- 標準ACLにより、PC AからPC Bへの通信が制御されることを確認
![PC AからPC BへPingを使用して疎通確認](/study03/images/課題3_7.png)

### PC AからRTB(192.168.3.2)へPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
- ACLを適用しているインターフェースを通過しないため、疎通可能な状態を確認
![PC AからRTB(192.168.3.2)へPingを使用して疎通確認](/study03/images/課題3_8.png)

### 検証②拡張アクセスリストにて、異なるネットワークのルータへのTelnet接続が制御されることを確認する
- 拡張ACLは、送信元に近いインターフェースへ設定するため、RTAへ設定する

**検証の妨げとなるため、RTBのインターフェースに適用した標準ACLを削除**
```
RTB>en
Password: 
RTB#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTB(config)#inter
RTB(config)#interface Gi
RTB(config)#interface GigabitEthernet 0/0
RTB(config-if)#no access-group 1 out
                   ^
% Invalid input detected at '^' marker.
	
RTB(config-if)#no ip access-group 1 out
RTB(config-if)#exit
RTB(config)#
```
- Configの流し込みを行っていた際に「no access-group 1 put」と誤入力 → 修正して「no ip access-group 1 out」で設定成功

**設定の保存**
```
RTB(config)#end
RTB#
%SYS-5-CONFIG_I: Configured from console by console

RTB#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTB#
RTB#
```

**RTAに拡張ACLの作成**
```
RTA>en
Password: 
RTA#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTA(config)#access-list 100 deny tcp 192.168.1.1 0.0.0.0 192.168.2.254 0.0.0.0 eq 23
RTA(config)#access-list 100 permit ip any any
RTA(config)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console

```
- Telnet → プロトコル: TCP・ポート番号: 23
- 拡張ACLで主に使用するプロトコルは、ip、icmp、tcp、udp
- tcpやudpの場合は、ポート番号を指定する

**作成したACLの確認**
```
RTA#show access-lists
Extended IP access list 100
    10 deny tcp host 192.168.1.1 host 192.168.2.254 eq telnet
    20 permit ip any any

RTA#
```
- Extended IP access-lists 100 の1行目が 192.168.1.1 から 192.168.2.254 の telnet を deny していることを確認
- Extended IP access-lists 100 の2行目が any から any の ip通信を permit していることを確認

**拡張ACLのインターフェースへの適用**
```
RTA#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTA(config)#inter
RTA(config)#interface Gi
RTA(config)#interface GigabitEthernet 0/0
RTA(config-if)#ip access-group 100 in
RTA(config-if)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console

```

**設定した内容（ACLの番号と適用方向）の確認**
```
RTA#show ip interface Gi
RTA#show ip interface GigabitEthernet 0/0
GigabitEthernet0/0 is up, line protocol is up (connected)
  Internet address is 192.168.1.254/24
  Broadcast address is 255.255.255.255
  Address determined by setup command
  MTU is 1500 bytes
  Helper address is not set
  Directed broadcast forwarding is disabled
  Outgoing access list is not set
  Inbound  access list is 100
  Proxy ARP is enabled
  Security level is default
  Split horizon is enabled
  ICMP redirects are always sent
  ICMP unreachables are always sent
  ICMP mask replies are never sent
  IP fast switching is disabled
  IP fast switching on the same interface is disabled
  IP Flow switching is disabled
  IP Fast switching turbo vector
  IP multicast fast switching is disabled
  IP multicast distributed fast switching is disabled
  Router Discovery is disabled
  IP output packet accounting is disabled

```
- Inbound  access list is 100 が設定されていることを確認

**設定の保存**
```
RTA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTA#
RTA#
```

### PC AからRTB(192.168.2.254)へPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
- PCAからRTB(192.168.2.254)のTelnetのみを拒否しているため、Pingは成功した
![PC AからRTB(192.168.2.254)へPingを使用して疎通確認](/study03/images/課題3_9.png)

### PC AからRTB(192.168.3.2)へTelnet接続
![PC AからRTB(192.168.3.2)へTelnet接続1](/study03/images/課題3_10.png)
![PC AからRTB(192.168.3.2)へTelnet接続2](/study03/images/課題3_11.png)

- 【「無効なホスト」等のメッセージが表示され、接続できなければ意図した制御ができています。】と課題には記載されているが、Telnet接続が成功し、VTYパスワード「Password:Cisco」を入力してログインし、enableパスワード「Password:Cisco」を入力して特権モードへ移行できたため、Telnet接続が制御されなかったことを確認
- 【PC AからRTB (192.168.3.2)へTelnet接続】と課題には記載されているが、IPアドレス: 192.168.3.2は、RTBのGigabitEthernet 0/1であり、拡張ACLの2行目のpermit ip any anyに該当するため、Telnet接続が制御されなかったと考えられる
- 拡張ACLの1行目に設定した、宛先IPアドレスのRTB GigabitEthernet 0/0(192.168.2.254)の場合、Telnet接続が制御されていると推測

### PC AからRTB GigabitEthernet0/0(192.168.2.254)へTelnet接続
![PC AからRTB(192.168.2.254)へTelnet接続1](/study03/images/課題3_12.png)
![PC AからRTB(192.168.2.254)へTelnet接続2](/study03/images/課題3_13.png)

**Telnetでのログイン成功画面**
```
User Access Verification

Password:
```
- 上記Telnetでのログイン成功画面が表示されず、キーを押しても何も反応しなかったので、Telnet接続が制御されていることを確認
- 拡張ACLにより、異なるネットワークのRTB  GigabitEthernet 0/0(192.168.2.254)へのTelnet接続が制御されることを確認
- ACLを適用しているインターフェースを通過しない場合、通信は許可される

### 学習成果・気付き

**技術的な学び**
- 標準ACLは、宛先に近いインターフェースに設定する
- ACLを適用しているインターフェースを通過しない場合、疎通可能な状態になる
- 拡張ACLは、送信元に近いインターフェースに設定する
- Telnet → プロトコル: TCP・ポート番号: 23
- 拡張ACLで主に使用するプロトコルは、ip、icmp、tcp、udp
- tcpやudpの場合は、ポート番号を指定する
- ACLは作成した順に、シーケンス番号が付与される
- インターフェースへ適用する際は、inかoutか、を指定する
- ACLの最後には暗黙のdenyが存在するため、許可したい通信は明示的にpermitで指定する必要がある
- ACL番号の範囲
- 標準ACL: 1-99、1300-1999
- 拡張ACL: 100-199、2000-2699
- ACLではサブネットマスクではなく、ワイルドカードマスクを使用する

**トラブルシューティング**
- ACL設定時の「ip」キーワード漏れの修正
- 拡張ACLの送信元・宛先IPアドレスの誤認識の解決

**設定コマンドの学び**
- Ctrl+Shift+6 が反応しない場合、Ctrl+C → コマンド表示を中断
- #show access-lists → ACL設定内容の確認
- #show ip interface[インターフェース名] → 設定したACLの番号と適用方向を確認する

**所感**
- 標準ACLでは送信元IPアドレスのみでの制御となるため、制御範囲が限定的であると感じた
- 拡張ACLを用いることで、送信元IPアドレス・宛先IPアドレス・プロトコル・ポート番号に応じた柔軟な制御が可能になる点を理解できた
- ACLの設定ミスは通信断の原因となるため、設定後にPingやTelnetなどを使用した検証・確認の重要性を実感した
