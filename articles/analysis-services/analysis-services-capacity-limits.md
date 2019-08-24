---
title: Azure Analysis Services resurs-och objekt gränser | Microsoft Docs
description: Beskriver Azure Analysis Services resurs-och objekt begränsningar.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 40a5b68a12724f2574af19bb10c276c54c5afba0
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997593"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services resurs-och objekt gränser

I den här artikeln beskrivs begränsningar för resurs och modell objekt.

## <a name="tier-limits"></a>Nivå gränser

För QPU-och minnes gränser för nivåerna Developer, Basic och standard, se [sidan Azure Analysis Services prissättning](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Objekt gränser

Dessa gränser är teoretiska. Prestandan minskas med lägre siffror.

|Object|Max storlek/siffror|  
|------------|----------------------------|  
|Databaser i en instans|16,000|  
|Kombinerat antal tabeller och kolumner i en databas|16,000|  
|Rader i en tabell|Obegränsat<br /><br /> **Honom** Med begränsningen ingen enskild kolumn i tabellen kan ha mer än 1 999 999 997 distinkta värden.|  
|Hierarkier i en tabell|15 999|  
|Nivåer i en hierarki|15 999|  
|Relationer|8,000|  
|Nyckel kolumner i alla tabeller|15 999|  
|Mått i tabeller|2 ^ 31-1 = 2 147 483 647|  
|Celler som returneras av en fråga|2 ^ 31-1 = 2 147 483 647|  
|Post storlek för käll frågan|64 KB|  
|Längd på objekt namn|512 tecken|  


