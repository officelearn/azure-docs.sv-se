---
title: Konfigurera företagsordlistan i Azure Data Catalog
description: How-to-artikel som belyser affärsordlistan i Azure Data Catalog för att definiera och använda ett gemensamt affärsordförråd för att tagga registrerade datatillgångar.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1065abecb1f0ef57eb13b1ec3f194f07ae01eaee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976796"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Ställ in företagsordlistan för styrd taggning

## <a name="introduction"></a>Introduktion

Azure Data Catalog möjliggör identifiering av datakällor, så att du enkelt kan identifiera och förstå de datakällor som du behöver för att utföra analyser och fatta beslut. Dessa funktioner gör störst effekt när du kan hitta och förstå det bredaste utbudet av tillgängliga datakällor.

En datakatalogfunktion som främjar större förståelse för resursdata är taggning. Genom att använda taggning kan du associera sökord med en tillgång eller en kolumn, vilket i sin tur gör det lättare att upptäcka tillgången via sökning eller surfning. Taggning hjälper dig också att lättare förstå sammanhanget och avsikten med tillgången.

Taggning kan dock ibland orsaka egna problem. Några exempel på problem som taggning kan införa är:

* Användningen av förkortningar på vissa tillgångar och utökad text på andra. Denna inkonsekvens hindrar identifieringen av tillgångar, även om avsikten var att tagga tillgångarna med samma tagg.
* Potentiella variationer i betydelse, beroende på sammanhanget. En tagg som heter *Intäkter* på en kunddatauppsättning kan till exempel innebära intäkter per kund, men samma tagg på en kvartalsvis försäljningsdatauppsättning kan innebära kvartalsintäkter för företaget.  

För att hjälpa till att lösa dessa och andra liknande utmaningar innehåller Data Catalog en affärsordlista.

Genom att använda affärsordlistan datakatalog kan en organisation dokumentera viktiga affärstermer och deras definitioner för att skapa ett gemensamt affärsordförråd. Den här styrningen möjliggör konsekvens i dataanvändningen i hela organisationen. När en term har definierats i affärsordlistan kan den tilldelas en datatillgång i katalogen. Den här metoden, *styrd taggning,* är samma metod som taggning.

## <a name="glossary-availability-and-privileges"></a>Ordlista tillgänglighet och privilegier

Affärsordlistan är endast tillgänglig i Standard Edition av Azure Data Catalog. Den kostnadsfria utgåvan av datakatalogen innehåller ingen ordlista och ger inga funktioner för styrd taggning.

Du kan komma åt affärsordlistan via **ordlistan** i navigeringsmenyn för datakatalogen.  

![Datakatalog - Få tillgång till affärsordlistan](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Datakatalogadministratörer och medlemmar i rollen ordlistaadministratörer kan skapa, redigera och ta bort ordlistor i affärsordlistan. Alla datakataloganvändare kan visa termdefinitioner och taggtillgångar med ordlistor.

![Datakatalog - Lägg till en ny ordlista](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Skapa ordlista

Datakatalogadministratörer och ordlistadministratörer kan skapa ordlistor genom att klicka på knappen **Ny term.** Varje ordlista innehåller följande fält:

* En företagsdefinition för termen
* En beskrivning som samlar in de avsedda användnings- eller affärsreglerna för tillgången eller kolumnen
* En lista över intressenter som vet mest om termen
* Den överordnade termen, som definierar hierarkin där termen är organiserad

## <a name="glossary-term-hierarchies"></a>Ordlista termhierarkier

Genom att använda affärsordlistan Data Catalog kan en organisation beskriva sin affärsordlista som en hierarki av termer, och den kan skapa en klassificering av termer som bättre representerar företagets taxonomi.

En term måste vara unik på en viss hierarkinivå. Dubblettnamn är inte tillåtna. Det finns ingen gräns för antalet nivåer i en hierarki, men en hierarki är ofta lättare att förstå när det finns tre nivåer eller färre.

Det är valfritt att använda hierarkier i affärsordlistan. Om du lämnar det överordnade termfältet tomt för ordlistetermer skapas en platt (icke-hierarkisk) lista med termer i ordlistan.  

## <a name="tagging-assets-with-glossary-terms"></a>Tagga tillgångar med ordlistor

När ordlistor har definierats i katalogen optimeras upplevelsen av taggningstillgångar för att söka i ordlistan när en användare skriver en tagg. Datakatalogportalen visar en lista med matchande ordlistor att välja mellan. Om användaren väljer en ordlista i listan läggs termen till i tillgången som en tagg (kallas även ordlistetagg). Användaren kan också välja att skapa en ny tagg genom att skriva en term som inte finns i ordlistan (kallas även en användartagg).

![Datatillgång taggad med en användartagg och två ordlista taggar](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Användartaggar är den enda typen av tagg som stöds i den kostnadsfria utgåvan av datakatalogen.

### <a name="hover-behavior-on-tags"></a>Hovringsbeteende på taggar

I datakatalogportalen är de två typerna av taggar visuellt distinkta och presenterar olika hovringsbeteenden. När du hovrar över en användartagg kan du se taggtexten och användaren eller användarna som har lagt till taggen. När du hovrar över en ordlistastagg visas även definitionen av ordlistan och en länk för att öppna affärsordlistan för att visa den fullständiga definitionen av termen.

### <a name="search-filters-for-tags"></a>Sök filter för taggar

Ordlistetaggar och användartaggar är båda sökbara och du kan använda dem som filter i en sökning.

## <a name="summary"></a>Sammanfattning

Genom att använda affärsordlistan i Azure Data Catalog och den reglerade taggning som den aktiverar kan du identifiera, hantera och identifiera datatillgångar på ett konsekvent sätt. Affärsordlistan kan främja inlärning av företagets ordförråd av organisationens medlemmar. Ordlistan stöder också insamling av meningsfulla metadata, vilket förenklar identifiering och förståelse av tillgångar.

## <a name="next-steps"></a>Nästa steg

* [REST API-dokumentation för affärsordlista](/rest/api/datacatalog/data-catalog-glossary)
