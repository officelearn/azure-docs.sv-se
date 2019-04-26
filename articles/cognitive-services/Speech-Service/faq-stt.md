---
title: Vanliga frågor och svar om tal till Text-tjänst i Azure
titleSuffix: Azure Cognitive Services
description: Få svar på de mest populära frågorna om tal till Text-tjänsten.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 06/11/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: 29ec50a1d2e0aebb1aeb73b38c662f4a18624754
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60539068"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Vanliga och frågor svar om tal till Text

Om du inte hittar svar på dina frågor i den här vanliga frågor och svar kan du kolla [andra supportalternativ](support.md).

## <a name="general"></a>Allmänt

**F: Vad är skillnaden mellan en baslinje-modell och ett anpassat tal till Text-modellen?**

**S**: En baslinje-modellen har tränats med hjälp av data som ägs av Microsoft och redan har distribuerats i molnet.  Du kan använda en anpassad modell för att anpassa en modell för att bättre passa för en viss miljö som har specifika omgivande ljuden eller språk. Oavsett, bilar eller störningar gator kräver en anpassad akustisk modell. Ämnen som biologi, fysik, radiologi, produktnamn och anpassade förkortningar kräver en anpassad språkmodell.

**F: Hur börjar jag om jag vill använda en baslinje-modell?**

**S**: Hämta först en [prenumerationsnyckel](get-started.md). Om du vill göra REST-anrop till predeployed basmodeller finns i den [REST API: er](rest-apis.md). Om du vill använda WebSockets, [ladda ned SDK](speech-sdk.md).

**F: Behöver jag alltid skapa en anpassad talmodell?**

**S**: Nej. Om programmet använder allmän dagliga språk, behöver du inte att anpassa en modell. Om ditt program används i en miljö där det finns lite eller ingen bakgrundsljud, behöver du inte anpassa en modell.

Du kan distribuera baslinje- och anpassade modeller i portalen och sedan köra precisionstester mot dem. Du kan använda den här funktionen för att mäta det arbete du utfört en baslinje-modell jämfört med en anpassad modell.

**F: Hur vet jag när bearbetningen för min datauppsättning eller en modell är klar?**

**S**: Status för modellen eller datauppsättningen i tabellen är för närvarande det enda sättet att veta. När bearbetningen är klar, är status **lyckades**.

**F: Kan jag skapa mer än en modell?**

**S**: Det finns ingen gräns för hur många modeller som du kan ha i din samling.

**F: Jag insåg att jag har gjort ett misstag. Hur avbryter mitt dataimporten jag eller modellera skapa pågående?**

**S**: För närvarande kan återställa du inte en akustiska eller språk anpassning process. Du kan ta bort importerade data och modeller när de är i ett avslutat tillstånd.

**F: Vad är skillnaden mellan modell för sökning och diktering och Konversationsanpassade modellen?**

**S**: Du kan välja från mer än en baslinje-modell i Speech-tjänsten. Konversationsanpassad modellen är användbart för att känna igen tal som sägs i ett konversationsanpassade format. Den här modellen är perfekt för att skriva av telefonsamtal. Sök och diktering modellen är perfekt för röst-utlöst appar. Universal modellen är en ny modell för att hantera både scenarier. Universal modellen är för närvarande vid eller över kvalitetsnivån av modellen Konversationsanpassade i de flesta länder.

**F: Kan jag uppdatera min befintliga modellen (model stapling)?**

**S**: Du kan inte uppdatera en befintlig modell. Kombinera den gamla datamängden med den nya datauppsättningen och readapt som en lösning.

Gammal datauppsättning och den nya datauppsättningen måste kombineras i en enda .zip-fil (för akustiska data) eller i en txt-fil (för språk data). När anpassningen är klar visas måste den nya, uppdaterade modellen distribueras för att få en ny slutpunkt

**F: När en ny version av en baslinje är tillgängliga är min distribution uppdateras automatiskt?**

**S**: Distributioner kommer inte att uppdateras automatiskt.

Om du har anpassat och distribuerat en modell med baslinjen V1.0, förblir den distributionen skick. Kunder kan inaktivera distribuerad modell, anpassa igen med hjälp av den nya versionen av baslinjen och omdistribuera.

**F: Vad händer om jag behöver högre samtidighet för min distribuerad modell än vad som är tillgängligt i portalen?**

**S**: Du kan skala upp din modell i steg om 20 samtidiga begäranden.

Kontakta oss om du behöver en högre skala.

**F: Kan jag hämta Mina modellen och köra den lokalt?**

**S**: Modeller kan inte hämtas och köras lokalt.

**F: Loggas Mina begäranden**

**S**: Du kan välja när du skapar en distribution för att stänga av spårning. I det här läget kommer utan ljud eller avskrifter att loggas. I annat fall loggas vanligtvis begäranden i Azure i säker lagring.

**F: Är Mina begäranden begränsas?**

**S**: REST API begränsar begäranden till 25 per 5 sekunder. Information finns i vår sidor för [tal till text](speech-to-text.md).

