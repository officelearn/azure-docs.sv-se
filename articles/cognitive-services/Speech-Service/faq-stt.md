---
title: Frågor om tal i text med vanliga frågor
titleSuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om tjänsten Tal till text.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/4/2019
ms.author: panosper
ms.openlocfilehash: a279aebdd19ebd3a41ddad0c1c279937e00838c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168465"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Frågor om tal i text med vanliga frågor

Om du inte hittar svar på dina frågor i den här vanliga frågor och svar kan du läsa [andra supportalternativ.](support.md)

## <a name="general"></a>Allmänt

**F: Vad är skillnaden mellan en baslinjemodell och en anpassad tal till text-modell?**

**S**: En baslinjemodell har tränats med hjälp av Microsoft-ägda data och distribueras redan i molnet. Du kan använda en anpassad modell för att anpassa en modell för att bättre passa en viss miljö som har specifikt omgivningsljud eller språk. Fabriksgolv, bilar eller bullriga gator skulle kräva en anpassad akustisk modell. Ämnen som biologi, fysik, radiologi, produktnamn och anpassade förkortningar skulle kräva en anpassad språkmodell.

**F: Var börjar jag om jag vill använda en baslinjemodell?**

**S:** Först får du en [prenumerationsnyckel](get-started.md). Om du vill ringa REST-anrop till de förbeställda baslinjemodellerna läser du [REST-API:erna](rest-apis.md). Om du vill använda WebSockets [laddar du ned SDK](speech-sdk.md).

**F: Behöver jag alltid bygga en anpassad talmodell?**

**A**: Nej. Om ditt program använder allmänt, dagligt språk behöver du inte anpassa en modell. Om ditt program används i en miljö där det finns lite eller inget bakgrundsljud behöver du inte anpassa en modell.

Du kan distribuera baslinjemodeller och anpassade modeller i portalen och sedan köra noggrannhetstester mot dem. Du kan använda den här funktionen för att mäta noggrannheten hos en baslinjemodell jämfört med en anpassad modell.

**F: Hur vet jag när bearbetningen för min datauppsättning eller modell är klar?**

**S**: För närvarande är statusen för modellen eller datauppsättningen i tabellen det enda sättet att veta. När bearbetningen är klar **lyckades**statusen .

**F: Kan jag skapa mer än en modell?**

**S:** Det finns ingen gräns för hur många modeller du kan ha i din samling.

**F: Jag insåg att jag gjorde ett misstag. Hur avbryter jag min dataimport eller modellskapande som pågår?**

**S:** För närvarande kan du inte återställa en akustisk eller språkanpassningsprocess. Du kan ta bort importerade data och modeller när de är i ett terminaltillstånd.

**F: Vad är skillnaden mellan modellen Sök och Diktering och konversationsmodellen?**

**S**: Du kan välja mellan mer än en baslinjemodell i taltjänsten. Konversationsmodellen är användbar när du vill känna igen tal som talas i konversationsstil. Denna modell är idealisk för att transkribera telefonsamtal. Sök- och dikteringsmodellen är idealisk för röstutlösta appar. Universal-modellen är en ny modell som syftar till att ta itu med båda scenarierna. Universal-modellen är för närvarande på eller över kvalitetsnivå för konversationsmodellen i de flesta språk.

**F: Kan jag uppdatera min befintliga modell (modellstapling)?**

**S:** Du kan inte uppdatera en befintlig modell. Som en lösning kan du kombinera den gamla datauppsättningen med den nya datauppsättningen och läsuppsättningen.

Den gamla datauppsättningen och den nya datauppsättningen måste kombineras i en enda ZIP-fil (för akustiska data) eller i en TXT-fil (för språkdata). När anpassningen är klar måste den nya, uppdaterade modellen omfördelas för att få en ny slutpunkt

**F: När en ny version av en baslinje är tillgänglig, uppdateras min distribution automatiskt?**

**S**: Distributioner uppdateras INTE automatiskt.

Om du har anpassat och distribuerat en modell med baslinjen V1.0 förblir distributionen som den är. Kunder kan inaktivera den distribuerade modellen, läsa med den nyare versionen av originalplanen och omdedela.

