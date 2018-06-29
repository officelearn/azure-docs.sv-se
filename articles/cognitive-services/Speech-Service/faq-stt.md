---
title: Vanliga frågor om tal till Text-tjänsten i Azure | Microsoft Docs
description: Här är svaren på de mest populära frågorna om tal till Text.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 64e505889ef9472603471d67a961985c1290663a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054993"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Vanliga och frågor svar om anpassade tal Service

Om du inte hittar svar på frågor här, kan du pröva att besvara anpassad tal Service-communityn på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) och [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Allmänt

**Fråga**: Vad är skillnaden mellan grundläggande och anpassad tal till Text modeller?

**Svaret**: baslinjen modeller har tränats med Microsoft ägs data och redan har distribuerats i molnet. Anpassade modeller Tillåt användaren att anpassa en modell så att de bättre passar en viss miljö med specifika bakgrundsljud eller språk. Fabriken golv, bilar, störningar streets kräver anpassats ljud modellen medan specifika ämnen, till exempel biologi, fysik, radiologi, produktnamn och anpassade förkortningar kräver en språkmodell.

**Fråga**: där startar om du vill använda en baslinje-modell?

**Svaret**: först måste du hämta en [prenumeration nyckeln](get-started.md). Om du vill göra REST-anrop till predeployed baslinjen modeller finns i [information här](rest-apis.md). Om du vill använda WebSockets hämtning av [SDK](speech-sdk.md)

**Fråga**: gör I alltid behöver för att skapa en anpassad tal-modell?

**Svaret**: Nej, om ditt program använder generiska dagliga språk utan anpassade ordförråd eller den sällsynta sedan kanske du inte behöver anpassa en modell. Dessutom om programmet som ska användas i en miljö där det finns lite eller ingen bakgrundsljud och du inte behöver anpassa antingen. Portalen kan du distribuera grundläggande och anpassad modeller och köra noggrannhet testerna mot dessa. Användarna kan använda den här funktionen för att mäta riktighet jämfört med baslinje en anpassad modell.

**Fråga**: hur vet jag när bearbetningen av datauppsättning eller modell har slutförts?

**Svaret**: status för modellen eller datamängd i tabellen är för närvarande bara vill veta.
När bearbetningen är klar blir statusen ”klar”.

**Fråga**: kan jag skapa mer än en modell i taget?

**Svaret**: det finns ingen gräns för hur många modeller finns i samlingen, men endast en kan skapas i taget på varje sida.
Exempelvis starta du inte en språk modellen processen om det finns en språkmodell i processen steget.
Du kan dock ha en ljud modell och en språkmodell bearbetning på samma gång. 

**Fråga**: jag insåg jag gjort ett misstag. Hur avbryta min import av data eller modell skapa pågår? 

**Svaret**: för närvarande du inte återställa en process för anpassning acoustic eller språk.
Importerade data kan tas bort när importen har slutförts

**Fråga**: Vad är skillnaden mellan sökningen & Dictation modeller och den vardagliga samtalsuttryck?

**Svaret**: det finns två grundläggande ljud & språk modeller att välja mellan i tjänsten anpassad tal.
sökfrågor eller dictation. Microsoft Conversational AM är lämplig för igenkänning av tal talas i samtalston.
Den här typen av tal riktas vanligtvis mot en annan person som i call Center eller möten.

**Fråga**: kan jag uppdatera Mina befintliga modellen (modellen stapling)?

**Svaret**: Det går inte att uppdatera befintliga modeller. Kombinera den gamla datamängden med den nya och readapt som en lösning.

De gamla och nya uppgifter som måste kombineras i en enda ZIP (om det är ljud data) eller en txt-fil om den är language-data. En gång anpassning görs nya uppdaterade modellen måste vara Frigör distribuerade du skaffar en ny slutpunkt

**Fråga**: Vad händer om jag behöver högre samtidighet än standardvärdet eller vad som erbjuds i portalen. 

**Svaret**: du kan skala upp din modell i steg om 20 samtidiga begäranden. 

Kontakta oss om du vill ha ökad skala.

**Fråga**: kan jag hämta Mina modellen och kör lokalt?

**Svaret**: modeller kan hämtas och köras lokalt.

**Fråga**: är Mina förfrågningar loggas?

**Svaret**: du kan välja när du skapar en distribution för att inaktivera spårning, då ingen ljud eller transcriptions kommer att loggas. Annars loggas vanligtvis begäranden i Azure i säker lagring. Om du har fler sekretessfrågor som förhindrar att du använder anpassade tal tjänsten, kontakta någon av supportkanaler.

## <a name="importing-data"></a>Importera Data

**Fråga**: Vad är gränsen för storleken på datauppsättningen? Varför? 

**Svaret**: den aktuella gränsen för en datauppsättning är 2 GB, på grund av begränsning på storleken på en fil för HTTP-överföring. 

**Fråga**: kan jag zip min textfiler för att ladda upp en större textfil? 

**Svaret**: Nej, för närvarande endast okomprimerade textfiler tillåts.

**Fråga**: data rapporten står det fanns misslyckade utterances. Vad är problemet?

**Svaret**: inte kan överföra 100% av utterances i en fil är inte ett problem.
Om majoriteten av utterances i en acoustic eller språk (till exempel > 95%) har importerats datauppsättningen kan användas. Det rekommenderas dock att du försöker förstå varför utterances misslyckades och åtgärda problem. De flesta vanliga problem, till exempel formateringsfel, är enkla att åtgärda. 

## <a name="creating-am"></a>Skapa AM

**Fråga**: hur mycket ljud data behöver jag?

**Svaret**: Vi rekommenderar att börja med 30 minuter till en timme ljud data

**Fråga**: vilka data ska jag in?

**Svaret**: samla in data som är så nära programscenariot och användningsfall som möjligt.
Insamling av data ska matcha målprogrammet och användare när det gäller enheter eller enheter, miljöer och typer av högtalare. I allmänhet du bör samla in data från som bred en uppsättning användare enligt möjligt. 

**Fråga**: hur får jag in den? 

**Svaret**: du kan skapa ett fristående program för insamling av data eller använda en del av hylla ljudinspelning programmet.
Du kan också skapa en version av programmet som loggar ljuddata och används den. 

**Fråga**: behöver jag transkribera anpassning data själv? 

**Svaret**: data måste vara sätt. Du kan transkribera själv eller använda en professional skrivfel-tjänst. Vissa av dessa Använd journalanteckningarna och andra använda gemensamt skapade.

**Fråga**: hur lång tid tar det för att skapa en anpassad ljud modell?

**Svaret**: bearbetningstid för att skapa en anpassad ljud modell är ungefär som längden på ljud datauppsättningen.
Därför en anpassad ljud modell som skapas från en uppsättning med fem timmars data tar cirka fem timmar att bearbeta. 

## <a name="offline-testing"></a>Offlinetestning

**Fråga**: kan jag utföra offline testning av min anpassade ljud modellen med hjälp av en anpassad språk modellen?

**Svaret**: Ja, bara välja anpassade språkmodell i listrutan när du ställer in testet offline

**Fråga**: kan jag utföra offline testning av min anpassade språk modellen med hjälp av en anpassad ljud modellen?

**Svaret**: Ja, markera bara anpassade ljud modellen i den nedrullningsbara menyn när du ställer in testet offline.

**Fråga**: Vad är Word Felfrekvens och hur beräknas den?

**Svaret**: Word Felfrekvens är utvärdering mått för taligenkänning. Den räknas som det totala antalet fel, vilket innefattar tillägg, borttagningar och ersättningar, dividerat med det totala antalet ord i referens skrivfel.

**Fråga**: hur tar jag reda på om resultatet av test av en är bra?

**Svaret**: resultaten visar en jämförelse mellan baslinje och ett som du anpassat.
Du bör sträva efter att slå baslinje-modellen för att göra anpassningen lönar

**Fråga**: hur jag räkna ut WER grundläggande modeller så att jag kan se om det fanns improvement? 

**Svaret**: offline testresultaten visar riktighet baslinjen riktighet anpassade modellen och förbättring under baslinjen

## <a name="creating-lm"></a>Skapa LM

**Fråga**: hur mycket textdata som måste du överföra?

**Svaret**: Det beror på hur olika ordförråd och fraser som används i ditt program är från början språk-modeller. För alla nya ord är det praktiskt att ge så många exempel som möjligt för förbrukning av orden. För vanliga fraser som används i ditt program, är inklusive fraser i språk data också användbart eftersom den anger att lyssna efter även dessa villkor. Det är vanligt att ha minst en 100 och vanligtvis flera hundra utterances i datauppsättningen språk eller mer. Även om vissa typer av frågor förväntas vara vanligare än andra, kan du infoga flera kopior av vanliga frågor i datauppsättningen.

**Fråga**: kan jag bara ladda upp en lista över ord?

**Svaret**: ladda upp en lista över ord kommer att hämta orden i ordförråd men inte lära systemet hur orden normalt används.
Genom att tillhandahålla fullständig eller partiell utterances (meningar eller fraser av saker som användare kan komma att säga) du kan lära dig nya ord språk modellen och hur de används. Anpassade språk modellen är bra inte bara för att hämta nya ord i systemet, men också för att justera sannolikheten för kända ord för ditt program. Ger fullständig utterances hjälper systemet lär du dig bättre. 

## <a name="next-steps"></a>Nästa steg

* [Felsökning](troubleshooting.md)
* [Viktig information](releasenotes.md)
