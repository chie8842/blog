+++
title = "Surface Bookを買ってGPGPUで遊ぼうと思ったけど障壁が高かった話"
draft = false
description = ""
tags = ["Surface","GPU","Hyper-V"]
categories = ["tech"]
date = "2017-03-14T02:09:13+09:00"
author = "chie8842"
type = "post"
url = "/2017/03/14/surfacebook買ってgpgpuで遊ぼうとおもったけど障壁が高かった話/"


+++

はじめに
------------
Surface BookにNVIDIAのGPUが付けられる！ということで、
仮想サーバ上でGPGPUを試してみよう！と思ったけど、挫折したときの記録です。


Surface Bookゲット
---------------

持ち歩き用にずっとMacBookAirを使ってたけど、今どきメモリ4GBとかで結構不便を感じることが多かったので、
思い切ってSurface Bookを買ってみた。

オプションでNVIDIAのGPUが付けることができるということ。
ちなみに、Surface Bookでは、CPU/メモリ/ストレージ/バッテリといった主なモジュールは画面側についているけれど、GPUはキーボード側についているらしい。
こんな薄いのにcorei7だしメモリ16GB、SSD512GBでGPUつきとか、すごい！！！

ということで、ぽん！買ってしまった！高かった！！！

仮想サーバでGPU
----------------
とりあえずGPU使うならLinuxでしょ！と思った。
VirtualBoxでは、デフォルトでPCIパススルーの機能がないらしいので、使えない。
（Linux版のVirtualBoxでは、現在experimentalで[extension packs](https://www.virtualbox.org/manual/ch01.html#intro-installing)があるらしい。）Hyper-Vは仮想GPU対応しているということなので、
Hyper-V上にUbuntuたててみた。
よしよし、GPUが見えるか確認するぞ！
どん！

```
$ lspci | grep -i nvidia
（何も表示されない。）
```


？？？

Hyper-Vの設定->物理GPUの設定を確認する。
Surface Bookには、

* Intel HD Graphics 520
* NVIDIA GeForce GPU

の2つのGPUを搭載している。

<img src="/img/20170308_hyper-v_setting.png" width="320px">

このうち、NVIDIA GeForce GPUを指定すると、
「このGPUはRemoteFXの最小要件を満たしていません」
とのこと。
Windows上で仮想CPUを使う場合、RemoteFXというのが必要らしい。
RemoteFXの詳しい説明は、以下参照。

[マイクロソフト RemoteFXの説明](https://technet.microsoft.com/ja-jp/library/ff817578(v=ws.10).aspx)

Surface Bookに搭載されているGPUの調査
-------------------

そもそも、GeForceのバージョンがかいてないけど、こいつは何者なのか。。。
DeviceManagerとか確認しても、バージョンがどこにも書いていなかった。。。

そこで、**[GPU-Z](https://www.techpowerup.com/download/gpu-z/)**という、搭載されているGPUに関するスペックやステータスなどの情報が見られる軽量ツールをインストールしてみた。

尚、GPU-Zを使うには、**[OpenCL](https://software.intel.com/en-us/intel-opencl)**がないと、エラーが発生してGPU情報がうまく見られない。OpenCLインストール後は、OS再起動が必要となる。

GPU-Zで確認できたSurface BookのGPU情報はこちら。

<img src="/img/20170308_gpu-z.png" width="320px">

この情報を、**[NVIDIAのGeForceのスペックページ](http://www.nvidia.co.jp/object/geforce-gtx-900m-graphics-cards-jp.html#pdpContent=2)**や**[NVIDIA モバイルグラフィックス・スペック性能比較|パソコン実験工房資料室](https://www.pc-koubou.jp/blog/nvidia_mobile_gpu_reference.php)**で確認されるほかのGeForceシリーズのGPUのスペックと比べてみる。

* コア
    Surface BookのGPUの搭載コアは、GM108というものである。これは、他のNVIDIAシリーズのGPUで言うと、
**GeForce 940M, 930M, 845M[^1], 840M, 830M**で採用されているらしい。
現在のノートPC用の最新は**GeForce 980**なので、決して新しくはなさそうである。
[^1]: GeForce 845MはNVIDIAの公式ページには載っていない。

* メモリ
  ノートPC用のGeForce GTXシリーズでは、**DDR3**と**GDDR5**の2種類のメモリが利用されている。GPU-Zで確認されたSurface BookのGPUの搭載メモリは、高性能のGDDR5の方だった。
メモリ容量は、多くのGeForce900M/800Mシリーズが2GB以上であるのに対し、Surface BookのGPUの搭載メモリは1024MBと小さい。

分かったこと
---------------
* Surface Bookには、GeForce GPUが搭載されているけれど、仮想GPUは使えない。
* Surface Book搭載のGPUは、NVIDIA公式ページで確認されるどのバージョンのGeForceシリーズのGPUとも一致しない。
スペック的には、GeForce 940M, 930M, 845M, 840M, 830Mが近い。決して新しくない。

うーん、GPGPU目当てでSurfaceを買うのはやめた方がいいかも。
次また時間があったら、Windows上でcudaインストールしてGPGPUできるか試してみようかなー。

