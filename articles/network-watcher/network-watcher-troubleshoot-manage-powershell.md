---
title: Felsöka Azure virtuell nätverksgateway och anslutningar - PowerShell | Microsoft Docs
description: Den här sidan förklarar hur du använder Azure Nätverksbevakaren felsöka PowerShell-cmdlet
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
ms.openlocfilehash: d0ee73ebb05999eed18e555a9b7a928e73c284e7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Felsöka virtuella nätverksgateway och anslutningar med hjälp av Azure Network Watcher PowerShell

> [!div class="op_single_selector"]
> - [Portal](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Nätverksbevakaren innehåller många funktioner relateras till att förstå nätverksresurserna i Azure. En av dessa funktioner är resurs felsökning. Felsökning av resursen kan anropas via portalen, PowerShell, CLI eller REST API. När den anropas, Nätverksbevakaren kontrollerar hälsotillståndet för en virtuell nätverksgateway eller en anslutning och returnerar resultatet.

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren.

En lista över stöds gateway typer besök [stöd för Gateway-typer](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Översikt

Felsökning av resursen ger möjlighet felsöka problem som uppstår med virtuella Nätverksgatewayer och anslutningar. När en begäran skickas till resursen felsökning, loggar som efterfrågas och kontrolleras. Resultaten returneras när kontroll har slutförts. Resursen felsökning förfrågningar är tidskrävande begäranden, vilket kan ta flera minuter att returnera ett resultat. Loggar från felsökning lagras i en behållare för ett lagringskonto som har angetts.

## <a name="retrieve-network-watcher"></a>Hämta Nätverksbevakaren

Det första steget är att hämta Nätverksbevakaren-instans. Den `$networkWatcher` variabel har skickats till den `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet i steg 4.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Hämta en Gateway för virtuell nätverksanslutning

I det här exemplet är resurs felsökning som kördes på en anslutning. Du kan också flytta den virtuella Nätverksgatewayen.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Felsökning av resursen returnerar data om hälsotillståndet för resursen, sparas även loggar till ett lagringskonto som ska granskas. I det här steget ska vi skapa ett lagringskonto, om det finns ett befintligt lagringskonto kan du använda den.

```powershell
$sa = New-AzureRmStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzureRmCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzureStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Kör Nätverksbevakaren resurs felsökning

Felsökning av resurser med den `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet. Vi skickar cmdlet objektet Nätverksbevakaren, Id för anslutning eller virtuell nätverksgateway, storage-konto-id och sökvägen för att lagra resultaten.

> [!NOTE]
> Den `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet är långvariga och kan ta några minuter att slutföra.

```powershell
Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

När du kör cmdleten, granskar Nätverksbevakaren resurs för att kontrollera hälsotillståndet. Den returnerar resultaten till shell och lagrar loggar av resultaten i storage-konto som har angetts.

## <a name="understanding-the-results"></a>Förstå resultaten

Åtgärden texten innehåller allmänna råd om hur du löser problemet. Om en åtgärd kan vidtas för utfärdande, finns en länk med mer information. I fall där det finns inga ytterligare riktlinjer, svaret innehåller URL: en för att öppna ett supportärende.  Mer information om egenskaperna för svaret och vad som ingår finns [nätverk Watcher Felsökning: översikt](network-watcher-troubleshoot-overview.md)

Anvisningar för att hämta filer från azure storage-konton, referera till [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som kan användas är Lagringsutforskaren. Mer information om Lagringsutforskaren hittar du här på följande länk: [Lagringsutforskaren](http://storageexplorer.com/)

## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats som stoppa VPN-anslutning, se [hantera Nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md) att spåra de grupp och säkerhet Nätverkssäkerhetsregler som kan vara i fråga.
