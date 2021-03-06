---
title: ストレージを自動拡張する - Azure portal - Azure Database for PostgreSQL - 単一サーバー
description: この記事では、Azure Database for PostgreSQL - 単一サーバーで Azure portal を使用して、ストレージを自動拡張するように構成する方法について説明します
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5e4f9d68d02edf456394d4ce10b7b6af5f8643d9
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769236"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Azure portal を使用して Azure Database for PostgreSQL でストレージを自動拡張する - 単一サーバー
この記事では、ワークロードに影響を与えることなく拡張されるように Azure Database for PostgreSQL サーバーのストレージを構成する方法について説明します。

サーバーが、割り当てられたストレージの上限に達すると、そのサーバーは読み取り専用としてマークされます。 ただし、ストレージの自動拡張を有効にした場合、サーバーのストレージは、増大するデータに合わせて拡張されます。 プロビジョニングされたストレージが 100 GB 未満のサーバーでは、空きストレージがプロビジョニングされたストレージの 1 GB または 10% のどちらか大きい方を下回ると、プロビジョニングされたストレージ サイズが 5 GB 単位ですぐに拡張されます。 プロビジョニングされたストレージが 100 GB を超えるサーバーの場合、空きストレージ領域がプロビジョニングされたストレージ サイズの 5% を下回ると、プロビジョニングされたストレージ サイズが 5% 単位で増加します。 [こちら](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage)で指定されているストレージの上限が適用されます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>ストレージの自動拡張を有効にする 

PostgreSQL サーバー ストレージの自動拡張を設定するには、これらの手順に従います。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for PostgreSQL サーバーを選択します。

2. PostgreSQL サーバーのページで **[設定]** にある **[価格レベル]** をクリックして、価格レベルのページを開きます。

3. **[自動拡張]** セクションで、 **[はい]** を選択してストレージの自動拡張を有効にします。

    ![Azure Database for PostgreSQL - 価格レベルの設定 - 自動拡張](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. **[OK]** をクリックして変更を保存します。

5. 通知により、自動拡張の有効化に成功したことが確認できます。

    ![Azure Database for PostgreSQL - 自動拡張の成功](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>次の手順

[メトリックに関するアラートを作成する方法](howto-alert-on-metric.md)について学習します。
