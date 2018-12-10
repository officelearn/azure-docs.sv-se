---
title: Uppgifter för en enskild deltagare i Team Data Science Process
description: En översikt över aktiviteter för en enskild deltagare på ett teamprojekt för data science.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6684cc9a0166df9c6d230f6b2a8c05acec5f91b3
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53136769"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Uppgifter för en enskild deltagare i Team Data Science Process

Det här avsnittet beskrivs de uppgifter som en enskild deltagare är väntat att slutföra sina data science-teamet. Målet är att upprätta samarbetsteam miljö som standardiserar på den [Team Data Science Process](overview.md) (TDSP). En översikt över rollerna som personal och förknippade aktiviteter som hanteras av en datavetenskapliga team standardisera om den här processen finns i [roller för Team Data Science Process och uppgifter](roles-tasks.md).

Uppgifter i projektet enskilda deltagare (dataexperter) för att ställa in TDSP-miljön för projektet illustreras på följande sätt: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** är lagringsplatsen för att underhålla din grupp om du vill dela användbara verktyg för hela gruppen. 
- **TeamUtilities** är den lagringsplats som ditt team för att underhålla specifikt för ditt team. 

Anvisningar om hur du kör ett datavetenskapsprojekt under TDSP finns i [körning av Dataforskningsprojekt](project-execution.md). 

>[AZURE.NOTE] Vi beskriver de steg som krävs för att ställa in en TDSP teammiljö med Azure DevOps i följande anvisningar. Vi anger du hur du utför dessa uppgifter med Azure DevOps eftersom det är hur vi implementerar TDSP på Microsoft. Om du använder en annan värd för koden plattform för din grupp, ändra inte de uppgifter som måste slutföras av gruppledare Allmänt. Men sättet att utföra dessa uppgifter kommer att vara olika.


## <a name="repositories-and-directories"></a>Databaser och kataloger

Den här självstudien använder förkortade namnen för databaser och kataloger. Dessa namn gör det lättare att följa åtgärderna mellan databaser och kataloger. Den här notationen (**R** för Git-lagringsplatser och **D** för lokala kataloger på din DSVM) används i följande avsnitt:

- **R2**: The GroupUtilities databasen på Git som din gruppansvarig har ställts in på din server för Azure DevOps-gruppen.
- **R4**: The TeamUtilities databasen på Git som din gruppledare har ställts in.
- **R5**: The projektets lagringsplats på Git som har ställts in av din projektledare.
- **D2**: den lokala katalogen klonas från R2.
- **D4**: den lokala katalogen klonas från R4.
- **D5**: den lokala katalogen klonas från R5.


## <a name="step-0-prerequisites"></a>Steg-0: krav

Kraven är uppfyllda genom att slutföra de uppgifter som tilldelats till din gruppchef som beskrivs i [Gruppansvarig uppgifter för team data science](group-manager-tasks.md). Följande krav måste uppfyllas innan du påbörjar lead gruppaktiviteter för att sammanfatta här: 
- Chefen grupp har konfigurerat den **GroupUtilities** databasen (om sådan finns). 
- Din gruppledare har konfigurerat den **TeamUtilities** databasen (om sådan finns).
- Din projektledare har konfigurerat på projektets lagringsplats. 
- Du har lagts till projektets lagringsplats genom din projektledare med behörighet att klona från och skicka tillbaka till projektets lagringsplats.

Andra **TeamUtilities** lagringsplatsen, nödvändiga är valfritt, beroende på om ditt team har en lagringsplats för team-specifika verktyg. Om någon av de andra tre förutsättningar inte har slutförts, kontaktar du din gruppledare, din projektledare eller deras delegerar du konfigurera den genom att följa anvisningarna för [gruppledare uppgifter för team data science](team-lead-tasks.md) eller för [ Projektet Lead-uppgifter för team data science](project-lead-tasks.md).

- Git måste installeras på din dator. Om du använder en virtuell dator på datavetenskap (DSVM) Git har installerats i förväg och du är redo att börja. Annars läser den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix).  
- Om du använder en **Windows DSVM**, måste du ha [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerat på datorn. README.md-filen, rulla ned till den **ladda ned och installera** och klicka på *senaste installationsprogrammet*. Detta tar dig till den senaste sidan för installationsprogrammet. Ladda ned installationsprogrammet .exe här och kör den. 
- Om du använder **Linux DSVM**, skapa en offentlig SSH-nyckel för din DSVM och lägga till den i din grupp Azure DevOps-tjänsterna. Mer information om SSH finns i den **offentlig skapa SSH-nyckel** i avsnittet den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix). 
- Om ditt team och/eller projektgrupp lead har skapat vissa Azure-fillagring som du behöver för att montera för din DSVM, bör du få Azure file storage information från dem. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>Steg 1 – 3: klona grupp, team och projekt databaser till lokal dator

Det här avsnittet innehåller anvisningar om slutföra tre första uppgifterna med enskilda deltagare i projektet: 

- Klona den **GroupUtilities** lagringsplatsen R2 till D2
- Klona den **TeamUtilities** lagringsplatsen R4 till D4 
- Klona den **projekt** lagringsplatsen R5 till D5.

Skapa en katalog på den lokala datorn ***C:\GitRepos*** (för Windows) eller ***$home/GitRepos*** (forLinux), och ändra sedan till den katalogen. 

Kör något av följande kommandon (som passar ditt operativsystem) för att klona din **GroupUtilities**, **TeamUtilities**, och **projekt** databaser till kataloger på din lokal dator: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Kontrollera att du ser de tre mapparna under projektkatalogen.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Kontrollera att du ser de tre mapparna under projektkatalogen.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>Steg 4 – 5: montera Azure file storage för din DSVM (valfritt)

Se anvisningarna i avsnitt 4 för att montera Azure file storage för din DSVM den [lead gruppaktiviteter för team data science](team-lead-tasks.md)

## <a name="next-steps"></a>Nästa steg

Här finns länkar till mer detaljerade beskrivningar av de roller och uppgifter som definieras av Team Data Science Process:

- [Gruppansvarig uppgifter för team data science](group-manager-tasks.md)
- [Lead gruppaktiviteter för team data science](team-lead-tasks.md)
- [Lead projektaktiviteter för team data science](project-lead-tasks.md)
- [Projektet enskilda deltagare för team data science](project-ic-tasks.md)

