---
title: Jaktfunktioner i Azure Sentinel| Microsoft-dokument
description: I den här artikeln beskrivs hur du använder azure sentinel-jaktfunktionerna.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: 52af688917aa531d125f83844df29a988ed7cb7e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686636"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Jakten på hot med Azure Sentinel

Om du är en utredare som vill vara proaktiv när det gäller att leta efter säkerhetshot kan Azure Sentinel kraftfulla jaktsöknings- och frågeverktyg söka efter säkerhetshot i organisationens datakällor. Men dina system och säkerhetsapparater genererar berg av data som kan vara svåra att tolka och filtrera till meningsfulla händelser. För att hjälpa säkerhetsanalytiker att proaktivt leta efter nya avvikelser som inte har upptäckts av dina säkerhetsappar, hjälper Azure Sentinels inbyggda jaktfrågor dig att ställa rätt frågor för att hitta problem i de data du redan har i nätverket. 

En inbyggd fråga innehåller till exempel data om de mest ovanliga processerna som körs på infrastrukturen – du vill inte ha en avisering om varje gång de körs, de kan vara helt oskyldiga, men du kanske vill ta en titt på frågan ibland för att se om det är något ovanligt. 



Med Azure Sentinel-jakt kan du dra nytta av följande funktioner:

- Inbyggda frågor: För att komma igång innehåller en startsida förinläst frågeexempel som är utformade för att komma igång och bekanta dig med tabellerna och frågespråket. Dessa inbyggda jaktfrågor utvecklas kontinuerligt av Microsofts säkerhetsforskare, lägger till nya frågor och finjusterar befintliga frågor för att ge dig en startpunkt för att leta efter nya identifieringar och ta reda på var du ska börja jaga efter början av nya attacker. 

- Kraftfullt frågespråk med IntelliSense: Byggt ovanpå ett frågespråk som ger dig den flexibilitet du behöver för att ta jakt till nästa nivå.

- Skapa egna bokmärken: Under jaktprocessen kan du stöta på matchningar eller resultat, instrumentpaneler eller aktiviteter som ser ovanliga eller misstänkta ut. För att markera dessa objekt så att du kan komma tillbaka till dem i framtiden, använd bokmärke funktionalitet. Med bokmärken kan du spara objekt för senare, som ska användas för att skapa en incident för undersökning. Mer information om bokmärken finns [i Använda bokmärken i jakt](hunting.md).
- Använd anteckningsböcker för att automatisera undersökningen: Anteckningsböcker är som steg-för-steg-spelböcker som du kan skapa för att gå igenom stegen i en undersökning och jakt.  Anteckningsböcker kapslar in alla jaktsteg i en återanvändbar spelbok som kan delas med andra i organisationen. 
- Fråga de lagrade data: Data är tillgängliga i tabeller som du kan fråga efter. Du kan till exempel fråga om att skapa processer, DNS-händelser och många andra händelsetyper.

- Länkar till community: Utnyttja kraften i den större communityn för att hitta ytterligare frågor och datakällor.
 
## <a name="get-started-hunting"></a>Kom igång med jakt

1. Klicka på **Jakt**i Azure Sentinel-portalen .
  ![Azure Sentinel börjar jaga](media/tutorial-hunting/hunting-start.png)

2. När du öppnar sidan **Jakt** visas alla jaktfrågor i en enda tabell. Tabellen visar alla frågor som skrivits av Microsofts team av säkerhetsanalytiker samt eventuella ytterligare frågor som du har skapat eller ändrat. Varje fråga ger en beskrivning av vad den jagar efter och vilken typ av data den körs på. Dessa mallar är grupperade efter deras olika taktik - ikonerna till höger kategoriserar vilken typ av hot, till exempel inledande åtkomst, uthållighet och exfiltration. Du kan filtrera dessa jaktfrågemallar med något av fälten. Du kan spara alla frågor i dina favoriter. Genom att spara en fråga till dina favoriter körs frågan automatiskt varje gång **jaktsidan** används. Du kan skapa en egen jaktfråga eller klona och anpassa en befintlig jaktfrågemall. 
 
2. Klicka på **Kör fråga** på sidan jaktfrågainformation om du vill köra en fråga utan att lämna jaktsidan.  Antalet träffar visas i tabellen. Gå igenom listan över jaktfrågor och deras matchningar. Kolla in vilket skede i dödskedjan matchen är förknippad med.

