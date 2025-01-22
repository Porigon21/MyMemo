## start*.elf
バイナリファームウェアBLOBがSoCのVideoCore GPUにロードされてブートプロセスの引き継ぎをする。
### start.elf<br>
  基本的なファームウェアが入っている。<br>
### start_x.elf<br>
  追加のコーデック付き。<br>
### tart_db.elf<br>
  デバッグに使用。<br>
### tart_cd.elf<br>
  コーディックや3Dなのファームウェアブロックのサポートとデバッグログサポートをなくしたファームウェア。<br>
  簡易バージョンで初期フレームバッファの制限も課す。だがgpu_mem=16が指定されると簡易ファームウェアがconfig.txtを自動的に使用する。(ここだけ翻訳のバグがあっため自己解釈で。)<br>
start4.elfやstart4x.elf、start4db.elfとかstart4cd.elfなどはRaspberry Pi 4 シリーズに固有の同等のファームウェアファイル。<br>


> [!NOTE]
> ※Raspberry pi 5はelfファイルを使わない。

## fixup*.dat
start*.elfより前のセクションのリストに記載されたファイルと一致するペアで見つかったリンカーファイル。

## cmdline.txt
起動時にカーネルに渡されるカーネルコマンドライン。

## config.txt
Raspberry Piを設定するための多くの構成パラメータがある。詳細はドキュメントに。<br>
重要:Raspberry Pi 5ではブートパーティションに空でないファイルが必要。

## issue.txt
ディストリビューションの日付、GitのコミットIDを含む。

## initramfs*
初期RAMディスクの内容。実際のルートファイルシステムをマウントする前に一時的なルートファイルシステムをメモリにロードする。

デバイスツリーBLOBファイル(*.dtb)
デバイスツリーBLOBファイルは、Raspberry Piのさまざまなモデルのハードウェア定義が含まれる。このファイルは、検出された Raspberry Piのモデルに基づいて、起動時にカーネルを設定する。

カーネルファイル ( *.img)
Raspberry Pi モデルに対応するさまざまなカーネルイメージ ファイル。
表は略。(Raspberry pi 4はkernel8.img(64bitカーネル。))

参考:
https://github.com/raspberrypi/documentation/blob/develop/documentation/asciidoc/computers/configuration/boot_folder.adoc

補足(ChatGPT先生から)
1. start.elf と fixup.dat のペア<br>
    start*.elf と fixup*.dat はセットで機能。fixup*.dat は、start*.elf が動作する際に必要な調整や構成データを提供する。
    例えば、start_cd.elf を使用する場合は、fixup_cd.dat をペアとして同じディレクトリに配置する必要がある。
2. config.txt の柔軟性<br>
    config.txt は多くのカスタマイズが可能で、クロック速度調整、メモリ割り当て、デバッグオプションなどを設定できる。
    実験的な機能を有効にするために arm_64bit=1 や dtoverlay を設定することもあります。
3. cmdline.txt の用途<br>
    cmdline.txt に記述するカーネルパラメータはすべて1行で記述する必要があります。複数行にすると正しく動作しません。ここでミスするとOSが起動しなくなるため気をつけること。
    例えば、自作OSの場合、root=/dev/mmcblk0p2 や console=serial0,115200 を設定してブート時のデバッグを有効にするのが一般的。
4. initramfs の重要性<br>
    自作OSを開発する場合、initramfs を作成することで、最低限のデバッグツールや必要なモジュールを事前にロードしておくことが可能。これにより、問題の原因を特定しやすくなる。
5. デバイスツリーの役割<br>
    *.dtb ファイルは、ハードウェアをカーネルに認識させるための「ハードウェアマップ」のような役割を果たす。
    自作OSで独自のデバイスツリーを使用する場合、ベースとなるデバイスツリー（例: bcm2711-rpi-4-b.dtb）をカスタマイズすることで、ハードウェアの動作を変更できる。<br>

===========================================================================================================<br>
Raspberry Pi 5 に関するポイント:<br>
Raspberry Pi 5がstart*.elf を使用しない点については、これまでのブートプロセスが大きく変わったことを示すかも?<br>
UEFI（Unified Extensible Firmware Interface）が導入されている可能性があり、より標準的なPCライクなブートプロセスを採用しているそうだ。

ちなカーネルはkernel_2712.img。Pi5 に最適化された64ビットカーネル。
============================================================================================================<br>
超重要: 起動しないときに確認すること<br>
Raspberry Pi 5は前述した通り、start.elfを使用しないため、緑色のACTランプが4回点滅するならそこに問題あり?<br>
参考:
https://forums.raspberrypi.com/viewtopic.php?t=367964 (日本人が遭遇したそうでありがたい。(英語は翻訳バグるし))
