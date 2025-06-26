# 第5回：DHCPの設定
- **実施日**: 2025/6/24
- **所要時間**: 約1.5時間

## 学習内容
- ルータにDHCPの機能を実装
- DHCPサーバによって、クライアントPCにIPアドレス、デフォルトゲートウェイが自動的に設定されることを確認

## 使用機器
- **ルータ**: Cisco 2911 ×1台　※Cisco 891FJを利用できなかったため、コマンド制限の少ないCisco 2911を使用
- **L2スイッチ**: Catalyst2960 ×1台
- **検証用PC** ×2台
- **LANケーブル** ×3本

## 構成図
![構成図](/study05/images/topology5.png)

## 設定内容

### ルータ（RTA）の設定
[ルータ（RTA）の全ての設定ファイルを見る](/study05/configs/router-config-RTA.txt)

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
RTA(config)#interface Gi
RTA(config)#interface GigabitEthernet 0/0
RTA(config-if)#ip address 192.168.1.254 255.255.255.0
RTA(config-if)#no shutdown

RTA(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

RTA(config-if)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console
```

**設定の確認**
```
RTA#show ru
RTA#show running-config
Building configuration...

Current configuration : 741 bytes
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
license udi pid CISCO2911/K9 sn FTX152434W1-
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
 no ip address
 duplex auto
 speed auto
 shutdown
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
```

**設定の保存**
```
RTA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTA#

```

### スイッチ（SWA）の設定
[スイッチ（SWA）の全ての設定ファイルを見る](/study05/configs/switch-config-SWA.txt)

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
```

### 検証用PC（PC A・PC B）の設定
#### 検証用PC（PC A・PC B）がIPアドレスを自動的に取得するため、デフォルトの設定の『Static』から『DHCP』に変更する
![検証用PC（PC A）『DHCP』に変更する](/study05/images/課題5_1.png)

- 『DHCP』にチェックを入れた後、「DHCP failed. APIPA is being used.」と表示された
- CCNA白本で調査したところ、以下のことが判明
- APIPA（Automatic Private IP Addressing）: DHCPを利用する環境で、DHCPが機能せずにIPアドレスが割り当てられない時に自動的にIPアドレス（リンクローカルアドレス）を割り当てる技術
- リンクローカルアドレス: APIPAの仕組みによって割り当てられるIPアドレス
- リンクローカルアドレスの範囲: 169.254.0.0 ～ 169.254.255.255　サブネットマスク: 255.255.0.0
- DHCPが利用できない場合、APIPAによって自動的にIPアドレス（リンクローカルアドレス）が割り当てられる

![検証用PC（PC B）『DHCP』に変更する](/study05/images/課題5_2.png)

### 検証：DHCPで、IPアドレス、デフォルトゲートウェイが自動設定されることを確認する

**RTAにDHCPサーバの設定**
```
RTA>en
Password: 
RTA#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
RTA(config)#service dhcp
RTA(config)#ip dhcp pool cisco
RTA(dhcp-config)#network 192.168.1.0 255.255.255.0
RTA(dhcp-config)#default-router 192.168.1.254
RTA(dhcp-config)#end
RTA#
%SYS-5-CONFIG_I: Configured from console by console
```

**設定したDHCPサーバの確認**
```
RTA#show running-config
Building configuration...

Current configuration : 825 bytes
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
ip dhcp pool cisco
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.254
!
!
!
ip cef
no ipv6 cef
!
!
!
!
license udi pid CISCO2911/K9 sn FTX152434W1-
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
 no ip address
 duplex auto
 speed auto
 shutdown
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
```
- 「no service dhcp」の記載がなく、DHCP機能が有効化されていることを確認
- DHCPプール名「cisco」が設定されていることを確認
- アドレス範囲「192.168.1.0 255.255.255.0」が設定されていることを確認
- 割り当てるデフォルトゲートウェイ「192.168.1.254」が設定されていることを確認

**設定の保存**
```
RTA#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
RTA#
```

### 検証用PC（PC A・PC B）に対して払い出されたIPアドレス・デフォルトゲートウェイをコマンドプロンプトで確認
- ipconfigコマンドで、PC AのIPアドレス・デフォルトゲートウェイの確認
![PC AのIPアドレス・デフォルトゲートウェイの確認](/study05/images/課題5_3.png)
- PC A → IPアドレス: 192.168.1.1　サブネットマスク: 255.255.255.0
- 払い出されたIPアドレスが指定したアドレス範囲「192.168.1.0/24」内に収まっていることを確認
- デフォルトゲートウェイ「192.168.1.254」が払い出されていることを確認

![PC BのIPアドレス・デフォルトゲートウェイの確認](/study05/images/課題5_4.png)
- ipconfigコマンドで、PC BのIPアドレス・デフォルトゲートウェイの確認
- PC B → IPアドレス: 192.168.1.2　サブネットマスク: 255.255.255.0
- 払い出されたIPアドレスが指定したアドレス範囲「192.168.1.0/24」内に収まっていることを確認
- デフォルトゲートウェイ「192.168.1.254」が払い出されていることを確認

### PC AからPC B（IPアドレス: 192.168.1.2）へPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC AからPC BへPingを使用して疎通確認](/study05/images/課題5_5.png)

### PC BからPC A（IPアドレス: 192.168.1.1）へPingを使用して疎通確認
- パケットを4回送信し、4回受信し、0%の損失を確認
![PC BからPC AへPingを使用して疎通確認](/study05/images/課題5_6.png)

### 学習成果・気付き

**技術的な学び**
- APIPA（Automatic Private IP Addressing）: DHCPを利用する環境で、DHCPが機能せずにIPアドレスが割り当てられない時に自動的にIPアドレス（リンクローカルアドレス）を割り当てる技術
- リンクローカルアドレス: APIPAの仕組みによって割り当てられるIPアドレス
- リンクローカルアドレスの範囲: 169.254.0.0 ～ 169.254.255.255　サブネットマスク: 255.255.0.0
- DHCPが利用できない場合、APIPAによって自動的にIPアドレス（リンクローカルアドレス）が割り当てられる
- ルータをDHCPサーバにする場合は、DHCPプール、デフォルトゲートウェイの設定を行う
- クライアントPCにはDHCPサーバを利用してIPアドレスの取得を行うように設定する

**トラブルシューティング**
- APIPAのリンクローカルアドレス（169.254.x.x）が割り当てられた場合は、DHCPサーバの設定を再確認
- 「service dhcp」が有効になっているか、IPアドレス・デフォルトゲートウェイの設定に誤りがないかを確認する

**設定コマンドの学び**
- (dhcp-config)#network [ネットワークアドレス] [サブネットマスク] → 割り当てるIPアドレスの範囲を開始IPアドレス・終了IPアドレスで指定するのではなく、ネットワークアドレスとサブネットマスクを指定する
- (dhcp-config)#default-router [デフォルトゲートウェイのIPアドレス] → 割り当てるデフォルトゲートウェイを指定
- #show running-config → DHCPサーバの設定内容を確認

**所感**
- DHCPの仕組みを設定から検証まで一通り体験できたことで、IPアドレスの自動割り当ての利便性やトラブル時の対処方法を学ぶことができた
- APIPAの動作を確認することで、DHCPが機能しない場合の代替手段についても理解が深まった
- 今後は、より複雑なネットワーク環境でのDHCP運用や、トラブルシューティングの検証を行う予定
