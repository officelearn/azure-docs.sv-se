---
title: Gruppuppgifter datavetenskap processen projektet leda - Azure | Microsoft Docs
description: En översikt över aktiviteter för ett projekt leda på en datavetenskap team projekt.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 58c5826240b7c49ba29c0d8e86a2896e3ce2f7f7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838406"
---
# <a name="project-lead-tasks"></a>Project Lead-aktiviteter

Den här självstudiekursen beskrivs vilka aktiviteter som är ett projekt ska slutföras för hans/hennes projektgruppen. Målet är att upprätta grupp användare miljö som standardiserar på den [Team datavetenskap Process](overview.md) (TDSP). TDSP är ett ramverk som utvecklats av Microsoft som innehåller en strukturerad sekvensen av aktiviteter att köra molnbaserade, prediktiva Analyslösningar effektivt. En beskrivning av rollerna personal och förknippade aktiviteter som hanteras av ett team för datavetenskap standardisera på den här processen finns [Team datavetenskap Process roller och aktiviteter](roles-tasks.md).

En **projekt leda** hanterar dagliga aktiviteter enskilda forskare med ett projekt för vetenskap specifika data. Arbetsflöde för aktiviteter som ska slutföras av projektet leder till att konfigurera den här miljön illustreras i följande bild:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Det här avsnittet beskriver uppgifter 1,2 och 6 för det här arbetsflödet för projektet leads för närvarande.

>[AZURE.NOTE] Vi beskriver de steg som krävs för att ställa in en TDSP teammiljö för ett projekt med Visual Studio Team Services VSTS () i följande instruktioner. Anger vi hur du utför dessa uppgifter med VSTS eftersom det är hur vi implementera TDSP på Microsoft. Om en annan värd för koden plattform används för din grupp, ändrar inte uppgifter som måste slutföras av team lead normalt. Men sättet att utföra dessa uppgifter ska vara olika.


## <a name="repositories-and-directories"></a>Databaser och kataloger

Den här kursen använder förkortade namn för databaser och kataloger. Dessa namn gör det lättare att följa åtgärder mellan databaser och kataloger. Notation (R för Git-databaser) och D för lokala kataloger på dina DSVM används i följande avsnitt:

- **R3**: teamet **ProjectTemplate** databasen på Git team-ledtid har ställt in.
- **R5**: projekt-databasen på Git du konfigurera för projektet.
- **D3**: den lokala katalogen klonad från R3.
- **D5**: den lokala katalogen klonad från R5.


## <a name="0-prerequisites"></a>0. Förutsättningar

Krav uppfylls genom att genomföra aktiviteter som tilldelats av din gruppansvarig som beskrivs i [Gruppansvarig uppgifter för ett datavetenskap team](group-manager-tasks.md) och att du kopplar lead som beskrivs i [Team lead uppgifter för ett datavetenskap team](team-lead-tasks.md). 

Följande krav måste uppfylla innan du börjar team lead uppgifter för att sammanfatta här: 

