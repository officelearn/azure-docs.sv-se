---
title: Felsökning av Azure Blockchain Workbench
description: Felsöker ett Azure Blockchain Workbench Preview-program.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324306"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Felsökning av förhandsversionen av Azure Blockchain Workbench

Ett PowerShell-skript finns tillgängligt för att hjälpa till med felsökning av utvecklare eller support. Skriptet genererar en sammanfattning och samlar in detaljerade loggar för felsökning. Insamlade loggar inkluderar:

* Blockchain-nätverk, såsom Ethereum
* Mikrotjänster för Blockchain Workbench
* Application Insights
* Azure-övervakning (Azure Monitor-loggar)

Du kan använda informationen för att bestämma nästa steg och fastställa orsaken till problem.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Felsöka skript

PowerShell-felsökningsskriptet är tillgängligt på GitHub. [Ladda ned en zip-fil](https://github.com/Azure-Samples/blockchain/archive/master.zip) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Kör skriptet
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Kör `collectBlockchainWorkbenchTroubleshooting.ps1` skriptet för att samla in loggar och skapa en ZIP-fil som innehåller en mapp med felsökningsinformation. Ett exempel:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skriptet accepterar följande parametrar:

| Parameter  | Beskrivning | Krävs |
|---------|---------|----|
| SubscriptionID | SubscriptionID för att skapa eller hitta alla resurser. | Ja |
| ResourceGroupName | Namn på Azure Resource Group där Blockchain Workbench har distribuerats. | Ja |
| OutputDirectory | Sökväg för att skapa utdata . ZIP-fil. Om inget anges, som standard till den aktuella katalogen. | Inga |
| LookbackHours | Antal timmar som ska användas när du drar telemetri. Standardvärdet är 24 timmar. Maximalt värde är 90 timmar | Inga |
| OmsSubscriptionId | Prenumerations-ID där Azure Monitor-loggar distribueras. Skicka bara den här parametern om Azure Monitor-loggarna för blockchain-nätverket distribueras utanför Blockchain Workbenchs resursgrupp.| Inga |
| OmsResourceGroup |Resursgruppen där Azure Monitor-loggar distribueras. Skicka bara den här parametern om Azure Monitor-loggarna för blockchain-nätverket distribueras utanför Blockchain Workbenchs resursgrupp.| Inga |
| OmsWorkspaceName | Namnet på Logganalysarbetsytan. Skicka endast den här parametern om Azure Monitor-loggarna för blockchain-nätverket distribueras utanför Blockchain Workbenchs resursgrupp | Inga |

## <a name="what-is-collected"></a>Vad samlas in?

Zip-filen innehåller följande mappstruktur:

| Mapp eller fil | Beskrivning  |
|---------|---------|
| \Sammanfattning.txt | Sammanfattning av systemet |
| \Mått\blockchain | Mätvärden om blockkedjan |
| \Mått\Arbetsbänk | Mätvärden om arbetsbänken |
| \Detaljer\Blockchain | Detaljerade loggar om blockchain |
| \Detaljer\Arbetsbänk | Detaljerade loggar om arbetsbänken |

Sammanfattningsfilen ger dig en ögonblicksbild av det övergripande tillståndet för programmet och hälsotillståndet för programmet. Sammanfattningen innehåller rekommenderade åtgärder, höjdpunkter de vanligaste felen och metadata om att köra tjänster.

Mappen **Mått** innehåller mått för olika systemkomponenter över tid. Utdatafilen `\Details\Workbench\apiMetrics.txt` innehåller till exempel en sammanfattning av olika svarskoder och svarstider under hela insamlingsperioden. Mappen **Information** innehåller detaljerade loggar för felsökning av specifika problem med Workbench eller det underliggande blockchain-nätverket. Innehåller till `\Details\Workbench\Exceptions.csv` exempel en lista över de senaste undantagen som har inträffat i systemet, vilket är användbart för felsökning av fel med smarta kontrakt eller interaktioner med blockkedjan. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Felsökningsguide för Azure Blockchain Workbench-programinsikter](https://aka.ms/workbenchtroubleshooting)
