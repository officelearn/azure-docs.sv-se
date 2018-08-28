---
title: Identifiera datakällor i Azure Data Catalog
description: Den här artikeln visar hur du identifierar registrerade datatillgångar med Azure Data Catalog, inklusive sökning och filtrering och använda träffar fokus funktionerna i Azure Data Catalog-portalen.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: f72ae3a3-6573-4710-89a7-f13555e1968c
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: f8e722073db967752747511e47921aec3c4a281a
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053609"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Identifiera datakällor i Azure Data Catalog
## <a name="introduction"></a>Introduktion
Azure Data Catalog är en fullständigt hanterad molntjänst som fungerar som ett system för registrering och upptäckt för företagsdatakällor. Med andra ord Data Catalog hjälper människor att identifiera, förstå och använda datakällor och det hjälper organisationer att få ut mer av sina befintliga data. När en datakälla har registrerats med Data Catalog indexeras dess metadata av tjänsten, så att du enkelt kan söka för att identifiera de data du behöver.

## <a name="searching-and-filtering"></a>Sökning och filtrering
I Data Catalog-identifieringen använder två primära mekanismer: sökning och filtrering.

Sökningen har utformats att vara både intuitiv och kraftfull. Som standard matchas sökvillkor mot en egenskap i katalogen, inklusive kommentarer av användaren.

Filtreringen är avsedd att komplettera sökningen. Du kan välja specifika egenskaper som experter, typ av datakälla, objekttyp och taggar. Du kan visa endast matchande datatillgångar och begränsa sökresultaten till matchande tillgångar.

Genom att använda en kombination av sökning och filtrering kan navigera du snabbt i datakällorna som har registrerats med Data Catalog för att identifiera de datakällor som du behöver.

## <a name="search-syntax"></a>Söksyntax
Men standard fritextsökningen är enkelt och intuitivt, kan du också använda söksyntax för Data Catalog för bättre kontroll över sökresultaten. Datakatalogssökning stöder följande metoder:

| Teknik | Användning | Exempel |
| --- | --- | --- |
| Enkel sökning |Enkel sökning som använder en eller flera sökvillkor. Resultatet är alla objekt som matchar en egenskap med en eller flera av de angivna villkoren. |`sales data` |
| Egenskapsomfång |Returnera endast datakällor där söktermen matchas med den angivna egenskapen. |`name:finance` |
| Booleska operatorer |Utöka eller begränsa sökningen med booleska åtgärder. |`finance NOT corporate` |
| Gruppera med parenteser |Använd paranteser att gruppera delar av frågan för att skapa en logisk isolering, särskilt i kombination med booleska operatorer. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Jämförelseoperatorer |Använda andra jämförelser än lika för egenskaper som innehåller datum och numeriska datatyper. |`modifiedTime > "11/05/2014"` |

Mer information om Data Catalog search finns i den [Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx) artikeln.

## <a name="hit-highlighting"></a>Träffmarkering
När du visar sökresultatet visas alla egenskaper som matchar de angivna sökvillkor (till exempel data certifikattillgångens namn, beskrivning och taggar) är markerade för att göra det returnerades lättare att identifiera varför en viss datatillgång av en viss sökning.

> [!NOTE]
> Om du vill inaktivera markering av träffar, använda den **Markera** växel i Data Catalog-portalen.
>
>

När du visar sökresultaten kan kanske det inte alltid är uppenbara varför en datatillgång tas med, även med markering av träffar aktiverat. Eftersom alla egenskaper söks som standard, kan en datatillgång returneras på grund av en matchning på en egenskap på kolumnnivå. Och eftersom flera användare kan kommentera registrerade datatillgångar med sina egna taggar och beskrivningar, inte alla metadata kan visas i listan över sökresultat.

I standard panelvy, varje panel som visas i sökresultatet innehåller en **visa sökterm matchar** ikonen så att du snabbt visa antalet matchningar och deras plats och att gå till dem om du vill.

 ![Träffmarkering och sökning matchar i Azure Data Catalog-portalen](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Sammanfattning
Eftersom registrering av datakälla med Data Catalog kopierar strukturella och beskrivande metadata från datakällan till katalogtjänsten, blir det lättare att identifiera och förstå datakällan. När du har registrerat en datakälla kan du identifiera den med hjälp av filtrering och söka från Data Catalog-portalen.

## <a name="next-steps"></a>Nästa steg
* Steg för steg-information om hur du identifierar datakällor finns i [Kom igång med Azure Data Catalog](data-catalog-get-started.md).
