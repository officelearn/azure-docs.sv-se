---
title: Komma igång – Microsoft Threat Modeling Tool - Azure | Microsoft-dokument
description: Det här är en djupare översikt som belyser det hotmodelleringsverktyg som är i funktion.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 1454826095bcced9b20935405c0befd5a1ed1ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728201"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Komma igång med verktyget för hotmodellering

Microsoft Threat Modeling Tool 2018 släpptes som GA i september 2018 som ett gratis **[klick-för-nedladdning](https://aka.ms/threatmodelingtool)**. Förändringen i leveransmekanismen gör det möjligt för oss att driva de senaste förbättringarna och buggfixar till kunder varje gång de öppnar verktyget, vilket gör det lättare att underhålla och använda.
Den här artikeln tar dig igenom processen att komma igång med Microsoft SDL hot modellering strategi och visar hur du använder verktyget för att utveckla stora hot modeller som en ryggrad i din säkerhetsprocess.

Den här artikeln bygger på befintlig kunskap om SDL-metoden för hotmodellering. En snabb granskning finns i webbprogram för **[hotmodellering](https://msdn.microsoft.com/library/ms978516.aspx)** och en arkiverad version av **[Avslöja säkerhetsbrister Med hjälp av](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** DEN STRIDE Approach MSDN artikeln som publicerades 2006.

För att snabbt sammanfatta, innebär metoden att skapa ett diagram, identifiera hot, mildra dem och validera varje begränsning. Här är ett diagram som belyser den här processen:

![SDL-process](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Starta hotmodelleringsprocessen

När du startar verktyget för hotmodellering kommer du att märka några saker, som du ser på bilden:

![Tom startsida](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Avsnittet Hotmodell

| Komponent                                   | Information                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Knappen Feedback, förslag och problem** | Tar dig **[MSDN Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** för allt SDL. Det ger dig en möjlighet att läsa igenom vad andra användare gör, tillsammans med lösningar och rekommendationer. Om du fortfarande inte hittar det du letar tmtextsupport@microsoft.com efter skickar du ett e-postmeddelande till vårt supportteam för att hjälpa dig                                                                                                                            |
| **Skapa en modell**                          | Öppnar en tom arbetsyta där du kan rita diagrammet. Se till att välja vilken mall du vill använda för din modell                                                                                                                                                                                                                                                                                                                                                                       |
| **Mall för nya modeller**                 | Du måste välja vilken mall som ska användas innan du skapar en modell. Vår huvudmall är Azure Threat Model Template, som innehåller Azure-specifika stenciler, hot och mildrande åtgärder. För generiska modeller väljer du SDL TM Knowledge Base på rullgardinsmenyn. Vill du skapa en egen mall eller skicka in en ny för alla användare? Kolla in vår **[GitHub-sida för mallarkiv](https://github.com/Microsoft/threat-modeling-templates)** för att läsa mer                              |
| **Öppna en modell**                            | <p>Öppnar tidigare sparade hotmodeller. Funktionen Nyligen öppnade modeller är bra om du behöver öppna dina senaste filer. När du håller muspekaren över markeringen hittar du två sätt att öppna modeller:</p><p><ul><li>Öppna från den här datorn – klassiskt sätt att öppna en fil med lokal lagring</li><li>Öppna från OneDrive – team kan använda mappar i OneDrive för att spara och dela alla sina hotmodeller på en enda plats för att öka produktiviteten och samarbetet</li></ul></p> |
| **Kom igång-guide**                   | Öppnar huvudsidan **[för Microsoft Threat Modeling Tool](threat-modeling-tool.md)**                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Avsnittet Mall

| Komponent               | Information                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Skapa ny mall** | Öppnar en tom mall som du kan bygga vidare på. Om du inte har omfattande kunskap om att bygga mallar från grunden, rekommenderar vi att du bygger från befintliga |
| **Öppna mall**       | Öppnar befintliga mallar som du kan göra ändringar i                                                                                                              |

Teamet för threat modeling tool arbetar ständigt med att förbättra verktygsfunktioner och -upplevelse. Några mindre förändringar kan ske under året, men alla större förändringar kräver omskrivningar i guiden. Se det ofta för att se till att du får de senaste meddelandena.

## <a name="building-a-model"></a>Bygga en modell

I det här avsnittet följer vi:

- Cristina (utvecklare)
- Ricardo (programansvarig) och
- Ashish (en testare)

De går igenom processen att utveckla sin första hotmodell.

> Ricardo: Hej Cristina, arbetade jag på hot modell diagram och ville se till att vi fick detaljerna rätt. Kan du hjälpa mig att se över det?
> Cristina: Absolut. Låt oss ta en titt.
> Ricardo öppnar verktyget och delar sin skärm med Cristina.

![Grundläggande hotmodell](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: Ok, ser enkelt ut, men kan du gå mig igenom det?
> Ricardo: Visst! Här är fördelningen:
> - Vår mänskliga användare dras som en extern enhet – en kvadrat
> - De skickar kommandon till vår webbserver – cirkeln
> - Webbservern konsulterar en databas (två parallella linjer)

Vad Ricardo visade bara Cristina är en DFD, kort för **[Data Flow Diagram](https://en.wikipedia.org/wiki/Data_flow_diagram)**. Med hotmodelleringsverktyget kan användare ange förtroendegränser, som indikeras av de röda prickade linjerna, för att visa var olika entiteter har kontroll. IT-administratörer kräver till exempel ett Active Directory-system för autentisering, så Active Directory ligger utanför deras kontroll.

> Cristina: Ser rätt för mig. Hoten då?
> Ricardo: Låt mig visa dig.

## <a name="analyzing-threats"></a>Analysera hot

När han klickar på analysvyn från ikonen menyn val (fil med förstoringsglas), han tas till en lista över genererade hot hot modellering tool hittades baserat på standardmallen, som använder SDL strategi som kallas **[STRIDE (Spoofing, Manipulering, Info Disclosure, Repudiation, Denial of Service och Elevation of Privilege)](https://en.wikipedia.org/wiki/STRIDE_(security))**. Tanken är att programvara kommer under en förutsägbar uppsättning hot, som kan hittas med hjälp av dessa 6 kategorier.

Detta tillvägagångssätt är som att säkra ditt hus genom att se till att varje dörr och fönster har en låsmekanism på plats innan du lägger till ett larmsystem eller jagar efter tjuven.

![Grundläggande hot](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo börjar med att välja det första objektet i listan. Så här går det till:

För det första förbättras interaktionen mellan de två stencilerna

![Interaktion](./media/threat-modeling-tool-getting-started/interaction.png)

För det andra visas ytterligare information om hotet i fönstret Hotegenskaper

![Information om interaktion](./media/threat-modeling-tool-getting-started/interactioninfo.png)

Det genererade hotet hjälper honom att förstå potentiella konstruktionsfel. Stride-kategoriseringen ger honom en uppfattning om potentiella attackvektorer, medan den ytterligare beskrivningen talar om för honom exakt vad som är fel, tillsammans med potentiella sätt att mildra det. Han kan använda redigerbara fält för att skriva anteckningar i motiveringsinformationen eller ändra prioritetsklassificeringar beroende på organisationens felfält.

Azure-mallar har ytterligare information som hjälper användarna att förstå inte bara vad som är fel, men också hur man åtgärdar det genom att lägga till beskrivningar, exempel och hyperlänkar till Azure-specifik dokumentation.

Beskrivningen fick honom att inse vikten av att lägga till en autentiseringsmekanism för att förhindra att användare blir falska, avslöjar det första hotet som ska bearbetas. Några minuter in i diskussionen med Cristina förstod de vikten av att implementera åtkomstkontroll och roller. Ricardo fyllde i några snabba anteckningar för att se till att dessa genomfördes.

När Ricardo gick in i hoten under Information Disclosure, insåg han att åtkomstkontrollplanen krävde några skrivskyddade konton för granskning och rapportgenerering. Han undrade om detta skulle vara ett nytt hot, men mildrande åtgärder var desamma, så han noterade hotet därefter.
Han tänkte också på information utlämnande lite mer och insåg att backup band skulle behöva kryptering, ett jobb för operationsteamet.

Hot som inte är tillämpliga på designen på grund av befintliga mildrande åtgärder eller säkerhetsgarantier kan ändras till "Ej tillämpligt" från listrutan Status. Det finns tre andra alternativ: Inte startad – standardval, Needs Investigation – som används för att följa upp objekt och mildras – när det har fungerat helt.

## <a name="reports--sharing"></a>Rapporter & delning

När Ricardo går igenom listan med Cristina och lägger till viktiga anteckningar, mildrande åtgärder / motiveringar, prioritet och statusändringar, väljer han Rapporter-> Skapa fullständig rapport-> Spara rapport, som skriver ut en fin rapport för honom att gå igenom med kollegor för att säkerställa rätt säkerhetsarbete genomförs.

![Information om interaktion](./media/threat-modeling-tool-feature-overview/report.png)

Om Ricardo vill dela filen i stället kan han enkelt göra det genom att spara i organisationens OneDrive-konto. När han gör det kan han kopiera dokumentlänken och dela den med sina kollegor. 

## <a name="threat-modeling-meetings"></a>Hotmodelleringsmöten

När Ricardo skickade sin hotmodell till sin kollega med OneDrive, Ashish, testare, var underwhelmed. Verkade som Ricardo och Cristina missade en hel del viktiga hörn fall, som lätt kan äventyras. Hans skepsis är ett komplement till hotmodeller.

I det här scenariot, efter ashish tog över hotmodellen, kallade han för två hot modellering möten: ett möte för att synkronisera på processen och gå igenom diagrammen och sedan ett andra möte för hot granskning och signering.

I det första mötet tillbringade Ashish 10 minuter gå alla genom SDL hot modelleringsprocessen. Han drog sedan upp hotmodelldiagrammet och började förklara det i detalj. Inom fem minuter hade en viktig komponent som saknades identifierats.

Några minuter senare hamnade Ashish och Ricardo i en längre diskussion om hur webbservern byggdes. Det var inte det perfekta sättet för ett möte att gå vidare, men alla var till överens om att upptäcka diskrepansen tidigt skulle spara dem tid i framtiden.

I det andra mötet gick teamet igenom hoten, diskuterade några sätt att ta itu med dem och skrev under på hotmodellen. De kontrollerade dokumentet i källkontroll och fortsatte med utvecklingen.

## <a name="thinking-about-assets"></a>Funderar på tillgångar

Vissa läsare som har hot modelleras kan märka att vi inte har talat om tillgångar alls. Vi har upptäckt att många programvaruingenjörer förstår sin programvara bättre än de förstår begreppet tillgångar och vilka tillgångar en angripare kan vara intresserad av.

Om du ska hota modell ett hus, kan du börja med att tänka på din familj, oersättliga bilder eller värdefulla konstverk. Kanske du kan börja med att tänka på vem som kan bryta sig in och det nuvarande säkerhetssystemet. Eller så kan du börja med att överväga de fysiska funktionerna, som poolen eller verandan. Dessa är analoga med att tänka på tillgångar, angripare eller programvarudesign. Någon av dessa tre tillvägagångssätt arbete.

Den strategi för hot modellering vi har lagt fram här är betydligt enklare än vad Microsoft har gjort tidigare. Vi fann att programvaran design strategi fungerar bra för många team. Vi hoppas att inkludera din.

## <a name="next-steps"></a>Efterföljande moment

Skicka dina frågor, kommentarer tmtextsupport@microsoft.comoch funderingar till . **[Ladda ned](https://aka.ms/threatmodelingtool)** verktyget för hotmodellering för att komma igång.
