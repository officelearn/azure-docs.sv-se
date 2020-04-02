---
title: Visa relativa svarstider till Azure-regioner från specifika platser
description: Lär dig hur du visar relativa svarstider över Internet-leverantörer till Azure-regioner från specifika platser.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 39f81731f20566d1a39f3f0931ff52c4e8b43ec0
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521384"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Visa relativ svarstid till Azure-regioner från specifika platser

> [!WARNING]
> Den här funktionen är för närvarande i förhandsversion och fortfarande testas för stabilitet.

I den här självstudien kan du läsa om hur du använder Azure [Network Watcher-tjänsten](network-watcher-monitoring-overview.md) för att hjälpa dig att bestämma vilken Azure-region som ska distribuera ditt program eller din tjänst i, baserat på din användares demografi. Dessutom kan du använda den för att utvärdera tjänsteleverantörers anslutningar till Azure.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Skapa en nätverksbevakare

Om du redan har en nätverksbevakare i minst en [Azure-region](https://azure.microsoft.com/regions)kan du hoppa över uppgifterna i det här avsnittet. Skapa en resursgrupp för nätverksbevakaren. I det här exemplet skapas resursgruppen i regionen östra USA, men du kan skapa resursgruppen i valfri Azure-region.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Skapa en nätverksbevakare. Du måste ha en nätverksbevakare skapad i minst en Azure-region. I det här exemplet skapas en nätverksbevakare i regionen Östra USA Azure.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Jämföra relativa nätverksdynsekvenser med en enda Azure-region från en viss plats

Utvärdera tjänsteleverantörer eller felsöka en användare som rapporterar ett problem som "platsen var långsam", från en viss plats till den azure-region där en tjänst distribueras. Följande kommando returnerar till exempel de genomsnittliga relativa svaren för Internet-leverantörer mellan delstaten Washington i USA och azureregionen West US 2 mellan den 13–15 december 2017:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Den region som du anger i föregående kommando behöver inte vara samma som den region som du angav när du hämtade nätverksbevakaren. Det föregående kommandot kräver helt enkelt att du anger en befintlig nätverksbevakare. Nätverksbevakaren kan finnas i alla regioner. Om du anger `-Country` `-State`värden för och måste de vara giltiga. Värdena är skiftlägeskänsliga. Data är tillgängliga för ett begränsat antal länder/regioner, delstater och städer. Kör kommandona i [Visa tillgängliga länder/regioner, delstater, städer och leverantörer](#view-available) för att visa en lista över tillgängliga länder/regioner, städer och tillstånd som ska användas med föregående kommando. 

> [!WARNING]
> Du måste ange ett datum inom de `-StartTime` `-EndTime`senaste 30 dagarna för och . Om du anger ett tidigare datum kommer inga data att returneras.

Utdata från föregående kommando följer:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

I den returnerade utdata är värdet för **Poäng** den relativa svarstiden för regioner och leverantörer. Poängen 1 är den värsta (högsta) latensen, medan 100 är den lägsta latensen. De relativa svaren är i genomsnitt för dagen. I föregående exempel, medan det är tydligt att svarstiderna var samma båda dagarna och att det finns en liten skillnad mellan latensen för de två leverantörerna, är det också tydligt att svarstiderna för båda leverantörerna är låga på 1-100-skalan. Även om detta förväntas, eftersom delstaten Washington i USA är fysiskt nära regionen West US 2 Azure, är ibland resultaten inte som förväntat. Ju större datumintervall du anger, desto mer kan du genomsnittlig svarstid över tid.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Jämföra relativa nätverksdynsekvenser över Azure-regioner från en viss plats

Om du i stället för att ange de relativa fördröjningarna `-Location`mellan en viss plats och en viss Azure-region med , vill du bestämma de relativa svaren till alla Azure-regioner från en viss fysisk plats, kan du också göra det. Följande kommando hjälper dig till exempel att utvärdera vilken azure-region som ska distribueras en tjänst i om dina primära användare är Comcast-användare i delstaten Washington:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Till skillnad från när du anger en enskild plats, om du inte anger en plats eller anger flera platser, till exempel "Västra US2", "Västra USA", måste du ange en Internet-leverantör när du kör kommandot. 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>Visa tillgängliga länder/regioner, delstater, städer och leverantörer

Data är tillgängliga för specifika Internetleverantörer, länder/regioner, delstater och städer. Om du vill visa en lista över alla tillgängliga Internet-leverantörer, länder/regioner, delstater och städer som du kan visa data för anger du följande kommando:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Data är endast tillgängliga för de länder/regioner, delstater och städer som returneras av föregående kommando. Det föregående kommandot kräver att du anger en befintlig nätverksbevakare. I exemplet angavs *NetworkWatcher_eastus* nätverksbevakare i en resursgrupp med namnet *NetworkWatcherRG*, men du kan ange en befintlig nätverksbevakare. Om du inte har en befintlig nätverksbevakare skapar du en genom att utföra uppgifterna i [Skapa en nätverksbevakare](#create-a-network-watcher). 

När du har kört föregående kommando kan du filtrera utdata som returneras genom att ange giltiga värden för **Land,** **Delstat**och **Ort**, om så önskas.  Om du till exempel vill visa listan över Internet-leverantörer som är tillgängliga i Seattle, Washington, i USA anger du följande kommando:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Värdet som anges för **Land** måste vara versaler och gemener. De värden som anges för **stat** och **stad** måste vara gemener. Värdena måste anges i utdata som returneras efter att kommandot har körts utan värden för **Land,** **Delstat**och **Ort**. Om du anger fel ärende eller anger ett värde för **Land,** **Delstat**eller **Ort** som inte finns i utdata som returneras efter att kommandot har körts utan värden för dessa egenskaper, är det returnerade utdata tomt.
