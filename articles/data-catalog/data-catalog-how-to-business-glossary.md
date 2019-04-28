---
title: Konfigurera företagsordlistan i Azure Data Catalog
description: Artikel om du markerar en företagsordlista i Azure Data Catalog för att definiera och använda en gemensam företagsterminologi till taggen registrerade datatillgångar.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 649a842c8c8890713bda938c8e11740c5c8be7aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001925"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Konfigurera en företagsordlista för hanterade taggar

## <a name="introduction"></a>Introduktion

Azure Data Catalog möjliggör identifiering av datakällan, så att du enkelt kan identifiera och förstå datakällorna som du behöver utföra analyser och fatta beslut. De här funktionerna få den största effekten när du kan hitta och förstå det bredaste utbudet av tillgängliga datakällor.

En funktion som främjar bättre förståelse för tillgångar data i Data Catalog är markering. Med taggar kan associera du nyckelord med en tillgång eller en kolumn, vilket i sin tur gör det lättare att identifiera tillgången via söka eller Bläddra. Taggning hjälper också att du enkelt kan förstå kontexten och syftet med tillgången.

Taggning kan dock ibland orsaka problem med sin egen. Några exempel på problem som Taggning kan medföra är:

* Användning av förkortningar på vissa tillgångar och utökade text på andra. Den här inkonsekvens hämmar identifieringen av tillgångar, även om avsikten var att tagga tillgångarna med samma tagg.
* Potentiella variationer i betydelse, beroende på kontext. Till exempel en tagg med namnet *intäkter* på en kund datauppsättning kan innebära intäkter efter kund, men samma tagg på en kvartalsvis försäljning datauppsättning kan innebära kvartalsvis intäkter för företaget.  

Data Catalog innehåller en företagsordlista för att åtgärda dessa och andra liknande utmaningar.

Genom att använda en företagsordlista för Data Catalog kan dokumentera en organisation viktiga termer och deras definitioner för att skapa en gemensam företagsterminologi. Den här styrning låter konsekvens i dataanvändning i organisationen. När en term som har definierats i en företagsordlista, kan du tilldela den till en datatillgång i katalogen. Den här metoden *regleras taggning*, är samma metod som Taggning.

## <a name="glossary-availability-and-privileges"></a>Ordlista tillgänglighet och behörigheter

En företagsordlista är endast tillgängliga i Standard Edition av Azure Data Catalog. Den kostnadsfria versionen av Data Catalog innehåller inte en ordlista och det ger inte funktioner för styrda taggar.

Du kan komma åt en företagsordlista via den **ordlista** alternativ i navigeringsmenyn för Data Catalog-portalen.  

![Åtkomst till en företagsordlista](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Data Catalog-administratörer och medlemmar i rollen ordliste-administratörer kan skapa, redigera och ta bort termer i ordlistan i en företagsordlista. Alla Data Catalog-användare kan visa Termdefinitioner och taggen tillgångar med termer från ordlistan.

![Att lägga till en ny Ordlisteterm](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Skapa ordlistetermer

Data Catalog-administratörer och ordliste-administratörer kan skapa ordlistetermer genom att klicka på den **ny Term** knappen. Varje orlistetermen innehåller följande fält:

* En företagsdefinition för termen
* En beskrivning som samlar in den tänkta användningen eller affärsregler för tillgång eller kolumn
* En lista över intressenter som mest känner termen
* Överordnad term som definierar hierarkin som termen organiseras

## <a name="glossary-term-hierarchies"></a>Ordlista termen hierarkier

En organisation kan beskriva dess företagsterminologi som en hierarki av villkoren med hjälp av en företagsordlista för Data Catalog, och den kan skapa en klassificering av termer som representerar bättre dess taxonomi.

En term måste vara unikt i en viss nivå i hierarkin. Dubblettnamn är inte tillåtna. Det finns ingen gräns för antalet nivåer i en hierarki, men en hierarki är ofta mer lätt att förstå när det finns tre nivåer eller färre.

Användning av hierarkier i en företagsordlista är valfritt. Lämna överordnat termen tomt för ordlistetermer skapar en fast (icke-hierarkisk) lista med termer i ordlistan.  

## <a name="tagging-assets-with-glossary-terms"></a>Tagga resurser med ordlistetermer

När ordlistetermer har definierats i katalogen, är upplevelsen av taggning tillgångar optimerad för att söka i ordlistan när användaren anger en tagg. Data Catalog-portalen visar en lista över matchande ordlistetermer att välja bland. Om användaren väljer en orlistetermen i listan, har termen lagts till tillgången som en etikett (kallas även en ordlistetagg). Du kan också välja att skapa en ny tagg genom att skriva en term som inte är i ordlistan (kallas även en användartagg).

![Datatillgång taggas med en användartagg och två ordlista taggar](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Användartaggar är den enda typen av taggen stöds i den kostnadsfria versionen av Data Catalog.

### <a name="hover-behavior-on-tags"></a>Hovra beteende på taggar

I Data Catalog-portalen är de två typerna av taggar visuellt distinkta och finns olika hovra beteenden. När du hovrar över en användartagg visas taggen texten och den eller de användare som har lagt till taggen. När du hovrar över en ordlistetagg se du också definitionen av Ordlistetermen och en länk för att öppna en företagsordlista om du vill visa den fullständiga definitionen av perioden.

### <a name="search-filters-for-tags"></a>Sökfilter för taggar

Ordlista taggar och användartaggar är båda sökbart och de kan användas som filter i en sökning.

## <a name="summary"></a>Sammanfattning

Med hjälp av en företagsordlista i Azure Data Catalog och den styrda Taggning kan du identifiera, hantera och identifiera datatillgångar på ett konsekvent sätt. En företagsordlista ge inlärning av företagsterminologi av organisationsmedlemmar i. Ordlistan stöder också samla in beskrivande metadata, vilket förenklar tillgångsidentifiering och förståelse.

## <a name="next-steps"></a>Nästa steg

* [REST API-dokumentation för affärsåtgärder ordlista](/rest/api/datacatalog/data-catalog-glossary)
