---
title: Jakt funktionerna i Azure Sentinel-förhandsversionen | Microsoft Docs
description: Den här artikeln beskriver hur du använder Azure Sentinel jakt funktionerna.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: adedc8bc1f574ae089f2a11033fab4f390c57a9a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087033"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>Hitta hot med Azure Sentinel-förhandsversion

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om du är en utredaren som vill vara proaktiv leta efter säkerhetshot jakt Azure Sentinel kraftfulla Sök och fråga i Verktyg för jakt efter säkerhetshot via din organisations datakällor. Men dina system och säkerhetsenheter genererar bergen som kan vara svårt att tolka och filtrera till meningsfulla händelser. För att säkerhet analytiker leta proaktivt efter nya avvikelser som inte identifieras av dina appar med säkerhet, Azure Sentinel' inbyggda jakt frågor hjälper dig i ställa de rätta frågorna för att hitta problem i de data som du redan har i nätverket. 

Till exempel en inbyggd fråga tillhandahåller data om mest ovanliga processer som körs på din infrastruktur – du vill inte en avisering om varje gång de körs, de kan vara ofarliga försöker helt, men kanske du vill ta en titt på frågan ibland för att se om th är något annorlunda ere. 



Med Azure Sentinel jakt kan du dra nytta av följande funktioner:

- Inbyggda frågor: Om du vill komma igång, ger en startsidan förinlästa fråga exempel utformats för att komma igång och bekanta dig med tabellerna och frågespråket. Dessa inbyggda jakt frågor har utvecklats av Microsofts säkerhetsforskare kontinuerligt, att lägga till nya frågor och justera befintliga frågor så att du får en startpunkt för att söka efter nya identifieringar och ta reda på var du ska börja jaga den början av nya attacker. 

- Kraftfullt frågespråk med IntelliSense-stöd: Bygger på ett frågespråk som ger dig den flexibilitet du behöver ta jakt till nästa nivå.

- Skapa din egen bokmärken: Du kan träffa på matchningar eller resultat, instrumentpaneler eller aktiviteter som ser ut ovanliga eller misstänkta under jakt-processen. För att markera de objekt så att du kan gå tillbaka till dem i framtiden, använder du funktionen bokmärket. Bokmärken kan du spara objekt för senare, som används för att skapa ett ärende för undersökning. Mer information om bokmärken finns i använda [bokmärken i jakt].

- Använda anteckningsböcker för att automatisera undersökning: Anteckningsböcker liknar stegvisa spelböcker som du kan skapa för att gå igenom stegen för en undersökning och jakt.  Anteckningsböcker kapslar in alla jakt steg i en återanvändbara spelbok som kan delas med andra i din organisation. 
- Fråga för lagrade data: Data är tillgängliga i tabeller som du kan fråga. Du kan till exempel fråga processgenerering, DNS-händelser och många andra händelsetyper.

- Länkar till communityn: Dra nytta av större communityn att hitta ytterligare frågor och datakällor.
 
## <a name="get-started-hunting"></a>Kom igång fiske

1. Sentinel-Azure-portalen klickar du på **jakt**.
  ![Azure Sentinel startar jakt](media/tutorial-hunting/hunting-start.png)

2. När du öppnar den **jakt** alla jakt frågorna visas i en enda tabell. Tabellen innehåller alla frågor som skrivits av Microsofts team med säkerhetsanalytiker samt eventuella ytterligare frågan du skapas eller ändras. Varje fråga innehåller en beskrivning av vad det hunts för och vilken typ av data som den körs på. Dessa mallar är grupperade efter sina olika taktik - ikonerna till höger kategorisera typ av hot, till exempel initial åtkomst, persistence och exfiltrering. Du kan filtrera mallarna jakt fråga med hjälp av något av fälten. Du kan spara alla frågor till dina Favoriter. Genom att spara en fråga till dina Favoriter, kör frågan automatiskt varje gång den **jakt** komma åt sidan. Du kan skapa egna jakt fråga eller klona och anpassa en befintlig mall för jakt fråga. 
 
2. Klicka på **Kör fråga** i jakten Frågedetaljer sidan om du vill köra en fråga utan att lämna sidan jakt.  Antal matchningar visas i tabellen. Granska listan över jakt frågor och deras matchningar. Kolla in vilken fas i kedjan kill matchningen är associerad med.

