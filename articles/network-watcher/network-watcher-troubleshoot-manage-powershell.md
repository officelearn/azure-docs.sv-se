---
title: Felsöka Azure VNet-gateway och anslutningar – Azure PowerShell
titleSuffix: Azure Network Watcher
description: På den här sidan beskrivs hur du använder felsöka PowerShell-cmdlet i Azure Network Watcher
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
ms.openlocfilehash: 0723ddc9b0e2f15d5c8e51c96d51f58f1313493a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673658"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Felsöka virtual network gateway och anslutningar med Azure Network Watcher PowerShell

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Powershell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher innehåller många funktioner när det gäller att förstå dina nätverksresurser i Azure. En av dessa funktioner är felsökning av resurser. Resursfelsökning kan anropas via portalen, PowerShell, CLI eller REST API. När network watcher anropas inspekterar den hälsotillståndet för en virtuell nätverksgateway eller en anslutning och returnerar dess resultat.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [Skapa en nätverksbevakare](network-watcher-create.md) för att skapa en Network Watcher.

En lista över gatewaytyper som stöds besöker [gatewaytyper som stöds](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Översikt

Felsökning av resurser ger möjlighet att felsöka problem som uppstår med virtuella nätverksgateways och anslutningar. När en begäran görs till resursfelsökning efterfrågas och inspekteras loggar. När inspektionen är klar returneras resultaten. Begäran om resursfelsökning är tidskrävande begäranden, vilket kan ta flera minuter att returnera ett resultat. Loggarna från felsökning lagras i en behållare på ett lagringskonto som har angetts.

## <a name="retrieve-network-watcher"></a>Hämta nätverksbevakare

Det första steget är att hämta Network Watcher-instansen. Variabeln `$networkWatcher` skickas till `Start-AzNetworkWatcherResourceTroubleshooting` cmdleten i steg 4.

```powershell
$networkWatcher = Get-AzNetworkWatcher -Location "WestCentralUS" 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Hämta en anslutning till virtual network gateway

I det här exemplet körs resursfelsökning på en anslutning. Du kan också skicka den till en virtuell nätverksgateway.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Resursfelsökning returnerar data om resursens hälsotillstånd, det sparar också loggar till ett lagringskonto som ska granskas. I det här steget skapar vi ett lagringskonto, om det finns ett befintligt lagringskonto kan du använda det.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Kör felsökning av nätverksbevakningsresurs

Du felsöker `Start-AzNetworkWatcherResourceTroubleshooting` resurser med cmdleten. Vi skickar cmdlet network watcher-objektet, Id för anslutnings- eller virtual network gateway, lagringskonto-ID och sökvägen för att lagra resultaten.

> [!NOTE]
> Cmdleten `Start-AzNetworkWatcherResourceTroubleshooting` är lång körd och kan ta några minuter att slutföra.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

När du har kört cmdleten granskar Network Watcher resursen för att verifiera hälsotillståndet. Resultaten returneras till skalet och lagrar loggar av resultaten i det angivna lagringskontot.

## <a name="understanding-the-results"></a>Förstå resultaten

Åtgärdstexten ger allmän vägledning om hur du löser problemet. Om en åtgärd kan vidtas för problemet får en länk ytterligare vägledning. Om det inte finns någon ytterligare vägledning, ger svaret url för att öppna ett supportärende.  Mer information om svarets egenskaper och vad som ingår finns i [översikt över felsökning](network-watcher-troubleshoot-overview.md) av Network Watcher

Instruktioner om hur du hämtar filer från azure storage-konton finns i [Komma igång med Azure Blob-lagring med .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som kan användas är Storage Explorer. Mer information om Storage Explorer finns här på följande länk: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats som stoppar VPN-anslutning läser du [Hantera nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md) för att spåra nätverkssäkerhetsgruppen och säkerhetsregler som kan vara i fråga.
