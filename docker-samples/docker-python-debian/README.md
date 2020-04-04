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

TODO: 使い方を明記

### 準備

### 開発コンテナ

### 実行コンテナ

## 設定項目

### .env
