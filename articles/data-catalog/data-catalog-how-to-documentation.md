---
title: Så här dokumenterar du datakällor i Azure Data Catalog
description: Hur du vill artikel som belyser hur du dokumenterar datatillgångar i Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: e9e9013d354585d04f205feb93a84d94c0f05905
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950188"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Så här dokumenterar du datakällor i Azure Data Catalog

## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en fullständigt hanterad molntjänst som fungerar som ett system för registrering och identifieringssystem för företagets datakällor. Med andra ord handlar **Azure Data Catalog** om att hjälpa människor att upptäcka, *förstå*och använda datakällor och hjälpa organisationer att få mer värde från sina befintliga data.

När en datakälla registreras med **Azure Data Catalog**kopieras och indexeras dess metadata av tjänsten, men artikeln slutar inte där. **Azure Data Catalog** tillåter också användare att tillhandahålla sin egen fullständiga dokumentation som kan beskriva användning och vanliga scenarier för datakällan.

I [Så här kommenterar](data-catalog-how-to-annotate.md)du datakällor och lär dig att experter som känner till datakällan kan kommentera den med taggar och en beskrivning. **Azure Data Catalog-portalen** innehåller en RTF-redigerare så att användarna kan dokumentera datatillgångar och behållare fullt ut. Redigeraren innehåller styckeformatering, till exempel rubriker, textformatering, punktlistor, numrerade listor och tabeller.

Taggar och beskrivningar är bra för enkla anteckningar. Men för att hjälpa datakonsumenter att bättre förstå användningen av en datakälla och affärsscenarier för en datakälla kan en expert tillhandahålla fullständig, detaljerad dokumentation. Det är enkelt att dokumentera en datakälla. Välj en datatillgång eller behållare och välj **Dokumentation**.

![Fliken Dokumentation i en datakatalog](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Dokumentera datatillgångar
Med hjälp av **Azure Data Catalog-dokumentationen** kan du använda datakatalogen som innehållslagringsplats för att skapa en fullständig beskrivning av dina datatillgångar. Du kan utforska detaljerat innehåll som beskriver behållare och tabeller. Om du redan har innehåll i en annan innehållsdatabas, till exempel SharePoint eller en filresurs, kan du lägga till i tillgångsdokumentationslänkarna för att referera till det befintliga innehållet. Den här funktionen gör dina befintliga dokument mer upptäckbara.

> [!NOTE]
> Dokumentation ingår inte i sökindex.
>

![Fliken Dokumentation och hyperlänk till webblänk](media/data-catalog-documentation/data-catalog-documentation2.png)

Dokumentationsnivån kan vara allt från att beskriva egenskaperna och värdet för en datatillgångsbehållare till en detaljerad beskrivning av tabellschemat i en behållare. Nivån på dokumentationen som tillhandahålls bör drivas av dina affärsbehov. Men i allmänhet, här är några för-och nackdelar med att dokumentera datatillgångar:

* Dokumentera bara en behållare: Allt innehåll finns på ett ställe, men kanske saknar nödvändiga detaljer för att användarna ska kunna fatta ett välgrundat beslut.
* Dokumentera bara tabellerna: Innehållet är specifikt för det objektet, men användarna har flera platser för dokument.
* Dokumentbehållare och register: Den mest övergripande metoden, men kan innebära mer underhåll av dokumenten.

## <a name="summary"></a>Sammanfattning
Genom att dokumentera datakällor med **Azure Data Catalog** kan du skapa en berättelse om dina datatillgångar så detaljerat som du behöver.  Genom att använda länkar kan du länka till innehåll som lagras i en befintlig innehållsdatabas, vilket sammanför dina befintliga dokument och datatillgångar. När användarna har upptäckt lämpliga datatillgångar kan de ha en fullständig uppsättning dokumentation.
