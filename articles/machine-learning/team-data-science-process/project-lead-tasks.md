---
title: Aktiviteter för projektledaren i teamdatavetenskapsprocessen
description: En detaljerad genomgång av aktiviteterna för en projektledning i ett teamdatavetenskapsprocessteam
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a4d3a1480a852218e698862a509c4af45e49eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76714411"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Projektledaruppgifter i teamdatavetenskapsprocessen

I den här artikeln beskrivs aktiviteter som en *projektledare* slutför för att skapa en databas för deras projektgrupp i [TDSP (Team Data Science Process).](overview.md) TDSP är ett ramverk utvecklat av Microsoft som tillhandahåller en strukturerad sekvens av aktiviteter för att effektivt köra molnbaserade, prediktiva analyslösningar. TDSP är utformad för att förbättra samarbete och teaminlärning. En översikt över personalroller och tillhörande uppgifter för ett data science-team som standardiserar på TDSP finns i [Roller och uppgifter för teamdatavetenskapsprocess](roles-tasks.md).

En projektledare hanterar enskilda datavetares dagliga verksamhet på ett specifikt datavetenskapsprojekt i TDSP. I följande diagram visas arbetsflödet för projektledaktiviteter:

![Arbetsflöde för projektleduppgift](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Den här självstudien omfattar steg 1: Skapa projektdatabas och steg 2: Såddprojektdatabas från gruppdatabasen ProjectTemplate. 

Steg 3: Skapa funktionsarbetsobjekt för projekt och steg 4: Lägg till händelser för projektfaser, se [Agil utveckling av datavetenskapliga projekt](agile-development.md).

Steg 5: Skapa och anpassa lagrings-/analystillgångar och dela, om det behövs, se [Skapa teamdata och analysresurser](team-lead-tasks.md#create-team-data-and-analytics-resources).

Steg 6: Konfigurera säkerhetskontroll för projektdatabasen finns [i Lägga till gruppmedlemmar och konfigurera behörigheter](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> Den här artikeln använder Azure Repos för att konfigurera ett TDSP-projekt, eftersom det är så du implementerar TDSP på Microsoft. Om ditt team använder en annan kodvärdplattform är projektledaktiviteterna desamma, men sättet att slutföra dem kan vara annorlunda.

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att [gruppchefen](group-manager-tasks.md) och [gruppledningen](team-lead-tasks.md) har ställt in följande resurser och behörigheter:

- Azure **DevOps-organisationen** för din dataenhet
- Ett team **för** ditt datascience-team
- Teammall och **verktygsdatabaser**
- **Behörigheter** för ditt organisationskonto för att skapa och redigera databaser för ditt projekt

Om du vill klona databaser och ändra innehåll på din lokala dator eller virtuell dator (DSVM) eller konfigurera Azure-fillagring och montera det på din DSVM, måste du också överväga den här checklistan:

- En Azure-prenumeration.
- Git installerad på din maskin. Om du använder en DSVM är Git förinstallerad. Annars kan du se [bilagan plattformar och verktyg](platforms-and-tools.md#appendix).
- Om du vill använda en DSVM skapas och konfigureras Windows eller Linux DSVM i Azure. Mer information och instruktioner finns i [dokumentationen för virtuell dator för datavetenskap](/azure/machine-learning/data-science-virtual-machine/).
- För en Windows DSVM, [Git Autentiseringshanteraren (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerad på din dator. Bläddra ned till avsnittet Hämta och **installera** i *README.md* och välj det **senaste installationsprogrammet**. Hämta *.exe-installationsprogrammet* från installationssidan och kör det. 
- För en Linux DSVM, en SSH offentlig nyckel som konfigureras på din DSVM och läggas till i Azure DevOps. Mer information och instruktioner finns i avsnittet **Skapa SSH-nyckel** i [bilagan Plattformar och verktyg](platforms-and-tools.md#appendix). 

## <a name="create-a-project-repository-in-your-team-project"></a>Skapa en projektdatabas i gruppprojektet

Så här skapar du en projektdatabas i gruppens **MyTeam-projekt:**

1. Gå till gruppens **projektSammanfattningssida** på *\//\<https:\<servernamn>/\<organisationsnamn>/ gruppnamn>*, till exempel **https:\//dev.azure.com/DataScienceUnit/MyTeam**och välj **Repos** från vänster navigering. 
   
1. Markera databasnamnet högst upp på sidan och välj sedan **Ny databas** i listrutan.
   
   ![Välj Ny databas](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. Kontrollera att **Git** är markerat under **Text**i dialogrutan **Skapa en ny databas** . Ange *DSProject1* under **Databasnamn**och välj sedan **Skapa**.
   
   ![Skapa databas](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Bekräfta att du kan se den nya **DSProject1-databasen** på sidan projektinställningar. 
   
   ![Projektdatabas i Projektinställningar](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Importera gruppmallen till projektdatabasen

Så här fyller du i projektdatabasen med innehållet i gruppmalldatabasen:

1. På gruppens **projektsammanfattningssida** väljer du **Repos** i den vänstra navigeringen. 
   
1. Markera databasnamnet högst upp på sidan och välj **DSProject1** i listrutan.
   
1. På sidan **DSProject1 väljer** du **Importera**. 
   
   ![Välj Importera](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. I dialogrutan **Importera en Git-databas** väljer du **Git** som **källtyp**och anger URL:en för **databasen TeamTemplate** under **Klon-URL**. URL:en är *\//\<https: servernamn\<>/ organisationsnamn\<>/ gruppnamn>/_git/\<gruppmallens databasnamn>*. Till exempel: **\/https: /dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. Välj **Importera**. Innehållet i gruppmalldatabasen importeras till projektdatabasen. 
   
   ![Importera gruppmalldatabas](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Om du behöver anpassa innehållet i projektdatabasen så att det uppfyller projektets specifika behov kan du lägga till, ta bort eller ändra databasfiler och mappar. Du kan arbeta direkt i Azure Repos eller klona databasen till din lokala dator eller DSVM, göra ändringar och genomföra och skicka uppdateringar till den delade projektdatabasen. Följ instruktionerna på [Anpassa innehållet i teamdatabaserna](team-lead-tasks.md#customize-the-contents-of-the-team-repositories).

## <a name="next-steps"></a>Nästa steg

Här är länkar till detaljerade beskrivningar av andra roller och uppgifter som definieras av Team Data Science Process:

- [Gruppchefsuppgifter för ett data science-team](group-manager-tasks.md)
- [Team lead-uppgifter för ett data science-team](team-lead-tasks.md)
- [Enskilda deltagaruppgifter för ett data science-team](project-ic-tasks.md)
