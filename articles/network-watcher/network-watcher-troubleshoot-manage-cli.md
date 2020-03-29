---
title: Felsöka Azure VNET Gateway och anslutningar – Azure CLI
titleSuffix: Azure Network Watcher
description: På den här sidan beskrivs hur du använder felsöka Azure Network Watcher i Azure CLI
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: dc0aa8e6099a7ec017aead2fe0f16e9712e17936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840731"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Felsöka virtual network gateway och anslutningar med Azure Network Watcher Azure CLI

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Powershell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher innehåller många funktioner när det gäller att förstå dina nätverksresurser i Azure. En av dessa funktioner är felsökning av resurser. Resursfelsökning kan anropas via portalen, PowerShell, CLI eller REST API. När network watcher anropas inspekterar den hälsotillståndet för en virtuell nätverksgateway eller en anslutning och returnerar dess resultat.

Om du vill utföra stegen i den här artikeln måste du [installera Azure-kommandoradsgränssnittet för Mac, Linux och Windows (CLI).](/cli/azure/install-azure-cli)

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [Skapa en nätverksbevakare](network-watcher-create.md) för att skapa en Network Watcher.

En lista över gatewaytyper som stöds besöker [gatewaytyper som stöds](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Översikt

Felsökning av resurser ger möjlighet att felsöka problem som uppstår med virtuella nätverksgateways och anslutningar. När en begäran görs till resursfelsökning efterfrågas och inspekteras loggar. När inspektionen är klar returneras resultaten. Begäran om resursfelsökning är tidskrävande begäranden, vilket kan ta flera minuter att returnera ett resultat. Loggarna från felsökning lagras i en behållare på ett lagringskonto som har angetts.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Hämta en anslutning till virtual network gateway

I det här exemplet körs resursfelsökning på en anslutning. Du kan också skicka den till en virtuell nätverksgateway. Följande cmdlet listar vpn-anslutningarna i en resursgrupp.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

När du har namnet på anslutningen kan du köra det här kommandot för att få dess resurs-ID:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Resursfelsökning returnerar data om resursens hälsotillstånd, det sparar också loggar till ett lagringskonto som ska granskas. I det här steget skapar vi ett lagringskonto, om det finns ett befintligt lagringskonto kan du använda det.

1. Skapa lagringskontot

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. Hämta lagringskontonycklarna

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. Skapa containern

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Kör felsökning av nätverksbevakningsresurs

Du felsöker `az network watcher troubleshooting` resurser med cmdleten. Vi skickar cmdlet resursgruppen, namnet på Network Watcher, ID för anslutningen, ID för lagringskontot och sökvägen till bloben för att lagra felsökningen resulterar i.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

När du har kört cmdleten granskar Network Watcher resursen för att verifiera hälsotillståndet. Resultaten returneras till skalet och lagrar loggar av resultaten i det angivna lagringskontot.

## <a name="understanding-the-results"></a>Förstå resultaten

Åtgärdstexten ger allmän vägledning om hur du löser problemet. Om en åtgärd kan vidtas för problemet får en länk ytterligare vägledning. Om det inte finns någon ytterligare vägledning, ger svaret url för att öppna ett supportärende.  Mer information om svarets egenskaper och vad som ingår finns i [översikt över felsökning](network-watcher-troubleshoot-overview.md) av Network Watcher

Instruktioner om hur du hämtar filer från azure storage-konton finns i [Komma igång med Azure Blob-lagring med .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som kan användas är Storage Explorer. Mer information om Storage Explorer finns här på följande länk: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats som stoppar VPN-anslutning läser du [Hantera nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md) för att spåra nätverkssäkerhetsgruppen och säkerhetsregler som kan vara i fråga.
