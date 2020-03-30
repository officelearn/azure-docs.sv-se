---
title: Resurs- och objektgränser för Azure Analysis Services | Microsoft-dokument
description: I den här artikeln beskrivs resurs- och objektgränser för en Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573198"
---
# <a name="analysis-services-resource-and-object-limits"></a>Resurs- och objektgränser för Analysis Services

I den här artikeln beskrivs begränsningar för resurs- och modellobjekt.

## <a name="tier-limits"></a>Nivågränser

För QPU- och minnesgränser för utvecklare, grundläggande och standardnivåer finns på [prissidan för Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Objektgränser

Dessa gränser är teoretiska. Prestanda kommer att minska med lägre siffror.

|Objekt|Maximala storlekar/siffror|  
|------------|----------------------------|  
|Databaser i en instans|16 000|  
|Kombinerat antal tabeller och kolumner i en databas|16 000|  
|Rader i en tabell|Unlimited<br /><br /> **Varning:** Med begränsningen att ingen enskild kolumn i tabellen kan ha mer än 1 999 999 997 distinkta värden.|  
|Hierarkier i en tabell|15,999|  
|Nivåer i en hierarki|15,999|  
|Relationer|8,000|  
|Nyckelkolumner i alla tabell|15,999|  
|Åtgärder i tabeller|2^31-1 = 2 147 483 647|  
|Celler som returneras av en fråga|2^31-1 = 2 147 483 647|  
|Poststorlek för källfrågan|64 K|  
|Namn på objektnamn|512 tecken|  


