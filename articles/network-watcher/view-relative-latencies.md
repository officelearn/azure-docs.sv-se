---
title: Visa relativa svarstiderna till Azure-regioner från specifika platser | Microsoft Docs
description: Lär dig mer om att visa relativa svarstiderna över Internetleverantörer till Azure-regioner från specifika platser.
services: network-watcher
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: a6c2ffa619eeff8b455df8a8b2157525af12c640
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
ms.locfileid: "27600959"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Visa relativa fördröjning till Azure-regioner från specifika platser

I den här kursen lär du dig hur du använder Azure [Nätverksbevakaren](network-watcher-monitoring-overview.md) för att hjälpa dig att avgöra vilka Azure-region för att använda programmet eller tjänsten i, baserat på din demografisk användare. Dessutom kan du använda den för att utvärdera-leverantörer anslutningar till Azure.  
        
## <a name="create-a-network-watcher"></a>Skapa en nätverksbevakaren

Om du redan har en nätverksbevakaren i minst en Azure [region](https://azure.microsoft.com/regions), kan du hoppa över aktiviteter i det här avsnittet. Skapa en resursgrupp för nätverksbevakaren. Resursgruppen har skapats i östra USA i det här exemplet, men du kan skapa resursgruppen i Azure-region.

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

Skapa en nätverksbevakaren. Du måste ha en nätverksbevakaren som skapats i minst en Azure-region. I det här exemplet skapas en nätverksbevakaren i East oss Azure-region.

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Jämför relativa nätverksfördröjningar med en enda Azure region från en viss plats

Utvärdera leverantörer eller felsöka en användare som rapporterade problemet som ”platsen har långsam”, från en specifik plats till azure-regionen där en tjänst har distribuerats. Följande kommando returnerar de genomsnittliga relativa Internet service provider fördröjningar mellan delstaten Washington i USA och West US 2 Azure-region mellan December 13-15 2017:

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Den region som du anger i det föregående kommandot behöver inte vara samma som den region som du angav när du hämtade nätverksbevakaren. Föregående kommando kräver bara att ange en befintlig nätverksbevakaren. Nätverksbevakaren kan finnas i en region. Om du anger värden för `-Country` och `-State`, de måste vara giltig. Värdena är skiftlägeskänsliga. Data är tillgängliga för ett begränsat antal länder, tillstånd och städer. Kör kommandon [visa tillgängliga land, tillstånd, orter och providers](#view-available) att visa en lista över tillgängliga land och orter tillstånd att använda med det föregående kommandot. 

> [!WARNING]
> Du måste ange ett datum efter 14 November 2017 för `-StartTime` och `-EndTime`. Ange ett datum före 14 November 2017 returnerar inga data. 

Utdata från det föregående kommandot visas nedan:

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

I returnerade resultatet är värdet för **poäng** är den relativa svarstiden över regioner och leverantörer. Ett resultat på 1 är sämsta (högsta) latens, medan 100 är den lägsta fördröjningen. De relativa svarstiderna medelvärdet för dagen. I föregående exempel, när den har Rensa som latens har samma båda dagarna och att det finns en liten skillnad mellan svarstiden för två leverantörer, den har även radera som svarstiderna för båda providers är låg på skalan 1-100. När detta är förväntat, eftersom delstaten Washington i USA fysiskt ligger nära den West US 2 Azure-regionen, ibland resultatet inte som förväntat. Ju större datumintervall du anger, Ju mer du kan genomsnittlig svarstid över tid.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Jämför relativa nätverksfördröjningar mellan Azure-regioner från en viss plats

Om istället för att ange de relativa fördröjningar mellan en specifik plats och en viss Azure-region med hjälp av `-Location`du vill avgöra relativa svarstider för alla Azure-regioner från en specifik fysisk plats, kan du göra det för. Till exempel hjälper följande kommando dig att utvärdera vilka azure-region för att distribuera en tjänst i om din primära användare Comcast användare finns i staten Washington:

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

>[!NOTE]
Till skillnad från måste när du anger en enda plats, om du inte ange en plats eller ange flera platser, till exempel ”Väst US2”, ”West US”, du ange en Internet-leverantör när du kör kommandot. 

## <a name="view-available"></a>Visa tillgängliga land, tillstånd, orter och providers

Data är tillgängliga för vissa Internetleverantörer, land, tillstånd och städer. Om du vill visa en lista över alla tillgängliga Internetleverantörer länder, tillstånd och orter som du kan visa data för, kan du ange följande kommando:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Data är bara tillgängliga för länder, tillstånd och orter som returneras av kommandot tidigare. Föregående kommando måste du ange en befintlig nätverksbevakaren. I exemplet som anges i *NetworkWatcher_eastus* nätverksbevakaren i en resursgrupp med namnet *NetworkWatcherRG*, men du kan ange alla befintliga nätverksbevakaren. Om du inte har en befintlig nätverksbevakaren, skapa en genom att slutföra uppgifterna i [skapa en nätverksbevakaren](#create-a-network-watcher). 

När du har kört kommandot tidigare, kan du filtrera resultatet som returneras genom att ange giltiga värden för **land**, **tillstånd**, och **Stad**om det behövs.  Till exempel om du vill visa listan över Internetleverantörer som är tillgängliga i Seattle, Washington i USA, anger du följande kommando:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Det angivna värdet för **land** måste vara versaler och gemener. De angivna värdena för **tillstånd** och **Stad** måste vara gemener. Värden måste anges i utdatan efter körning av kommandot utan värden för **land**, **tillstånd**, och **Stad**. Om du anger felaktigt fallet eller ange ett värde för **land**, **tillstånd**, eller **Stad** som inte är i utdatan efter körning av kommandot utan värden för dessa Egenskaper för returnerade resultatet är tom.
