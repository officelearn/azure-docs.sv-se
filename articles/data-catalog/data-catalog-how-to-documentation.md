---
title: Så här dokumenterar du datakällor i Azure Data Catalog
description: Artikel visar hur du kan dokumentera datatillgångar i Azure Data Catalog.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 053b1701-b848-4ada-b726-6f485caa9961
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 9049e0b25db37f296e27476fbb93418d13c4dcc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001572"
---
# <a name="document-data-sources"></a>Dokumentera datakällor
## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en fullständigt hanterad molntjänst som fungerar som ett registrerings- och identifieringssystem för företagsdatakällor. Med andra ord **Azure Data Catalog** handlar om hjälper användarna att identifiera, *förstå*, och använda datakällor och hjälper organisationer att få ut mer av sina befintliga data.

När en datakälla har registrerats med **Azure Data Catalog**, dess metadata kopieras och indexeras av tjänsten, men budskapet slutar inte det. **Azure Data Catalog** låter också användare innehåller sina egna komplett dokumentation som kan beskriva användnings- och vanliga scenarier för datakällan.

I [så här kommenterar du datakällor](data-catalog-how-to-annotate.md), du lära dig att kan kommentera av experter som känner till datakällan med taggar och en beskrivning. Den **Azure Data Catalog** portal innehåller en textredigerare så att användare kan fullständigt dokumentera datatillgångar och behållare. Redigeraren innehåller punkt formatering, till exempel rubriker, textformatering, punktlistor, numrerade listor och tabeller.

Taggar och beskrivningar är användbara för enkel anteckningar. Dock kan en expert för att bättre förstå användningen av en datakälla och affärsscenarier för en datakälla datakonsumenter kan tillhandahålla fullständig, detaljerad dokumentation. Det är enkelt att dokumentera en datakälla. Välj en datatillgång eller behållare och välj **dokumentation**.

![](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Dokumentera datatillgångar
Fördelen med **Azure Data Catalog** dokumentationen kan du använda datakatalogen som en för att skapa en fullständig berättelse för dina datatillgångar. Du kan utforska detaljerat innehåll som beskriver behållare och tabeller. Om du redan har innehållet i en annan innehållsdatabas använder till exempel SharePoint eller en filresurs, du kan lägga till i tillgången dokumentationslänkarna att referera till den här befintligt innehåll. Den här funktionen gör det enklare att hitta dina befintliga dokument.

> [!NOTE]
> Dokumentation om ingår inte i search-index.
>
>

![](media/data-catalog-documentation/data-catalog-documentation2.png)

Nivån av dokumentationen kan variera mellan som beskriver egenskaper och värdet för en databehållare tillgång till en detaljerad beskrivning av tabellens schema i en behållare. Nivån av dokumentationen bör styras av dina affärsbehov. Men i allmänhet följer här några- och nackdelar med dokumentera datatillgångar:

* Dokument bara en behållare: Allt innehåll som är på samma plats, men kanske saknar nödvändig information för användare att fatta ett välgrundat beslut.
* Dokumentera bara tabeller: Innehållet är specifika för objektet, men användarna har flera platser för dokument.
* Dokumentera behållare och tabeller: Mest omfattande metod, men kan leda till mer underhåll av dokument.

## <a name="summary"></a>Sammanfattning
Dokumentera datakällor med **Azure Data Catalog** kan skapa en berättelse om dina datatillgångar i så mycket information som du behöver.  Du kan länka till innehåll som lagras i en befintlig innehållsdatabas använder som sammanför dina befintliga docs och datatillgångar med hjälp av länkar. När användarna identifiera lämpliga datatillgångar, kan de ha en fullständig uppsättning dokumentation.
