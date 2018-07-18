---
title: Felsöka Azure Vnet-Gateway och anslutningar – PowerShell | Microsoft Docs
description: Den här sidan förklaras hur du använder Azure Network Watcher felsöka PowerShell-cmdlet
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 67c34156d6a397cdeb4bb50c712b1bb651c2f257
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090436"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Felsöka virtuella nätverkets Gateway och anslutningar som använder Azure Network Watcher PowerShell

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher innehåller många funktioner som den relaterar till att förstå nätverksresurserna i Azure. En av dessa funktioner är resurs felsökning. Felsökning av resursen kan anropas via portal, PowerShell, CLI eller REST API. När den anropas, Network Watcher kontrollerar hälsotillståndet för en virtuell nätverksgateway eller en anslutning och returnerar resultatet.

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Network Watcher](network-watcher-create.md) att skapa en Network Watcher.

En lista över stöds gateway typer besök [stöds gatewaytyper](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Översikt

Felsökning av resurs gör möjligheten felsöka problem som kan uppstå med Virtual Network-gatewayer och anslutningar. När en begäran skickas till felsöknings-resurs, som loggar ska efterfrågas och kontrolleras. När kontrollen är klar visas returneras resultaten. Resursen felsökning begärandena långvariga begäranden, vilket kan ta flera minuter att beräkna ett resultat. Loggar från felsökning lagras i en behållare på ett lagringskonto som har angetts.

## <a name="retrieve-network-watcher"></a>Hämta Network Watcher

Det första steget är att hämta Network Watcher-instans. Den `$networkWatcher` variabeln skickas till den `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet i steg 4.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Hämta en Gateway för virtuell nätverksanslutning

I det här exemplet som felsökning av resurs kördes på en anslutning. Du kan även skicka det en virtuell nätverksgateway.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Felsökning av resurs returnerar data om hälsotillståndet för resursen, sparas även loggar till ett lagringskonto som ska granskas. I det här steget ska vi skapa ett lagringskonto, om det finns ett befintligt lagringskonto kan du använda den.

```powershell
$sa = New-AzureRmStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzureRmCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzureStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Kör felsökning av Network Watcher-resurs

Felsökning av resurser med den `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet. Vi skickar cmdlet: en Network Watcher-objektet, Id för anslutningen eller det virtuella nätverkets Gateway, storage-konto-id och sökvägen för att lagra resultatet.

> [!NOTE]
> Den `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet körs länge och kan ta några minuter att slutföra.

```powershell
Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

När du kör cmdleten, granskar Network Watcher resurs för att kontrollera hälsotillståndet. Den returnerar resultaten till gränssnittet och lagrar loggar resultat i det angivna lagringskontot.

## <a name="understanding-the-results"></a>Förstå resultaten

Åtgärd-texten innehåller allmänna råd om hur du löser problemet. Om en åtgärd kan utföras på problemet, länk en med ytterligare vägledning. I fall där det finns inga ytterligare vägledning, svaret anger webbadressen för att öppna ett supportärende.  Mer information om egenskaperna för svaret och vad som ingår finns [felsökning av Network Watcher-översikt](network-watcher-troubleshoot-overview.md)

Instruktioner om att ladda ned filer från azure storage-konton finns i [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som kan användas är Storage Explorer. Mer information om Storage Explorer finns här på följande länk: [Storage Explorer](http://storageexplorer.com/)

## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats som stoppa VPN-anslutning, se [hantera Nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md) att spåra de och Nätverkssäkerhetsregler som kan vara i fråga.
