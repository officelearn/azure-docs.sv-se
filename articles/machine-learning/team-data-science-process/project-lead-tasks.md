---
title: Team Data Science Process-projektbeskrivning uppgifter – Azure | Microsoft Docs
description: En översikt över aktiviteter för en projektledare på ett teamprojekt för data science.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 0b5129c4642a0c4381591f751bc4d5a51990b256
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442905"
---
# <a name="project-lead-tasks"></a>Project Lead-aktiviteter

Den här självstudien beskrivs de uppgifter som är en projektledare väntat att slutföra för hans/hennes projektteamet. Målet är att upprätta samarbetsteam miljö som standardiserar på den [Team Data Science Process](overview.md) (TDSP). TDSP är ett ramverk som utvecklats av Microsoft och som tillhandahåller en strukturerade sekvensen av aktiviteter att köra molnbaserad förutsägande Analyslösningar effektivt. En översikt över rollerna som personal och förknippade aktiviteter som hanteras av en datavetenskapliga team standardisera om den här processen finns i [roller för Team Data Science Process och uppgifter](roles-tasks.md).

En **projektbeskrivning** hanterar dagliga aktiviteter för enskilda datapersonal i en specifik datavetenskapsprojekt. Arbetsflödet för aktiviteter som ska slutföras av projektet leder till att konfigurera den här miljön illustreras i följande bild:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Det här avsnittet innehåller för närvarande uppgifter 1,2 och 6 av det här arbetsflödet för projektet leads.

>[AZURE.NOTE] Vi beskriver de steg som krävs för att ställa in en TDSP teammiljö för ett projekt med Azure DevOps i följande anvisningar. Vi anger du hur du utför dessa uppgifter med Azure DevOps eftersom det är hur vi implementerar TDSP på Microsoft. Om du använder en annan värd för koden plattform för din grupp, ändra inte de uppgifter som måste slutföras av gruppledare Allmänt. Men sättet att utföra dessa uppgifter kommer att vara olika.


## <a name="repositories-and-directories"></a>Databaser och kataloger

Den här självstudien använder förkortade namnen för databaser och kataloger. Dessa namn gör det lättare att följa åtgärderna mellan databaser och kataloger. Den här notationen (R för Git-lagringsplatser) och D för lokala kataloger på din DSVM används i följande avsnitt:

- **R3**: teamet **ProjectTemplate** databasen på Git din gruppledare har konfigurerat.
- **R5**: på projektets lagringsplats på Git som du konfigurerar för ditt projekt.
- **D3**: den lokala katalogen klonas från R3.
- **D5**: den lokala katalogen klonas från R5.


## <a name="0-prerequisites"></a>0. Förutsättningar

Kraven är uppfyllda genom att slutföra de uppgifter som tilldelats till din gruppchef som beskrivs i [Gruppansvarig uppgifter för team data science](group-manager-tasks.md) och att du kopplar lead som beskrivs i [lead gruppaktiviteter för team data science](team-lead-tasks.md). 

Följande krav måste uppfylla innan du börjar lead gruppaktiviteter för att sammanfatta här: 

