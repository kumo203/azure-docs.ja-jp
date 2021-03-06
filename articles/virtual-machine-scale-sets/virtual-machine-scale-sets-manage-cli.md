---
title: "Azure CLI 2.0 を使用した仮想マシン スケール セットの管理 | Microsoft Docs"
description: "仮想マシン スケール セットを管理するための一般的な Azure CLI 2.0 コマンド (インスタンスの起動と停止、スケール セット容量の変更の方法など)。"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 2348db8f19391292f79608092a3c2482216493c6
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2017
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Azure CLI 2.0 を使用した仮想マシン スケール セットの管理
仮想マシン スケール セットのライフサイクルを通じて、1 つ以上の管理タスクを実行する必要がある場合があります。 さらに、各種ライフサイクルのタスクを自動化するスクリプトを作成するほうが便利な場合もあります。 この記事では、これらのタスクを実行するための一般的な Azure CLI 2.0 コマンドの一部について説明します。

これらの管理タスクを実行するには、最新の Azure CLI 2.0 ビルドが必要です。 最新バージョンをインストールして使用する方法については、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 仮想マシン スケール セットを作成する必要がある場合は、[Azure Portal でスケール セットを作成](virtual-machine-scale-sets-portal-create.md)できます。


## <a name="view-information-about-a-scale-set"></a>スケール セットに関する情報を表示する
スケール セットに関する全体的な情報を表示するには、[az vmss show](/cli/azure/vmss#show) を使用します。 次の例を実行すると、*myResourceGroup* リソース グループ内の *myScaleSet* という名前のスケール セットに関する情報を取得できます。 実際の名前を次のように入力してください。

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>スケール セットの VM を表示する
スケール セット内の VM インスタンスの一覧を表示するには、[az vmss list-instances](/cli/azure/vmss#list-instances) を使用します。 次の例を実行すると、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内のすべての VM インスタンスを一覧表示できます。 これらの名前に実際の値を入力してください。

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

特定の VM インスタンスに関する追加情報を表示するには、`--instance-id` パラメーターを [az vmss get-instance-view](/cli/azure/vmss#get-instance-view) に追加し、表示するインスタンスを指定します。 次の例を実行すると、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内の VM インスタンス *0* に関する情報を表示できます。 実際の名前を次のように入力してください。

```azurecli
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>VM の接続情報を一覧表示する
スケール セット内の VM に接続するには、割り当てられたパブリック IP アドレスとポート番号に SSH または RDP を使用して接続します。 既定では、リモート接続トラフィックを各 VM に転送する Azure ロード バランサーに NAT (Network Address Translation) 規則が追加されます。 スケール セット内の VM インスタンスに接続するためのアドレスとポートを一覧表示するには、[az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info) を使用します。 次の例を実行すると、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内の VM インスタンスの接続情報を一覧表示できます。 これらの名前に実際の値を入力してください。

```azurecli
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>スケール セットの容量を変更する
前述のコマンドでは、スケール セットと VM インスタンスに関する情報が表示されました。 スケール セット内のインスタンスの数を増減するには、容量を変更します。 スケール セットでは、必要な数の VM を作成または削除した後、アプリケーション トラフィックを受信するように VM を構成します。

現時点でスケール セットに存在するインスタンスの数を確認するには、[az vmss show](/cli/azure/vmss#show) と *sku.capacity* に対するクエリを使います。

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

[az vmss scale](/cli/azure/vmss#scale) を使って、スケール セット内の仮想マシンの数を手動で増減させることができます。 次の例では、スケール セット内の VM の数を *5* に設定します。

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

スケール セットの容量を更新するには数分かかります。 スケール セットの容量を減らした場合、インスタンス ID が最も大きい VM が最初に削除されます。


## <a name="stop-and-start-vms-in-a-scale-set"></a>スケール セット内の VM を停止および起動する
スケール セット内の 1 つ以上の VM を停止するには、[az vmss stop](/cli/azure/vmss/stop) を使用します。 `--instance-ids` パラメーターには、停止する VM を 1 つ以上指定することができます。 インスタンス ID を指定しない場合は、スケール セット内のすべての VM が停止されます。 複数の VM を停止するには、それぞれのインスタンス ID をスペースで区切ります。

次の例を実行すると、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内のインスタンス *0* が停止されます。 実際の値を次のように入力してください。

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

停止した VM は割り当てられたままであり、引き続きコンピューティングの料金が発生します。 VM の割り当てを解除してストレージの料金のみが課金されるようにするには、[az vmss deallocate](/cli/azure/vmss#deallocate) を使用します。 複数の VM の割り当てを解除するには、それぞれのインスタンス ID をスペースで区切ります。 次の例を実行すると、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内のインスタンス *0* が停止され、割り当てが解除されます。 実際の値を次のように入力してください。

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>スケール セット内の VM を起動する
スケール セット内の 1 つ以上の VM を起動するには、[az vmss start](/cli/azure/vmss#start) を使用します。 `--instance-ids` パラメーターには、起動する VM を 1 つ以上指定することができます。 インスタンス ID を指定しない場合は、スケール セット内のすべての VM が起動されます。 複数の VM を起動するには、それぞれのインスタンス ID をスペースで区切ります。

次の例を実行すると、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内のインスタンス *0* が起動されます。 実際の値を次のように入力してください。

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>スケール セット内の VM を再起動する
スケール セット内の 1 つ以上の VM を再起動するには、[az vmss restart](/cli/azure/vmss#restart) を使用します。 `--instance-ids` パラメーターには、再起動する VM を 1 つ以上指定することができます。 インスタンス ID を指定しない場合は、スケール セット内のすべての VM が再起動されます。 複数の VM を再起動するには、それぞれのインスタンス ID をスペースで区切ります。

次の例を実行すると、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内のインスタンス *0* が再起動されます。 実際の値を次のように入力してください。

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>スケール セットから VM を削除する
スケール セット内の 1 つ以上の VM を削除するには、[az vmss delete-instances](/cli/azure/vmss#delete-instances) を使用します。 `--instance-ids`` パラメーターには、削除する VM を 1 つ以上指定することができます。 インスタンス ID に * を指定した場合は、スケール セット内のすべての VM が削除されます。 複数の VM を削除するには、それぞれのインスタンス ID をスペースで区切ります。

次の例を実行すると、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内のインスタンス *0* が削除されます。 実際の値を次のように入力してください。

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>次のステップ
スケール セットに対する他の一般的なタスクとして、[アプリケーションのデプロイ](virtual-machine-scale-sets-deploy-app.md)や [VM インスタンスのアップグレード](virtual-machine-scale-sets-upgrade-scale-set.md)があります。 Azure CLI を使用して[自動スケール ルールを構成する](virtual-machine-scale-sets-autoscale-overview.md)こともできます。