# network-portfolio
ネットワーク構築練習用ポートフォリオ
# ネットワーク構築練習用ポートフォリオ

はじめまして。 前職は映像ディレクターとして従事しておりましたが、IT業界へのキャリアチェンジを目指し、未経験からネットワークエンジニアとしてのキャリアを志望しております。 
2025年5月にCCNA（Cisco Certified Network Associate）を取得し、ネットワーク構築スキルを高めるために、Cisco Packet TracerとCisco Modeling Labsを使ってハンズオン学習を続けています。 

このリポジトリでは、「ネットワーク構築マラソン」（株式会社CAIRN 運営） の課題をベースに、構築トポロジ、設定内容、動作検証結果などをまとめています。
実務未経験ではありますが、構築の手順やトラブルシューティングの思考などを可視化することで、ポートフォリオとして採用担当者の方にスキルと姿勢を伝えられればと思っております。

## 保有資格
- CCNA（Cisco Certified Network Associate） 【2025年5月8日 取得】

## 使用ツール
- Cisco Packet Tracer
- Cisco Modeling Labs（無料版 / 5ノードまで）
- GitHub / Markdown

## 課題一覧
- task01_basic-routing：静的ルーティングの構築と疎通確認
- task02_vlan-stp：VLAN、STPの設定と検証
- task03_ospf：OSPFルーティングの構成　など

## 学習記録

### 第1回：Cisco機器の基本設定【実機検証】

#### 学習内容
- Ciscoスイッチ、ルータの基本設定（ホスト名、パスワード、IPアドレス）  
- Pingを使用したPCからルータへの疎通確認
- Telnetを使用したPCからルータへのリモートログイン確認

#### 使用機器
- **ルータ**: Cisco 2911 ×1台
  - ※Cisco 891FJがなかったため、コマンド制限の少ないCisco 2911を使用
- **L2スイッチ**: Catalyst2960 ×1台
- **検証用PC**: ×1台
- **LANケーブル**: ×2本

#### 構成図
![ネットワーク構成図](images/session01/network-diagram.png)

#### 設定内容

##### スイッチの設定
[完全な設定ファイルを見る](configs/session01/switch-config.txt)

**ホスト名の設定**
```
Switch>en
Switch#conf t
Switch(config)#hostname SWA
```

**enableパスワード設定**
```
SWA(config)#enable password Cisco
SWA(config)#enable secret Cisco
```

**使用しないインターフェースの無効化**
```
SWA(config)#interface range FastEthernet 0/2 - 23
SWA(config-if-range)#shutdown
```

##### ルータの設定  
[完全な設定ファイルを見る](configs/session01/router-config.txt)

**ホスト名の設定**
```
Router>en
Router#conf t
Router(config)#hostname RTA
```

**IPアドレス設定**
```
RTA(config)#interface GigabitEthernet 0/0
RTA(config-if)#ip address 192.168.1.254 255.255.255.0
RTA(config-if)#no shutdown
```

**Telnet設定**
```
RTA(config)#line vty 0 4
RTA(config-line)#password Cisco
RTA(config-line)#login
```

#### 検証結果

##### PC設定
![PC IPアドレス設定](images/session01/pc-ip-config.png)

##### 疎通確認テスト
![Pingテスト結果](images/session01/ping-test.png)

##### リモートログインテスト
![Telnetログイン](images/session01/telnet-test.png)

#### 学習成果・気付き

**機器選択について**
- Cisco 891FJに近い性能で、コマンド制限の少ない機器を調査
- Cisco 829では多くのコマンド制限があり、エラーが頻発
- ChatGPT・Claude・Geminiを活用してCisco 2911を選択
- 結果的にコマンド制限なく検証を完了できた

**技術的な学び**
- インターフェースにIPアドレスを設定することでリモートログインが可能になる
- 検証用PCは検証ネットワークに合わせたIPアドレス設定が必要
- セキュリティ対策として使用しないポートの無効化が重要

---

## 今後の学習予定
- 第2回：VLAN設定
- 第3回：ルーティング設定
- 第4回：ACL（アクセスコントロールリスト）
