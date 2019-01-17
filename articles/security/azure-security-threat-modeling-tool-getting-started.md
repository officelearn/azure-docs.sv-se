---
title: Komma igång - Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Det här är en bättre överblick markering Threat Modeling Tool fungerar i praktiken.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 6315e6d39a3b68854beb6563d075e3c79ca93a69
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359484"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Komma igång med Threat Modeling Tool

Microsoft Threat Modeling Tool 2018 gavs ut som allmänt tillgänglig i September 2018 som en kostnadsfri  **[klickar du på att hämta](https://aka.ms/threatmodelingtool)**. Ändringen av leveransmekanismen gör att vi kan skicka de senaste förbättringar och felkorrigeringar till kunder varje gång de öppnar verktyget, vilket gör det enklare att underhålla och använda.
Den här artikeln tar dig igenom processen med att komma igång med Microsoft SDL hotet modellering metoden och visar hur du använder för att utveckla fantastiska hotmodeller som en ryggraden för din säkerhetsprocess.

Den här artikeln bygger vidare på befintliga kunskaper i SDL hotet modellering metod. En snabb genomgång finns i **[Threat Modeling webbprogram](https://msdn.microsoft.com/library/ms978516.aspx)** och en arkiverad version av **[avslöja säkerhetsfel med hjälp av STRIDE-metoden](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** MSDN-artikeln publiceras i 2006.

Sammanfattningsvis snabbt sättet är att skapa ett diagram, identifiera hot, åtgärda dem och verifiera varje avhjälpande åtgärd. Här är ett diagram som visar den här processen:

![SDL-processen](./media/azure-security-threat-modeling-tool-feature-overview/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Startar hotet modellera processen

När du startar Threat Modeling Tool, ser du några saker som visas i bilden:

![Tom startsida](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Threat modellen avsnittet

| Komponent                                   | Information                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Feedback, förslag och problem med knappen** | Tar dig den **[MSDN-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** för allt som rör SDL-processen. Det ger dig möjlighet att läsa igenom vad andra användare gör, tillsammans med lösningar och rekommendationer. Om du fortfarande inte hittar det du letar efter, e- tmtextsupport@microsoft.com för Vårt supportteam som hjälper dig att                                                                                                                            |
| **Skapa en modell**                          | Öppnar en tom arbetsyta att rita diagrammet. Se till att välja vilken mall som du vill använda för din modell                                                                                                                                                                                                                                                                                                                                                                       |
| **Mall för nya modeller**                 | Du måste välja vilken mall som ska användas innan du skapar en modell. Vårt huvudsakliga mallen är Azure Threat modellen mallen, som innehåller Azure-specifika stenciler, hot och åtgärder. För allmän modeller, väljer du SDL TM Knowledge Base från den nedrullningsbara menyn. Vill du skapa en egen mall eller skicka ett nytt lösenord för alla användare? Kolla in våra **[Mallagret](https://github.com/Microsoft/threat-modeling-templates)** GitHub Page mer                              |
| **Öppna en modell**                            | <p>Öppnas sparat hotmodeller. Funktionen nyligen öppnade modeller är bra om du vill öppna senaste filer. När du hovrar över valet hittar du 2 olika sätt att öppna modeller:</p><p><ul><li>Öppna från den här datorn – klassiska sätt att öppna en fil med hjälp av lokal lagring</li><li>Öppna från OneDrive-team kan använda mappar i OneDrive att spara och dela sina hotmodeller på en enda plats för att öka produktiviteten och samarbetet</li></ul></p> |
| **Kom igång-Guide**                   | Öppnar den **[Microsoft Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** huvudsidan                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Mallavsnittet

| Komponent               | Information                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Skapa ny mall** | Öppnar en tom mall för dig att skapa. Om du inte har djupa kunskaper i att skapa mallar från grunden, rekommenderar vi att du skapar från befintliga |
| **Öppna mallen**       | Öppnar befintliga mallar som du kan göra ändringar i                                                                                                              |

Threat Modeling Tool-teamet arbetar ständigt för att förbättra funktioner för verktyg och Upplev. Några mindre ändringar kan äga rum under loppet av året, men alla viktiga ändringar kräver omskrivningar i guiden. Referera till det ofta så du får de senaste nyheterna.

## <a name="building-a-model"></a>Att skapa en modell

I det här avsnittet så vi:

- Cristina (utvecklare)
- Ricardo (projektledare) och
- Ashish (Testare)

De går igenom processen för att utveckla sina första hotmodell.

> Ricardo: Hej Cristina, jag arbetat med modelldiagram för hot och ville se till att vi fick att informationen är rätt. Kan du hjälpa mig att titta igenom?
> Cristina: Absolut. Låt oss ta en titt.
> Ricardo öppnas verktyget och delar sin skärmen med Cristina.

![Grundläggande Hotmodell](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: OK, verkar enkelt, men kan du prova jag det?
> Ricardo: Att! Här är en analys på detaljnivå:
> - Vår mänskliga användaren ritas som en extern enhet – en ruta
> - De skickar kommandon till vår webbserver – cirkeln
> - Webbservern är consulting en databas (två parallella linjer)

Vad Ricardo just visade Cristina är en DFD kort för  **[dataflödesdiagram](https://en.wikipedia.org/wiki/Data_flow_diagram)**. Threat Modeling Tool kan du ange förtroendegränser, som anges med röd kantlinjerna, så att var olika entiteter är i kontrollen. IT-administratörer kräver en Active Directory-system i autentiseringssyfte, så att Active Directory är utanför deras kontroll.

> Cristina: Det verkar rätt för mig. Vad gäller hoten?
> Ricardo: Låt mig visar.

## <a name="analyzing-threats"></a>Analysera hot

När han klickar på analysvy från ikonen menyalternativet (fil med förstoringsglas), han hämtas till en lista över genererade hot i Threat Modeling Tool hitta utifrån standardmallen som använder SDL-metoden som kallas  **[ STRIDE (förfalskning, manipulering, avslöjande av information, Repudiation, Denial of Service och rättighetsökning)](https://en.wikipedia.org/wiki/STRIDE_(security))**. Tanken är att programvaran kommer under en förutsägbar uppsättning hot, som finns med hjälp av dessa 6 kategorier.

Den här metoden fungerar som säkra huset genom att se till att varje dörr- och har en mekanism för låsning på plats innan du lägger till ett larmsystem eller jaga efter tjuven.

![Grundläggande hot](./media/azure-security-threat-modeling-tool-feature-overview/basicthreats.png)

Ricardo börjar genom att välja det första objektet i listan. Här är vad som händer:

Först utökas interaktionen mellan de två stencilerna

![Interaktion](./media/azure-security-threat-modeling-tool-feature-overview/interaction.png)

Andra, ytterligare information om hotet som visas i fönstret Egenskaper för hot

![Interaktion Info](./media/azure-security-threat-modeling-tool-feature-overview/interactioninfo.png)

Det genererade hotet hjälper han förstå potentiella design-fel. STRIDE kategorisering ger honom en idé på potentiella angreppsmetoder, medan ytterligare beskrivning anger att exakt vad som är fel, tillsammans med möjliga sätt att lösa det. Han kan använda redigerbart fält för att göra anteckningar i motivering information eller ändra prioritet klassificeringarna beroende på företagets bugg-fältet.

Azure-mallar har ytterligare information som hjälper användarna att förstå inte bara fel, utan också hur du åtgärdar det genom att lägga till beskrivningar, exempel och länkar till specifika Azure-dokumentationen.

Beskrivningen gjort honom inser hur viktigt för att lägga till en autentiseringsmekanism som hindrar användare från förfalskas, avslöja första hotet för att bearbetas i. Ett par minuter till diskussion med Cristina, förstår de vikten av att implementera åtkomstkontroll och roller. Ricardo fyllt i vissa snabbanteckningar att kontrollera att dessa implementerades.

Eftersom Ricardo försattes i hot under avslöjande av Information, realiserade han planen för åtkomstkontroll krävs vissa skrivskyddad konton för granskning och rapportgenerering. Han funderat över om det ska vara ett nya hot, men åtgärder har samma, så han anges hotet detta.
Han tror lite mer om avslöjande av information och insåg att de båda banden kommer att behöva kryptering, ett jobb för driftsteamet.

Ej tillämpligt för designen på grund av befintliga åtgärder eller säkerhet hot garanterar kan ändras till ”ej tillämpligt” Status listrutan. Det finns tre alternativ: Inte startad – används standardvalet måste undersökning – för att följa upp objekt och Mitigated – när den helt har arbetat med.

## <a name="reports--sharing"></a>Rapporter och dela

När Ricardo går igenom listan med Cristina och lägger till viktiga meddelanden, åtgärder/berättiganden, prioritet och status ändras kan han väljer rapporter -> Skapa hela rapporten -> Spara rapport som skriver ut en bra rapport att gå igenom med kollegor så att rätt säkerhet arbetsrelaterad implementeras.

![Interaktion Info](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

Om Ricardo vill dela filen i stället göra han enkelt det genom att spara i sitt företags OneDrive-konto. När han gör som han Kopiera länk till dokumentet och dela den med sin kollegor. 

## <a name="threat-modeling-meetings"></a>Threat modellering möten

När Ricardo skickas sin hotmodell till sin kollega med OneDrive, Ashish testaren, har underwhelmed. Såg ut Ricardo och Cristina missade ett ganska stort antal viktiga specialfall, som lätt kan äventyras att vara. Hans angående det är ett komplement till hotmodeller.

I det här scenariot när Ashish tog över hotmodell, han kallas för två threat modellering möten: ett möte för att synkronisera om processen och gå igenom diagrammen och sedan ett andra möte för mot hot granska och signering.

I den första möten att Ashish 10 minuter walking alla via SDL-hotet modellera processen. Han samlat in modelldiagram för hot och igång förklarar i detalj. En viktig komponent saknas har identifierats inom fem minuter.

Ett par minuter senare, Ashish och Ricardo gick in i en utökad beskrivning av hur webbservern har skapats. Det var inte det bästa sättet för ett möte för att fortsätta, men alla godkänt så småningom att identifiera avvikelsen tidigt kommer att spara dem tid i framtiden.

I andra mötet teamet visade hur hoten beskrivs några sätt att åtgärda dem och godkänt för hotmodell. De kontrolleras dokumentet till källkontroll och fortsatte med utveckling.

## <a name="thinking-about-assets"></a>Har du funderingar kring tillgångar

Vissa läsare som har modellerats threat märka att vi inte pratat om tillgångar alls. Vi har upptäckt att många programmerare tar förstår sin programvara bättre än de förstå begreppet tillgångar och vilka resurser en angripare kan vara intresserad av.

Om du planerar att hotmodell ett hus, kan du börja med tänka din familj, oersättliga foton och värdefulla illustrationer. Kanske kan du börja med att tänka på vem som kan logga in och det aktuella säkerhetssystemet. Eller du kan börja med att hänsyn tagits till de fysiska funktionerna, till exempel poolen eller främre porch. Det här är detsamma som tänka tillgångar eller angripare programdesign. Något av följande tre metoder fungerar.

Metod för insamling av modellering som vi har visas här är betydligt enklare än vad Microsoft har gjort tidigare. Vi har upptäckt att Designmetoden programvara fungerar bra för många team. Vi hoppas att som omfattar själv.

## <a name="next-steps"></a>Nästa steg

Skicka frågor, kommentarer och frågor till tmtextsupport@microsoft.com. **[Ladda ned](https://aka.ms/threatmodelingtool)**  Threat Modeling Tool att komma igång.
