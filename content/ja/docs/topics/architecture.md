---
title: "Helm アーキテクチャ"
description: "Helm アーキテクチャの概要を説明します。"
aliases: ["/docs/architecture/"]
weight: 8
---

# Kubernetes Helm アーキテクチャ

このドキュメントでは、Helm アーキテクチャの概要について説明します。

## Helm の目的

Helm は、_チャート_ と呼ばれる Kubernetes パッケージを管理するためのツールです。
Helm は次のことができます。

- 最初から新しいチャートを作成する
- チャートをチャートアーカイブ (tgz) ファイルにパッケージ化する
- チャートが保存されているチャートリポジトリを操作する
- チャートを既存の Kubernetes クラスターにインストールおよびアンインストールする
- Helm でインストールされたチャートのリリースサイクルを管理する

Helm には、3つの重要な概念があります。

1. _チャート_ は、Kubernetes アプリケーションのインスタンスを作成するために必要な
情報のバンドルです
2. _コンフィグ_ には、リリース可能なオブジェクトを作成するためにパッケージ化された
   チャートにマージできる構成情報が含まれています
3. _リリース_ は、特定の _コンフィグ_ と組み合わせた _チャート_ の
   実行中のインスタンスです

## コンポーネント

Helm は、2つの異なる部分に実装される実行可能ファイルです。

**Helm クライアント** は、エンドユーザー向けのコマンドラインクライアントです。
クライアントは次のことを担当します。

- ローカルチャート開発
- リポジトリの管理
- リリースの管理
- Helm ライブラリとのインターフェース
  - インストールするチャートを送信する
  - 既存のリリースのアップグレードまたはアンインストールの要求

**Helm ライブラリ** は、すべての Helm 操作を実行するためのロジックを提供します。
Kubernetes API サーバーとインターフェースし、次の機能を提供します。

- チャートと構成を組み合わせてリリースを構築する
- チャートを Kubernetes にインストールし、後続のリリースオブジェクトを提供する
- Kubernetes との対話によるチャートのアップグレードとアンインストール

スタンドアロンの Helm ライブラリは Helm ロジックをカプセル化しているため、
さまざまなクライアントが活用できます。

## 実装

Helm クライアントとライブラリは、Go プログラミング言語で記述されています。

ライブラリは、Kubernetes クライアントライブラリを使用して Kubernetes と通信します。
現在、そのライブラリは REST + JSON を使用しています。
Kubernetes 内にあるシークレットに情報を保存します。独自のデータベースは必要ありません。

構成ファイルは、可能な場合は YAML で作成されます。