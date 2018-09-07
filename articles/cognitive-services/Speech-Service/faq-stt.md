---
title: Vanliga frågor och svar om tal till Text-tjänst i Azure
description: Få svar på de mest populära frågorna om tal till Text-tjänsten.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 31515d6867fc5524df1b081932dd2a28b0cf989c
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022166"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Vanliga och frågor svar om tal till Text

Om du inte hittar svar på dina frågor i den här vanliga frågor och svar kan du kolla [andra supportalternativ](support.md).

## <a name="general"></a>Allmänt

**F: Vad är skillnaden mellan en baslinje-modell och ett anpassat tal till Text-modellen?**

**En**: en baslinje-modell som har tränats med hjälp av data som ägs av Microsoft och redan har distribuerats i molnet.  Du kan använda en anpassad modell för att anpassa en modell för att bättre passa för en viss miljö som har specifika omgivande ljuden eller språk. Oavsett, bilar eller störningar gator kräver en anpassad akustisk modell. Ämnen som biologi, fysik, radiologi, produktnamn och anpassade förkortningar kräver en anpassad språkmodell.

**F: var börjar jag om jag vill använda en baslinje-modell?**

**En**: hämta först en [prenumerationsnyckel](get-started.md). Om du vill göra REST-anrop till predeployed basmodeller finns i den [REST API: er](rest-apis.md). Om du vill använda WebSockets, [ladda ned SDK](speech-sdk.md).

**F: jag alltid behöver skapa en anpassad talmodell?**

**En**: Nej. Om programmet använder allmän dagliga språk, behöver du inte att anpassa en modell. Om ditt program används i en miljö där det finns lite eller ingen bakgrundsljud, behöver du inte anpassa en modell. 

Du kan distribuera baslinje- och anpassade modeller i portalen och sedan köra precisionstester mot dem. Du kan använda den här funktionen för att mäta det arbete du utfört en baslinje-modell jämfört med en anpassad modell.

**F: hur vet jag när bearbetningen för min datauppsättning eller en modell är klar?**

**En**: för närvarande status för modellen eller datauppsättningen i tabellen är det enda sättet du behöver veta. När bearbetningen är klar, är status **lyckades**.

**F: kan jag skapa mer än en modell?**

**En**: det finns ingen gräns för hur många modeller som du kan ha i din samling.

**F: jag insåg att jag har gjort ett misstag. Hur avbryter mitt dataimporten jag eller modellera skapa pågående?**

**En**: för närvarande kan du inte återställa en akustiska eller språk anpassning process. Du kan ta bort importerade data och modeller när de är i ett avslutat tillstånd.

**F: Vad är skillnaden mellan modell för sökning och diktering och Konversationsanpassade modellen?**

**En**: du kan välja bland fler än en baslinje-modell i Speech-tjänsten. Konversationsanpassad modellen är användbart för att känna igen tal som sägs i ett konversationsanpassade format. Den här modellen är perfekt för att skriva av telefonsamtal. Sök och diktering modellen är perfekt för röst-utlöst appar. Universal modellen är en ny modell för att hantera både scenarier.

**F: kan jag uppdatera min befintliga modellen (model stapling)?**

**En**: du kan inte uppdatera en befintlig modell. Kombinera den gamla datamängden med den nya datauppsättningen och readapt som en lösning.

Gammal datauppsättning och den nya datauppsättningen måste kombineras i en enda .zip-fil (för akustiska data) eller i en txt-fil (för språk data). När anpassningen är klar visas måste den nya, uppdaterade modellen distribueras för att få en ny slutpunkt

**F: Vad händer om jag behöver högre samtidighet för min distribuerad modell än vad som är tillgängligt i portalen?** 

**En**: du kan skala upp din modell i steg om 20 samtidiga begäranden. 

Kontakta oss om du behöver en högre skala.

**F: kan jag hämta Mina modellen och köra den lokalt?**

**En**: modeller kan inte hämtas och köras lokalt.

**F: är Mina förfrågningar loggas?**

**En**: du kan välja när du skapar en distribution för att stänga av spårning. I det här läget kommer utan ljud eller avskrifter att loggas. I annat fall loggas vanligtvis begäranden i Azure i säker lagring. 

Om du har fler sekretessfrågor som förhindrar att du använder med custom Speech service kan du kontakta någon av supportkanalerna.

## <a name="importing-data"></a>Importera data

**F: Vad är gränsen för storleken på en datauppsättning och varför är det gränsen?**

**En**: den aktuella gränsen för en datauppsättning är 2 GB. Gränsen är på grund av begränsningen på storleken på en fil för HTTP-överföring. 

