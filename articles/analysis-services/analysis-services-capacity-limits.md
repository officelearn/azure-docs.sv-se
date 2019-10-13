---
title: Azure Analysis Services resurs-och objekt gränser | Microsoft Docs
description: Beskriver Azure Analysis Services resurs-och objekt begränsningar.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9fe43602c66af72357e16822ee9d4b5a741d3f86
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298697"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services resurs-och objekt gränser

I den här artikeln beskrivs begränsningar för resurs och modell objekt.

## <a name="tier-limits"></a>Nivå gränser

För QPU-och minnes gränser för nivåerna Developer, Basic och standard, se [sidan Azure Analysis Services prissättning](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Objekt gränser

Dessa gränser är teoretiska. Prestandan minskas med lägre siffror.

|Objekt|Max storlek/siffror|  
|------------|----------------------------|  
|Databaser i en instans|16 000|  
|Kombinerat antal tabeller och kolumner i en databas|16 000|  
|Rader i en tabell|Obegränsat<br /><br /> **Varning:** Med begränsningen ingen enskild kolumn i tabellen kan ha mer än 1 999 999 997 distinkta värden.|  
|Hierarkier i en tabell|15 999|  
|Nivåer i en hierarki|15 999|  
|Relationer|8,000|  
|Nyckel kolumner i alla tabeller|15 999|  
|Mått i tabeller|2 ^ 31-1 = 2 147 483 647|  
|Celler som returneras av en fråga|2 ^ 31-1 = 2 147 483 647|  
|Post storlek för käll frågan|64 KB|  
|Längd på objekt namn|512 tecken|  


