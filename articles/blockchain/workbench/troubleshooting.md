---
title: Fel sökning för för hands versionen av Azure blockchain Workbench
description: Felsöka ett för hands versions program för Azure blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8fec065b629f2f2b93e78a63521ea0ce4669dd4e
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844047"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Fel sökning för för hands versionen av Azure blockchain Workbench

Det finns ett PowerShell-skript som hjälper dig med fel sökning eller support för utvecklare. Skriptet genererar en sammanfattning och samlar in detaljerade loggar för fel sökning. Insamlade loggar omfattar:

* Blockchain-nätverk, till exempel Ethereum
* Blockchain Workbench mikrotjänster
* Application Insights
* Azure-övervakning (Azure Monitor loggar)

Du kan använda informationen för att fastställa nästa steg och fastställa rotor saken till problem.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Felsöka skript

PowerShell-felsöknings skriptet finns på GitHub. [Ladda ned en zip-fil](https://github.com/Azure-Samples/blockchain/archive/master.zip) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Kör skriptet
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

`collectBlockchainWorkbenchTroubleshooting.ps1` Kör skriptet för att samla in loggar och skapa en zip-fil som innehåller en mapp med felsöknings information. Exempel:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skriptet accepterar följande parametrar:

| Parameter  | Beskrivning | Obligatorisk |
|---------|---------|----|
| subscriptionID | SubscriptionID för att skapa eller hitta alla resurser. | Ja |
| ResourceGroupName | Namnet på den Azure-resurs grupp där blockchain Workbench har distribuerats. | Ja |
| OutputDirectory | Sökväg för att skapa utdata. ZIP-fil. Om inget värde anges används den aktuella katalogen som standard. | Nej |
| LookbackHours | Antal timmar som ska användas vid hämtning av telemetri. Standardvärdet är 24 timmar. Maximalt värde är 90 timmar | Nej |
| OmsSubscriptionId | Prenumerations-ID där Azure Monitor loggar har distribuerats. Skicka bara den här parametern om Azure Monitors loggarna för blockchain-nätverket distribueras utanför blockchain Workbenchs resurs grupp.| Nej |
| OmsResourceGroup |Resurs gruppen där Azure Monitor loggar distribueras. Skicka bara den här parametern om Azure Monitors loggarna för blockchain-nätverket distribueras utanför blockchain Workbenchs resurs grupp.| Nej |
| OmsWorkspaceName | Namnet på Log Analytics arbets ytan. Skicka bara den här parametern om Azure Monitor loggar för blockchain-nätverket har distribuerats utanför Blockchains Workbenchs resurs grupp | Nej |

## <a name="what-is-collected"></a>Vad samlas in?

ZIP-filen med utdata innehåller följande mappstruktur:

| Mapp eller fil | Beskrivning  |
|---------|---------|
| \Summary.txt | Sammanfattning av systemet |
| \Metrics\blockchain | Mått för blockchain |
| \Metrics\Workbench | Mått om Workbench |
| \Details\Blockchain | Detaljerade loggar om blockchain |
| \Details\Workbench | Detaljerade loggar om Workbench |

Sammanfattnings filen ger en ögonblicks bild av det övergripande tillståndet för programmet och tillståndet för programmet. Sammanfattningen innehåller rekommenderade åtgärder, markerar de viktigaste felen och metadata om att köra tjänster.

Mappen **mått** innehåller mått för olika system komponenter över tid. Utdatafilen `\Details\Workbench\apiMetrics.txt` innehåller till exempel en sammanfattning av olika svars koder och svars tider under samlings perioden. Mappen **information** innehåller detaljerade loggar för fel sökning av specifika problem med Workbench eller det underliggande blockchain-nätverket. `\Details\Workbench\Exceptions.csv` Innehåller till exempel en lista över de senaste undantagen som har inträffat i systemet, vilket är användbart för fel sökning av fel med smarta kontrakt eller interaktioner med blockchain. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Fel söknings guide för Azure blockchain Workbench Application Insights](https://aka.ms/workbenchtroubleshooting)
