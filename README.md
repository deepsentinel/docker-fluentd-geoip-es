Docker Image for [fluent-plugin-geoip](https://github.com/y-ken/fluent-plugin-geoip)
====

これは何？
----

iptables が拒否したログから [kibana](https://github.com/elasticsearch/kibana) でアクセス元MAP を作るために必要な fluentd と plugin が入った docker image です。

元ネタは [fluentdとKibanaでSSHアクセス元マップ - Qiita](http://qiita.com/hiconyan/items/e847793a291760a7ac1d) です。

使い方
----

1. イメージを作成 または pull
2. 設定ファイルを作成
3. fluentd の実行
4. Kibana の設定

#### 注意事項
- ElasticSearch は設定については記述しません
- iptables の設定についても特に記述しません（LOG ターゲットを使って出力されていることを想定しています）

### 1. イメージの作成

**build する場合**

```console
$ git clone https://github.com/yokogawa-k/docker-fluentd-geoip-es.git
$ cd docker-fluentd-geoip-es
$ docker build -t yokogawa/fluentd-geoip-es .
```

**docker pull する場合**

```console
$ docker pull yokogawa/fluentd-geoip-es
```

### 2. 設定ファイルを作成

`sample.conf` を参考に自分の環境に合わせて設定してください。

- 読み込むファイルをコンテナにどのように見せるか決めてください（サンプルではホストの `/var/log` をコンテナの `/rootfs/var/log` に ro でマウントしています）
- ポジションファイルやバッファファイルはホストのどこに保存するか決めてください（サンプルでは `${PWD}/var/pos/` と `${PWD}/var/buffer/` 以下に保存します）

### 3. fluentd の実行

```console
$ docker run -d -v /var/log:/rootfs/var/log:ro -v ${PWD}:/work --name="fluentd-geip-es" yokogawa/fluentd-geoip-es -c sample.conf
```

**添付の `run` ファイルを利用する場合**

```consle
$ ./run -c sample.conf
```

### 4. Kibana の設定

`MAP` Type の Panel を追加し、`country` を target field にしてください。

おまけ
----

### 元ネタと違うところ

GeoIP のデータベースはイメージ作成時に最新のものを取ってきてイメージに含めています。

### GeoIP のデータベースファイルに関して

GeoIP のデータベースは一ヶ月に一回の頻度で更新されているようですが、このイメージ内のデータベースは更新されていません。これはどうにかして常に最新のデータベースを含む形にできればと考えています。

