---
title: "Felsöka Azure virtuell nätverksgateway och anslutningar - Azure CLI 1.0 | Microsoft Docs"
description: "Den här sidan förklarar hur du använder Azure Nätverksbevakaren felsöka Azure CLI 1.0"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 2acbc47970acf0eb2aa1aea8535d7157bc73cbb6
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli-10"></a>Felsöka virtuella nätverksgateway och anslutningar med hjälp av Azure Network Watcher Azure CLI 1.0

> [!div class="op_single_selector"]
> - [Portal](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Nätverksbevakaren innehåller många funktioner relateras till att förstå nätverksresurserna i Azure. En av dessa funktioner är resurs felsökning. Felsökning av resursen kan anropas via portalen, PowerShell, CLI eller REST API. När den anropas, Nätverksbevakaren kontrollerar hälsotillståndet för en virtuell nätverksgateway eller en anslutning och returnerar resultatet.

Den här artikeln använder plattformsoberoende Azure CLI version 1.0, som är tillgänglig för Windows, Mac och Linux. 

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren.

En lista över stöds gateway typer besök [stöd för Gateway-typer](/network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Översikt

Felsökning av resursen ger möjlighet felsöka problem som uppstår med virtuella Nätverksgatewayer och anslutningar. När en begäran skickas till resursen felsökning, loggar som efterfrågas och kontrolleras. Resultaten returneras när kontroll har slutförts. Resursen felsökning förfrågningar är tidskrävande begäranden, vilket kan ta flera minuter att returnera ett resultat. Loggar från felsökning lagras i en behållare för ett lagringskonto som har angetts.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Hämta en Gateway för virtuell nätverksanslutning

I det här exemplet är resurs felsökning som kördes på en anslutning. Du kan också flytta den virtuella Nätverksgatewayen. Följande cmdlet visar vpn-anslutningar i en resursgrupp.

```azurecli
azure network vpn-connection list -g resourceGroupName
```

Du kan också köra kommandot för att se anslutningar i en prenumeration.

```azurecli
azure network vpn-connection list -s subscription
```

När du har namnet på anslutningen kan köra du det här kommandot för att hämta dess resurs-Id:

```azurecli
azure network vpn-connection show -g resourceGroupName -n connectionName
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Felsökning av resursen returnerar data om hälsotillståndet för resursen, sparas även loggar till ett lagringskonto som ska granskas. I det här steget ska vi skapa ett lagringskonto, om det finns ett befintligt lagringskonto kan du använda den.

1. Skapa lagringskontot

    ```azurecli
    azure storage account create -n storageAccountName -l location -g resourceGroupName
    ```

1. Hämta nycklar för lagringskonto

    ```azurecli
    azure storage account keys list storageAccountName -g resourcegroupName
    ```

1. Skapa behållaren

    ```azurecli
    azure storage container create --account-name storageAccountName -g resourcegroupName --account-key {storageAccountKey} --container logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Kör Nätverksbevakaren resurs felsökning

Felsökning av resurser med den `network watcher troubleshoot` cmdlet. Vi skickar cmdlet resursgrupp, namnet på Nätverksbevakaren Id för anslutningen, Id för storage-konto och sökvägen till blob att lagra felsökning resulterar i.

```azurecli
azure network watcher troubleshoot -g resourceGroupName -n networkWatcherName -t connectionId -i storageId -p storagePath
```

När du kör cmdleten, granskar Nätverksbevakaren resurs för att kontrollera hälsotillståndet. Den returnerar resultaten till shell och lagrar loggar av resultaten i storage-konto som har angetts.

## <a name="understanding-the-results"></a>Förstå resultaten

Åtgärden texten innehåller allmänna råd om hur du löser problemet. Om en åtgärd kan vidtas för utfärdande, finns en länk med mer information. I fall där det finns inga ytterligare riktlinjer, svaret innehåller URL: en för att öppna ett supportärende.  Mer information om egenskaperna för svaret och vad som ingår finns [nätverk Watcher Felsökning: översikt](network-watcher-troubleshoot-overview.md)

Anvisningar för att hämta filer från azure storage-konton, referera till [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som kan användas är Lagringsutforskaren. Mer information om Lagringsutforskaren hittar du här på följande länk: [Lagringsutforskaren](http://storageexplorer.com/)

## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats som stoppa VPN-anslutning, se [hantera Nätverkssäkerhetsgrupper](../virtual-network/virtual-network-manage-nsg-arm-portal.md) att spåra de grupp och säkerhet Nätverkssäkerhetsregler som kan vara i fråga.
