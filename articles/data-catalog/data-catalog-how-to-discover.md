---
title: Identifiera datakällor i Azure Data Catalog
description: I den här artikeln beskrivs hur du identifierar registrerade datatillgångar med Azure Data Catalog, inklusive sökning och filtrering och användning av träffmarkeringsfunktionerna i Azure Data Catalog-portalen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b12cb94832a1ea977fb13f5f2271984dc8780cee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736379"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Identifiera datakällor i Azure Data Catalog

## <a name="introduction"></a>Introduktion

Azure Data Catalog är en fullständigt hanterad molntjänst som fungerar som ett system för registrering och identifiering för företagets datakällor. Med andra ord hjälper Data Catalog människor att upptäcka, förstå och använda datakällor. Det hjälper organisationer att få mer värde från sina befintliga data. När en datakälla har registrerats med Data Catalog indexeras dess metadata av tjänsten, så att du enkelt kan söka för att identifiera de data du behöver.

## <a name="searching-and-filtering"></a>Söka och filtrera

Identifiering i datakatalog använder två primära mekanismer: sökning och filtrering.

Sökningen har utformats att vara både intuitiv och kraftfull. Som standard matchas sökvillkor mot en egenskap i katalogen, inklusive kommentarer av användaren.

Filtreringen är avsedd att komplettera sökningen. Du kan välja specifika egenskaper som experter, datakälltyp, objekttyp och taggar. Du kan bara visa matchande datatillgångar och begränsa sökresultaten till matchande tillgångar.

Genom att använda en kombination av sökning och filtrering kan du snabbt navigera i de datakällor som har registrerats med Data Catalog för att identifiera de datakällor du behöver.

## <a name="search-syntax"></a>Sök syntax

Även om standardsökningen för fritext är enkel och intuitiv kan du också använda söksyntaxen för datakatalog för större kontroll över sökresultaten. Data Catalog-sökning stöder följande tekniker:

| Teknik | Användning | Exempel |
| --- | --- | --- |
| Grundläggande sökning |Grundläggande sökning som använder ett eller flera söktermer. Resultat är alla tillgångar som matchar en egenskap med ett eller flera av de angivna termerna. |`sales data` |
| Fastighetsconpering |Returnera endast datakällor där söktermen matchas med den angivna egenskapen. |`name:finance` |
| Booleska operatorer |Bredda eller begränsa en sökning med hjälp av booleska åtgärder. |`finance NOT corporate` |
| Gruppera med parentes |Använd parenteser för att gruppera delar av frågan för att uppnå logisk isolering, särskilt tillsammans med booleska operatorer. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Jämförelseoperatorer |Använd andra jämförelser än likhet för egenskaper som har numeriska datatyper och datumdatatyper. |`modifiedTime > "11/05/2014"` |

Mer information om datakatalogsökning finns i artikeln [Azure Data Catalog.](/rest/api/datacatalog/#search-syntax-reference)

## <a name="hit-highlighting"></a>Träffmarkering

När du visar sökresultat markeras alla egenskaper som visas som matchar de angivna söktermerna (till exempel datatillgångsnamn, beskrivning och taggar) för att göra det enklare att identifiera varför en viss datatillgång returnerades av en viss sökning.

> [!NOTE]
> Om du vill inaktivera träffmarkering använder du **knappen Markera** i datakatalogportalen.

När du visar sökresultat kanske det inte alltid är uppenbart varför en datatillgång ingår, även med träffmarkering aktiverad. Eftersom alla egenskaper genomsöks som standard kan en datatillgång returneras på grund av en matchning på en egenskap på kolumnnivå. Och eftersom flera användare kan kommentera registrerade datatillgångar med sina egna taggar och beskrivningar visas inte alla metadata i listan över sökresultat.

I standardpanelvyn innehåller varje panel som visas i sökresultaten ikonen **För att visa svarsmatchningar,** så att du snabbt kan visa antalet träffar och deras plats och hoppa till dem om du vill.

 ![Slå till genom att markera och söka i Azure Data Catalog-portalen](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Sammanfattning

Eftersom registrering av en datakälla med Data Catalog kopierar strukturella och beskrivande metadata från datakällan till katalogtjänsten blir datakällan lättare att upptäcka och förstå. När du har registrerat en datakälla kan du identifiera den genom att filtrera och söka inifrån datakatalogportalen.

## <a name="next-steps"></a>Nästa steg

* Stegvis information om hur du identifierar datakällor finns i [Komma igång med Azure Data Catalog](data-catalog-get-started.md).
