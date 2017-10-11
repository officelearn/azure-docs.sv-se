---
title: "Hur du identifierar datakällor i Azure Data Catalog | Microsoft Docs"
description: "Den här artikeln visar hur du identifierar registrerade datatillgångar med Azure Data Catalog, inklusive sökning och filtrering och använda träffar färgmarkera funktionerna i Azure Data Catalog-portalen."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: f72ae3a3-6573-4710-89a7-f13555e1968c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 9ff67dcb5ecb00440f73f979fd8d2b79a570c674
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Hur du identifierar datakällor i Azure Data Catalog
## <a name="introduction"></a>Introduktion
Azure Data Catalog är en helt hanterad molntjänst som fungerar som ett system för registrering och identifiering för företagets datakällor. Med andra ord Data Catalog hjälper användare att identifiera, förstå och använda datakällor och det hjälper organisationer som ger mer värde ur befintliga data. När en datakälla har registrerats med Data Catalog kan indexeras dess metadata av tjänsten, så att du enkelt kan söka för att identifiera de data du behöver.

## <a name="searching-and-filtering"></a>Sökning och filtrering
Identifieringen i Data Catalog använder två primära mekanismer: sökning och filtrering.

Sökningen har utformats att vara både intuitiv och kraftfull. Som standard matchas sökvillkor mot en egenskap i katalogen, inklusive kommentarer av användaren.

Filtreringen är avsedd att komplettera sökningen. Du kan välja specifika egenskaper som till exempel experter, typ av datakälla, objekttyp och taggar. Du kan visa endast matchande datatillgångar och begränsa sökresultaten till matchande tillgångar.

Genom att använda en kombination av sökning och filtrering kan navigera du snabbt datakällor som har registrerats med Data Catalog för att identifiera de datakällor som du behöver.

## <a name="search-syntax"></a>Söksyntax
Men fritext standardsökningen är enkel och intuitiv, kan du också använda söksyntax för Data Catalog för större kontroll över sökresultaten. Data katalogsökning stöder följande tekniker:

| Teknik | Användning | Exempel |
| --- | --- | --- |
| Enkel sökning |Enkel sökning som använder en eller flera sökvillkor. Resultatet är alla objekt som matchar en egenskap med ett eller flera av de villkor som angetts. |`sales data` |
| Egenskapsomfång |Returnera endast datakällor där söktermen matchas med den angivna egenskapen. |`name:finance` |
| Booleska operatorer |Utöka eller begränsa sökningen med booleska åtgärder. |`finance NOT corporate` |
| Gruppera med parenteser |Använd parenteser för att gruppera delar av frågan för att logisk isolering, särskilt i kombination med booleska operatorer. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Jämförelseoperatorer |Använda andra jämförelser än lika för egenskaper som innehåller datum och numeriska datatyper. |`modifiedTime > "11/05/2014"` |

Mer information om Data Catalog search finns i [Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx) artikel.

## <a name="hit-highlighting"></a>Träffmarkering
När du visar sökresultat, visas alla egenskaper som matchar de angivna sökvillkoren (till exempel data Tillgångsnamn, beskrivning och taggar) är markerade för att göra det returnerades lättare att identifiera varför en viss datatillgång av en viss sökning.

> [!NOTE]
> Om du vill inaktivera träffar markeringen genom att använda den **Markera** växla i Data Catalog-portalen.
>
>

När du visar sökresultat, kanske den inte alltid är uppenbara varför en datatillgång ingår, även om träffar markering aktiverad. Eftersom alla egenskaper söks som standard kan en datatillgång returneras på grund av en matchning på en egenskap på kolumnnivå. Och eftersom flera användare kan kommentera registrerade datatillgångar med egna etiketter och beskrivningar, inte alla metadata kan inte visas i listan över sökresultat.

I standard panelvy, varje bildruta som visas i sökresultatet innehåller en **visa sökterm matchar** ikonen, så att du snabbt kan visa antalet matchningar och deras plats och att gå till dem om du vill.

 ![Uppnått syntaxmarkering och sökning matchar i Azure Data Catalog-portalen](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Sammanfattning
Eftersom registrera en datakälla med Data Catalog kopierar strukturella och beskrivande metadata från datakällan till katalogtjänsten, blir det lättare att identifiera och förstå datakällan. När du har registrerat en datakälla kan du identifiera med hjälp av filtrering och söka från Data Catalog-portalen.

## <a name="next-steps"></a>Nästa steg
* Steg för steg-information om hur du identifierar datakällor finns [Kom igång med Azure Data Catalog](data-catalog-get-started.md).
