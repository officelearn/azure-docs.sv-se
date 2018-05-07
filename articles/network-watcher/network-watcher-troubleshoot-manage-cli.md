---
title: Felsöka Azure virtuell nätverksgateway och anslutningar - Azure CLI 2.0 | Microsoft Docs
description: Den här sidan förklarar hur du använder Azure Nätverksbevakaren felsöka Azure CLI 2.0
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 81a31365a222cde8e57258ff47d8a64af53c05c2
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli-20"></a>Felsöka virtuella nätverksgateway och anslutningar med hjälp av Azure Network Watcher Azure CLI 2.0

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Nätverksbevakaren innehåller många funktioner relateras till att förstå nätverksresurserna i Azure. En av dessa funktioner är resurs felsökning. Felsökning av resursen kan anropas via portalen, PowerShell, CLI eller REST API. När den anropas, Nätverksbevakaren kontrollerar hälsotillståndet för en virtuell nätverksgateway eller en anslutning och returnerar resultatet.

Den här artikeln använder våra nästa generations CLI för hantering av resursdistributionsmodell, Azure CLI 2.0, som är tillgänglig för Windows, Mac och Linux.

Om du vill utföra stegen i den här artikeln, måste du [installera Azure-kommandoradsgränssnittet för Mac, Linux och Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren.

En lista över stöds gateway typer besök [stöd för Gateway-typer](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Översikt

Felsökning av resursen ger möjlighet felsöka problem som uppstår med virtuella Nätverksgatewayer och anslutningar. När en begäran skickas till resursen felsökning, loggar som efterfrågas och kontrolleras. Resultaten returneras när kontroll har slutförts. Resursen felsökning förfrågningar är tidskrävande begäranden, vilket kan ta flera minuter att returnera ett resultat. Loggar från felsökning lagras i en behållare för ett lagringskonto som har angetts.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Hämta en Gateway för virtuell nätverksanslutning

I det här exemplet är resurs felsökning som kördes på en anslutning. Du kan också flytta den virtuella Nätverksgatewayen. Följande cmdlet visar vpn-anslutningar i en resursgrupp.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

När du har namnet på anslutningen kan köra du det här kommandot för att hämta dess resurs-Id:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Felsökning av resursen returnerar data om hälsotillståndet för resursen, sparas även loggar till ett lagringskonto som ska granskas. I det här steget ska vi skapa ett lagringskonto, om det finns ett befintligt lagringskonto kan du använda den.

1. Skapa lagringskontot

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. Hämta nycklar för lagringskonto

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. Skapa behållaren

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Kör Nätverksbevakaren resurs felsökning

Felsökning av resurser med den `az network watcher troubleshooting` cmdlet. Vi skickar cmdlet resursgrupp, namnet på Nätverksbevakaren Id för anslutningen, Id för storage-konto och sökvägen till blob att lagra felsökning resulterar i.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

När du kör cmdleten, granskar Nätverksbevakaren resurs för att kontrollera hälsotillståndet. Den returnerar resultaten till shell och lagrar loggar av resultaten i storage-konto som har angetts.

## <a name="understanding-the-results"></a>Förstå resultaten

Åtgärden texten innehåller allmänna råd om hur du löser problemet. Om en åtgärd kan vidtas för utfärdande, finns en länk med mer information. I fall där det finns inga ytterligare riktlinjer, svaret innehåller URL: en för att öppna ett supportärende.  Mer information om egenskaperna för svaret och vad som ingår finns [nätverk Watcher Felsökning: översikt](network-watcher-troubleshoot-overview.md)

Anvisningar för att hämta filer från azure storage-konton, referera till [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som kan användas är Lagringsutforskaren. Mer information om Lagringsutforskaren hittar du här på följande länk: [Lagringsutforskaren](http://storageexplorer.com/)

## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats som stoppa VPN-anslutning, se [hantera Nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md) att spåra de grupp och säkerhet Nätverkssäkerhetsregler som kan vara i fråga.
