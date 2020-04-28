---
title: Azure Analysis Services resurs-och objekt gränser | Microsoft Docs
description: I den här artikeln beskrivs resurs-och objekt begränsningar för en Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "73573198"
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


