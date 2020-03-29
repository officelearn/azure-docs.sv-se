---
title: Roller och uppgifter om teamdatavetenskapsprocess
description: En översikt över de viktigaste komponenterna, personalrollerna och tillhörande uppgifter för en datavetenskapsgrupp.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c1ed731943abf0efdd99ea54d2318fa402835e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720018"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Roller och uppgifter om teamdatavetenskapsprocess

Team Data Science Process (TDSP) är ett ramverk utvecklat av Microsoft som tillhandahåller en strukturerad metod för att effektivt skapa prediktiva analyslösningar och intelligenta program. I den här artikeln beskrivs de viktigaste personalrollerna och tillhörande uppgifter för ett data science-team som standardiserar den här processen.

Den här inledande artikeln länkar till självstudier om hur du ställer in TDSP-miljön. Självstudierna innehåller detaljerad vägledning för hur du använder Azure DevOps-projekt, Azure-databaser och Azure-anslagstavlor.  Det motiverande målet är att gå från koncept till modellering och till distribution.

Självstudierna använder Azure DevOps eftersom det är så du implementerar TDSP på Microsoft. Azure DevOps underlättar samarbete genom att integrera rollbaserad säkerhet, hantering och spårning av arbetsobjekt samt kodhosting, delning och källkontroll. Självstudierna använder också en Virtuell Azure [Data Science Machine](https://aka.ms/dsvm) (DSVM) som analysskrivbord, som har flera populära datavetenskapsverktyg förkonfigurerade och integrerade med Microsoft-programvara och Azure-tjänster. 

Du kan använda självstudierna för att implementera TDSP med andra kodvärdar, agila planerings- och utvecklingsverktyg och miljöer, men vissa funktioner kanske inte är tillgängliga.

## <a name="structure-of-data-science-groups-and-teams"></a>Datavetenskapliga gruppers och teams struktur

Datavetenskapsfunktioner i företag organiseras ofta i följande hierarki:

- Datavetenskap grupp
  - Datascience team/eller team inom gruppen

I en sådan struktur finns det gruppleads och lagledare. Vanligtvis utförs ett data science-projekt av ett data science-team. Datascience team har projektledare för projektledning och styrning uppgifter, och enskilda datavetare och ingenjörer för att utföra datavetenskap och datateknik delar av projektet. Den första projektinställningen och styrningen utförs av grupp-, grupp- eller projektleads.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definition och uppgifter för de fyra TDSP-rollerna
Med antagandet att datavetenskapsenheten består av team inom en grupp finns det fyra olika roller för TDSP-personal:

1. **Gruppchef**: Hanterar hela datavetenskapsenheten i ett företag. En data science-enhet kan ha flera team, som alla arbetar med flera datavetenskapliga projekt i olika affärsvertikaser. En gruppchef kan delegera sina uppgifter till ett surrogat, men de uppgifter som är associerade med rollen ändras inte.
   
2. **Team Lead**: Hanterar ett team i data science-enheten i ett företag. Ett team består av flera dataforskare. För en liten datascience-enhet kan gruppchefen och grupp leadet vara samma person.
   
3. **Projektledare**: Hanterar enskilda datavetares dagliga verksamhet i ett specifikt datavetenskapsprojekt.
   
4. **Projekt enskilda bidragsgivare:** Dataforskare, affärsanalytiker, dataingenjörer, arkitekter och andra som utför ett datavetenskapsprojekt.

> [!NOTE]
> Beroende på ett företags struktur och storlek kan en enskild person spela mer än en roll, eller mer än en person kan fylla en roll.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Uppgifter som ska slutföras av de fyra rollerna

I följande diagram visas de uppgifter på den högsta nivån för varje gruppdatavetenskapsprocessroll. Det här schemat och följande, mer detaljerade disposition av uppgifter för varje TDSP-roll kan hjälpa dig att välja den självstudiekurs du behöver baserat på ditt ansvar.

![Översikt över roller och uppgifter](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Gruppchefsuppgifter

Gruppchefen eller en utsedd TDSP-systemadministratör utför följande uppgifter för att anta TDSP:

- Skapar en Azure **DevOps-organisation** och ett gruppprojekt inom organisationen. 
- Skapar en **projektmalldatabas** i Azure DevOps-gruppprojektet och gör det från den projektmalldatabas som utvecklats av Microsoft TDSP-teamet. Microsoft TDSP-projektmallens databas innehåller:
  - En **standardiserad katalogstruktur**, inklusive kataloger för data, kod och dokument.
  - En uppsättning **standardiserade dokumentmallar** som styr en effektiv datavetenskapsprocess.
- Skapar en **verktygsdatabas**och gör det från den verktygsdatabas som utvecklats av Microsoft TDSP-teamet. TDSP-verktygsarkivet från Microsoft tillhandahåller en uppsättning användbara verktyg för att effektivisera en datavetares arbete. Microsoft-verktygsdatabasen innehåller verktyg för interaktiv datautforskning, analys, rapportering och baslinjemodellering och rapportering.
- Ställer in **säkerhetskontrollprincipen** för organisationskontot.

Detaljerade instruktioner finns i [Gruppchefsuppgifter för ett data science-team](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Team lead-uppgifter

Gruppledningen eller en utsedd projektadministratör slutför följande uppgifter för att anta TDSP:

- Skapar ett **teamprojekt** i gruppens Azure DevOps-organisation.
- Skapar **projektmalldatabasen** i projektet och hämtar den från den gruppprojektmalldatabas som har konfigurerats av grupphanteraren eller ombudet.
- Skapar **teamverktygsdatabasen,** gör det från gruppverktygsdatabasen och lägger till teamspecifika verktyg i databasen.
- Du kan också skapa [Azure-fillagring](https://azure.microsoft.com/services/storage/files/) för att lagra användbara datatillgångar för teamet. Andra gruppmedlemmar kan montera den här delade molnfilarkivet på sina analysskrivbord.
- Du kan också montera Azure-fillagringen på teamets **DSVM** och lägga till gruppdataresurser i den.
- Konfigurerar **säkerhetskontroll** genom att lägga till gruppmedlemmar och konfigurera deras behörigheter.

Detaljerade instruktioner finns i [Team Lead-uppgifter för ett data science-team](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Projekt lead-aktiviteter

Projektledningen slutför följande uppgifter för att anta TDSP:

- Skapar en **projektdatabas** i gruppprojektet och gör det från projektmalldatabasen.
- Du kan också skapa **Azure-fillagring** för att lagra projektets datatillgångar.
- Du kan också montera Azure-fillagringen i **DSVM** och lägga till projektdataresurser i den.
- Skapar **säkerhetskontroll** genom att lägga till projektmedlemmar och konfigurera deras behörigheter.

Detaljerade instruktioner finns i [Projektledaktiviteter för ett data science-team](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Projekt enskilda deltagaraktiviteter

Projektpersonen, vanligtvis en datavetare, utför följande uppgifter med hjälp av TDSP:

- Klonar **projektdatabasen** som har konfigurerats av projektledaren.
- Du kan också montera det delade teamet och projicera **Azure-fillagring** på sin **virtuella datavetenskapdator** (DSVM).
- Utför projektet.

Detaljerade instruktioner för introduktion till ett projekt finns i [Projekt enskilda deltagaruppgifter för ett datavetenskapslag](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Arbetsflöde för körning av datavetenskapsprojekt

Genom att följa relevanta självstudier kan datavetare, projektleads och gruppleads skapa arbetsobjekt för att spåra alla aktiviteter och faser för projekt från början till. Med hjälp av Azure Repos främjas samarbete mellan datavetare och säkerställer att artefakterna som genereras under projektkörningen är versionskontrollerade och delade av alla projektmedlemmar. Med Azure DevOps kan du länka dina Azure Boards-arbetsobjekt till dina Azure Repos-databasgrenar och enkelt spåra vad som har gjorts för ett arbetsobjekt.

Följande bild beskriver TDSP-arbetsflödet för projektkörning:

![Typiskt arbetsflöde för datavetenskapsprojekt](./media/roles-tasks/overview-project-execute.png)

Arbetsflödesstegen kan grupperas i tre aktiviteter:

- Projekt Leder genomföra sprint planering
- Data Forskare utvecklar `git` artefakter på grenar för att ta itu med arbetsobjekt
- Projektleads eller andra gruppmedlemmar gör kodgranskningar och sammanfogar arbetsgrenar till huvudgrenen

Detaljerade instruktioner om arbetsflöde för projektgenomförande finns i [Agil utveckling av datavetenskapliga projekt](agile-development.md).

## <a name="tdsp-project-template-repository"></a>Databas för TDSP-projektmall

Använd Microsoft TDSP-teamets [projektmallsdatabas](https://github.com/Azure/Azure-TDSP-ProjectTemplate) för att stödja effektiv projektgenomförande och samarbete. Databasen ger dig en standardiserad katalogstruktur och dokumentmallar som du kan använda för dina egna TDSP-projekt.

## <a name="next-steps"></a>Nästa steg

Utforska mer detaljerade beskrivningar av de roller och uppgifter som definieras av teamdatavetenskapsprocessen:

- [Gruppchefsuppgifter för ett data science-team](group-manager-tasks.md)
- [Team lead-uppgifter för ett data science-team](team-lead-tasks.md)
- [Projektledaraktiviteter för ett datavetenskapslag](project-lead-tasks.md)
- [Projekt enskilda deltagaruppgifter för ett data science-team](project-ic-tasks.md)