3. Gör en snabb granskning av den underliggande frågan i frågeinformationsfönstret eller klicka på **Visa frågeresultat** för att öppna frågan i Logganalys. Granska matchningarna för frågan längst ned.

4.    Klicka på raden och välj **Lägg till bokmärke** för att lägga till de rader som ska undersökas - du kan göra detta för allt som ser misstänkt ut. 

5. Gå sedan tillbaka till **huvudsidan Jakt** och klicka på fliken **Bokmärken** för att se alla misstänkta aktiviteter. 

6. Välj ett bokmärke och klicka sedan på **Undersök** för att öppna undersökningsupplevelsen. Du kan filtrera bokmärkena. Om du till exempel undersöker en kampanj kan du skapa en tagg för kampanjen och sedan filtrera alla bokmärken baserat på kampanjen.

1. När du har upptäckt vilken jaktfråga som ger insikter om högt värde i möjliga attacker kan du också skapa anpassade identifieringsregler baserat på din fråga och visa dessa insikter som aviseringar till dina säkerhetsincidentsvarare.

 

## <a name="query-language"></a>Frågespråk 

Jakt i Azure Sentinel baseras på Kusto-frågespråk. Mer information om frågespråket och operatorer som stöds finns i [Fråga språkreferens](/azure/azure-monitor/log-query/get-started-queries).

## <a name="public-hunting-query-github-repository"></a>Fråga om offentlig jakt fråga GitHub-databas

Kolla in [jaktfrågedatabasen](https://github.com/Azure/Orion). Bidra och använd exempelfrågor som delas av våra kunder.

 

## <a name="sample-query"></a>Exempelfråga

En typisk fråga börjar med ett tabellnamn följt \|av en serie operatorer avgränsade med .

I exemplet ovan börjar du med tabellnamnet SecurityEvent och lägger till rörelement efter behov.

1. Definiera ett tidsfilter om du bara vill granska poster från de föregående sju dagarna.

2. Lägg till ett filter i frågan för att bara visa händelse-ID 4688.

3. Lägg till ett filter i frågan på Kommandoraden om du bara vill innehålla instanser av cscript.exe.

4. Bara de kolumner som du är intresserad av att utforska och begränsa resultaten till 1 000 och klicka på **Kör fråga**.
5. Klicka på den gröna triangeln och kör frågan. Du kan testa frågan och köra den för att leta efter avvikande beteende.

## <a name="useful-operators"></a>Användbara operatorer

Frågespråket är kraftfullt och har många tillgängliga operatorer, vissa användbara operatorer listas här:

**där** - Filtrera en tabell till den delmängd av rader som uppfyller ett predikat.

**sammanfatta** - Skapa en tabell som sammanställer innehållet i indatatabellen.

**join** - Sammanfoga raderna med två tabeller för att skapa en ny tabell genom att matcha värdena för de angivna kolumnerna från varje tabell.

**antal** - Returnera antalet poster i indatapostuppsättningen.

**överst** - Returnera de första N-posterna sorterade efter de angivna kolumnerna.

**gräns** - Returnera upp till angivet antal rader.

**projekt** - Markera de kolumner som ska inkluderas, byta namn på eller släppa och infoga nya beräknade kolumner.

**utöka** - Skapa beräknade kolumner och lägg till dem i resultatuppsättningen.

**makeset** - Returnera en dynamisk (JSON) matris av uppsättningen distinkta värden som Expr tar i gruppen

**hitta** - Hitta rader som matchar ett predikat över en uppsättning tabeller.

## <a name="save-a-query"></a>Spara en fråga

Du kan skapa eller ändra en fråga och spara den som din egen fråga eller dela den med användare som finns i samma klientorganisation.

   ![Spara fråga](./media/tutorial-hunting/save-query.png)

Skapa en ny jaktfråga:

1. Klicka på **Ny fråga** och välj **Spara**.
2. Fyll i alla tomma fält och välj **Spara**.

   ![Ny fråga](./media/tutorial-hunting/new-query.png)

Klona och ändra en befintlig jaktfråga:

1. Markera jaktfrågan i tabellen som du vill ändra.
2. Markera ellipsen (...) på raden i frågan som du vill ändra och välj **Klonfrågan**.

   ![klona fråga](./media/tutorial-hunting/clone-query.png)
 

3. Ändra frågan och välj **Skapa**.

   ![anpassad fråga](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du kör en jaktundersökning med Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:


- [Använda anteckningsböcker för att köra automatiserade jaktkampanjer](notebooks.md)
- [Använd bokmärken för att spara intressant information under jakt](bookmarks.md)
