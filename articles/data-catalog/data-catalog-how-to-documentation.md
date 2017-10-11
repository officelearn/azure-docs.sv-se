---
title: "Så här dokumenterar du datakällor | Microsoft Docs"
description: "Artikel syntaxmarkering så här dokumenterar du datatillgångar i Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: spelluru
manager: NA
editor: 
tags: 
ms.assetid: 053b1701-b848-4ada-b726-6f485caa9961
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/03/2017
ms.author: spelluru
ms.openlocfilehash: ffe951f60afb57524568fe1ed3b3668d0088e124
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="document-data-sources"></a>Dokumentera datakällor
## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en helt hanterad molntjänst som fungerar som ett system för registrering och identifieringssystem för företagets datakällor. Med andra ord **Azure Data Catalog** handlar om hjälper människor identifiera, *förstå*, och använda datakällor och hjälper organisationer att få ut mer av sina befintliga data.

När en datakälla har registrerats med **Azure Data Catalog**, dess metadata kopieras och indexeras av tjänsten, men artikeln inte det avslutas. **Azure Data Catalog** dessutom tillåter användarna att ange sina egna komplett dokumentation som kan beskriver användnings- och vanliga scenarier för datakällan.

I [så här kommenterar du datakällor](data-catalog-how-to-annotate.md), du lära dig att experter som vet datakällan kan kommentera det med taggar och en beskrivning. Den **Azure Data Catalog** portal innehåller en textredigerare så att användare kan fullständigt dokumentera datatillgångar och behållare. Redigeraren innehåller styckeformatering, till exempel rubriker, textformatering, punktlista, numrerade listor och tabeller.

Taggar och beskrivningar är perfekt för enkla anteckningar. För att bättre förstå användningen av en datakälla och affärsscenarier för en datakälla för datakonsumenterna kan dock en expert tillhandahålla fullständig, detaljerad dokumentation. Det är enkelt att dokumentera en datakälla. Välj en datatillgång eller behållaren och välj **dokumentationen**.

![](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Dokumentera datatillgångar
Fördelen med **Azure Data Catalog** dokumentationen kan du använda Data Catalog som en innehållsdatabas för att skapa en fullständig berättelsen för dina datatillgångar. Du kan utforska detaljerad innehåll som beskriver behållare och tabeller. Om du redan har innehåll i en annan innehållsdatabasen, till exempel SharePoint eller en filresurs, kan du lägga till tillgången dokumentationen länkar till den här befintligt innehåll. Den här funktionen gör det lättare att hitta din befintliga dokument.

> [!NOTE]
> Dokumentationen ingår inte i sökindex.
>
>

![](media/data-catalog-documentation/data-catalog-documentation2.png)

Nivån av dokumentationen kan variera mellan som beskriver egenskaper och värdet för en tillgång behållare för data att en detaljerad beskrivning av tabellens schema i en behållare. Nivån av dokumentationen bör styrs av dina affärsbehov. Men i allmänhet följer här några- och nackdelar med dokumentera datatillgångar:

* Dokumentera bara en behållare: allt innehåll på en plats, men saknar nödvändiga information för användare att fatta ett välgrundat beslut.
* Dokumentera bara tabellerna: innehåll som är specifik för objektet, men användarna har flera platser för dokument.
* Dokumentera behållare och tabeller: mest omfattande metod, men kan leda till flera underhåll av dokumenten.

## <a name="summary"></a>Sammanfattning
Dokumentera datakällor med **Azure Data Catalog** kan skapa en berättelsen om dina datatillgångar i så mycket information som du vill.  Du kan länka till innehåll som lagras i en befintlig innehåll databas som sammanför din befintliga dokumenten och datatillgångar med hjälp av länkar. När användarna identifiera lämpliga datatillgångar, har de en fullständig uppsättning dokumentation.