- Din **gruppen Azure DevOps-tjänsterna** (eller gruppkonto på andra kodvärd plattform) har ställts in av hanteraren för gruppen.
- Din **TeamProjectTemplate databasen** (R3) har ställts in under gruppkontot för av din gruppledare på kodvärd plattformen som du tänker använda.
- Du har **behörighet** av din gruppledare för att skapa databaser på din gruppkonto för ditt team.
- Git måste installeras på din dator. Om du använder en virtuell dator på datavetenskap (DSVM) Git har installerats i förväg och du är redo att börja. Annars läser den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix).  
- Om du använder en **Windows DSVM**, måste du ha [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerat på datorn. README.md-filen, rulla ned till den **ladda ned och installera** och klicka på *senaste installationsprogrammet*. Detta tar dig till den senaste sidan för installationsprogrammet. Ladda ned installationsprogrammet .exe här och kör den. 
- Om du använder **Linux DSVM**, skapa en offentlig SSH-nyckel för din DSVM och lägga till den i din grupp Azure DevOps-tjänsterna. Mer information om SSH finns i den **offentlig skapa SSH-nyckel** i avsnittet den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Skapa en lagringsplats för projektet (R5)

- Logga in på din grupp Azure DevOps-tjänster på *https://\<Azure DevOps-tjänstnamn\>. visualstudio.com*. 
- Under **senaste projekt och team**, klickar du på **Bläddra**. Ett fönster som visar en lista över alla projekt för Azure DevOps-tjänsterna. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Klicka på projektnamnet där du ska skapa projektets lagringsplats. I det här exemplet klickar du på **MyTeam**. 
- Klicka sedan på **analysera** så att de dirigeras till sidan för projektet **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Klicka på **samarbeta på kod** så att de dirigeras till sidan med git i ditt projekt.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Klicka på nedpilen i det övre vänstra hörnet och välj **+ ny lagringsplats**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- I den **skapar en ny lagringsplats** fönstret, ange ett namn för projektet git-lagringsplatsen. Kontrollera att du väljer **Git** som typ av databasen. I det här exemplet använder vi namnet *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Skapa din ***DSProject1*** projektet git-lagringsplats, klicka på **skapa**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Dirigera DSProject1 projektets lagringsplats

Den här uppgiften är att seed den **DSProject1** projektets lagringsplats (R5) från ditt projekt mallagret (R3). Seeding proceduren använder kataloger D3 och D5 på din lokala DSVM som mellanliggande mellanlagring platser. Sammanfattningsvis seeding sökvägen är: R3 -> D3 -> D5 R5 ->.

Om du vill anpassa din **DSProject1** projektet projektets lagringsplats för att uppfylla vissa specifika behov, gör du det i det näst sista steget i följande procedur. Här är en sammanfattning av de steg som används till att dirigera innehållet i den **DSProject1** projektets lagringsplats. De enskilda stegen motsvarar underavsnitt i seeding procedur:

- Klona projektet mallagret till lokal katalog: team R3 - klona om du vill -> lokala D3.
- Klonade DSProject1 lagringsplats till en lokal katalog: team R5 - klona om du vill -> lokala D5.
- Kopiera klonade projektinnehåll till lokal klon av lagringsplatsen för DSProject1: D3 - innehållet kopieras till D5 ->.
- (Valfritt) Anpassning av lokala D5.
- Dra lokala DSProject1 innehåll till teamet databaser: D5 - innehållet Lägg till team R5 ->.


### <a name="clone-your-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Klona projektet malldatabasen (R3) till en katalog (D3) på den lokala datorn.

Skapa en katalog på den lokala datorn:

- *C:\GitRepos\MyTeamCommon* för Windows 
- *$home/GitRepos/MyTeamCommon* för Linux

Ändra till katalogen. Kör sedan följande kommando för att klona databasen för mall för ditt projekt till den lokala datorn. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Om du använder Azure DevOps som värd för koden-plattformen, vanligtvis den *HTTPS-URL: en för ditt projekt mallagret* är:

 ***https://\<Azure DevOps-tjänstnamn\>.visualstudio.com/\<ditt projektnamn\>/_git/\<lagringsplats för projektet mallnamn\>***. 

I det här exemplet har vi:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Om du använder Azure DevOps som värd för koden-plattformen, vanligtvis den *SSH URL: en för projektet malldatabasen* är:

***SSH: / /\<Azure DevOps-tjänstnamn\>@\<Azure DevOps-tjänstnamn\>.visualstudio.com:22/\<Your projektnamn > /_git/\<din projektmall namnet på lagringsplatsen\>.*** 

I det här exemplet har vi:

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Klona databasen för DSProject1 (R5) till en katalog (D5) på den lokala datorn

Gå till katalogen för **GitRepos**, och kör följande kommando för att klona databasen för ditt projekt till den lokala datorn. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Om du använder Azure DevOps som värd för koden-plattformen, vanligtvis den _HTTPS-URL: en för på projektets lagringsplats_ är ***https://\<Azure DevOps-tjänstnamn\>.visualstudio.com/\<Your projektnamn > /_git/ < lagringsplats projektnamnet\>***. I det här exemplet har vi ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Om du använder Azure DevOps som värd för koden-plattformen, vanligtvis den _SSH URL: en för på projektets lagringsplats_ är _ssh: / / < tjänstnamn för Azure DevOps\>@< tjänstnamn för Azure DevOps\>. visualstudio.com:22/<Your Project Name>/\_git / < lagringsplats projektnamnet\>. I det här exemplet har vi ***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Kopiera innehållet i D3 till D5 

Nu i din lokala dator måste du kopiera innehållet i _D3_ till _D5_, förutom git-metadata i .git directory. Följande skript kommer att göra jobbet. Se till att ange korrekt och fullständig sökvägar till katalogerna. Källmapp är det för ditt team (_D3_); målmappen är en för ditt projekt (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Nu kan du se i _DSProject1_ mapp, kopieras alla filer (förutom .git) från _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Nu kan du se i _DSProject1_ mapp, kopieras alla filer (förutom metadata i .git) från _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Anpassa D5 om du behöver (valfritt)

Om ditt projekt måste vissa specifika kataloger eller dokument, än de som du får från din projektmall (kopieras till katalogen D5 i föregående steg), kan du anpassa innehållet i D5 nu. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-azure-devops-services"></a>Lägga till innehållet i DSProject1 i D5 till R5 på din grupp Azure DevOps-tjänster

Nu måste du skicka innehållet **_DSProject1_** till _R5_ lagringsplatsen i ditt projekt på din grupps Azure DevOps-tjänsterna. 


- Ändra till katalogen **D5**. 
- Använd följande git-kommandon för att lägga till innehåll i **D5** till **R5**. Kommandona är detsamma för både Windows och Linux-system. 
    
    Git status git att lägga till.
    Git commit -m ”push från win DSVM” git-push
    
- Bekräfta ändringen och push. 

>[AZURE.NOTE] Om det här är första gången du skickar till en Git-lagringsplats, måste du konfigurera globala parametrar *user.name* och *user.email* innan du kör den `git commit` kommando. Kör följande två kommandon:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Om du checkar in till flera Git-lagringsplatser, använder du samma namn och e-postadress för dem alla. Med samma namn och e-postadress bevisar praktiskt vid ett senare tillfälle när du skapar Power BI-instrumentpaneler för att spåra dina Git-aktiviteter i flera databaser.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Skapa och montera Azure file storage som projektresurser (valfritt)

Om du vill skapa Azure-fillagring för att dela data, till exempel projektet rådata eller funktioner som genererats för projektet, så att alla projektmedlemmar har åtkomst till samma datauppsättningar från flera Dsvm följer du instruktionerna i avsnitt 3 och 4 i [ Team Lead-uppgifter för team data science](team-lead-tasks.md). 


## <a name="next-steps"></a>Nästa steg

Här finns länkar till mer detaljerade beskrivningar av de roller och uppgifter som definieras av Team Data Science Process:

- [Gruppansvarig uppgifter för team data science](group-manager-tasks.md)
- [Lead gruppaktiviteter för team data science](team-lead-tasks.md)
- [Lead projektaktiviteter för team data science](project-lead-tasks.md)
- [Projektet enskilda deltagare för team data science](project-ic-tasks.md)