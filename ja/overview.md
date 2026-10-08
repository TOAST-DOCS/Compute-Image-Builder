<!-- machine_translated: true -->

<!-- pre-align:aligned sig=4fb95f840e9f -->

<a id="compute-image-builder-overview"></a>
## Compute > Image Builder > 概要 { #compute-image-builder-overview }

イメージビルダー(Image Builder)はNHN Cloud{% if "gov" in build_flags %} (公共機関向け){% endif %}が提供するOSイメージまたは個人イメージをベースにユーザーのニーズに合った個人イメージを作成するサービスです。

<a id="service-features"></a>
## サービスの特徴 { #service-features }
* ベースイメージとアプリケーションインストールコンポーネント、ユーザースクリプトを組み合わせて、簡単に個人イメージを作成できます。
* ユーザーがインスタンスからイメージを作成するプロセスを自動化し、作業中に発生する可能性のあるエラーを最小限に抑えることができます。
* NHN Cloud{% if "gov" in build_flags %} (公共機関向け){% endif %}が提供するOSイメージにはデフォルトのセキュリティ設定が適用されているため、セキュリティの脅威から安全な個人イメージを作成できます。
* 継続的に管理されるさまざまなアプリケーションインストールコンポーネントを使用できます。

{% if "gov" not in build_flags %}> [参考]
> イメージビルダーサービスは、2023年9月現在、韓国(パンギョ)、韓国(ピョンチョン)リージョンでのみ使用できます。

{% endif %}<a id="image-template"></a>

<a id="image-template"></a>
## イメージテンプレート { #image-template }
イメージテンプレートはイメージを作成するための情報を記録した文書です。アプリケーションインストールコンポーネントとユーザースクリプトを記録しておき、定期的にアップデートされるOSイメージのみ変更して個人イメージを最新の状態に維持できます。

<a id="build-task"></a>
## ビルド作業 { #build-task }
イメージテンプレートごとにビルド作業を管理します。進行中または完了したビルド作業の詳細ログを確認し、作成された個人イメージリストを確認できます。

<a id="private-image"></a>
## 個人イメージ { #private-image }
イメージビルダーを利用して作成した個人イメージはイメージサービス(**Compute > Image**)で管理できます。詳細な内容は[イメージサービスユーザーガイド](/Compute/Image/ja/overview/)を参照してください。