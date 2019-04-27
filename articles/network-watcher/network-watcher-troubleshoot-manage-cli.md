---
title: Felsöka Azure Vnet-Gateway och anslutningar – Azure CLI | Microsoft Docs
description: Den här sidan förklaras hur du använder Azure Network Watcher felsöka Azure CLI
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: 8910b4963c7f40b5b6ecdde06c7c7a0c9b86e97a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680343"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Felsöka virtuella nätverkets Gateway och anslutningar som använder Azure Network Watcher Azure CLI

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST-API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher innehåller många funktioner som den relaterar till att förstå nätverksresurserna i Azure. En av dessa funktioner är resurs felsökning. Felsökning av resursen kan anropas via portal, PowerShell, CLI eller REST API. När den anropas, Network Watcher kontrollerar hälsotillståndet för en virtuell nätverksgateway eller en anslutning och returnerar resultatet.

Om du vill utföra stegen i den här artikeln, måste du [installera Azures kommandoradsgränssnitt för Mac, Linux och Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Network Watcher](network-watcher-create.md) att skapa en Network Watcher.

En lista över stöds gateway typer besök [stöds gatewaytyper](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Översikt

Felsökning av resurs gör möjligheten felsöka problem som kan uppstå med Virtual Network-gatewayer och anslutningar. När en begäran skickas till felsöknings-resurs, som loggar ska efterfrågas och kontrolleras. När kontrollen är klar visas returneras resultaten. Resursen felsökning begärandena långvariga begäranden, vilket kan ta flera minuter att beräkna ett resultat. Loggar från felsökning lagras i en behållare på ett lagringskonto som har angetts.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Hämta en Gateway för virtuell nätverksanslutning

I det här exemplet som felsökning av resurs kördes på en anslutning. Du kan även skicka det en virtuell nätverksgateway. Följande cmdlet visar vpn-anslutningar i en resursgrupp.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

När du har namnet på anslutningen kan köra du det här kommandot för att få dess resurs-Id:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Felsökning av resurs returnerar data om hälsotillståndet för resursen, sparas även loggar till ett lagringskonto som ska granskas. I det här steget ska vi skapa ett lagringskonto, om det finns ett befintligt lagringskonto kan du använda den.

1. Skapa lagringskontot

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. Hämta kontonycklar för storage

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. Skapa containern

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Kör felsökning av Network Watcher-resurs

Felsökning av resurser med den `az network watcher troubleshooting` cmdlet. Vi skickar cmdleten resursgruppen och namnet på Network Watcher, Id för anslutningen, Id för storage-konto, och sökvägen till blobben som ska lagra Felsök resulterar i.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

När du kör cmdleten, granskar Network Watcher resurs för att kontrollera hälsotillståndet. Den returnerar resultaten till gränssnittet och lagrar loggar resultat i det angivna lagringskontot.

## <a name="understanding-the-results"></a>Förstå resultaten

Åtgärd-texten innehåller allmänna råd om hur du löser problemet. Om en åtgärd kan utföras på problemet, länk en med ytterligare vägledning. I fall där det finns inga ytterligare vägledning, svaret anger webbadressen för att öppna ett supportärende.  Mer information om egenskaperna för svaret och vad som ingår finns [felsökning av Network Watcher-översikt](network-watcher-troubleshoot-overview.md)

Instruktioner om att ladda ned filer från azure storage-konton finns i [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som kan användas är Storage Explorer. Mer information om Storage Explorer finns här på följande länk: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats som stoppa VPN-anslutning, se [hantera Nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md) att spåra de och Nätverkssäkerhetsregler som kan vara i fråga.
