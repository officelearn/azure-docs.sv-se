---
title: Vanliga frågor om tal till text-tjänsten i Azure
titleSuffix: Azure Cognitive Services
description: Få svar på de vanligaste frågorna om tal till text-tjänsten.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: bde68a70ac047433e86b7e06bc5f4a56bdd28595
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028508"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Vanliga frågor och svar om tal till text

Om du inte kan hitta svar på dina frågor i dessa vanliga frågor och svar kan du titta närmare på [andra support alternativ](support.md).

## <a name="general"></a>Allmänt

**F: Vad är skillnaden mellan en bas linje modell och en anpassad tal till text modellen?**

**S**: En bas linje modell har tränats med Microsoft-ägda data och har redan distribuerats i molnet.  Du kan använda en anpassad modell för att anpassa en modell så att den bättre passar en specifik miljö som har ett speciellt omgivnings brus eller språk. I fabriks golv, bilar eller störningar i gator krävs en anpassad akustisk modell. Ämnen som biologi, fysik, Radiology, produkt namn och anpassade akronymer kräver en anpassad språk modell.

**F: Var börjar jag om jag vill använda en bas linje modell?**

**S**: Börja med att hämta en [prenumerations nyckel](get-started.md). Om du vill göra REST-anrop till de fördistribuerade bas linje modellerna kan du läsa mer i [REST-API: erna](rest-apis.md). Om du vill använda WebSockets laddar du [ned SDK: n](speech-sdk.md).

**F: Behöver jag alltid skapa en anpassad tal modell?**

**S**: Nej. Om programmet använder generisk, dagligt språk behöver du inte anpassa en modell. Om programmet används i en miljö där det inte finns något eller något bakgrunds brus behöver du inte anpassa en modell.

Du kan distribuera bas linje och anpassade modeller i portalen och sedan köra test av precisions testning. Du kan använda den här funktionen för att mäta precisionen för en bas linje modell jämfört med en anpassad modell.

**F: Hur vet jag när bearbetningen av data uppsättningen eller modellen är slutförd?**

**S**: För närvarande är statusen för modellen eller data uppsättningen i tabellen det enda sättet att känna till. När bearbetningen är **klar har statusen slutförts.**

**F: Kan jag skapa fler än en modell?**

**S**: Det finns ingen gräns för hur många modeller du kan ha i din samling.

**F: Jag har gjort ett misstag. Hur gör jag för att vill du avbryta data import eller skapande av modell som pågår?**

**S**: För närvarande kan du inte återställa en akustisk eller språk anpassnings process. Du kan ta bort importerade data och modeller när de är i ett Terminal-tillstånd.

**F: Vad är skillnaden mellan söknings-och dikterings modellen och en konversations modell?**

**S**: Du kan välja bland fler än en bas linje modell i tal tjänsten. Konversations modellen är användbar för att känna igen tal som talas i ett konversations format. Den här modellen är idealisk för att skriva om telefonsamtal. Sök-och dikterings modellen är idealisk för röst lösa appar. Den universella modellen är en ny modell som syftar på båda scenarierna. Den universella modellen är för närvarande på eller över kvalitets nivån för konversations modellen i de flesta nationella inställningar.

**F: Kan jag uppdatera min befintliga modell (modell stackning)?**

**S**: Det går inte att uppdatera en befintlig modell. Som en lösning kombinerar du den gamla data uppsättningen med den nya data uppsättningen och omanpassning.

Den gamla data uppsättningen och den nya data uppsättningen måste kombineras i en enda. zip-fil (för akustiska data) eller i en txt-fil (för språk data). När anpassningen är färdig måste den nya, uppdaterade modellen omdistribueras för att få en ny slut punkt

**F: När en ny version av en bas linje är tillgänglig uppdateras min distribution automatiskt?**

**S**: Distributioner kommer inte att uppdateras automatiskt.

Om du har anpassat och distribuerat en modell med baseline V 1.0, kommer distributionen att förbli oförändrad. Kunder kan inaktivera den distribuerade modellen och sedan anpassa den igen med hjälp av den nya versionen av bas linjen och omdistribuera.

**F: Vad händer om jag behöver högre samtidighet för min distribuerade modell än vad som erbjuds i portalen?**

**S**: Du kan skala upp din modell i steg om 20 samtidiga begär Anden.

Kontakta [tal support](mailto:speechsupport@microsoft.com?subject=Request%20for%20higher%20concurrency%20for%20Speech-to-text) om du behöver en högre skala.

**F: Kan jag hämta min modell och köra den lokalt?**

**S**: Det går inte att ladda ned och köra modeller lokalt.

**F: Är mina förfrågningar loggade?**

**S**: Du kan välja när du skapar en distribution för att stänga av spårningen. Då kommer inget ljud eller avskrifter att loggas. Annars loggas förfrågningar vanligt vis i Azure i säkert lagrings utrymme.

**F: Är mina förfrågningar begränsade?**

**S**: REST API begränsar begär anden till 25 per 5 sekunder. Information finns på våra sidor för [tal till text](speech-to-text.md).

**F: Hur jag debiteras för ljud med dubbla kanaler?**

