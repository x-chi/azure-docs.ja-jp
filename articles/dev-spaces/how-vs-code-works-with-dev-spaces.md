---
title: Visual Studio Code と Azure Dev Spaces が連動するしくみ
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Visual Studio Code と Azure Dev Spaces による、Kubernetes アプリケーションのデバッグと迅速な反復処理の支援について説明します。
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: b6ab1330399ab2eb7afc9be61a7767a22ca82320
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942483"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Visual Studio Code と Azure Dev Spaces が連動するしくみ

Visual Studio Code と [Azure Dev Spaces 拡張機能][azds-extension]を使用し、Azure Dev Spaces を利用するサービスを準備し、実行し、デバッグできます。 Visual Studio Code と Azure Dev Spaces 拡張機能でできること:

* AKS でサービスを実行し、デバッグするためのアセットを生成する
* Java、Node.js、.NET Core サービスを開発スペースで実行する
* 開発スペースで実行している Java、Node.js、.NET Core サービスを直接デバッグする

## <a name="generate-assets"></a>アセットを生成する

Visual Studio Code と Azure Dev Spaces 拡張機能で生成されるプロジェクト用のアセット:

* Maven を使用する Java アプリケーション、Node.js アプリケーション、.NET Core アプリケーション用の Dockerfile
* ほとんどすべての言語の Helm グラフと Dockerfile
* プロジェクトの [Azure Dev Spaces 構成ファイル][azds-yaml]である `azds.yaml` ファイル
* Maven を使用する Java アプリケーション、Node.js アプリケーション、.NET Core アプリケーション用のプロジェクトの Visual Studio Code 起動構成が含まれる `.vscode` フォルダー

Dockerfile、Helm グラフ、`azds.yaml` ファイルは `azds prep` の実行時に生成されるものと同じアセットです。 これらのファイルを Visual Studio コードの外で利用し、AKS で実行することもできます。たとえば、`azds up` を実行します。 `.vscode` フォルダーは、Visual Studio Code から AKS のプロジェクトを実行する目的でのみ、Visual Studio コードによって使用されます。

## <a name="run-your-service-in-aks"></a>AKS でサービスを実行する

プロジェクト用のアセットを生成したら、Visual Studio Code から、既存の開発スペースで Java、Node.js、.NET Core サービスを実行できます。 Visual Studio Code の *[デバッグ]* ページで、`.vscode` ディレクトリから起動構成を呼び出し、プロジェクトを実行できます。

AKS クラスターを作成し、Visual Studio Code の外でクラスターの Azure Dev Spaces を有効にする必要があります。 たとえば、Azure CLI または Azure portal を利用してこの設定を行うことができます。 `azds prep` を実行することで生成されたアセットなど、Visual Studio Code の外で作成された既存の Dockerfile、Helm グラフ、`azds.yaml` ファイルを再利用できます。 Visual Studio Code の外で生成されたアセットを再利用する場合も、`.vscode` ディレクトリを用意する必要があります。 この `.vscode` ディレクトリは Visual Studio コードと Azure Dev Spaces 拡張機能で再生成できます。既存のアセットを上書きすることはありません。

.NET Core プロジェクトの場合、Visual Studio Code から .NET サービスを実行するには、[C# 拡張機能][csharp-extension]をインストールする必要があります。 Maven を使用する Java プロジェクトの場合も、Visual Studio Code から Java サービスを実行するには、[Java Debugger for Azure Dev Spaces 拡張機能][java-extension]をインストールし、[Maven をインストールして構成する][maven]必要があります。

## <a name="debug-your-service-in-aks"></a>AKS でサービスをデバッグする

プロジェクトを起動したら、Visual Studio Code から直接、開発スペースで実行している Java、Node.js、.NET Core サービスをデバッグできます。 `.vscode` ディレクトリの起動構成からは、開発スペースでデバッグが有効になっているサービスの実行に関して、付加的なデバッグ情報が与えられます。 Visual Studio Code は、開発スペースで実行中のコンテナーのデバッグ プロセスにも付随し、ブレーク ポイントの設定、変数の調査、その他のデバッグ操作を可能にします。


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Visual Studio Code を Azure Dev Spaces と共に使用する

Visual Studio Code と Azure Dev Spaces 拡張機能の Azure Dev Spaces との連動は次のクイックスタートで確認できます。

* [Visual Studio Code と Java を使用した迅速な反復処理とデバッグ][quickstart-java]
* [Visual Studio Code と .NET を使用した迅速な反復処理とデバッグ][quickstart-netcore]
* [Visual Studio Code と Node.js を使用した迅速な反復処理とデバッグ][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