**F: Kan jag ladda ner min modell och köra den lokalt?**

**S**: Modeller kan inte hämtas och köras lokalt.

**F: Loggas mina begäranden?**

**S:** Du kan välja när du skapar en distribution för att stänga av spårningen. Då loggas inga ljud- eller transkriptioner. Annars loggas begäranden vanligtvis i Azure i säker lagring.

**F: Begränsas mina begäranden?**

**S**: REST API begränsar begäranden till 25 per 5 sekunder. Detaljer finns på våra sidor för [tal till text](speech-to-text.md).

**F: Hur debiteras jag för ljud med dubbla kanaler?**

**S:** Om du skickar in varje kanal separat (varje kanal i sin egen fil) debiteras du under hela varje fil. Om du skickar in en enda fil med varje kanal multiplexed tillsammans, då du kommer att debiteras under hela den enda filen. Mer information om priser finns på [prissidan](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)för Azure Cognitive Services .

> [!IMPORTANT]
> Om du har ytterligare sekretessproblem som förbjuder dig att använda den anpassade taltjänsten kontaktar du någon av supportkanalerna.

## <a name="increasing-concurrency"></a>Ökad samtidighet

**F: Vad händer om jag behöver högre samtidighet för min distribuerade modell än vad som erbjuds i portalen?**

**S:** Du kan skala upp modellen i steg om 20 samtidiga begäranden.

Skapa en supportbegäran i [Azure-supportportalen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)med den information som krävs . Lägg inte upp informationen på någon av de offentliga kanalerna (GitHub, Stackoverflow, ...) som nämns på [supportsidan](support.md).

För att öka samtidigheten för en ***anpassad modell***behöver vi följande information:

