---
title: Uppgifter för en enskild medarbetare i teamdatavetenskapsprocessen
description: En detaljerad genomgång av aktiviteterna för en enskild medarbetare i ett data science-teamprojekt.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d9942c31b63de77196b8b51b88376cb8ef74b990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721259"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Uppgifter för en enskild medarbetare i teamdatavetenskapsprocessen

I det här avsnittet beskrivs de aktiviteter som en *enskild deltagare* slutför för att skapa ett projekt i TDSP [(Team Data Science Process).](overview.md) Målet är att arbeta i en samarbetsgruppsmiljö som standardiserar på TDSP. TDSP är utformad för att förbättra samarbete och teaminlärning. En översikt över de personalroller och deras associerade uppgifter som hanteras av ett data science-team som standardiserar på TDSP finns i [Roller och uppgifter för teamdatavetenskap.](roles-tasks.md)

Följande diagram visar de aktiviteter som projekt enskilda deltagare (datavetare) har slutfört för att konfigurera sin gruppmiljö. Instruktioner om hur du utför ett data science-projekt under TDSP finns i [Genomförande av datavetenskapliga projekt](project-execution.md). 

![Enskilda deltagaruppgifter](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** är den databas som projektgruppen har för att dela projektmallar och resurser.
- **TeamUtilities** är den verktygsdatabas som teamet underhåller specifikt för ditt team. 
- **GroupUtilities** är den databas som gruppen har för att dela användbara verktyg i hela gruppen. 

> [!NOTE] 
> I den här artikeln används Azure Repos och en virtuell datavetenskapdator (DSVM) för att konfigurera en TDSP-miljö, eftersom det är så här du implementerar TDSP på Microsoft. Om ditt team använder andra plattformar för kodhosting eller utveckling är de enskilda deltagaruppgifterna desamma, men sättet att slutföra dem kan vara annorlunda.

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att följande resurser och behörigheter har ställts in av [gruppchefen,](group-manager-tasks.md) [gruppledningen](team-lead-tasks.md)och [projektledaren:](project-lead-tasks.md)

- Azure **DevOps-organisationen** för din data science-enhet
- En **projektdatabas** som har konfigurerats av projektet leder till att dela projektmallar och resurser
- **GroupUtilities-** och **TeamUtilities-databaser** som inrättats av gruppchefen och gruppledningen, om tillämpligt
- **Azure-fillagring** konfigurerad för delade resurser för ditt team eller projekt, om tillämpligt
- **Behörigheter** för dig att klona från och skicka tillbaka till projektdatabasen 

Om du vill klona databaser och ändra innehåll på din lokala dator eller DSVM, eller montera Azure-fillagring till din DSVM, måste du överväga den här checklistan:

- En Azure-prenumeration.
- Git installerad på din maskin. Om du använder en DSVM är Git förinstallerad. Annars kan du se [bilagan plattformar och verktyg](platforms-and-tools.md#appendix).
- Om du vill använda en DSVM skapas och konfigureras Windows eller Linux DSVM i Azure. Mer information och instruktioner finns i [dokumentationen för virtuell dator för datavetenskap](/azure/machine-learning/data-science-virtual-machine/).
- För en Windows DSVM, [Git Autentiseringshanteraren (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerad på din dator. Bläddra ned till avsnittet Hämta och **installera** i *README.md* och välj det **senaste installationsprogrammet**. Hämta *.exe-installationsprogrammet* från installationssidan och kör det. 
- För en Linux DSVM, en SSH offentlig nyckel som konfigureras på din DSVM och läggas till i Azure DevOps. Mer information och instruktioner finns i avsnittet **Skapa SSH-nyckel** i [bilagan Plattformar och verktyg](platforms-and-tools.md#appendix). 
- Azure-fillagringsinformationen för alla Azure-fillagringar som du behöver montera på din DSVM. 

## <a name="clone-repositories"></a>Klona databaser

Om du vill arbeta med databaser lokalt och driva dina ändringar upp till det delade teamet och projektarkiven kopierar eller *klonar* du först databaserna till den lokala datorn. 

1. I Azure DevOps går du till gruppens projektsammanfattningssida på *\//\<https: servernamn>/\<organisationsnamn>/\<gruppnamnet>*, till exempel **https:\//dev.azure.com/DataScienceUnit/MyTeam**.
   
1. Välj **Repos** i den vänstra navigeringen och högst upp på sidan väljer du den databas som du vill klona.
   
1. På reposidan väljer du **Klona** längst upp till höger.
   
1. I dialogrutan **Klona databas** väljer du **HTTPS** för en HTTP-anslutning eller **SSH** för en SSH-anslutning och kopierar klon-URL:en under **Kommandoraden** till Urklipp.
   
   ![Klona repo](./media/project-ic-tasks/clone.png)
   
1. På din lokala dator eller DSVM skapar du följande kataloger:
   
   - För Windows: **C:\GitRepos**
   - För Linux: **$home/GitRepos**
   
1. Ändra till katalogen som du skapade.
   
1. I Git Bash kör `git clone <clone URL>` du kommandot för varje databas som du vill klona. 
   
   Följande kommando klonar till exempel **TeamUtilities-databasen** till *MyTeam-katalogen* på den lokala datorn. 
   
   **HTTPS-anslutning:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH-anslutning:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Bekräfta att du kan se mapparna för de klonade databaserna i den lokala projektkatalogen.
   
   ![Tre lokala databasmappar](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Montera Azure-fillagring till din DSVM

Om ditt team eller projekt har delade resurser i Azure-fillagring monterar du fillagringen på din lokala dator eller DSVM. Följ instruktionerna på [Mount Azure-fillagring på din lokala dator eller DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Nästa steg

Här är länkar till detaljerade beskrivningar av andra roller och uppgifter som definieras av Team Data Science Process:

- [Gruppchefsuppgifter för ett data science-team](group-manager-tasks.md)
- [Team lead-uppgifter för ett data science-team](team-lead-tasks.md)
- [Projektledaraktiviteter för ett datavetenskapslag](project-lead-tasks.md)

