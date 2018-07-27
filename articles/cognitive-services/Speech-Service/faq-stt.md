---
title: Vanliga frågor om tal till Text-tjänst på Azure
description: Här är några svar på de mest populära frågorna om tal till Text.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: e5ba01c25646578da22f054659051be3515e9e4b
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281838"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Vanliga och frågor svar om tal till Text

Om du inte hittar svar på dina frågor i den här vanliga frågor och svar kan du läsa andra supportalternativ [här](support.md).

## <a name="general"></a>Allmänt

**Fråga**: Vad är skillnaden mellan grundläggande och anpassad tal till Text modeller?

**Svar**: basmodeller har tränats med Microsoft som ägs av data och redan har distribuerats i molnet. Anpassade modeller Tillåt användaren att anpassa en modell för att bättre passa för en viss miljö med specifika omgivande ljuden eller språk. Oavsett, bilar, bort störande gator kräver anpassats akustisk modell medan specifika ämnen, till exempel biologi, fysik, radiologi, produktnamn och anpassade förkortningar kräver en anpassad språkmodell.

**Fråga**: hur börjar jag om jag vill använda en baslinje-modell?

**Svar**: först måste du hämta en [prenumerationsnyckel](get-started.md). Om du vill göra REST-anrop till predeployed basmodeller läser den [information här](rest-apis.md). Om du vill använda WebSockets ladda ned den [SDK](speech-sdk.md)

**Fråga**: gör I alltid behöver för att skapa en anpassad talmodell?

**Svar**: Nej, om ditt program använder allmän dagliga språk behöver du inte anpassa en modell. Dessutom om ditt program används i en miljö där det finns lite eller ingen bakgrundsljud och du behöver inte anpassa antingen. Portalen kan du distribuera baslinje- och anpassade modeller och köra precisionstester mot dessa. Användare kan använda den här funktionen för att mäta korrektheten i en baslinje vs en anpassad modell.

**Fråga**: hur vet jag när bearbetningen av min datauppsättning eller en modell är klar?

**Svar**: för närvarande status för den modellen eller en uppsättning data i tabellen är det enda sättet du behöver veta.
När bearbetningen är klar, ska status vara ”lyckades”.

**Fråga**: kan jag skapa mer än en modell?

**Svar**: det finns ingen gräns för hur många modeller som finns i din samling.

**Fråga**: jag insåg att jag har gjort ett misstag. Hur avbryter mitt dataimporten jag eller modellera skapa pågående? 

**Svar**: för närvarande du inte återställa en akustiska eller språk anpassning process. Importerade data och modeller kan tas bort när de är i ett avslutat tillstånd.

**Fråga**: Vad är skillnaden mellan sökningen & diktering modeller och Konversationsanpassade modeller?

**Svar**: det finns fler än en baslinjemodeller att välja bland i Speech-tjänsten. Konversationsanpassad modellen passar känna igen tal som sägs i ett konversationsanpassade format. Den här modellen skulle vara optimal för att skriva av anrop vid sökning och diktering är perfekt för rösten appar. Universal är en ny modell för att hantera både scenarier.

**Fråga**: kan jag uppdatera min befintliga modellen (model stapling)?

**Svar**: Det går inte att uppdatera befintliga modeller. Kombinera den gamla datamängden med den nya och readapt som en lösning.

De gamla och nya uppgifter som måste kombineras i en enda ZIP (om det är akustiska data) eller en txt-fil om den är språk-data. En gång anpassning görs nya uppdaterade modellen måste vara ta bort distribuerade att hämta en ny slutpunkt

**Fråga**: Vad händer om jag behöver högre samtidighet för min distribuerad modell än vad som är tillgängligt i portalen. 

**Svar**: du kan skala upp din modell i steg om 20 samtidiga begäranden. 

Kontakta oss om du behöver hög skala.

**Fråga**: kan jag hämta Mina modellen och köra den lokalt?

**Svar**: modeller kan inte hämtas och köras lokalt.

**Fråga**: är Mina förfrågningar loggas?

**Svar**: du kan välja när du skapar en distribution för att stänga av spårning, då ingen ljud eller avskrifter kommer att loggas. Annars är begäranden vanligtvis inloggad i Azure säker lagring. Om du har fler sekretessen som förhindrar med Custom Speech Service kan du kontakta någon av supportkanalerna.

## <a name="importing-data"></a>Importera Data

**Fråga**: Vad är gränsen för storleken på datauppsättningen? Varför? 

**Svar**: den aktuella gränsen för en datauppsättning är 2 GB på grund av begränsningen på storleken på en fil för HTTP-överföring. 