3. Utföra en snabb genomgång av den underliggande frågan i informationsfönstret eller klicka på **Visa frågeresultat** att öppna frågan i Log Analytics. Granska matchningarna för frågan längst ned.

4.  Klicka på raden och välj **Lägg till bokmärke** att lägga till rader undersökas – du kan göra detta för sådant som ser misstänkt ut. 

5. Gå sedan tillbaka till huvudfönstret **jakt** och klicka på den **bokmärken** fliken för att se alla misstänkta aktiviteter. 

6. Välj ett bokmärke och klicka sedan på **Undersök** att öppna felsökningsfunktionen. Du kan filtrera bokmärken. Om du undersöker en kampanj kan du skapa en tagg för kampanjen och sedan filtrera alla bokmärken som baseras på kampanjen.

1. Du kan också skapa anpassade identifiering regler baserat på din fråga och visa upp dessa insikter som varningar till din security incident svarare när du upptäckt vilken jakt fråga ger värdefulla insikter om eventuella attacker.

 

## <a name="query-language"></a>Frågespråk 

Jakt i Azure Sentinel baseras på Azure Log Analytics-frågespråket. Mer information om frågespråk och operatorer som stöds finns i [Frågespråksreferens](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Offentliga jakt fråga GitHub-lagringsplats

Kolla in den [jakt fråga databasen](https://github.com/Azure/Orion). Bidra med och använda exempelfrågor som delas av våra kunder.

 

## <a name="sample-query"></a>Exempelfråga

En typisk frågan börjar med ett tabellnamn följt av en serie operatörer avgränsade med \|.

Namnge SecurityEvent i exemplet ovan start med tabell- och lägga till via rörledningar element efter behov.

1. Definiera ett tidsfilter för att granska bara poster från de senaste sju dagarna.

2. Lägg till ett filter i frågan för att endast visa händelse-ID 4688.

3. Lägg till ett filter i frågan på kommandoraden innehåller bara instanser av cscript.exe.

4. Projektet endast de kolumner som du är intresserad av att utforska och begränsa resultaten till 1000 och på **Kör fråga**.
5. Klicka på grön triangel och köra frågan. Du kan testa frågan och kör det du söker efter avvikande beteende.

## <a name="useful-operators"></a>Användbara operatorer

Frågespråket är kraftfulla och har många tillgängliga operatörer, vissa användbara operatorer visas här:

**där** -filtrera en tabell till underuppsättningen av rader som uppfyller ett predikat.

**Sammanfatta** – skapa en tabell som sammanställer innehållet i indatatabellen.

**Anslut till** -sammanfogar raderna i två tabeller för att skapa en ny tabell genom att matcha värdena för den eller de angivna kolumnerna från varje tabell.

**antal** -returnerar antalet poster i den inkommande uppsättningen av poster.

**översta** -returnera den första N poster sorterade efter de angivna kolumnerna.

**gränsen** -gå tillbaka upp till det angivna antalet rader.

**projektet** – Markera kolumnerna som ska tas med, byta namn på eller ta bort och infoga nya beräknade kolumner.

**utöka** – skapa beräknade kolumner och lägga till dem i resultatmängden.

**makeset** -returnerar en dynamisk (JSON)-matris av en uppsättning distinkta värden som uttryck tar i gruppen

**hitta** -hitta rader som matchar ett predikat över en uppsättning tabeller.

## <a name="save-a-query"></a>Spara en fråga

Du kan skapa eller ändra en fråga och spara den som en egen fråga eller dela den med användare som ingår i samma klient.

   ![Spara fråga](./media/tutorial-hunting/save-query.png)

Skapa en ny jakt-fråga:

1. Klicka på **ny fråga** och välj **spara**.
2. Fyll i alla tomma fält och välj **spara**.

   ![Ny fråga](./media/tutorial-hunting/new-query.png)

Klona och ändra en befintlig jakt-fråga:

1. Välj frågan som jakt i tabellen som du vill ändra.
2. Välj ellipsen (...) i raden för den fråga du vill ändra och välj **klona frågan**.

   ![Klona frågan](./media/tutorial-hunting/clone-query.png)
 

3. Ändra frågan och välj **skapa**.

   ![anpassad fråga](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du kör en jakt undersökning med Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:


- [Använda anteckningsböcker för att köra automatiserade jakt kampanjer](notebooks.md)
- [Använda bokmärken för att spara intressant information vid jakt](bookmarks.md)