# docker-python-debian

Python用Dockerコンテナ

## 実行Dockerコンテナ環境

* OS : Debian
* コンテナ
    * python2
        * Pythonバージョン: .envのPYTHON2_VERSIONで指定
        * ソースコード格納先： ./python2
    * python3
        * Pythonバージョン: .envのPYTHON3_VERSIONで指定
        * ソースコード格納先： ./python3
    * python-project
        * Pythonバージョン: Dockerfileにて指定
        * Pythonライブラリ: requirements.txtで指定
        * イメージ名: .envのPROJECT_NAMEで指定
        * ソースコード格納先： ./PROJECT_NAME

## 開発Dockerコンテナ環境

* OS : Debian
* コンテナ
    * python-project-dev
        * 継承元: python-project
        * IDE: vscode(接続元サーバの$HOME/.vscode_serverをマウント)
        * SSH接続ポート: .envのLOCAL_SSH_PORTで指定
        * ホスト名: ${PROJECT_NAME}-dev

## コンテナ使い方

### リポジトリを取得

1. sample_codesのリポジトリから本ディレクトリだけをクローンする。
```bash
$ mkdir docker-samples
$ cd docker-samples
$ git init
$ git config core.sparsecheckout true
$ git remote add origin git@github.com:laikuaut/sample_codes.git
$ echo "docker-samples/docker-python-debian/" > .git/info/sparse-checkout
$ git pull origin develop
$ cd docker-samples/docker-python-debian/
```
2. サービス一覧を確認する
```
$ docker-compose config --services
python2
python3
python-project
python-project-dev
```

### Dockerサービス

#### python2サービス

pipでのライブラリ不要なら`python2/`配下のmain.pyファイルを編集することでスクリプト実行が可能。

1. `.env`の`PYTHON2_VERSION`にpython2のバージョンを指定する。
```
PYTHON2_VERSION=[Pythonバージョン]
```
2. `python2/main.py`にプログラムを書く。
3. サービスを実行する。
```bash
$ docker-compose run --rm python2
hello world
```

#### python3サービス

pipでのライブラリ不要なら`python3/`配下のmain.pyファイルを編集することでスクリプト実行が可能。

1. `.env`の`PYTHON3_VERSION`にpython3のバージョンを指定する。
```
PYTHON3_VERSION=[Pythonバージョン]
```
2. `python3/main.py`にプログラムを書く。
3. サービスを実行する。
```bash
$ docker-compose run --rm python3
hello world
```

#### python-projectサービス

pipでのライブラリを必要とする場合に、本プロジェクトを利用する。

1. `.env`の`PROJECT_NAME`にプロジェクト名を指定する。
```
PROJECT_NAME=python-app
```
2. プロジェクト名に合わせてフォルダ名を変更
```
$ mv python-app [プロジェクト名]
```
3. `Dockerfile`を開き、ベースコンテナ名にて利用するPythonバージョンを指定する。
```Dockerfile
FROM python:[Pythonバージョン]
```
4. `[プロジェクト名]/requirements.txt`に必要なライブラリを記載
5. `[プロジェクト名]/main.py`にプログラムを書く。
vscodeで開発する場合は、python-project-devサービスを利用。
6. Dockerイメージをビルドする
```
$ docker-compose build
```
7. サービスを実行する。
```
$ docker-compose run --rm python-project
```

#### python-project-devサービス

python-projectの開発環境として本プロジェクトを利用する。
python-projectサービスにて必要な手順は実施済みの前提とする。

1. `.env`に以下を設定
```
# サーバで利用している作業ユーザのIDを指定。`id -u`で確認
USER_ID=1000
# サーバで利用している作業ユグループのIDを指定。`id -g`で確認
GROUP_ID=1000
# コンテナ内で利用するユーザ名を指定。（任意）
USER_NAME=dev
# コンテナ内にSSH接続する際のポート番号を指定。（任意）
LOCAL_SSH_PORT=10022
```
2. Dockerイメージをビルドする
```
$ docker-compose build
```
3. Dockerコンテナを起動する
```
$ docker-compose up -d python-project-dev
```
4. SSH接続を確認。
```
$ ssh -o 'StrictHostKeyChecking no' [ユーザ名]@localhost -p [接続ポート]
```
5. vscodeで接続を確認。
.ssh/configに以下の用に追加。
※ SSHトンネルの設定はうまくいかなかったので、ポート転送にて代用。
```
Host aws-dev-1
    HostName [サーバホスト名]
    Port 22
    User [サーバユーザ名]
    IdentityFile "[秘密鍵パス]"
    LocalForward 10022 localhost:10022
Host aws-dev-1-container
    HostName localhost
    Port 10022
    User dev
    StrictHostKeyChecking no
```
