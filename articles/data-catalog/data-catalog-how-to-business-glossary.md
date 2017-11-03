---
title: "Ställ in en företagsordlista för styrs märkning i Azure Data Catalog | Microsoft Docs"
description: "Markera företagsordlista i Azure Data Catalog för att definiera och använder en gemensam terminologi för företag att tagga artikel registrerade datatillgångar."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: b3d63dbe-1ae7-499f-bc46-42124e950cd6
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 83ca3b2d89a335a5fd6dddeaca7c11f6d0492234
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Ställ in en företagsordlista för styrs taggning
## <a name="introduction"></a>Introduktion
Azure Data Catalog gör datakälla identifiering så att du lätt kan identifiera och förstå datakällorna som du behöver utföra analyser och fatta beslut. Dessa funktioner gör den största effekten när du kan hitta och förstå mycket stort antal typer av datakällor.

En funktion som främjar bättre kunskap om tillgångar data i Data Catalog är markering. Du kan associera nyckelord med en tillgång eller en kolumn, vilket i sin tur gör det lättare att identifiera tillgången via söka eller bläddra genom att använda taggar. Taggning hjälper dig också mer enkelt förstå kontext och syftet med tillgången.

Taggning kan dock ibland orsaka problem egna. Det är några exempel på problem som kan leda till märkning:

* Användning av förkortningar på vissa tillgångar och utökade text på andra. Den här inkonsekvens hämmar identifiering av tillgångar, även om avsikten var att tagga tillgångar med samma tagg.
* Potentiella variationer i betydelse, beroende på kontext. Till exempel en tagg kallas *intäkter* på en kund datauppsättning kan innebära intäkter av kunden, men samma tagg för ett kvartal försäljning datauppsättningen kan innebära kvartalsvisa intäkter för företaget.  

Data Catalog innehåller en företagsordlista för att åtgärda dessa och andra liknande utmaningar.

Genom att använda en företagsordlista för Data Catalog kan dokumentera en organisation viktiga termer och definitioner att skapa en gemensam ordlista för företag. Den här styrning kan konsekvens i dataanvändning i organisationen. När en term som har definierats i en företagsordlista, kan den tilldelas en datatillgång i katalogen. Den här metoden *styrs taggning*, är samma metod som Taggning.

## <a name="glossary-availability-and-privileges"></a>Ordlista tillgänglighet och privilegier
En företagsordlista är endast tillgänglig i Standard Edition av Azure Data Catalog. Den kostnadsfria Edition av Data Catalog innehåller inte en ordlista och ger inte funktioner för styrda Taggning.

Du kan komma åt en företagsordlista via den **ordlista** alternativet i navigeringsmenyn i Data Catalog-portalen.  

![Åtkomst till företagsordlistan](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Data Catalog administratörer och medlemmar i rollen ordlista administratörer kan skapa, redigera och ta bort ordlista i en företagsordlista. Alla Data Catalog-användare kan visa Termdefinitioner och tagg tillgångar med termer från ordlistan.

![Lägga till en ny Ordlisteterm](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Skapa ordlista
Data Catalog administratörer och ordlista administratörer kan skapa ordlista genom att klicka på den **ny Term** knappen. Varje termen innehåller följande fält:

* Företag definition för termen
* En beskrivning som samlar in avsedda användning eller affärsregler för tillgången eller kolumn
* En lista över intressenter som mest känner termen
* Överordnad term som definierar hierarkin termen ordnas

## <a name="glossary-term-hierarchies"></a>Ordlista termen hierarkier
En organisation kan beskriva dess ordförråd för företag som en hierarki med villkoren med hjälp av en företagsordlista för Data Catalog och den kan skapa en klassificering av termer som representerar bättre dess klassificering för företaget.

Ett villkor som måste vara unika på en viss nivå i hierarkin. Dubblettnamn är inte tillåtna. Det finns ingen gräns för antalet nivåer i en hierarki, men en hierarki är ofta lättare att förstå när det finns tre nivåer eller färre.

Användning av hierarkier i företagsordlistan är valfritt. Om du lämnar överordnat termen tomt för ordlista skapar en platt (icke-hierarkisk) lista över villkor i ordlistan.  

## <a name="tagging-assets-with-glossary-terms"></a>Taggning tillgångar med ordlista
När ordlista har definierats i katalogen, är upplevelse av taggning tillgångar optimerad för att söka i ordlistan när användaren anger en tagg. Data Catalog-portalen visar en lista över matchande ordlista kan välja mellan. Om användaren väljer en Ordlisteterm i listan, läggs termen till tillgången som en etikett (kallas även en ordlista tagg). Användaren kan också välja att skapa en ny tagg genom att skriva en term som inte är i ordlistan (kallas även en användartagg för).

![Datatillgång märkta med taggen för en användare och två ordlista taggar](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Användaren taggar är den enda typen av taggen stöds i den kostnadsfria Edition av Data Catalog.
>
>

### <a name="hover-behavior-on-tags"></a>Hovra beteende på taggar
De två typerna av taggar finns i Data Catalog-portalen visuellt distinkta och finns olika hovra beteenden. Du kan se tag-text och en eller flera användare som har lagts till taggen när du hovrar över en användartagg för. När du hovrar över en ordlista tagg kan se du också definitionen av termen eller en länk för att öppna en företagsordlista om du vill visa fullständig definition av termen.

### <a name="search-filters-for-tags"></a>Sökfilter för taggar
Ordlista taggar och användaren taggar är båda sökbara och du kan använda dem som filter i en sökning.

## <a name="summary"></a>Sammanfattning
Med hjälp av en företagsordlista i Azure Data Catalog och styrda taggarna kan du identifiera, hantera och identifiera datatillgångar i ett konsekvent sätt. En företagsordlista kan befordra inlärning av företag ordförråd av organisationsmedlemmar. Ordlistan stöder också samla in beskrivande metadata, vilket förenklar tillgången identifiering och förstå.

## <a name="next-steps"></a>Nästa steg
* [REST API-dokumentation för affärsåtgärder ordlista](https://msdn.microsoft.com/library/mt708855.aspx)
