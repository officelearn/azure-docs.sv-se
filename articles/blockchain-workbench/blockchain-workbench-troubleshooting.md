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
ms.openlocfilehash: 8a2715666c4fff490f5184b7b8719b412952b9bf
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
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
| resourceGroupName | Namnet på Azure-resursgrupp där Blockchain arbetsstationen har distribuerats. | Ja |
| OutputDirectory | Sökvägen till skapa utdata. ZIP-filen. Om inte anges används den aktuella katalogen. | Nej
| OmsSubscriptionId | Prenumerations-id där OMS distribueras. Bara skicka den här parametern om OMS för nätverkets blockchain distribueras utanför Blockchain arbetsstationen resursgruppen.| Nej |
| OmsResourceGroup |Resursgruppen där OMS distribueras. Bara skicka den här parametern om OMS för nätverkets blockchain distribueras utanför Blockchain arbetsstationen resursgruppen.| Nej |
| OmsWorkspaceName | Namnet på OMS-arbetsytan. Bara skicka den här parametern om OMS för nätverkets blockchain distribueras utanför Blockchain arbetsstationen resursgruppen. | Nej |

## <a name="what-is-collected"></a>Vad samlas in?

Utdata ZIP-filen innehåller följande mappstrukturen:

| Mappen \ fil | Beskrivning  |
|---------|---------|
| \Summary.txt | Sammanfattning av systemet |
| \metrics\blockchain | Mått om blockchain |
| \metrics\workbench | Mått om arbetsstationen |
| \details\blockchain | Detaljerade loggar om blockchain |
| \details\workbench | Detaljerade loggar om arbetsstationen |

Översikt över fil får du en ögonblicksbild av det övergripande tillståndet för programmet och hälsotillståndet för programmet. Sammanfattningen innehåller rekommenderade åtgärder, visar översta fel och metadata om kör tjänster.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Azure Blockchain arbetsstationen-arkitektur](blockchain-workbench-architecture.md)