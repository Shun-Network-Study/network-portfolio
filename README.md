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

task01_basic-routing/
├── topology.png（トポロジ図）
├── config.txt（各機器の設定）
└── README.md（課題の説明・手順・学び）

# 課題：静的ルーティングの設定

## 構成概要
静的ルーティングで3拠点間の通信を確立。ルータ3台・PC3台を使用。

## 使用機器
- Cisco Packet Tracer
- ルータ：3台（PT-Router）
- スイッチ：3台（PT-Switch）
- PC：3台

## 構成図
![トポロジ図](topology.png)

## 設定内容（抜粋）
```bash
R1(config)# ip route 192.168.2.0 255.255.255.0 10.0.0.2

---

<a name="files"></a>
## 5. 🖼 スクリーンショット・設定ファイルの貼り方

- トポロジ図は **Packet TracerやCMLの画面をスクショして、PNGで保存**
  - Windows：`Win + Shift + S` で範囲指定スクショ可
- コンフィグは `show run` の内容を `config.txt` にコピペ
- `README.md` で画像を表示するには同じフォルダに `topology.png` を保存しておけばOKです：

```markdown
![構成図](topology.png)
