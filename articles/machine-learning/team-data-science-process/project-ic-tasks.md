---
title: "Gruppuppgifter vetenskap av data för en enskild bidragsgivare - Azure | Microsoft Docs"
description: "En översikt över aktiviteter för en enskild bidragsgivare på en datavetenskap team projekt."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev;
ms.openlocfilehash: 526260c3f61a203fe2770a0c6d0d23d95916a2b7
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="individual-contributor-tasks"></a>Enskilda aktiviteter för deltagare

Det här avsnittet beskrivs de åtgärder som en enskild bidragsgivare är förväntas utföra för sina datavetenskap team. Målet är att upprätta grupp användare miljö som standardiserar på den [Team datavetenskap Process](overview.md) (TDSP). En beskrivning av rollerna personal och förknippade aktiviteter som hanteras av ett team för datavetenskap standardisera på den här processen finns [Team datavetenskap Process roller och aktiviteter](roles-tasks.md).

Uppgifter i projektet enskilda deltagare (datavetare) för att ställa in miljön för projektet TDSP illustreras på följande sätt: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** är lagringsplatsen för att underhålla din grupp om du vill dela användbara verktyg för hela gruppen. 
- **TeamUtilities** är lagringsplatsen för att underhålla din grupp specifikt för din grupp. 

Anvisningar om hur du kör ett datavetenskap projekt under TDSP finns [körning av datavetenskap projekt](project-execution.md). 

>[AZURE.NOTE] Vi beskriver de steg som krävs för att ställa in en TDSP teammiljö med hjälp av Visual Studio Team Services VSTS () i följande instruktioner. Anger vi hur du utför dessa uppgifter med VSTS eftersom det är hur vi implementera TDSP på Microsoft. Om en annan värd för koden plattform används för din grupp, ändrar inte uppgifter som måste slutföras av team lead normalt. Men sättet att utföra dessa uppgifter ska vara olika.


## <a name="repositories-and-directories"></a>Databaser och kataloger

Den här kursen använder förkortade namn för databaser och kataloger. Dessa namn gör det lättare att följa åtgärder mellan databaser och kataloger. Notation (**R** för Git-databaser och **D** för lokala kataloger på dina DSVM) används i följande avsnitt:

- **R2**: den GroupUtilities databasen på Git som din gruppansvarig har ställts in på servern VSTS grupp.
- **R4**: den TeamUtilities databasen på Git som team-ledtid har ställts in.
- **R5**: I projektet databasen på Git som har ställts in av ditt projekt lead.
- **D2**: den lokala katalogen klonad från R2.
- **D4**: den lokala katalogen klonad från R4.
- **D5**: den lokala katalogen klonad från R5.


## <a name="step-0-prerequisites"></a>Steg-0: förutsättningar

Krav uppfylls genom att genomföra aktiviteter som tilldelats av din gruppansvarig som beskrivs i [Gruppansvarig uppgifter för ett datavetenskap team](group-manager-tasks.md). Följande krav måste uppfyllas innan du påbörjar team lead uppgifter för att sammanfatta här: 
- Grupp-hanteraren har ställt in den **GroupUtilities** databasen (eventuella). 
- Team-ledtid har ställt in den **TeamUtilities** databasen (eventuella).
- Ditt projekt lead har ställt in projekt-databasen. 
- Du har lagts till lagringsplatsen för projektet av dina projekt lead med behörighet att klona från och skickar tillbaka till projektet lagringsplatsen.

Andra **TeamUtilities** databasen, nödvändiga är valfritt, beroende på om din grupp har en team-specifika verktyg-databas. Om någon av andra tre förutsättningar inte har slutförts, kontakta ditt team lead, projekt-ledtid eller deras ombud för att konfigurera genom att följa anvisningarna för [Team leda uppgifter för ett datavetenskap team](team-lead-tasks.md) eller för [ Projektet Lead uppgifter för ett datavetenskap team](project-lead-tasks.md).

- Git måste vara installerad på datorn. Om du använder en virtuell dator på vetenskap (DSVM) Git redan har installerats och du är redo att börja. Annars går du till den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix).  
- Om du använder en **Windows DSVM**, måste du ha [Git autentiseringsuppgifter Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerat på datorn. I filen README.md rulla ned till den **ladda ned och installera** avsnittet och klicka på den *senaste installer*. Då kommer du till sidan senaste installer. Hämta .exe installationsprogrammet härifrån och köra den. 
- Om du använder **Linux DSVM**, skapar en offentlig SSH-nyckel på din DSVM och lägga till den i din grupp VSTS server. Mer information om SSH finns i **skapa SSH offentlig nyckel** i avsnittet den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix). 
- Om din grupp och/eller projekt lead har skapat några Azure fillagring som du behöver montera din DSVM, bör du hämta Azure file storage-informationen från dem. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>Steg 1 – 3: klona grupp och team projekt databaser till den lokala datorn

Det här avsnittet innehåller instruktioner för slutfört projekt enskilda deltagare först tre uppgifter: 

- Klona den **GroupUtilities** databasen R2 till D2
- Klona den **TeamUtilities** databasen R4 till D4 
- Klona den **projekt** databasen R5 till D5.

Skapa en katalog på din lokala dator ***C:\GitRepos*** (för Windows) eller ***$home/GitRepos*** (forLinux) och ändra till katalogen. 

Kör något av följande kommandon (som passar ditt operativsystem) för att klona din **GroupUtilities**, **TeamUtilities**, och **projekt** databaser till kataloger på dina lokal dator: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Bekräfta att du ser de tre mapparna under projektkatalogen.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Bekräfta att du ser de tre mapparna under projektkatalogen.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>Steg 4 och 5: montera Azure fillagring till din DSVM (valfritt)

Att montera Azure file storage till din DSVM kan se anvisningarna i avsnittet 4 i den [Team lead uppgifter för ett datavetenskap team](team-lead-tasks.md)

## <a name="next-steps"></a>Nästa steg

Här är länkar till mer detaljerade beskrivningar av de roller och aktiviteter som definierats av Team av vetenskapliga data:

- [Gruppera Manager aktiviteter för en datavetenskap-teamet](group-manager-tasks.md)
- [Gruppuppgifter Lead för ett datavetenskap team](team-lead-tasks.md)
- [Projektet Lead uppgifter för ett datavetenskap team](project-lead-tasks.md)
- [Projektet enskilda deltagare för ett datavetenskap team](project-ic-tasks.md)

