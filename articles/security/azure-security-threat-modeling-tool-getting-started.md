---
title: "Komma igång - Microsoft Threat Modeling verktyget - Azure | Microsoft Docs"
description: "Det här är en djupare översikt syntaxmarkering hot Modeling verktyg i åtgärden."
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 73d6b42e7a97d6041f6213a1f7d060806734d763
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Komma igång med verktyget Modeling hot

Moln- och Enterprise Security Tools-teamet släppte hot Modeling verktyget Preview tidigare i år som ett kostnadsfritt  **[Klicka på Hämta](https://aka.ms/tmtpreview)**. Ändringen i leveransmekanismen gör det möjligt för oss att skicka de senaste förbättringarna och felkorrigeringar till kunder varje gång de öppnar verktyget, vilket gör det enklare att underhålla och använda.
Den här artikeln tar dig igenom processen att komma igång med Microsoft SDL-hotet modeling metod och visar hur du kan använda för att utveckla bra hot modeller som ett stamnät av säkerhet.

Den här artikeln bygger på befintliga kunskaper i SDL-hotet modeling metod. För en snabb granskning, referera till  **[hot Modeling webbprogram](https://msdn.microsoft.com/library/ms978516.aspx)**  och en arkiverad version av  **[upptäcka säkerhet fel med hjälp av STRIDE-metoden](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)**  MSDN-artikel publicerade 2006.

Sammanfattningsvis snabbt, innebär tillvägagångssättet att skapa ett diagram, identifiera hot, minimera dem och verifiera varje lösning. Här är ett diagram som visar den här processen:

![SDL-processen](./media/azure-security-threat-modeling-tool-feature-overview/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Starta hotet modellera processen

När du startar verktyget Modeling hot, Lägg märke till några saker som visas i bilden:

![Tom startsida](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Hot modellen avsnitt

| Komponent                                   | Information                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Feedback, förslag och problem med knappen** | Tar dig den  **[MSDN-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)**  för allt som rör SDL. Den ger dig möjlighet att läsa igenom vad andra användare gör, tillsammans med lösningar och rekommendationer. Om du fortfarande inte hittar det du söker kan e- tmtextsupport@microsoft.com för supportteamet som hjälper dig att                                                                                                                            |
| **Skapa en modell**                          | Öppnar början att rita diagrammet. Se till att välja vilken mall som du vill använda för din modell                                                                                                                                                                                                                                                                                                                                                                       |
| **Mall för nya modeller**                 | Du måste välja vilken mall som ska användas innan du skapar en modell. Vår Huvudmall är Azure hot modellen mallen, som innehåller Azure-specifika stenciler, hot och åtgärder. Välj SDL TM Knowledge Base på menyn för allmän modeller. Vill du skapa en egen mall eller skicka en ny för alla användare? Kolla in våra  **[mall databasen](https://github.com/Microsoft/threat-modeling-templates)**  GitHub Page mer                              |
| **Öppna en modell**                            | <p>Öppnar sparat hot modeller. Funktionen nyligen öppnade modeller är bra om du behöver öppna senaste filerna. När du hovrar över markeringen hittar 2 olika sätt att öppna modeller:</p><p><ul><li>Öppna från den här datorn – klassiska sätt att öppna en fil som använder lokal lagring</li><li>Öppna från OneDrive-team med mappar i OneDrive kan spara och dela alla sina hot modeller i en enda plats för att öka produktiviteten och samarbete</li></ul></p> |
| **Kom igång-Guide**                   | Öppnar den  **[hot Modeling verktyget](./azure-security-threat-modeling-tool.md)**  huvudsida                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Template-sektion

| Komponent               | Information                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Skapa en ny mall** | Öppnar en tom mall att bygga på. Om du inte har omfattande kunskaper i att skapa mallar från början, rekommenderar vi att du skapar från befintliga |
| **Öppna mall**       | Öppnar befintliga mallar som du kan göra ändringar                                                                                                              |

Hot Modeling verktyget-teamet arbetar ständigt för att förbättra verktyget funktioner och erfarenhet. Några mindre ändringar kan ske under loppet av året, men alla viktiga ändringar kräver omskrivningar i handboken. Referera till ofta för att säkerställa att du får de senaste aviseringarna.

## <a name="building-a-model"></a>Skapa en modell

I det här avsnittet så vi:

- Cristina (utvecklare)
- Ricardo (Programhanteraren) och
- Ashish (en tester)

De ska genom processen att utveckla sina första hotmodell.

> Ricardo: Hej Cristina, jag har arbetat med hot modelldiagram och vill kontrollera att vi information rätt. Kan du hjälpa mig titta igenom?
> Cristina: absolut. Låt oss ta en titt.
> Ricardo delar sina skärmen med Cristina öppnar verktyget.

![Grundläggande Hotmodell](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: Ok, verkar enkla, men kan du hjälpa mig via den?
> Ricardo: till! Här är en uppdelning:
> - Vår mänsklig användaren ritas som en extern enhet, en ruta
> - De skickar kommandon till vår webbservern – cirkeln
> - Webbservern är samråd med en databas (två parallella linjer)

Vad Ricardo har visat Cristina är en DFD kort för  **[dataflödesdiagram](https://en.wikipedia.org/wiki/Data_flow_diagram)**. Verktyget Modeling hot tillåter användare att ange förtroendegränser anges med röd kantlinjerna att visa där olika enheter finns i kontrollen. IT-administratörer kräver en Active Directory-system i autentiseringssyfte, så att Active Directory är utanför deras kontroll.

> Cristina: Verkar rätt för mig. Nyheter om hot?
> Ricardo: Jag visar.

## <a name="analyzing-threats"></a>Analysera hot

När han klickar på analysvyn från ikonen menyalternativet (fil med förstoringsglas), han hämtas till en lista över genererade hot hot Modeling verktyget hitta utifrån standardmallen som använder SDL-metod som kallas  **[ STRIDE (-förfalskning, manipulering, avslöjande av information, Denial of Service och rättighetsökning)](https://en.wikipedia.org/wiki/STRIDE_(security))**. Tanken är att programvaran kommer under en förutsägbar uppsättning hot, som finns med hjälp av dessa 6 kategorier.

Den här metoden fungerar som säkra hemmet genom att säkerställa att varje dörr- och har en mekanism för låsning på plats innan du lägger till ett larmsystem eller jaga efter tjuven.

![Grundläggande hot](./media/azure-security-threat-modeling-tool-feature-overview/basicthreats.png)

Ricardo börjar genom att välja det första objektet i listan. Här är vad som händer:

Först förbättrad interaktionen mellan de två stencilerna

![Interaktion](./media/azure-security-threat-modeling-tool-feature-overview/interaction.png)

Andra, ytterligare information om hot visas i fönstret Egenskaper för hot

![Interaktion Info](./media/azure-security-threat-modeling-tool-feature-overview/interactioninfo.png)

Det genererade hotet hjälper honom förstå potentiella design-fel. STRIDE kategorisering får han eller hon en uppfattning om potentiella angreppsmetoder, medan ytterligare beskrivning anger att exakt vad som är fel, tillsammans med potentiella sätt att minska den. Han kan använda redigerbart fält för att skriva anteckningar i motivering information eller ändra prioritet klassificeringar beroende på företagets bugg fältet.

Azure-mallar har ytterligare information som hjälper användarna att förstå inte bara fel, utan också hur du löser det genom att lägga till beskrivningar, exempel och länkar till specifika Azure-dokumentationen.

Beskrivningen gjorts honom inser viktigt för att lägga till en autentiseringsmetod för att förhindra att användare förfalskas, avslöja första hotet bearbetas. Några minuter till diskussion med Cristina, förstå de vikten av implementera åtkomstkontroll och roller. Ricardo fylls i vissa snabba anteckningar för att se till att dessa implementerades.

Eftersom Ricardo försattes i hot under avslöjande av Information, insåg han planen för åtkomstkontroll krävs vissa skrivskyddad konton för granskning och rapportgenerering. Han funderat över om detta ska vara ett nya hot, men åtgärder har detsamma, så han anges hotet detta.
Han tänkte lite mer om avslöjande av information och insåg att banden kommer att behöva ett jobb för driftteamet-kryptering.

Hot kan inte användas på designen på grund av befintliga åtgärder eller säkerhet garanterar kan ändras till ”ej tillämpligt” Status listrutan. Det finns tre alternativ: inte startat – standardvalet måste undersökningen – används för att följa upp artiklar och Mitigated – när den fullständiga har arbetat med.

## <a name="reports--sharing"></a>Rapporter & delning

När Ricardo går igenom listan med Cristina och lägger till viktiga anteckningar, åtgärder/skäl, prioritet och status ändras, he väljer rapporter -> Skapa fullständig rapport -> Spara rapporten, som skriver ut en bra rapport för att gå igenom med kollegor Se till att rätt säkerhet fungerar implementeras.

![Interaktion Info](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

Om Ricardo vill dela filen i stället göra han enkelt det genom att spara i sin organisation OneDrive-konto. När han har som han kopiera länken dokument och dela den med sin kollegor. 

## <a name="threat-modeling-meetings"></a>Hot modellering möten

När Ricardo skickas sin hotmodell till sin kollega med OneDrive, Ashish testaren, var underwhelmed. Visat sig som Ricardo och Cristina missade ganska många viktiga specialfall, som lätt kan äventyras. Hans angående är ett komplement till hot modeller.

I detta scenario när Ashish tog över hot-modellen han kallas för två hot modellering möten: ett möte att synkronisera på processen och gå igenom diagram och en andra möte för hot granska och signering.

I den första möten att Ashish 10 minuter går alla via SDL-hotet modellera processen. Han hämtas hot modellen diagrammet och igång förklarar i detalj. En viktig komponent saknas har identifierats inom fem minuter.

Några minuter senare, Ashish och Ricardo gick in i en utökad beskrivning av hur webbservern har skapats. Det var inte det bästa sättet för ett möte för att fortsätta, men alla överens så småningom att identifiering av avvikelse tidigt ska spara dem tidpunkt i framtiden.

I andra möte teamet gått igenom hot, beskrivs några metoder för att behandla dem och signerat på hot-modellen. De markerat dokumentet till källkontroll och fortsätter med utveckling.

## <a name="thinking-about-assets"></a>Om du tänker tillgångar

Vissa läsare som har hot modelleras eventuellt se att vi inte har diskuterats tillgångar alls. Vi har upptäckt att många programmerare tar lättare att förstå sina program än de förstå begreppet tillgångar och vilka tillgångar en angripare kan vara intresserad av.

Om du ska hotmodell ett hus, kan du börja med tänka din familj, oersättliga foton och värdefulla illustrationer. Kanske kan du börja med att tänka på vem som kan logga in och det aktuella. Eller du kan börja med att ta hänsyn till de fysiska funktionerna, till exempel poolen eller främre porch. Dessa är detsamma som tänka tillgångar, angripare eller programvara design. Någon av dessa tre metoder fungerar.

Metod för hot modellering vi visas här är betydligt enklare än vad Microsoft har gjort tidigare. Påträffades att Designmetoden programvara fungerar bra för många grupper. Vi hoppas som innehåller din.

## <a name="next-steps"></a>Nästa steg

Skicka frågor, kommentarer och frågor till tmtextsupport@microsoft.com. **[Hämta](https://aka.ms/tmtpreview)**  verktyget Modeling hotet för att komma igång.