Om du har fler sekretessfrågor som förhindrar att du använder med custom Speech service kan du kontakta någon av supportkanalerna.

## <a name="importing-data"></a>Importera data

**F: Vad är gränsen för storleken på en datauppsättning och varför är det gränsen?**

**S**: Den aktuella gränsen för en datauppsättning är 2 GB. Gränsen är på grund av begränsningen på storleken på en fil för HTTP-överföring. 

**F: Kan jag zip min textfiler, så jag kan ladda upp en större textfil?** 

**S**: Nej. För närvarande tillåts bara okomprimerade textfiler.

**F: Rapporten data säger det fanns misslyckade yttranden. Vad är problemet?**

**S**: Misslyckas att överföra 100 procent av uttryck i en fil är inte ett problem. Om det stora flertalet yttranden i en språkdata eller språk datauppsättning (till exempel, mer än 95 procent) har importerats, kan datauppsättningen användas. Vi rekommenderar dock att du försöker förstå varför talade misslyckades och åtgärda problem. De vanligaste problem, till exempel formateringsfel, är enkla att åtgärda. 

## <a name="creating-an-acoustic-model"></a>Skapa en akustisk modell

**F: Hur mycket akustiska data behöver jag?**

**S**: Vi rekommenderar att du börjar med mellan 30 minuter och en timmes akustiska data.

**F: Vilka data bör jag samla in?**

**S**: Samla in data som är så nära programmet scenario och användningsfall som möjligt. Insamling av data ska matcha målprogrammet och användare när det gäller enheter eller enheter, miljöer och typer av talare. I allmänhet du ska samla in data från som bred olika talare som möjligt. 

**F: Hur ska jag samla in akustiska data?**

**S**: Du kan skapa ett fristående program för insamling av data eller använda kablingen ljudinspelning programvara. Du kan också skapa en version av programmet som loggar ljuddata och använder sedan data. 

**F: Behöver jag transkribera anpassning data mig?**

**S**: Visst! Du kan transkribera själv eller använda en professionell avskrift-tjänst. Vissa användare föredrar journalanteckningarna och andra använda gemensamt skapade eller göra avskrifter själva.

## <a name="accuracy-testing"></a>Precisionstest

**F: Kan jag utföra offline testning av min anpassad akustisk modell med hjälp av en anpassad språkmodell?**

**S**: Ja, markera bara anpassade språkmodellen i den nedrullningsbara menyn när du ställer in offline testet.

**F: Kan jag utföra offline testning av mina anpassade språkmodell genom att använda en anpassad akustisk modell?**

**S**: Ja, markera bara anpassad akustisk modell i den nedrullningsbara menyn när du ställer in offline testet.

**F: Vad är ordet Felfrekvens (WER) och hur beräknas den?**

**S**: WER är utvärderingen mått för taligenkänning. WER räknas som det totala antalet fel, vilket innefattar tillägg, borttagningar och ersättningar, dividerat med det totala antalet ord i referens avskrift. Mer information finns i [word Felfrekvens](https://en.wikipedia.org/wiki/Word_error_rate).

**F: Hur vet jag om resultatet av ett test av Precision är bra?**

**S**: Resultaten visar en jämförelse mellan baslinje-modellen och modellen du anpassat. Du bör syfta till att Microsoft baseline-modellen för att göra anpassning lönar.

**F: Hur vet jag WER av en grundläggande modell så att jag kan se om det var en förbättring av?** 

**S**: Resultaten av offline visa baslinje riktighet anpassade modellen och förbättring över baslinje.

## <a name="creating-a-language-model"></a>Skapa en språkmodell

**F: Hur mycket textdata behöver jag att ladda upp?**

**S**: Det beror på hur olika ordförråd och fraser som används i ditt program är från från språkmodeller. För alla nya ord är det praktiskt att ange så många exempel som möjligt av användningen av orden. För vanliga fraser som används i ditt program, är inklusive fraser i språk data också användbart eftersom det anger att även lyssna efter dessa villkor. Det är vanligt att ha minst 100 och vanligtvis flera hundra eller flera uttryck i datauppsättningen språk. Även om vissa typer av frågor som förväntas vara vanligare än andra, kan du infoga flera kopior av de vanliga frågorna i datauppsättningen.

**F: Kan jag bara ladda upp en lista över ord?**

**S**: Ladda upp en lista över ord läggs orden vokabulär, men det kommer inte Lär systemet hur orden används vanligtvis för. Genom att tillhandahålla fullständigt eller partiellt yttranden (meningar eller satser saker som användare kan komma att säga), kan språkmodellen lära dig nya ord och hur de används. Anpassade språkmodellen är bra inte bara för att lägga till nya ord i systemet, utan också för att ändra sannolikheten för kända ord för ditt program. Att tillhandahålla fullständig yttranden hjälper systemet lär du dig bättre. 

## <a name="next-steps"></a>Nästa steg

* [Felsökning](troubleshooting.md)
* [Viktig information](releasenotes.md)
