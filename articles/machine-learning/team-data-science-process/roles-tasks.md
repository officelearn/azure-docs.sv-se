---
title: Roller för team Data Science Process och uppgifter
description: En översikt över i viktiga komponenter, personal-roller och tillhörande uppgifter för ett projekt i teamform data science.
author: marktab
manager: cgronlun
editor: cgronlun
services: machine-learning
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 051f80f16254c25853e476f66e8696423641073b
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250798"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Roller för team Data Science Process och uppgifter

Team Data Science Process är ett ramverk som utvecklats av Microsoft och som tillhandahåller en strukturerad metod för att skapa prediktiva Analyslösningar och intelligenta program effektivt. Den här artikeln beskrivs rollerna som personal och förknippade aktiviteter som hanteras av data science team standardisera om den här processen.

Den här introduktionen innehåller länkar till självstudier som innehåller instruktioner om hur du ställer in miljön för TDSP för hela data science-grupp, data science team och projekt.
Det ger detaljerad vägledning med Azure DevOps i självstudierna. Azure DevOps innehåller en kodvärd plattformen och flexibel planering verktyg för att hantera gruppaktiviteter, styra åtkomst och hantera databaser.

Du kan använda den här informationen för att implementera TDSP på din egen värd för kod och flexibel planeringsverktyg.

## <a name="structures-of-data-science-groups-and-teams"></a>Data science grupper och team

Data science-funktioner i företag kan ofta ordnas i följande hierarki:

1. ***Data science grupp/s***

2. ***Data science-teamet/s i grupp/s***

I en struktur finns grupp och team leads. Vanligtvis ett datavetenskapsprojekt görs genom ett datavetenskapliga team, vilket kan bestå av projektet leads (för hantering och styrning projektaktiviteter) och datavetare eller tekniker (enskilda deltagare / tekniska personal) som ska köra datavetenskap och data tekniska delar av projektet. Före körning görs installation och styrning av gruppen, team eller projekt leads.

## <a name="definition-of-four-tdsp-roles"></a>Definitionen av fyra TDSP-roller
Med ovanstående antagandet att det finns fyra olika roller för team-personal:

1. ***Gruppansvarig***. Gruppansvarig är ansvarig för alla data science-enhet i ett företag. En data science-enhet kan ha flera team som arbetar med flera dataforskningsprojekt i olika företag branscher. Gruppchef kan delegera sina uppgifter till en surrogate, men ändra inte de uppgifter som är associerade med rollen.

2. ***Team Lead***. Gruppledare hanterar ett team för data science-enheten på ett företag. Ett team består av flera dataexperter. För data science-enhet med endast ett litet antal dataexperter vara Gruppansvarig och den gruppledare samma person.

3. ***Projektet Lead***. En projektledare hanterar dagliga aktiviteter för enskilda datapersonal i en specifik datavetenskapsprojekt.

4. ***Projektet enskilda deltagare***. Dataforskare, affärsanalytiker, Data-tekniker, Architect, osv. En enskild deltagare projektet körs ett datavetenskapsprojekt.


> [!NOTE]
> Beroende på strukturen i ett företag, en enskild person kan spela upp mer än en roll eller det kan finnas mer än en person som arbetar på en roll. Det kan ofta vara skiftläget för små företag eller företag med ett litet antal personal i deras organisation, data science.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Aktiviteter som ska slutföras av fyra personal

Följande bild visar de översta uppgifterna för personal efter roll i att anta och genomföra Team Data Science Process som conceptualized av Microsoft.

![Översikt över roller och uppgifter](./media/roles-tasks/overview-tdsp-top-level.png)

Det här schemat och följande, mer detaljerad översikt över aktiviteter som är kopplade till varje roll i TDSP hjälper dig att välja den aktuella självstudien baserat på dina ansvarsområden i organisationen.

