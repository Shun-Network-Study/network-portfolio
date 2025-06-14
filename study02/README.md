# 第2回：ルーティングの設定【実機検証】
- **実施日**: 2025/6/13
- **所要時間**: 約4時間

## 学習内容
- スタティックルートの設定
- OSPFの設定
- AD値の理解
- スタティックルート設定後、Pingを使用した異なるネットワーク間の疎通確認
- OSPF設定後、Pingを使用した疎通確認と、ルーティングテーブルでの動的ルーティング学習の確認

## 使用機器
- **ルータ**: Cisco 2911 ×2台　※Cisco 891FJがなかったため、コマンド制限の少ないCisco 2911を使用
- **L2スイッチ**: Catalyst2960 ×2台
- **検証用PC** ×2台
- **LANケーブル** ×5本

## 構成図
![構成図](/study02/images/topology2.png)

## 設定内容

### ルータ（RTA）の設定
[全ての設定ファイルを見る](/study02/configs/router-config-RTA.txt)

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
RTA(config)#interface G
RTA(config)#interface GigabitEthernet 0/0
RTA(config-if)#ip addoress 192.168.1.254 255.255.255.0
                     ^
% Invalid input detected at '^' marker.
	
RTA(config-if)#no shutdown

RTA(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

RTA(config-if)#ip address 192.168.1.254 255.255.255.0
RTA(config-if)#no shutdown
RTA(config-if)#exit
RTA(config)#int
RTA(config)#interface Gi
RTA(config)#interface GigabitEthernet 0/1
RTA(config-if)#ip address 192.168.3.1 255.255.255.0
RTA(config-if)#no shutdown

RTA(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

RTA(config-if)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console

```
- Configの流し込みを行っていた所「ip addoress」と誤入力 → 修正して「ip address」で設定成功

**設定の確認**
```
RTA#show run
Building configuration...

Current configuration : 757 bytes
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
no ip cef
no ipv6 cef
!
!
!
!
license udi pid CISCO2911/K9 sn FTX152445S9-
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
```

**設定の保存**
```
RTA#
RTA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTA#
RTA#
RTA#
```

### ルータ（RTB）の設定
[全ての設定ファイルを見る](/study02/configs/router-config-RTB.txt)

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

RTB(config-if)#end
RTB#
%SYS-5-CONFIG_I: Configured from console by console
```

**設定の確認**
```
RTB#show run
Building configuration...

Current configuration : 757 bytes
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
no ip cef
no ipv6 cef
!
!
!
!
license udi pid CISCO2911/K9 sn FTX15246438-
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


RTB#
RTB#
RTB#
```

**設定の保存**
```
RTB#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTB#
```

### スイッチ（SWA）の設定
[全ての設定ファイルを見る](/study02/configs/switch-config-SWA.txt)

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
[全ての設定ファイルを見る](/study02/configs/switch-config-SWB.txt)

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

### 検証PC（PC A・PC B）の設定
#### 検証PC（PC A・PC B）にIPアドレスを割り当てる
- PC A → IPアドレス:192.168.1.1　サブネットマスク:255.255.255.0
![検証PC（PC A）にIPアドレスを割り当てる](/study02/images/課題2_1.png)

- PC B → IPアドレス:192.168.2.1　サブネットマスク:255.255.255.0
![検証PC（PC B）にIPアドレスを割り当てる](/study02/images/課題2_2.png)

#### 検証PC（PC A・PC B）にデフォルトゲートウェイを設定する
- PC Aのデフォルトゲートウェイ（RTA） → IPアドレス:192.168.1.254 サブネットマスク:255.255.255.0
![検証PC（PC A）にデフォルトゲートウェイを設定する](/study02/images/課題2_3.png)

- PC Bのデフォルトゲートウェイ（RTB） → IPアドレス:192.168.2.254 サブネットマスク:255.255.255.0
![検証PC（PC B）にデフォルトゲートウェイを設定する](/study02/images/課題2_4.png)
- 端末（PCやスマホ）が自身の所属するネットワークの外へ通信を行う場合には、ルータにルーティングをしてもらわなければならないため、デフォルトゲートウェイの設定が必要になる

### 検証PC（PC A・PC B）の設定の確認
- ipconfigコマンドで、PC AのIPアドレス・デフォルトゲートウェイの確認
![PC AのIPアドレス・デフォルトゲートウェイの確認](/study02/images/課題2_5.png)

- ipconfigコマンドで、PC BのIPアドレス・デフォルトゲートウェイの確認
![PC BのIPアドレス・デフォルトゲートウェイの確認](/study02/images/課題2_6.png)

### PC AからRTAへPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC AからRTAへPingを使用して疎通確認](/study02/images/課題2_7.png)

### PC BからRTBへPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC BからRTBへPingを使用して疎通確認](/study02/images/課題2_8.png)

### 検証①スタティックルートを設定し、異なるネットワークの端末へ疎通が可能なことを確認する
**RTAにスタティックルートの設定**
```
RTA>en
Password: 
RTA#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTA(config)#ip route 192.168.2.0 255.255.255.0 192.168.3.2
RTA(config)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console

```

**設定したスタティックルートの確認**
```
RTA#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.1.0/24 is directly connected, GigabitEthernet0/0
L       192.168.1.254/32 is directly connected, GigabitEthernet0/0
S    192.168.2.0/24 [1/0] via 192.168.3.2
     192.168.3.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.3.0/24 is directly connected, GigabitEthernet0/1
L       192.168.3.1/32 is directly connected, GigabitEthernet0/1

RTA#
```
- プロトコルが「S」であり、192.168.2.0/24の宛先へ192.168.3.2がネクストホップになっていることを確認

**RTBにスタティックルートの設定**
```
RTB>en
Password: 
RTB#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTB(config)#ip route 192.168.1.0 255.255.255.0 192.168.3.1
RTB(config)#end
RTB#
%SYS-5-CONFIG_I: Configured from console by console
```

**設定したスタティックルートの確認**
```
RTB#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

S    192.168.1.0/24 [1/0] via 192.168.3.1
     192.168.2.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.2.0/24 is directly connected, GigabitEthernet0/0
L       192.168.2.254/32 is directly connected, GigabitEthernet0/0
     192.168.3.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.3.0/24 is directly connected, GigabitEthernet0/1
L       192.168.3.2/32 is directly connected, GigabitEthernet0/1

RTB#
```
- プロトコルが「S」であり、192.168.1.0/24の宛先へ192.168.3.1がネクストホップになっていることを確認

### PC AからPC BへPingを使用して疎通確認
- 1回目は、パケットを4回送信し、3回受信し、25%の損失を確認
- 2回目は、パケットを4回送信し、4回受信し、0%の損失を確認
- CCNA白本で調査した所、相手機器に対してはじめて疎通確認を行うときは、ARPによる問い合わせをしていてタイムアウトしている可能性があるため、再度Pingを実行すると100%成功する、と記載されていた
- ARPによるアドレス解決（IPアドレスから対応するMACアドレスを求める処理）を行っていたと考えられる
![PC AからPC BへPingを使用して疎通確認](/study02/images/課題2_9.png)

### PC BからPC AへPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC BからRTBへPingを使用して疎通確認](/study02/images/課題2_10.png)

### 検証②OSPFを設定し、動的にルーティングを学習していることを確認する
**RTAにOSPFの設定**
```
RTA>en
Password: 
RTA#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTA(config)#router ospf 1
RTA(config-router)#network 192.168.1.254 0.0.0.0 area 0
RTA(config-router)#network 192.168.3.1 0.0.0.0 area 0
RTA(config-router)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console
```
**設定したOSPFの確認**
```
RTA#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.1.0/24 is directly connected, GigabitEthernet0/0
L       192.168.1.254/32 is directly connected, GigabitEthernet0/0
S    192.168.2.0/24 [1/0] via 192.168.3.2
     192.168.3.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.3.0/24 is directly connected, GigabitEthernet0/1
L       192.168.3.1/32 is directly connected, GigabitEthernet0/1

RTA#
```
- OSPFのAD値が110、スタティックルートのAD値が1で、AD値が低いルーティングが優先されるため、設定したOSPFのルートがルーティングテーブルに表示されない
- 複数のルーティングプロトコルで経路を学習している場合、1.ロンゲストマッチ　2.AD値　3.メトリックの順番に沿って適切なルーティングが採用され、ルーティングテーブルに載る

**設定したスタティックルートを削除**
```
RTA#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTA(config)#no ip route 192.168.2.0 255.255.255.0 192.168.3.2
RTA(config)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console

```
- Cisco機器の多くは、コマンドの前に「no」を付けることで削除ができる

**設定したOSPFの確認**
```
RTA#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.1.0/24 is directly connected, GigabitEthernet0/0
L       192.168.1.254/32 is directly connected, GigabitEthernet0/0
     192.168.3.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.3.0/24 is directly connected, GigabitEthernet0/1
L       192.168.3.1/32 is directly connected, GigabitEthernet0/1

RTA#

```
- スタティックルートは削除されたが、OSPFのルートが表示されていなかった
- RTBにOSPFの設定を行っていないからだと仮定

**RTBにOSPFの設定**
```
RTB>en
Password: 
RTB#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTB(config)#no ip route 192.168.1.0 255.255.255.0 192.168.3.1
RTB(config)#router ospf 1
RTB(config-router)#network 192.168.2.254 0.0.0.0 area 0
RTB(config-router)#network 192.168.3.2 0.0.0.0 area 0
RTB(config-router)#
03:32:03: %OSPF-5-ADJCHG: Process 1, Nbr 192.168.3.1 on GigabitEthernet0/1 from LOADING to FULL, Loading Done

RTB(config-router)#
```

```
03:32:03: %OSPF-5-ADJCHG: Process 1, Nbr 192.168.3.1 on GigabitEthernet0/1 from LOADING to FULL, Loading Done

```
- OSPFで隣接ルータ（RTA）とのネイバー関係が確立されたログを確認

**設定したOSPFの確認**
```
RTB#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

O    192.168.1.0/24 [110/2] via 192.168.3.1, 00:00:46, GigabitEthernet0/1
     192.168.2.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.2.0/24 is directly connected, GigabitEthernet0/0
L       192.168.2.254/32 is directly connected, GigabitEthernet0/0
     192.168.3.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.3.0/24 is directly connected, GigabitEthernet0/1
L       192.168.3.2/32 is directly connected, GigabitEthernet0/1

RTB#
```
- プロトコルが「O」であり、192.168.1.0/24の宛先へ192.168.3.1がネクストホップになっている、OSPFのルートが表示された

**設定の保存**
```
RTB#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTB#
```

**RTAで設定したOSPFの確認**
```
03:52:35: %OSPF-5-ADJCHG: Process 1, Nbr 192.168.3.2 on GigabitEthernet0/1 from LOADING to FULL, Loading Done


RTA>en
Password: 
RTA#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.1.0/24 is directly connected, GigabitEthernet0/0
L       192.168.1.254/32 is directly connected, GigabitEthernet0/0
O    192.168.2.0/24 [110/2] via 192.168.3.2, 00:11:06, GigabitEthernet0/1
     192.168.3.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.3.0/24 is directly connected, GigabitEthernet0/1
L       192.168.3.1/32 is directly connected, GigabitEthernet0/1

RTA#
RTA#
```
- プロトコルが「O」であり、192.168.2.0/24の宛先へ192.168.3.2がネクストホップになっている、OSPFのルートが表示された
- RTBにOSPFの設定をする前は、ルーティングテーブルにOSPFのルートが表示されていなかった
- OSPFは片方のルータのみ有効化した場合、ルートが学習されないため、OSPFのルートはルーティングテーブルに表示されない
- 両方のルータでOSPFを有効化する必要がある

```
03:52:35: %OSPF-5-ADJCHG: Process 1, Nbr 192.168.3.2 on GigabitEthernet0/1 from LOADING to FULL, Loading Done

```
- OSPFで隣接ルータ（RTB）とのネイバー関係が確立されたログを確認

**設定の保存**
```
RTA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTA#
```

### PC AからPC BへPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC AからPC BへPingを使用して疎通確認_OSPF](/study02/images/課題2_11.png)

### PC BからPC AへPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC BからRTBへPingを使用して疎通確認_OSPF](/study02/images/課題2_12.png)

### 学習成果・気付き

**技術的な学び**
- 端末（PCやスマホ）が自身の所属するネットワークの外へ通信を行う場合には、ルータにルーティングをしてもらわなければならないため、デフォルトゲートウェイの設定が必要になる
- 相手機器に対してはじめてPingを使用して疎通確認を行うときは、ARPによる問い合わせをしていてタイムアウトしている可能性があるため、再度Pingを実行すると100%成功する
- 複数のルーティングプロトコルで経路を学習している場合、1.ロンゲストマッチ　2.AD値　3.メトリックの順番に沿って適切なルーティングが採用され、ルーティングテーブルに載る
- OSPFは片方のルータのみ有効化した場合、ルートが学習されないため、OSPFのルートはルーティングテーブルに表示されない
- 両方のルータでOSPFを有効化する必要がある
- OSPFは、有効化させてインターフェースを指定することで、動的に経路を学習する
- AD値が低いルーティングが優先される

**設定コマンドの学び**
- これまでは#show interfaces statusコマンドでスイッチに存在する全てのインターフェースを確認していたが、#show ip interface briefコマンドの方が、スイッチとルータの両方で全インターフェースを確認できるので便利
- Cisco機器の多くは、コマンドの前に「no」を付けることで削除ができる
