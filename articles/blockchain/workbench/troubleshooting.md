---
title: Azure Blockchain Workbench felsökning
description: Hur du felsöker ett program med Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b55c84773d99c325689fbc5182e75c7cb108d00a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896341"
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Azure Blockchain Workbench felsökning

Ett PowerShell-skript kan hjälpa till med felsökning utvecklare eller stöd för. Skriptet genererar en sammanfattning och samlar in detaljerade loggar för felsökning. Insamlade loggar innehåller:

* Blockchain-nätverket, till exempel Ethereum
* Blockchain Workbench mikrotjänster
* Application Insights
* Azure Monitoring (Azure Monitor-loggarna)

Du kan använda informationen för att avgöra nästa steg och fastställa orsaken till problem.

## <a name="troubleshooting-script"></a>Felsökning av skript

PowerShell felsöka skriptet finns på GitHub. [Ladda ned en zip-fil](https://github.com/Azure-Samples/blockchain/archive/master.zip) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Kör skriptet
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Kör den `collectBlockchainWorkbenchTroubleshooting.ps1` skript för att samla in loggar och skapa en ZIP-fil som innehåller en mapp med information om felsökning. Exempel:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skriptet godkänner följande parametrar:

| Parameter  | Beskrivning | Obligatoriskt |
|---------|---------|----|
| prenumerations-ID | Prenumerations-ID att skapa eller leta upp alla resurser. | Ja |
| ResourceGroupName | Namnet på Azure-resursgrupp där Blockchain Workbench har distribuerats. | Ja |
| OutputDirectory | Sökväg till skapa utdata. ZIP-filen. Om den inte anges som standard den aktuella katalogen. | Nej |
| LookbackHours | Antal timmar som ska användas för att hämta telemetri. Standardvärdet är 24 timmar. Högsta tillåtna värde är 90 timmar | Nej |
| OmsSubscriptionId | Prenumerations-ID var Azure Monitor loggar distribueras. Skicka bara den här parametern om Azure Monitor-loggar för nätverket blockchain distribueras utanför Blockchain Workbench resursgrupp.| Nej |
| OmsResourceGroup |Resursgruppen där Azure Monitor-loggar har distribuerats. Skicka bara den här parametern om Azure Monitor-loggar för nätverket blockchain distribueras utanför Blockchain Workbench resursgrupp.| Nej |
| OmsWorkspaceName | Namnet på Log Analytics-arbetsytan. Endast skicka den här parametern om Azure Monitor-loggar för nätverket blockchain distribueras utanför Blockchain Workbench resursgrupp | Nej |

## <a name="what-is-collected"></a>Vad samlas in?

Utdata ZIP-filen innehåller följande mappstrukturen:

| Mapp eller fil | Beskrivning  |
|---------|---------|
| \Summary.txt | Sammanfattning av systemet |
| \Metrics\blockchain | Mått om blockkedja |
| \Metrics\Workbench | Mått om workbench |
| \Details\Blockchain | Detaljerade loggar om blockkedja |
| \Details\Workbench | Detaljerade loggar om workbench |

Sammanfattningsfilen får du en ögonblicksbild av det övergripande tillståndet för programmet och hälsotillståndet för programmet. Sammanfattningen innehåller rekommenderade åtgärder, illustrerar viktigaste fel och metadata om att köra tjänster.

Den **mått** mappen innehåller mått för olika systemkomponenter som över tid. Till exempel utdatafilen `\Details\Workbench\apiMetrics.txt` innehåller en sammanfattning av olika svarskoder och svarstider under hela insamlingsperioden. Den **information** mappen innehåller detaljerade loggar för felsökning av specifika problem med Workbench eller det underliggande blockchain-nätverket. Till exempel `\Details\Workbench\Exceptions.csv` innehåller en lista över de senaste undantag som har inträffat i systemet, vilket är användbart för felsökning av fel med smarta kontrakt och interaktioner med blockchain. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Felsökningsguide för Azure Blockchain Workbench Application Insights](https://aka.ms/workbenchtroubleshooting)
