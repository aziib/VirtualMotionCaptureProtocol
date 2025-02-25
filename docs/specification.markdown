---
layout: page
title:  "VMC Protocol プロトコル仕様概要"
subtitle: "プロトコル仕様の詳細をご説明します"
description: "VMCProtocol - ゲーム、ツール、配信環境など、あらゆる場所で使いやすいモーションキャプチャプロトコル仕様"
image: vmpc_logo_128x128.png
hero_image: image.gif
hero_height: is-fullwidth
hero_darken: true
---

# はじめに
対応アプリケーションは本プロトコルの全てに対応しているとは限りません。  
1項目でも利用可能であれば、対応とすることができます。  

基本的に安定した同一ローカルネットワーク内での利用が想定されています。  
インターネットを超えての利用は想定されていません。

# 目次
- [はじめに](#はじめに)
- [バージョン](#バージョン)
- [用語](#用語)
- [通信形式について](#通信形式について)
- OSCのデバッグ
- [Marionette(モーション受信側アプリケーション)受信仕様](marionette-spec)
- [Performer(モーション送信側アプリケーション)受信仕様](performer-spec)

# バージョン
現在のプロトコルバージョンはV3.0です。

# 用語
単にサーバー、クライアントと表記すると混乱が発生します。OSCでは受信側がサーバー、送信側がクライアントとなるためです。

そのためVMC Protocolでは、以下の独自の用語を定義します。

+ **Marionette** - モーションを受信し、描画などを行います。(必須)  
最終的に画面や映像・通信に成果を出すための存在です。  
(例: EVMC4U, VMC4UE, その他モーション受信対応アプリケーション)
+ **Performer** - 主にモーションを処理し、全身ボーン情報と補助情報をMarionetteに送信します。(必須)  
(例: バーチャルモーションキャプチャー, Waidayo, VSeeFace, MocapForAll, TDPTなど)
+ **Assistant** - 主にモーションの処理はせず、補助的な情報をPerformerに送信します。(オプション)  
補助情報のみを送信する担当です。(一部ボーン、トラッカー姿勢、表情など)   
(例: face2vmcモードのWaidayo, Sknuckle, Simple Motion Tracker, Uni-studioなど)

**注意: Assistant(39540)→Marionette(39539) の直結は、大抵望み通りの結果を得られません。(送信される情報が不足していることが多いです)**  
**必ずAssistant(39540)→(39540)Performer(39539)→Marionette(39539)と経由してください。**  

AssistantとPerformerの両方の機能を持つソフトウェア(切替可能なもの)もあります。

![flow](flow.gif)

# 通信形式について
VMC Protocolでは、基本的にOpen Sound Control(OSC)の単方向UDP通信による実装で通信を行います。

詳細な通信のための規定として以下があります。

+ 通信の際はOSCの適切な型を使用します。
+ 文字列はUTF-8エンコードで行い、日本語が送信されることがあります。
+ ポート番号は、MarionetteはPort:39539で待受、PerformerはPort:39540で待受するのを基本としますが、  
UX観点より送信アドレス・受信ポートを変更可能にすることをおすすめします。
+ パケットは適切な範囲(1500byte以内)でbundle化されており、受信者は適切に扱う必要があります。
+ 送信周期は送信者の任意の間隔で行われます。すべてのメッセージが毎周期送信されるわけではありません。  
また、送信側は送信周期の間隔を調整できるようにするか、十分低い頻度で送信するようにしてください。  
+ 受信側は不要なメッセージは破棄してください。すべてのメッセージを必ず処理する必要はありません。
+ どのメッセージを送信するか、受信するかは双方の実装に依存します。
+ 不明なアドレス、多すぎる引数は無視する必要があります。
+ 拡張仕様として定義されている以上に少なすぎる引数、型の違う引数を検出した場合はエラーとするか無視してください。

# OSCのデバッグ
データのチェックには以下が便利です。  
[VMCProtocolMonitor](https://github.com/gpsnmeajp/VMCProtocolMonitor)

一般的なOSCモニタツールも使用することができます。  
[OSCDataMonitor(Github)](https://github.com/kasperkamperman/OSCDataMonitor)  
[OSCDataMonitor(Download)](https://www.kasperkamperman.com/blog/processing-code/osc-datamonitor/)  


# [Marionette(モーション受信側アプリケーション)受信仕様](marionette-spec)
# [Performer(モーション送信側アプリケーション)受信仕様](performer-spec)