> [!NOTE]
> I följande anvisningar visas hur du ställer in en TDSP-miljö och utföra andra data science-åtgärder i Azure DevOps. Vi anger du hur du utför dessa uppgifter med Azure DevOps eftersom det är vad vi använder för att implementera TDSP på Microsoft. Azure DevOps underlättar samarbete genom att integrera hanteringen av arbetsobjekt som spårar aktiviteter och en värdtjänst för kod som används för att dela verktyg, ordna versioner och tillhandahålla rollbaserad säkerhet. Du kan välja andra plattformar, om du föredrar att utföra uppgifter som beskrivs av TDSP. Men beroende på din plattform, vissa funktioner som utnyttjas från Azure DevOps kanske inte tillgänglig.
>
>Instruktionerna här också använda den [Data Science Virtual Machine (DSVM)](https://aka.ms/dsvm) på Azure moln som analys-skrivbordet med flera populära verktyg för datavetenskap som redan har konfigurerats och integreras med olika Microsoft-programvara och Azure tjänster. Du kan använda DSVM eller någon annan utvecklingsmiljö för att implementera TDSP.


## <a name="group-manager-tasks"></a>Gruppansvarig uppgifter

Följande åtgärder har utförts av Gruppansvarig (eller en utsedd administratör för TDSP-system) för att införa TDSP:

- Skapa en **gruppkontot** på en kod som är värd för plattform (till exempel GitHub, Git, Azure DevOps eller andra)
- Skapa en **projekt mallagret** på gruppkonto och dirigering från malldatabasen projekt har utvecklats av Microsoft TDSP-team. TDSP-projekt malldatabasen från Microsoft
    - tillhandahåller en **standardiserade katalogstruktur** inklusive kataloger för data, kod och dokument,
    - innehåller en uppsättning **standardiserade mallar** att vägleda en effektiv data science process.
- Skapa en **verktyget databasen**, och dirigera från Verktyg-databasen som har utvecklats av Microsoft TDSP-team. Databaser för TDSP-verktyg från Microsoft innehåller
    - en uppsättning användbara verktyg för att göra verk som tillhör en dataexpert effektivare, inklusive verktyg för interaktiv datagranskning, analys och rapportering och för baslinjen modellering och rapportering.
- Konfigurera den **kontroll säkerhetsprincip** av de två databaserna på ditt gruppkonto.

Detaljerade stegvisa anvisningar finns i [Gruppansvarig uppgifter för team data science](group-manager-tasks.md).


## <a name="team-lead-tasks"></a>Gruppaktiviteter för Lead

Följande åtgärder har utförts av teamet leda (eller en avsedda projektadministratör) att införa TDSP:

- Om Azure DevOps väljs vara kod värdplattform för versionshantering och samarbete, skapa en **projekt** på gruppens Azure DevOps-tjänsterna. I annat fall kan den här aktiviteten hoppas över.
- Skapa den **projekt mallagret** under projektet och dirigering från malldatabasen grupp projekt som konfigurerats av din gruppchef eller delegerat av hanteraren för.
- Skapa den **team verktyget databasen**, och Lägg till team-specifika verktyg på lagringsplatsen.
- (Valfritt) Skapa **[Azure-fillagring](https://azure.microsoft.com/services/storage/files/)** som används för att lagra datatillgångar som kan vara användbara för hela gruppen. Andra gruppmedlemmar kan montera den här delade molnet filarkiv på sina datorer för analys.
- (Valfritt) Montera Azure file storage för att den **Data Science Virtual Machine** (DSVM) för teamet leda och Lägg till datatillgångar på den.
- Konfigurera den **säkerhetskontroll** genom att lägga till gruppmedlemmar och konfigurera sina privilegier.

Detaljerade stegvisa anvisningar finns i [gruppledare uppgifter för team data science](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Project Lead-aktiviteter

Följande åtgärder har utförts genom att införa TDSP projektbeskrivning:

- Skapa en **projektets lagringsplats** under projektet, och dirigera från malldatabasen projekt.
- (Valfritt) Skapa **Azure-fillagring** som används för att lagra datatillgångar i projektet.
- (Valfritt) Montera Azure file storage för att den **Data Science Virtual Machine** (DSVM) projektets leda och lägga till projektet datatillgångar på den.
- Konfigurera den **säkerhetskontroll** genom att lägga till projektmedlemmar och konfigurera sina privilegier.

Detaljerade stegvisa anvisningar finns i [projektbeskrivning uppgifter för team data science](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Enskild deltagare projektaktiviteter

Följande åtgärder har utförts av projektet enskilda deltagare (vanligtvis inom Data Science) att göra data science-projekt med TDSP:

- Klona den **projektets lagringsplats** ställa in genom ditt projekt lead.
- (Valfritt) Montera den delade **Azure-fillagring** team och projekt på sina **Data Science Virtual Machine** (DSVM).
- Köra projektet.


Detaljerade stegvisa anvisningar för registreringen till ett projekt, se [projekt enskilda deltagare för team data science](project-ic-tasks.md).


## <a name="data-science-project-execution"></a>Data science projektkörning

Genom att följa de relevanta uppsättningen anvisningar, skapa dataexperter och projektledare gruppledare arbetsobjekt för att spåra alla aktiviteter och steg som krävs för ett projekt från början till dess slut. Med hjälp av git också främjar samarbete mellan dataforskare och garanterar att de artefakter som genereras under projektkörning är version kontrollerade och delas av alla projektmedlemmar.

Instruktioner för projektkörning av har utvecklats baserat på antagandet att båda dina arbetsuppgifter och projektet git-lagringsplatser finns på Azure DevOps. Med Azure DevOps för både kan du länka dina arbetsuppgifter med Git-grenar av ditt projekt-databaser. På så sätt kan spåra du lätt vad som har gjorts för ett arbetsobjekt.

Följande bild visar arbetsflödet för projektkörning av med TDSP.

![Typiska Data Science Projektkörning](./media/roles-tasks/overview-project-execute.png)

Arbetsflödet innehåller steg som kan grupperas i tre aktiviteter:

- Sprint planera (projektbeskrivning)
- Utveckla artefakter på git-grenar kan hantera arbetsobjekt (Data Science)
- Kodgranskning och slå samman grenar med master grenar (projektbeskrivning eller andra gruppmedlemmar)

Detaljerade stegvisa instruktioner om arbetsflöde för verkställande av projektet, se [körningen av dataforskningsprojekt](project-execution.md).

## <a name="project-structure"></a>Projektstruktur

Använd det här [projekt mallagret](https://github.com/Azure/Azure-TDSP-ProjectTemplate) för effektiv projektkörning och samarbete. Den här lagringsplatsen ger en standardiserad directory struktur och dokumentet mallar som du kan använda för dina egna TDSP-projekt.

## <a name="next-steps"></a>Nästa steg

Utforska mer detaljerade beskrivningar av de roller och uppgifter som definieras av Team Data Science Process:

- [Gruppansvarig uppgifter för team data science](group-manager-tasks.md)
- [Lead gruppaktiviteter för team data science](team-lead-tasks.md)
- [Lead projektaktiviteter för team data science](project-lead-tasks.md)
- [Projektet enskilda deltagare för team data science](project-ic-tasks.md)