- Din **grupp VSTS server** (eller gruppkonto på andra koden värd plattform) har ställts in av din grupp manager.
- Din **TeamProjectTemplate databasen** (R3) har ställts in på ditt gruppkonto av ditt team lead på plattformen värd för kod som du tänker använda.
- Du har **behörighet** av ditt team leda till att skapa databaser på din gruppkonto för din grupp.
- Git måste vara installerad på datorn. Om du använder en virtuell dator på vetenskap (DSVM) Git redan har installerats och du är redo att börja. Annars går du till den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix).  
- Om du använder en **Windows DSVM**, måste du ha [Git autentiseringsuppgifter Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerat på datorn. I filen README.md rulla ned till den **ladda ned och installera** avsnittet och klicka på den *senaste installer*. Då kommer du till sidan senaste installer. Hämta .exe installationsprogrammet härifrån och köra den. 
- Om du använder **Linux DSVM**, skapar en offentlig SSH-nyckel på din DSVM och lägga till den i din grupp VSTS server. Mer information om SSH finns i **skapa SSH offentlig nyckel** i avsnittet den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Skapa en databas i projektet (R5)

- Logga in på din grupp VSTS server på *https://\<VSTS servernamn\>. visualstudio.com*. 
- Under **senaste projekt & team**, klickar du på **Bläddra**. Ett fönster som öppnas visas alla grupprojekt på VSTS-servern. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Klicka på teamprojektets namn som du ska skapa projekt-databasen. I det här exemplet klickar du på **MyTeam**. 
- Klicka på **analysera** omdirigeras till startsidan för grupprojektet **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Klicka på **samarbeta på koden** omdirigeras till sidan git av ditt team projekt.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Klicka på nedpilen längst upp till vänster och välj **+ ny lagringsplats**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- I den **skapar en ny lagringsplats** och ange ett namn för ditt projekt git-lagringsplats. Kontrollera att du väljer **Git** som typ av databasen. I det här exemplet använder vi namnet *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Att skapa din ***DSProject1*** projektet git-lagringsplatsen, klicka på **skapa**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Startvärde för DSProject1 projekt databasen

Den här uppgiften är att seed den **DSProject1** projekt databasen (R5) från din mall lagringsplats för team projekt (R3). Seeding används kataloger D3 och D5 på din lokala DSVM som mellanliggande fristående platser. Sammanfattningsvis seeding sökvägen är: R3 -> D3 -> D5 R5 ->.

Om du behöver anpassa din **DSProject1** projekt lagringsplatsen för att uppfylla vissa specifika projekt krävs, gör du det i det näst sista steget i följande procedur. Här följer en sammanfattning av de steg som används som startvärde för innehållet i den **DSProject1** projekt-databasen. De enskilda stegen motsvarar underavsnitt i seeding procedur:

- Klona team projekt mallen databasen till lokal katalog: team R3 - klona om du vill -> lokala D3.
- Klona DSProject1 databasen till en lokal katalog: team R5 - klona om du vill -> lokala D5.
- Kopiera klonade team projekt mallens innehåll till lokala kloning av DSProject1 databasen: D3 - innehållet kopieras till -> D5.
- (Valfritt) Anpassning av lokala D5.
- Över lokala DSProject1 innehållet till team databaser: D5 - innehållet Lägg till team R5 ->.


### <a name="clone-your-team-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Klona team projekt mallen databasen (R3) till en katalog (D3) på den lokala datorn.

Skapa en katalog på den lokala datorn:

- *C:\GitRepos\MyTeamCommon* för Windows 
- *$home/GitRepos/MyTeamCommon* för Linux

Ändra till katalogen. Kör sedan följande kommando för att klona team projekt mallen databasen till den lokala datorn. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Om du använder VSTS som värd för kod-plattform vanligtvis den *HTTPS-URL för ditt team projekt mallen lagringsplats* är:

 ***https://\<VSTS servernamn\>.visualstudio.com/\<ditt team projektnamn\>/_git/\<ditt team projekt databasen mallnamn\>***. 

I det här exemplet har vi:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Om du använder VSTS som värd för kod-plattform vanligtvis den *SSH URL för team projekt mallen databasen* är:

***SSH: / /\<VSTS servernamn\>@\<VSTS servernamn\>.visualstudio.com:22/\<din Teamprojektets namn > /_git/\<ditt team projekt databasen mallnamn \>.*** 

I det här exemplet har vi:

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Klona lagringsplatsen DSProject1 (R5) till en katalog (D5) på den lokala datorn

Gå till katalogen för **GitRepos**, och kör följande kommando för att klona lagringsplatsen för projektet till den lokala datorn. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Om du använder VSTS som värd för kod-plattform vanligtvis den _HTTPS-URL för projektet lagringsplats_ är ***https://\<VSTS servernamn\>.visualstudio.com/\<ditt Team Projektet namn > /_git/ < ditt projektnamn databasen\>***. I det här exemplet har vi ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Om du använder VSTS som värd för kod-plattform vanligtvis den _SSH URL för projektet databasen_ är _ssh: / / < VSTS servernamn\>@< VSTS servernamn\>.visualstudio.com:22/<Your Team Project Name> / \_git / < ditt projektnamn databasen\>. I det här exemplet har vi ***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Kopiera innehållet i D3 till D5 

Nu i din lokala dator måste du kopiera innehållet i _D3_ till _D5_, förutom git-metadata i .git directory. Följande skript kommer att göra jobbet. Se till att ange korrekt och fullständig sökvägar till kataloger. Källmapp är en för ditt team (_D3_); målmappen är en för ditt projekt (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Nu kan du se i _DSProject1_ mapp, kopieras alla filer (med undantag av .git) från _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Nu kan du se i _DSProject1_ mapp, kopieras alla filer (utom metadata i .git) från _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Anpassa D5 om du behöver (valfritt)

Om ditt projekt måste vissa specifika kataloger eller dokument, än de som du får från mallen team projekt (kopieras till katalogen D5 i föregående steg), kan du anpassa innehållet i D5 nu. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-vsts-server"></a>Lägga till innehållet i DSProject1 i D5 R5 på din grupp VSTS server

Nu måste du push innehållet **_DSProject1_** till _R5_ databasen i projektet team på din grupp VSTS server. 


- Ändra till katalogen **D5**. 
- Använd följande git-kommandon för att lägga till innehållet i **D5** till **R5**. Kommandona är samma för både Windows- och Linux-system. 
    
    Git status git lägga till.
    Git commit -m ”push från win DSVM” git push
    
- Bekräfta ändring och push. 

>[AZURE.NOTE] Om det här är första gången du genomför en Git-lagringsplatsen, måste du konfigurera globala parametrar *user.name* och *user.email* innan du kör den `git commit` kommando. Kör följande två kommandon:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Om du checkar in till flera Git-databaser, Använd samma namn och e-postadress i alla. Med samma namn och e-postadress bevisar praktiskt vid ett senare tillfälle när du skapar PowerBI instrumentpaneler för att spåra Git-aktiviteter i flera databaser.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Skapa och montera Azure file storage som projektresurser (valfritt)

Om du vill skapa Azure file storage för att dela data, till exempel projektet rådata eller funktioner som genererats för ditt projekt, så att alla projektmedlemmar har åtkomst till samma datauppsättningar från flera DSVMs Följ instruktionerna i avsnitt 3 och 4 i [ Gruppuppgifter Lead för ett datavetenskap team](team-lead-tasks.md). 


## <a name="next-steps"></a>Nästa steg

Här är länkar till mer detaljerade beskrivningar av de roller och aktiviteter som definierats av Team av vetenskapliga data:

- [Gruppera Manager aktiviteter för en datavetenskap-teamet](group-manager-tasks.md)
- [Gruppuppgifter Lead för ett datavetenskap team](team-lead-tasks.md)
- [Projektet Lead uppgifter för ett datavetenskap team](project-lead-tasks.md)
- [Projektet enskilda deltagare för ett datavetenskap team](project-ic-tasks.md)