**Fråga**: kan jag zip min textfiler för att ladda upp en större textfil? 

**Svar**: Nej, för närvarande endast okomprimerade textfiler tillåts.

**Fråga**: data rapporten står det fanns misslyckade yttranden. Vad är problemet?

**Svar**: misslyckas att överföra 100% av uttryck i en fil är inte ett problem.
Om det stora flertalet yttranden i en språkdata eller språk data (till exempel > 95%) har importerats, kan datauppsättningen vara användbar. Vi rekommenderar dock att du försöker förstå varför talade misslyckades och åtgärda problem. De vanligaste problem, till exempel formateringsfel, är enkla att åtgärda. 

## <a name="creating-am"></a>Skapa AM

**Fråga**: hur mycket akustiska data behöver jag?

**Svar**: Vi rekommenderar att du börjar med 30 minuter att en timmes akustiska data.

**Fråga**: vilka data bör jag samla in?

**Svar**: samla in data som ligger så nära programmet scenario och användningsfall som möjligt.
Insamling av data ska matcha målprogrammet och användare när det gäller enheter eller enheter, miljöer och typer av talare. I allmänhet du ska samla in data från som bred olika talare som möjligt. 

**Fråga**: hur ska jag samla in den? 

**Svar**: du kan skapa ett fristående program för insamling av data eller använda en del av hylla ljudinspelning programmet.
Du kan också skapa en version av programmet som loggar ljuddata och används den. 

**Fråga**: behöver jag transkribera anpassning data mig? 

**Svar**: Ja! Du kan transkribera själv eller använda en professionell avskrift-tjänst. Vissa användare föredrar journalanteckningarna medan andra använder gemensamt skapade eller göra avskrifter själva.

## <a name="accuracy-testing"></a>Precisionstest

**Fråga**: kan jag utföra offline testning av min anpassad akustisk modell med hjälp av en anpassad språkmodell?

**Svar**: Ja, bara välja modellen med anpassat språk i listrutan när du ställer in offline testet

**Fråga**: kan jag utföra offline testning av mina anpassade språkmodell med hjälp av en anpassad akustisk modell?

**Svar**: Ja, markera bara anpassad akustisk modell i den nedrullningsbara menyn när du ställer in offline testet.

**Fråga**: Vad är priset för Word-fel (WER) och hur beräknas den?

**Svar**: Word fel hastighet (WER) är utvärderingen mått för taligenkänning. Räknas den som det totala antalet fel, vilket innefattar tillägg, borttagningar och ersättningar, dividerat med det totala antalet ord i referens avskrift. Mer information finns [här](https://en.wikipedia.org/wiki/Word_error_rate).

**Fråga**: hur tar jag reda på om resultatet av ett test av Precision är bra?

**Svar**: resultatet visar en jämförelse mellan baslinje och de som du anpassat.
Du bör syfta till beat baslinje-modellen för att göra anpassningen någonsin bortkastade pengar.

**Fråga**: hur jag ta reda på ordet Felfrekvens av de grundläggande modellerna så att jag kan se om det var improvement? 

**Svar**: offline testresultaten visa riktighet baslinje riktighet anpassade modellen och förbättring över baslinje.

## <a name="creating-lm"></a>Skapa LM

**Fråga**: hur mycket textdata kan jag överföra?

**Svar**: Det beror på hur olika ordförråd och fraser som används i ditt program är från från språkmodeller. För alla nya ord är det praktiskt att ange så många exempel som möjligt av användningen av orden. För vanliga fraser som används i ditt program, är inklusive fraser i språk data också användbart eftersom det anger att lyssna efter även dessa villkor. Det är vanligt att ha minst 100 och vanligtvis flera hundra yttranden i datauppsättningen språk eller mer. Även om vissa typer av frågor som förväntas vara vanligare än andra, kan du infoga flera kopior av de vanliga frågorna i datauppsättningen.

**Fråga**: kan bara överföra en lista över ord?

**Svar**: ladda upp en lista över ord kommer att få ord i att ordförråd men inte lära systemet hur orden används vanligtvis för.
Genom att tillhandahålla fullständigt eller partiellt yttranden (meningar eller satser saker som användare kan komma att säga) du kan lära dig nya ord språkmodellen och hur de används. Anpassade språkmodellen är bra inte bara för att hämta nya ord i systemet, men också för att ändra sannolikheten för kända ord för ditt program. Att tillhandahålla fullständig yttranden hjälper systemet lär du dig bättre. 

## <a name="next-steps"></a>Nästa steg

* [Felsökning](troubleshooting.md)
* [Viktig information](releasenotes.md)