**S**: Om du skickar varje kanal separat (varje kanal i en egen fil) debiteras du per varaktighet för varje fil. Om du skickar en enskild fil med varje kanal som är multiplexad tillsammans debiteras du för den enskilda filens varaktighet.

> [!IMPORTANT]
> Om du har ytterligare sekretess problem som hindrar dig från att använda tjänsten för anpassad röst kontaktar du en av support kanalerna.

## <a name="importing-data"></a>Importera data

**F: Vad är gränsen för storleken på en data uppsättning och varför är den begränsad?**

**S**: Den aktuella gränsen för en data uppsättning är 2 GB. Gränsen beror på begränsningen av storleken på en fil för HTTP-uppladdning. 

**F: Kan jag zippa mina textfiler så att jag kan ladda upp en större textfil?** 

**S**: Nej. För närvarande tillåts endast okomprimerade textfiler.

**F: Data rapporten säger att det inte gick att yttranden. Vad är problemet?**

**S**: Det gick inte att överföra 100 procent av yttranden i en fil. Om den stora delen av yttranden i en akustisk eller språk data uppsättning (till exempel mer än 95 procent) har importer ATS kan data uppsättningen användas. Vi rekommenderar dock att du försöker förstå varför yttranden misslyckades och korrigerar problemen. De vanligaste problemen, till exempel formateringsfel, är enkla att åtgärda. 

## <a name="creating-an-acoustic-model"></a>Skapa en akustisk modell

**F: Hur mycket akustiska data behöver jag?**

**S**: Vi rekommenderar att du börjar med mellan 30 minuter och en timmes akustiska data.

**F: Vilka data ska jag samla in?**

**S**: Samla in data som är så nära program scenariot och användnings fallet som möjligt. Data insamlingen bör matcha mål programmet och användare vad gäller enheter, enheter, miljöer och typer av högtalare. I allmänhet bör du samla in data från så många olika högtalare som möjligt. 

**F: Hur ska jag samla in akustiska data?**

**S**: Du kan skapa ett fristående data insamlings program eller använda ljud inspelnings program från andra program. Du kan också skapa en version av programmet som loggar ljuddata och sedan använder dessa data. 

**F: Behöver jag skriva av anpassnings data själv?**

**S**: Visst! Du kan skriva av den själv eller använda en professionell avskrifts tjänst. Vissa användare föredrar professionella scheman och andra använder gemensamt skapade eller utför avskrifterna själva.

## <a name="accuracy-testing"></a>Precisions testning

**F: Kan jag utföra offline-testning av min anpassade akustiska modell med hjälp av en anpassad språk modell?**

**S**: Ja, markera bara den anpassade språk modellen på den nedrullningsbara menyn när du ställer in offline-testet.

**F: Kan jag utföra offline-testning av min anpassade språk modell genom att använda en anpassad akustisk modell?**

**S**: Ja, markera bara den anpassade akustiska modellen på den nedrullningsbara menyn när du ställer in offline-testet.

**F: Vad är en fel frekvens i Word (WER) och hur beräknas den?**

**S**: WER är utvärderings måttet för tal igenkänning. WER räknas som det totala antalet fel, inklusive infogningar, borttagningar och ersättningar, dividerat med det totala antalet ord i referens avskriften. Mer information finns i [fel frekvens för Word](https://en.wikipedia.org/wiki/Word_error_rate).

**F: Hur gör jag för att avgöra om resultatet av ett precisions test är lämpligt?**

**S**: Resultatet visar en jämförelse mellan bas linje modellen och den modell som du har anpassat. Du bör sträva efter att ändra bas linje modellen för att göra anpassningar.

**F: Hur gör jag för att reda på WER för en bas modell så att jag kan se om det har förbättrats?** 

**S**: Offline-test resultatet visar bas linje noggrannheten för den anpassade modellen och förbättringen över bas linjen.

## <a name="creating-a-language-model"></a>Skapa en språk modell

**F: Hur mycket text data behöver jag ladda upp?**

**S**: Det beror på hur olika vokabulär och fraser som används i ditt program kommer från start språks modellerna. För alla nya ord är det praktiskt att ange så många exempel som möjligt för användningen av dessa ord. För vanliga fraser som används i ditt program, inklusive fraser i språk data, är det också användbart eftersom det instruerar systemet att även lyssna efter de här villkoren. Det är vanligt att ha minst 100 och ofta flera hundra eller fler yttranden i språk data uppsättningen. Även om vissa typer av frågor förväntas vara vanligare än andra kan du infoga flera kopior av de vanliga frågorna i data uppsättningen.

**F: Kan jag bara ladda upp en lista med ord?**

**S**: När du överför en lista med ord läggs orden till i ord listan, men det visar inte hur orden vanligt vis används. Genom att tillhandahålla fullständig eller partiell yttranden (meningar eller fraser av saker som användare sannolikt kommer att säga) kan språk modellen lära sig de nya orden och hur de används. Den anpassade språk modellen är inte bara till för att lägga till nya ord i systemet, utan även för att justera sannolikheten för kända ord för ditt program. Att tillhandahålla fullständig yttranden hjälper systemet att lära sig bättre. 

## <a name="next-steps"></a>Nästa steg

* [Felsökning](troubleshooting.md)
* [Viktig information](releasenotes.md)
