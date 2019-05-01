---
title: Visa relativa fördröjning till Azure-regioner från specifika platser | Microsoft Docs
description: Lär dig mer om att visa relativa fördröjning mellan Internetleverantörer till Azure-regioner från specifika platser.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: b4a50657442422786f49c931aa6c2610d49846b1
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939882"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Visa relativ svarstid till Azure-regioner från specifika platser

I de här självstudierna lär du dig hur du använder Azure [Network Watcher](network-watcher-monitoring-overview.md) tjänsten för att hjälpa dig att avgöra vilka Azure-regionen att distribuera programmet eller tjänsten i, baserat på demografiska användaren. Du kan dessutom använda det för att utvärdera tjänstleverantörers anslutningar till Azure.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Skapa en network watcher

Om du redan har en nätverksbevakare i minst en Azure [region](https://azure.microsoft.com/regions), kan du hoppa över aktiviteterna i det här avsnittet. Skapa en resursgrupp för network watcher. I det här exemplet resursgruppen har skapats i regionen östra USA, men du kan skapa resursgruppen i alla Azure-regioner.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Skapa en network watcher. Du måste ha en nätverksbevakare som skapats i minst en Azure-region. I det här exemplet skapas en network watcher i regionen östra USA Azure.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Jämför relativa nätverksfördröjningar med en enda Azure-region från en viss plats

Utvärdera tjänstleverantörer eller felsöka en användare rapporterar ett problem som ”platsen har långsam”, från en specifik plats till azure-region där du distribuerar en tjänst. Följande kommando returnerar de genomsnittliga relativa Internet service provider svarstiderna mellan delstaten Washington i USA och västra USA 2 Azure-region mellan 13 – 15 December 2017:

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
> Den region som du anger i det föregående kommandot behöver inte vara samma som den region som du angav när du hämtade nätverksbevakaren. Föregående kommando kräver bara att ange en befintlig nätverksbevakaren. Nätverksbevakaren kan finnas i alla regioner. Om du anger värden för `-Country` och `-State`, de måste vara giltig. Värdena är skiftlägeskänsliga. Data är tillgängliga för ett begränsat antal länder/regioner, tillstånd och städer. Kör kommandon [visa tillgängliga länder/regioner, stater, städer och providers](#view-available) att visa en lista över tillgängliga länder/regioner, orter och delstater för att använda med det föregående kommandot. 

> [!WARNING]
> Du måste ange ett datum inom de senaste 30 dagarna för `-StartTime` och `-EndTime`. Tidigare datum resulterar i att inga data returnerades.

Utdata från föregående kommando visas nedan:

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

I den returnerade utdatan, värdet för **poäng** är relativ svarstid mellan regioner och leverantörer. Ett resultat på 1 är den sämsta (högsta) svarstiden, medan 100 är kortast svarstid. De relativa svarstiderna genomsnitt för dagen. I exemplet ovan när den har Rensa som befinner har samma båda dagarna och att det finns en liten skillnad mellan svarstiden för två providrar, det har även radera som svarstiderna för både providers är låg på 1-100 skala. Medan detta förväntas, eftersom delstaten Washington i USA är fysiskt nära västra USA 2 Azure-region, ibland resultat är inte som förväntat. Ju större datumintervall som du anger, Ju mer du kan genomsnittlig svarstid för över tid.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Jämför relativa nätverksfördröjningar mellan Azure-regioner från en viss plats

IF, i stället för att ange de relativa fördröjning mellan en specifik plats och en specifik Azure-region med hjälp av `-Location`, du vill avgöra relativa svarstider till alla Azure-regioner från en specifik fysisk plats, kan du göra det för. Till exempel följande kommando hjälper dig att utvärdera vilka azure-region du distribuerar en tjänst i om dina primära användare finns Comcast användare finns i delstaten Washington:

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
> Till skillnad från måste när du anger en enda plats, om du inte ange en plats eller ange flera platser, till exempel ”västra 2”, ”West US”, du ange en Internet-leverantör när du kör kommandot. 

## <a name="view-available"></a>Visa tillgängliga länder/regioner, stater, städer och leverantörer

Data är tillgängliga för specifika Internetleverantörer, länder/regioner, tillstånd och städer. Om du vill visa en lista över alla tillgängliga Internet-leverantörer, länder/regioner, tillstånd och städer, som du kan visa data för, kan du ange följande kommando:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Data är endast tillgängliga för länder/regioner, tillstånd och städer som returneras av det föregående kommandot. Föregående kommando måste du ange en befintlig nätverksbevakaren. I exemplet som anges i *NetworkWatcher_eastus* network watcher i en resursgrupp med namnet *NetworkWatcherRG*, men du kan ange eventuella befintliga nätverksbevakaren. Om du inte har en befintlig nätverksbevakaren kan skapa en när du har slutfört uppgifterna i [skapa en network watcher](#create-a-network-watcher). 

När det föregående kommandot har körts kan du filtrera utdata som returneras genom att ange giltiga värden för **land**, **tillstånd**, och **Stad**om det behövs.  Exempel: Om du vill visa listan över Internetleverantörer som är tillgängliga i Seattle, Washington i USA, anger du följande kommando:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Det angivna värdet för **land** måste vara versaler och gemener. De angivna värdena för **tillstånd** och **Stad** måste vara i gemener. Värdena måste anges i utdata som returneras när du har kört kommandot utan värden för **land**, **tillstånd**, och **Stad**. Om du anger fel skiftläge eller ange ett värde för **land**, **tillstånd**, eller **Stad** som inte är i utdata som returneras när du har kört kommandot utan värden för dessa egenskaperna för returnerade resultatet är tom.
