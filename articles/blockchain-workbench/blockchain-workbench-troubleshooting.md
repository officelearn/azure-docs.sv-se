---
title: Azure Blockchain arbetsstationen felsökning
description: Så här felsöker du ett program med Azure Blockchain arbetsstationen.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 419ed6dc76101366e47ae94067f7b671a10c94e2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Azure Blockchain arbetsstationen felsökning

Ett PowerShell-skript kan hjälpa till med utvecklare felsökning eller stöd för. Skriptet genererar en översikt och samlar in detaljerade loggar för felsökning. Insamlade loggar innefattar:

* Blockchain nätverk, till exempel Ethereum
* Blockchain arbetsstationen mikrotjänster
* Application Insights
* Azure övervakning (OMS)

Du kan använda informationen för att avgöra nästa steg och ta reda på orsaken till problem. 

## <a name="troubleshooting-script"></a>Felsökning av skript

PowerShell felsöka skriptet finns på GitHub. [Ladda ned en zip-fil](https://github.com/Azure-Samples/blockchain/archive/master.zip) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Kör skriptet
[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

Kör den `collectBlockchainWorkbenchTroubleshooting.ps1` skript för att samla in loggar och skapa en ZIP-fil som innehåller en mapp med information om felsökning. Exempel:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skriptet accepterar följande parametrar:

| Parameter  | Beskrivning | Krävs |
|---------|---------|----|
| Prenumerations-ID | SubscriptionID som ska skapa och hitta alla resurser. | Ja |
| ResourceGroupName | Namnet på Azure-resursgrupp där Blockchain arbetsstationen har distribuerats. | Ja |
| OutputDirectory | Sökvägen till skapa utdata. ZIP-filen. Om inte anges används den aktuella katalogen. | Nej |
| LookbackHours | Antal timmar som ska användas när dra telemetri. Standardvärdet är 24 timmar. Högsta tillåtna värde är 90 timmar | Nej |
| OmsSubscriptionId | Prenumerations-ID där OMS distribueras. Bara skicka den här parametern om OMS för nätverkets blockchain distribueras utanför Blockchain arbetsstationen resursgruppen.| Nej |
| OmsResourceGroup |Resursgruppen där OMS distribueras. Bara skicka den här parametern om OMS för nätverkets blockchain distribueras utanför Blockchain arbetsstationen resursgruppen.| Nej |
| OmsWorkspaceName | Namnet på OMS-arbetsytan. Bara skicka den här parametern om OMS för nätverkets blockchain distribueras utanför Blockchain arbetsstationen resursgruppen. | Nej |

## <a name="what-is-collected"></a>Vad samlas in?

Utdata ZIP-filen innehåller följande mappstrukturen:

| Mapp eller fil | Beskrivning  |
|---------|---------|
| \Summary.txt | Sammanfattning av systemet |
| \Metrics\blockchain | Mått om blockchain |
| \Metrics\Workbench | Mått om arbetsstationen |
| \Details\Blockchain | Detaljerade loggar om blockchain |
| \Details\Workbench | Detaljerade loggar om arbetsstationen |

Översikt över fil får du en ögonblicksbild av det övergripande tillståndet för programmet och hälsotillståndet för programmet. Sammanfattningen innehåller rekommenderade åtgärder, visar översta fel och metadata om kör tjänster.

Den **mått** mappen innehåller mätvärden för olika komponenter över tid. Till exempel utdatafilen `\Details\Workbench\apiMetrics.txt` innehåller en sammanfattning av olika svarskoder och svarstider under hela samlingen. Den **information** mappen innehåller detaljerade loggar för felsökning av problem med arbetsstationen eller det underliggande blockchain-nätverket. Till exempel `\Details\Workbench\Exceptions.csv` innehåller en lista över de senaste undantag som uppstått i systemet, vilket är användbart för felsökning av fel med smart kontrakt och interaktioner med blockchain. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Azure Blockchain arbetsstationen-arkitektur](blockchain-workbench-architecture.md)