**F: kan jag zip min textfiler, så jag kan ladda upp en större textfil?** 

**En**: Nej. För närvarande tillåts bara okomprimerade textfiler.

**F: data rapporten säger det fanns misslyckade yttranden. Vad är problemet?**

**En**: misslyckas att överföra 100 procent av uttryck i en fil är inte ett problem. Om det stora flertalet yttranden i en språkdata eller språk datauppsättning (till exempel, mer än 95 procent) har importerats, kan datauppsättningen användas. Vi rekommenderar dock att du försöker förstå varför talade misslyckades och åtgärda problem. De vanligaste problem, till exempel formateringsfel, är enkla att åtgärda. 

## <a name="creating-an-acoustic-model"></a>Skapa en akustisk modell

**F: hur mycket akustiska data behöver jag?**

**En**: Vi rekommenderar att du börjar med mellan 30 minuter och en timmes akustiska data.

**F: vilka data bör jag samla in?**

**En**: samla in data som ligger så nära programmet scenario och användningsfall som möjligt. Insamling av data ska matcha målprogrammet och användare när det gäller enheter eller enheter, miljöer och typer av talare. I allmänhet du ska samla in data från som bred olika talare som möjligt. 

**F: hur ska jag samla in akustiska data?**

**En**: du kan skapa ett fristående program för insamling av data eller använda kablingen ljudinspelning programvara. Du kan också skapa en version av programmet som loggar ljuddata och använder sedan data. 

**F: Jag behöver transkribera anpassning data mig?**

**En**: Ja! Du kan transkribera själv eller använda en professionell avskrift-tjänst. Vissa användare föredrar journalanteckningarna och andra använda gemensamt skapade eller göra avskrifter själva.

## <a name="accuracy-testing"></a>Precisionstest

**F: kan jag utföra offline testning av min anpassad akustisk modell med hjälp av en anpassad språkmodell?**

**En**: Ja, bara välja modellen med anpassat språk i den nedrullningsbara menyn när du ställer in offline testet.

**F: kan jag utföra offline testning av mina anpassade språkmodell genom att använda en anpassad akustisk modell?**

**En**: Ja, markera bara anpassad akustisk modell i den nedrullningsbara menyn när du ställer in offline testet.

**F: Vad är ordet Felfrekvens (WER) och hur beräknas den?**

**En**: WER är utvärderingen mått för taligenkänning. WER räknas som det totala antalet fel, vilket innefattar tillägg, borttagningar och ersättningar, dividerat med det totala antalet ord i referens avskrift. Mer information finns i [word Felfrekvens](https://en.wikipedia.org/wiki/Word_error_rate).

**F: hur tar jag reda på om resultatet av ett test av Precision är bra?**

**En**: resultatet visar en jämförelse mellan baslinje-modellen och modellen du anpassat. Du bör syfta till att Microsoft baseline-modellen för att göra anpassning lönar.

**F: hur vet jag WER av en grundläggande modell så att jag kan se om det var en förbättring av?** 

**En**: offline testresultaten visa baslinje riktighet anpassade modellen och förbättring över baslinje.

## <a name="creating-a-language-model"></a>Skapa en språkmodell

**F: hur mycket textdata behöver jag att ladda upp?**

**En**: Det beror på hur olika ordförråd och fraser som används i ditt program är från från språkmodeller. För alla nya ord är det praktiskt att ange så många exempel som möjligt av användningen av orden. För vanliga fraser som används i ditt program, är inklusive fraser i språk data också användbart eftersom det anger att även lyssna efter dessa villkor. Det är vanligt att ha minst 100 och vanligtvis flera hundra eller flera uttryck i datauppsättningen språk. Även om vissa typer av frågor som förväntas vara vanligare än andra, kan du infoga flera kopior av de vanliga frågorna i datauppsättningen.

**F: kan jag bara ladda upp en lista över ord?**

**En**: ladda upp en lista över ord läggs orden vokabulär, men det kommer inte Lär systemet hur orden används vanligtvis för. Genom att tillhandahålla fullständigt eller partiellt yttranden (meningar eller satser saker som användare kan komma att säga), kan språkmodellen lära dig nya ord och hur de används. Anpassade språkmodellen är bra inte bara för att lägga till nya ord i systemet, utan också för att ändra sannolikheten för kända ord för ditt program. Att tillhandahålla fullständig yttranden hjälper systemet lär du dig bättre. 

## <a name="next-steps"></a>Nästa steg

* [Felsökning](troubleshooting.md)
* [Viktig information](releasenotes.md)