- Den region där modellen distribueras,
- slutpunkts-ID för den distribuerade modellen:
  - Kom till [Custom Speech Portal](https://aka.ms/customspeech),
  - Logga in (vid behov),
  - välj projekt och distribution,
  - välj den slutpunkt du behöver samtidighetsökning för,
  - kopiera `Endpoint ID`.

För att öka samtidigheten för en ***basmodell***behöver vi följande information:

- Regionen för din tjänst,

och antingen

- en åtkomsttoken för din prenumeration (se [här),](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#how-to-get-an-access-token)

eller

- Resurs-ID:et för din prenumeration:
  - Gå till [Azure-portalen](https://portal.azure.com),
  - välj `Cognitive Services` i sökrutan,
  - från de visade tjänsterna väljer den taltjänst som du vill att samtidigheten ska ökas för,
  - visa `Properties` för den här tjänsten,
  - kopiera hela `Resource ID`.

## <a name="importing-data"></a>Importera data

**F: Vad är gränsen för storleken på en datauppsättning och varför är det gränsen?**

**S**: Den aktuella gränsen för en datauppsättning är 2 GB. Gränsen beror på begränsningen av storleken på en fil för HTTP-överföring.

**F: Kan jag zippa mina textfiler så att jag kan ladda upp en större textfil?**

**A**: Nej. För närvarande är endast okomprimerade textfiler tillåtna.

**F: Datarapporten säger att det inte fanns några yttranden. Vad är problemet?**

**S:** Att inte överföra 100 procent av yttrandena i en fil är inte ett problem. Om de allra flesta yttranden i en akustisk datauppsättning eller språkdatauppsättning (till exempel mer än 95 procent) har importerats kan datauppsättningen användas. Vi rekommenderar dock att du försöker förstå varför yttrandena misslyckades och åtgärda problemen. De vanligaste problemen, till exempel formateringsfel, är lätta att åtgärda.

## <a name="creating-an-acoustic-model"></a>Skapa en akustisk modell

**F: Hur mycket akustiska data behöver jag?**

**S**: Vi rekommenderar att du börjar med mellan 30 minuter och en timmes akustiska data.

**F: Vilka data ska jag samla in?**

**S:** Samla in data som ligger så nära programscenariot och användningsfallet som möjligt. Datainsamlingen bör matcha målprogrammet och användarna när det gäller enhet eller enheter, miljöer och typer av högtalare. I allmänhet bör du samla in data från så många olika talare som möjligt.

**F: Hur ska jag samla in akustiska data?**

**S:** Du kan skapa ett fristående datainsamlingsprogram eller använda ljudinspelningsprogram som inte är på hyllan. Du kan också skapa en version av ditt program som loggar ljuddata och sedan använder data.

**F: Måste jag transkribera anpassningsdata själv?**

**A**: Ja! Du kan transkribera den själv eller använda en professionell transkriptionstjänst. Vissa användare föredrar professionella transkriberare och andra använder crowdsourcing eller göra transkriptioner själva.

## <a name="accuracy-testing"></a>Noggrannhetstestning

**F: Kan jag utföra offlinetester av min anpassade akustiska modell med hjälp av en anpassad språkmodell?**

**S:** Ja, välj bara den anpassade språkmodellen i den nedrullningsbara menyn när du ställer in offlinetestet.

**F: Kan jag utföra offlinetester av min anpassade språkmodell med hjälp av en anpassad akustisk modell?**

**S:** Ja, välj bara den anpassade akustiska modellen i rullgardinsmenyn när du ställer in offlinetestet.

**F: Vad är word felfrekvens (WER) och hur beräknas det?**

**S**: WER är utvärderingsmåttet för taligenkänning. WER räknas som det totala antalet fel, vilket inkluderar infogningar, borttagningar och ersättningar, dividerat med det totala antalet ord i referensranskriptionen. Mer information finns i [word error rate](https://en.wikipedia.org/wiki/Word_error_rate).

**F: Hur avgör jag om resultaten av ett noggrannhetstest är bra?**

**S**: Resultaten visar en jämförelse mellan baslinjemodellen och den modell som du har anpassat. Du bör sträva efter att slå baslinjemodellen för att göra anpassning värt besväret.

**F: Hur avgör jag WER för en basmodell så att jag kan se om det fanns en förbättring?**

**S**: Offlinetestresultaten visar baslinjenoggrannheten för den anpassade modellen och förbättringen jämfört med baslinjen.

## <a name="creating-a-language-model"></a>Skapa en språkmodell

**F: Hur mycket textdata behöver jag ladda upp?**

**S**: Det beror på hur olika ordförråd och fraser som används i ditt program är från startspråksmodellerna. För alla nya ord är det bra att ge så många exempel som möjligt på användningen av dessa ord. För vanliga fraser som används i ditt program, inklusive fraser i språkdata är också användbart eftersom det talar om för systemet att också lyssna efter dessa termer. Det är vanligt att ha minst 100, och vanligtvis flera hundra eller fler yttranden i språket datauppsättningen. Om vissa typer av frågor förväntas vara vanligare än andra kan du infoga flera kopior av vanliga frågor i datauppsättningen.

**F: Kan jag bara ladda upp en lista med ord?**

**S:** Ladda upp en lista med ord kommer att lägga till orden i ordförrådet, men det kommer inte att lära systemet hur orden vanligtvis används. Genom att ge fullständiga eller partiella yttranden (meningar eller fraser av saker som användarna sannolikt kommer att säga), kan språkmodellen lära sig de nya orden och hur de används. Den anpassade språkmodellen är bra inte bara för att lägga till nya ord i systemet, men också för att justera sannolikheten för kända ord för ditt program. Att ge fullständiga yttranden hjälper systemet att lära sig bättre.

## <a name="tenant-model-custom-speech-with-office-365-data"></a>Klientmodell (anpassat tal med Office 365-data)

**F: Vilken information ingår i klientmodellen och hur skapas den?**

**A.** En klientmodell skapas med [offentliga gruppmeddelanden](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) och dokument som kan ses av vem som helst i organisationen.

**F: Vilka talupplevelser förbättras av klientmodellen?**

**A.** När klientmodellen är aktiverad, skapad och publicerad används den för att förbättra igenkänningen för alla företagsprogram som skapats med taltjänsten. som också skickar en användare AAD token som anger medlemskap till företaget.

De talupplevelser som är inbyggda i Office 365, till exempel Diktering och PowerPoint-textning, ändras inte när du skapar en klientmodell för dina taltjänstprogram.

## <a name="next-steps"></a>Nästa steg

- [Troubleshooting](troubleshooting.md) (Felsökning)
- [Viktig information](releasenotes.md)
