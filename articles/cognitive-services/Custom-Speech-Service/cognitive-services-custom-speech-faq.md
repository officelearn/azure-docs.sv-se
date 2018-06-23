---
title: Vanliga frågor om tjänsten anpassad tal i Azure | Microsoft Docs
description: Här är svaren på populäraste frågor om tjänsten anpassad tal.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: panosper
ms.openlocfilehash: a929869b36387b3257b672308ceca36c84ff8cae
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351858"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Vanliga och frågor svar om anpassade tal Service

Om du inte hittar svar på frågor här, kan du pröva att besvara anpassad tal Service-communityn på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) och [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Allmänt

**Fråga**: hur vet jag när bearbetningen av datauppsättning eller modell har slutförts?

**Svaret**: status för modellen eller datamängd i tabellen är för närvarande bara vill veta.
När bearbetningen är klar blir statusen ”klar”.
Vi arbetar på förbättrade metoder för kommunikation Bearbetningsstatus, till exempel e-postmeddelande.

**Fråga**: kan jag skapa mer än en modell i taget?

**Svaret**: det finns ingen gräns för hur många modeller finns i samlingen, men endast en kan skapas i taget på varje sida.
Exempelvis starta du inte en språk modellen processen om det finns en språkmodell i processen steget.
Du kan dock ha en ljud modell och en språkmodell bearbetning på samma gång. 

**Fråga**: jag insåg jag gjort ett misstag. Hur avbryta min import av data eller modell skapa pågår? 

**Svaret**: för närvarande du inte återställa en acoustic eller språk anpassning process.
Importerade data kan tas bort när importen har slutförts

**Fråga**: Vad är skillnaden mellan sökningen & Dictation modeller och den vardagliga samtalsuttryck?

**Svaret**: det finns två grundläggande ljud & språk modeller att välja mellan i tjänsten anpassad tal.
sökfrågor eller dictation. Microsoft Conversational AM är lämplig för igenkänning av tal talas i samtalston.
Den här typen av tal riktas vanligtvis mot en annan person som i call Center eller möten.

**Fråga**: kan jag uppdatera Mina befintliga modellen (modellen stapling)?

**Svaret**: vi inte har möjlighet att uppdatera en befintlig modell med nya data.
Om du har en ny datauppsättning och du vill anpassa en befintlig modell anpassa du nytt den med nya data och gamla datamängden som du använde.
De gamla och nya uppgifter som måste kombineras i en enda ZIP (om det är ljud data) eller en txt-fil om den är språket data en gång anpassning görs nya uppdaterade modellen måste vara Frigör distribuerade du skaffar en ny slutpunkt

**Fråga**: Vad händer om jag behöver högre samtidighet än standardvärdet. 

**Svaret**: du kan skala upp din modell i steg 5 samtidiga begäranden som vi kallar skalenheter. Varje skalningsenhet garanterar att din modell kan bearbeta 5 ljudström samtidigt. Du kan köpa till 100 skalenheter (eller 500 samtidiga begäranden).

Kontakta oss om du behöver högre än.

**Fråga**: kan jag hämta Mina modellen och kör lokalt?

**Svaret**: vi aktivera inte modeller som ska hämtas och köras lokalt.

**Fråga**: är Mina förfrågningar loggas?

**Svaret**: du kan välja när du skapar en distribution för att inaktivera spårning, då ingen ljud eller transcriptions kommer att loggas. Annars loggas vanligtvis begäranden i Azure i säker lagring. Om du har fler sekretessfrågor som förhindrar att du använder anpassade tal tjänsten du kontakta oss.

## <a name="importing-data"></a>Importera Data

**Fråga**: Vad är gränsen för storleken på datauppsättningen? Varför? 

**Svaret**: den aktuella gränsen för en datauppsättning är 2 GB, på grund av begränsning på storleken på en fil för HTTP-överföring. 

**Fråga**: kan jag zip min textfiler för att ladda upp en större textfil? 

**Svaret**: Nej, för närvarande endast okomprimerade textfiler tillåts.

**Fråga**: data rapporten står det fanns misslyckade utterances. Är detta ett problem?

**Svaret**: om endast några utterances kunde inte importeras, detta är inte ett problem.
Om majoriteten av utterances i en acoustic eller språk (t.ex. > 95%) har importerats datauppsättningen kan användas. Det rekommenderas dock att du försöker förstå varför utterances misslyckades och åtgärda problem.
De flesta vanliga problem, till exempel formateringsfel, är enkla att åtgärda. 

## <a name="creating-am"></a>Skapa AM

**Fråga**: hur mycket ljud data behöver jag?

**Svaret**: Vi rekommenderar att börja med 30 minuter till en timme ljud data

**Fråga**: vilken typ av data ska jag in?

**Svaret**: du bör samla in data som är så nära programscenariot och användningsfall som möjligt.
Det innebär att datainsamlingen ska matcha målprogrammet och användare när det gäller enheter eller enheter, miljöer och typer av högtalare. I allmänhet du bör samla in data från som bred en uppsättning användare enligt möjligt. 

**Fråga**: hur får jag in den? 

**Svaret**: du kan skapa ett fristående program för insamling av data eller använda en del av hylla ljudinspelning programmet.
Du kan också skapa en version av programmet som loggar ljuddata och används den. 

**Fråga**: behöver jag transkribera anpassning data själv? 

**Svaret**: data måste vara sätt. Du kan transkribera själv eller använda en professional skrivfel-tjänst. Vissa av dessa Använd journalanteckningarna och andra använda gemensamt skapade. Vi rekommenderar också kan en tjänst skrivfel på begäran.

**Fråga**: hur lång tid tar det för att skapa en anpassad ljud modell?

**Svaret**: bearbetningstid för att skapa en anpassad ljud modell är ungefär som längden på ljud datauppsättningen.
Så, en anpassad ljud modell som skapas från en uppsättning med fem timme data tar cirka fem timmar att bearbeta. 

## <a name="offline-testing"></a>Offlinetestning

**Fråga**: kan jag utföra offline testning av min anpassade ljud modellen med hjälp av en anpassad språk modellen?

**Svaret**: Ja, bara välja anpassade språkmodell i listrutan när du ställer in testet offline

**Fråga**: kan jag utföra offline testning av min anpassade språk modellen med hjälp av en anpassad ljud modellen?

**Svaret**: Ja, markera bara anpassade ljud modellen i den nedrullningsbara menyn när du ställer in testet offline.

**Fråga**: Vad är Word Felfrekvens och hur beräknas den?

**Svaret**: Word Felfrekvens är utvärdering mått för taligenkänning. Den räknas som det totala antalet fel, vilket innefattar tillägg, borttagningar och ersättningar, dividerat med det totala antalet ord i referens skrivfel.

**Fråga**: jag nu vet testresultaten i mina egna modellen, är det en bra eller dålig tal?

**Svaret**: resultaten visar en jämförelse mellan baslinje och ett som du anpassat.
Du bör sträva efter att slå baslinje-modellen för att göra anpassningen lönar

**Fråga**: hur jag räkna ut WER grundläggande modeller så att jag kan se om det fanns improvement? 

**Svaret**: offline testresultaten visar riktighet baslinjen riktighet anpassade modellen och förbättring under baslinjen

## <a name="creating-lm"></a>Skapa LM

**Fråga**: hur mycket textdata som måste du överföra?

**Svaret**: handlar det svårt att ge ett exakt svar, eftersom det beror på hur olika ordförråd och fraser som används i ditt program är från början språk-modeller. För alla nya ord är det praktiskt att ge så många exempel som möjligt för förbrukning av orden. För vanliga fraser som används i ditt program, är inklusive de på språket data också användbart eftersom den anger att lyssna efter även dessa villkor.
Det är vanligt att ha minst 100 och vanligtvis flera hundra utterances i datauppsättningen språk eller mer.
Även om vissa typer * av frågor förväntas vara vanligare än andra, du kan infoga flera kopior av vanliga frågor i datauppsättningen.

**Fråga**: kan jag bara ladda upp en lista över ord?

**Svaret**: ladda upp en lista över ord kommer att hämta orden i ordförråd men inte lära systemet hur orden normalt används.
Genom att tillhandahålla fullständig eller partiell utterances (meningar eller fraser av saker som användare kan komma att säga) Läs språk modellen nya ord och hur de används. Anpassade språk modellen är bra inte bara för att hämta nya ord i systemet, men också för att justera sannolikheten för kända ord för ditt program. Ger fullständig utterances hjälper Läs det här systemet. 

-----

 * [Översikt](cognitive-services-custom-speech-home.md)
 * [Igång](cognitive-services-custom-speech-get-started.md)
 * [Ordlista](cognitive-services-custom-speech-glossary.md)
