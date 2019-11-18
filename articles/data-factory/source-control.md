---
title: Käll kontroll i Azure Data Factory
description: Lär dig hur du konfigurerar käll kontroll i Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 5f497bd06868d586f8378cb81e870a750b8a1670
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122895"
---
# <a name="source-control-in-azure-data-factory"></a>Käll kontroll i Azure Data Factory

Gränssnittet för Azure Data Factory användar gränssnitt (UX) har två upplevelser som är tillgängliga för visuell redigering:

- Redigera direkt med Data Factory tjänsten
- Redigera med Azure databaser git eller GitHub-integrering

> [!NOTE]
> Det finns bara stöd för redigering direkt med Data Factory tjänsten i Azure Government molnet.

## <a name="author-directly-with-the-data-factory-service"></a>Redigera direkt med Data Factory tjänsten

När du redigerar direkt med Data Factory tjänsten är det enda sättet att spara ändringar via knappen **publicera alla** . När du har klickat på det publiceras alla ändringar som du gjort direkt till Data Factory tjänsten. 

![Publicerings läge](media/author-visually/data-factory-publish.png)

Redigering direkt med Data Factory tjänsten har följande begränsningar:

- Tjänsten Data Factory innehåller inte en lagrings plats för att lagra JSON-entiteterna för dina ändringar.
- Tjänsten Data Factory är inte optimerad för samarbete eller versions kontroll.

> [!NOTE]
> Redigering direkt med Data Factory tjänsten inaktive ras i Azure Data Factory UX när en git-lagringsplats har kon figurer ATS. Ändringar kan göras direkt till tjänsten via PowerShell eller SDK.

## <a name="author-with-azure-repos-git-integration"></a>Redigera med Azure databaser git-integrering

Visuell redigering med Azure databaser git-integrering stöder käll kontroll och samarbete för arbete på dina data Factory-pipeliner. Du kan associera en data fabrik med en Azure databaser git-organisations databas för käll kontroll, samarbete, versions hantering och så vidare. En enda Azure databaser git-organisation kan ha flera databaser, men en Azure databaser git-lagringsplats kan bara associeras med en data fabrik. Om du inte har någon Azure databaser-organisation eller-databas, följer du [dessa anvisningar](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) för att skapa dina resurser.

> [!NOTE]
> Du kan lagra skript-och datafiler i en git-lagringsplats för Azure databaser. Du måste dock ladda upp filerna manuellt till Azure Storage. En Data Factory pipeline överför inte automatiskt skript eller datafiler som lagras i en git-lagringsplats i Azure databaser till Azure Storage.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Konfigurera en Azure databaser git-lagringsplats med Azure Data Factory

Du kan konfigurera en Azure databaser git-lagringsplats med en data fabrik på två sätt.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Konfigurations metod 1: Azure Data Factory start sida

På Azure Data Factory start sida väljer du **Konfigurera kod lagrings plats**.

![Konfigurera en Azure databaser Code-lagringsplats](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Konfigurations metod 2: redigera arbets yta för UX
I Azure Data Factory UX-redigerings arbets ytan väljer du den **Data Factory** list rutan och väljer sedan **Konfigurera kod lagrings plats**.

![Konfigurera kod lagrings inställningarna för UX-redigering](media/author-visually/configure-repo-2.png)

Båda metoderna öppnar konfigurations fönstret för lagrings inställningar.

![Konfigurera kod lagrings inställningarna](media/author-visually/repo-settings.png)

I konfigurations fönstret visas följande inställningar för Azure databaser Code-lagringsplatsen:

| Inställning | Beskrivning | Värde |
|:--- |:--- |:--- |
| **Typ av databas** | Typen av Azure databaser Code-lagringsplatsen.<br/> | Azure DevOps git eller GitHub |
| **Azure Active Directory** | Ditt Azure AD-klient namn. | `<your tenant name>` |
| **Azure databaser-organisation** | Ditt Azure databaser-organisations namn. Du kan hitta ditt Azure databaser-organisations namn på `https://{organization name}.visualstudio.com`. Du kan [Logga in på din Azure databaser-organisation](https://www.visualstudio.com/team-services/git/) för att få åtkomst till din Visual Studio-profil och se dina databaser och projekt. | `<your organization name>` |
| **ProjectName** | Ditt Azure databaser-projekt namn. Du kan hitta ditt Azure databaser-projekt namn på `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **RepositoryName** | Ditt namn på din Azure databaser Code-lagringsplats. Azure databaser-projekt innehåller git-lagringsplatser för att hantera din käll kod när projektet växer. Du kan skapa en ny databas eller använda en befintlig databas som redan finns i ditt projekt. | `<your Azure Repos code repository name>` |
| **Samarbets gren** | Din Azure databaser Collaboration-gren som används för publicering. Som standard är det `master`. Ändra den här inställningen om du vill publicera resurser från en annan gren. | `<your collaboration branch name>` |
| **Rotmapp** | Rotmappen i din Azure databaser-samarbets gren. | `<your root folder name>` |
| **Importera befintliga Data Factory resurser till lagrings platsen** | Anger om befintliga data Factory-resurser ska importeras från UX **redigerings arbets ytan** till en Azure databaser git-lagringsplats. Markera rutan om du vill importera data Factory-resurser till den tillhör ande git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt (det vill säga länkade tjänster och data uppsättningar exporteras till separata JSON-data). När den här rutan inte är markerad importeras inte de befintliga resurserna. | Vald (standard) |
| **Gren att importera resurs till** | Anger i vilken gren Data Factory-resurserna (pipelines, data uppsättningar, länkade tjänster osv.) importeras. Du kan importera resurser till någon av följande grenar: a. Samarbete b. Skapa nytt c. Använd befintlig |  |

> [!NOTE]
> Om du använder Microsoft Edge och inte ser några värden i list rutan för ditt Azure DevOps-konto lägger du till https://*. VisualStudio. com i listan över betrodda platser.

### <a name="use-a-different-azure-active-directory-tenant"></a>Använd en annan Azure Active Directory klient

Du kan skapa en Azure Repos eller Git-lagringsplats i en annan Azure Active Directory-klientorganisation. Om du vill ange en annan Azure AD-klient måste du ha administratörs behörighet för den Azure-prenumeration som du använder.

### <a name="use-your-personal-microsoft-account"></a>Använd din personliga Microsoft-konto

Om du vill använda en personlig Microsoft-konto för git-integrering kan du länka din personliga Azure-lagrings platsen till din organisations Active Directory.

1. Lägg till din personliga Microsoft-konto till din organisations Active Directory som gäst. Mer information finns i [Lägg till Azure Active Directory B2B-samarbets användare i Azure Portal](../active-directory/b2b/add-users-administrator.md).

2. Logga in på Azure Portal med din personliga Microsoft-konto. Växla sedan till din organisations Active Directory.

3. Gå till avsnittet Azure-DevOps där du nu ser din personliga lagrings platsen. Välj lagrings platsen och Anslut med Active Directory.

Efter dessa konfigurations steg är din personliga lagrings platsen tillgänglig när du konfigurerar git-integrering i Data Factory användar gränssnitt.

Mer information om hur du ansluter Azure-databaser till din organisations Active Directory finns i [ansluta din Azure DevOps-organisation till Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Redigera med GitHub-integrering

Visuell redigering med GitHub-integrering stöder käll kontroll och samarbete för arbete på dina data Factory-pipeliner. Du kan associera en data fabrik med en GitHub-lagringsplats för käll kontroll, samarbete, versions hantering. Ett enskilt GitHub-konto kan ha flera databaser, men en GitHub-lagringsplats kan bara associeras med en data fabrik. Om du inte har ett GitHub-konto eller en lagrings plats, följer du [anvisningarna](https://github.com/join) för att skapa dina resurser.

GitHub-integreringen med Data Factory stöder både offentlig GitHub ( [https://github.com](https://github.com)) och GitHub Enterprise. Du kan använda både offentliga och privata GitHub-databaser med Data Factory så länge du har Läs-och Skriv behörighet till lagrings platsen i GitHub.

Om du vill konfigurera en GitHub-lagrings platsen måste du ha administratörs behörighet för den Azure-prenumeration som du använder.

För en nio minuters introduktion och demonstration av den här funktionen, se följande videoklipp:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Konfigurera en GitHub-lagringsplats med Azure Data Factory

Du kan konfigurera en GitHub-lagringsplats med en data fabrik på två sätt.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Konfigurations metod 1: Azure Data Factory start sida

På Azure Data Factory start sida väljer du **Konfigurera kod lagrings plats**.

![Konfigurera en Azure databaser Code-lagringsplats](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Konfigurations metod 2: redigera arbets yta för UX

I Azure Data Factory UX-redigerings arbets ytan väljer du den **Data Factory** list rutan och väljer sedan **Konfigurera kod lagrings plats**.

![Konfigurera kod lagrings inställningarna för UX-redigering](media/author-visually/configure-repo-2.png)

Båda metoderna öppnar konfigurations fönstret för lagrings inställningar.

![Inställningar för GitHub-lagringsplats](media/author-visually/github-integration-image2.png)

I konfigurations fönstret visas följande inställningar för GitHub-lagringsplats:

| **Inställning** | **Beskrivning**  | **Värde**  |
|:--- |:--- |:--- |
| **Typ av databas** | Typen av Azure databaser Code-lagringsplatsen. | GitHub |
| **Använda GitHub Enterprise** | Kryss ruta för att välja GitHub Enterprise | omarkerat (standard) |
| **GitHub Enterprise-URL** | GitHub Enterprise-rot-URL. Till exempel: https://github.mydomain.com. Krävs endast om **Använd GitHub Enterprise** är valt | `<your GitHub enterprise url>` |                                                           
| **GitHub-konto** | Namnet på GitHub-kontot. Det här namnet kan hittas från https:\//GitHub.com/{account Name}/{repository Name}. Om du navigerar till den här sidan uppmanas du att ange GitHub OAuth-autentiseringsuppgifter för ditt GitHub-konto. | `<your GitHub account name>` |
| **Namn på databas**  | Ditt GitHub kod lagrings namn. GitHub-konton innehåller git-databaser för att hantera din käll kod. Du kan skapa en ny databas eller använda en befintlig databas som redan finns i ditt konto. | `<your repository name>` |
| **Samarbets gren** | Din GitHub Collaboration-gren som används för publicering. Som standard är originalet. Ändra den här inställningen om du vill publicera resurser från en annan gren. | `<your collaboration branch>` |
| **Rotmapp** | Rotmappen i din GitHub-samarbets gren. |`<your root folder name>` |
| **Importera befintliga Data Factory resurser till lagrings platsen** | Anger om befintliga data Factory-resurser ska importeras från UX redigerings arbets ytan till en GitHub-lagringsplats. Markera rutan om du vill importera data Factory-resurser till den tillhör ande git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt (det vill säga länkade tjänster och data uppsättningar exporteras till separata JSON-data). När den här rutan inte är markerad importeras inte de befintliga resurserna. | Vald (standard) |
| **Gren att importera resurs till** | Anger i vilken gren Data Factory-resurserna (pipelines, data uppsättningar, länkade tjänster osv.) importeras. Du kan importera resurser till någon av följande grenar: a. Samarbete b. Skapa nytt c. Använd befintlig |  |

### <a name="known-github-limitations"></a>Kända GitHub-begränsningar

- Du kan lagra skript-och datafiler i en GitHub-lagringsplats. Du måste dock ladda upp filerna manuellt till Azure Storage. En Data Factory pipeline överför inte automatiskt skript eller datafiler som lagras i en GitHub-lagringsplats till Azure Storage.

- GitHub Enterprise med en äldre version än 2.14.0 fungerar inte i Microsoft Edge-webbläsaren.

- GitHub-integrering med Data Factory visuella redigerings verktyg fungerar bara i den allmänt tillgängliga versionen av Data Factory.

## <a name="switch-to-a-different-git-repo"></a>Växla till en annan git-lagrings platsen

Om du vill växla till en annan git-lagrings platsen klickar du på ikonen **git lagrings platsen-inställningar** i det övre högra hörnet på sidan Data Factory översikt. Om du inte ser ikonen rensar du din lokala webbläsares cacheminne. Välj ikonen för att ta bort associationen med den aktuella lagrings platsen.

![Git-ikon](media/author-visually/remove-repo.png)

När fönstret databas inställningar visas väljer du **ta bort git**. Ange data fabriks namnet och klicka på **Bekräfta** för att ta bort git-lagringsplatsen som är associerad med din data fabrik.

![Ta bort associationen med den aktuella git-lagrings platsen](media/author-visually/remove-repo2.png)

När du har tagit bort associationen med den aktuella lagrings platsen kan du konfigurera git-inställningarna så att de använder en annan lagrings platsen och sedan importera befintliga Data Factory resurser till den nya lagrings platsen. 

## <a name="version-control"></a>Versionskontroll

Med versions kontroll system (även kallat _käll kontroll_) kan utvecklare samar beta om kod och spåra ändringar som görs i kodbasen. Käll kontroll är ett viktigt verktyg för projekt med flera utvecklare.

### <a name="creating-feature-branches"></a>Skapa funktions grenar

Varje Azure databaser git-lagringsplats som är associerad med en data fabrik har en samarbets gren. (`master` är standard grenen för samarbete). Användare kan också skapa funktions grenar genom att klicka på **+ ny gren** i rutan förgrening. När fönstret ny förgrening visas anger du namnet på din funktions gren.

![Skapa en ny gren](media/author-visually/new-branch.png)

När du är redo att sammanfoga ändringarna från din funktions gren till samarbets grenen klickar du på grenen gren och väljer **skapa pull-begäran**. Den här åtgärden tar dig till Azure databaser git där du kan generera pull-förfrågningar, göra kod granskningar och slå samman ändringar i samarbets grenen. (`master` är standard). Du får bara publicera till tjänsten Data Factory från samarbets grenen. 

![Skapa en ny pull-begäran](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurera publicerings inställningar

För att konfigurera publicerings grenen – det vill säga grenen där Resource Manager-mallar sparas – Lägg till en `publish_config.json`-fil till rotmappen i samarbets grenen. Data Factory läser filen, letar efter fältet `publishBranch`och skapar en ny gren (om det inte redan finns) med det angivna värdet. Sedan sparas alla Resource Manager-mallar på den angivna platsen. Exempel:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

När du anger en ny publicerings gren tar Data Factory inte bort den tidigare publicerings grenen. Om du vill ta bort den tidigare publicerings grenen tar du bort den manuellt.

> [!NOTE]
> Data Factory läser bara `publish_config.json`-filen när den laddar fabriken. Om du redan har en fabrik som är inläst i portalen uppdaterar du webbläsaren för att ändringarna ska börja gälla.

### <a name="publish-code-changes"></a>Publicera kod ändringar

När du har sammanfogat ändringar i samarbets grenen (`master` är standard) klickar du på **publicera** för att publicera kod ändringarna manuellt i huvud grenen till data Factorys tjänsten.

![Publicera ändringar i Data Factorys tjänsten](media/author-visually/publish-changes.png)

En sida i fönstret öppnas där du bekräftar att publicerings grenen och de väntande ändringarna är korrekta. När du har verifierat ändringarna klickar du på **OK** för att bekräfta publiceringen.

![Bekräfta rätt publicerings gren](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Huvud grenen är inte representativ för vad som distribueras i Data Factorys tjänsten. Huvud grenen *måste* publiceras manuellt till data Factorys tjänsten.

## <a name="advantages-of-git-integration"></a>Fördelar med git-integrering

-   **Käll kontroll**. När dina data Factory-arbetsbelastningar blir viktiga, vill du integrera din fabrik med Git för att utnyttja flera fördelar med käll kontroll som följande:
    -   Möjlighet att spåra/granska ändringar.
    -   Möjlighet att återställa ändringar som introducerade buggar.
-   **Delvis sparade**. När du gör många ändringar i fabriken kommer du att upptäcka att du i normal LIVE-läge kan spara ändringarna som utkast, eftersom du inte är redo eller om du inte vill förlora dina ändringar om datorn kraschar. Med git-integrering kan du fortsätta att spara ändringarna stegvis och bara publicera på fabriken när du är klar. Git fungerar som en mellanlagringsplats för ditt arbete tills du har testat dina ändringar till din belåtenhet.
-   **Samarbete och kontroll**. Om du har flera team medlemmar som är anslutna till samma fabrik kanske du vill låta dina medarbetare samar beta med varandra via en kod gransknings process. Du kan också konfigurera din fabrik så att inte alla deltagare till fabriken har behörighet att distribuera till fabriken. Grupp medlemmar kan bara tillåtas att göra ändringar via git, men endast vissa personer i teamet får publicera ändringar i fabriken.
-   **Visar differenser**. I git-läge får du se en fin diff av nytto lasten som är på väg att publicera till fabriken. Den här differensen visar alla resurser/entiteter som har ändrats/lagts till/tagits bort sedan den senaste gången du publicerade till din fabrik. Baserat på denna diff kan du antingen fortsätta med publiceringen eller gå tillbaka och kontrol lera ändringarna och sedan komma tillbaka senare.
-   **Bättre CI/CD**. Om du använder git-läge kan du konfigurera din versions pipeline så att den utlöses automatiskt så snart det finns några ändringar som görs i utvecklings fabriken. Du kan också anpassa de egenskaper i fabriken som är tillgängliga som parametrar i Resource Manager-mallen. Det kan vara användbart att endast behålla den obligatoriska uppsättningen egenskaper som parametrar och att allt annat är hårdkodat.
-   **Bättre prestanda**. En genomsnittlig fabrik läser in tio gånger snabbare i git-läge än i normalt LIVE-läge, eftersom resurserna hämtas via git.

## <a name="best-practices-for-git-integration"></a>Metod tips för git-integrering

### <a name="permissions"></a>Behörigheter

Normalt vill du inte att alla grupp medlemmar ska ha behörighet att uppdatera fabriken. Följande behörighets inställningar rekommenderas:

*   Alla grupp medlemmar måste ha Läs behörighet till data fabriken.
*   Endast en Välj uppsättning personer ska kunna publiceras till fabriken. För att göra det måste användaren ha rollen som **Data Factory Contributor** på fabriken. Mer information om behörigheter finns i [roller och behörigheter för Azure Data Factory](concepts-roles-permissions.md).
   
Vi rekommenderar att inte tillåta direkta incheckningar i samarbets grenen. Den här begränsningen kan hjälpa till att förhindra buggar som varje incheckning går igenom en pull-begäran.

### <a name="using-passwords-from-azure-key-vault"></a>Använda lösen ord från Azure Key Vault

Vi rekommenderar att du använder Azure Key Vault för att lagra anslutnings strängar eller lösen ord för Data Factory länkade tjänster. Av säkerhets skäl lagrar vi inte någon sådan hemlig information i git, så alla ändringar i länkade tjänster publiceras omedelbart till den Azure Data Factory tjänsten.

Genom att använda Key Vault kan du göra kontinuerlig integrering och distribution enklare eftersom du inte behöver ange dessa hemligheter under distributionen av Resource Manager-mallar.

## <a name="troubleshooting-git-integration"></a>Felsöka git-integrering

### <a name="stale-publish-branch"></a>Inaktuell publicerings gren

Om publicerings grenen inte är synkroniserad med huvud grenen och innehåller inaktuella resurser trots en senaste publicering, prova följande steg:

1. Ta bort din aktuella git-lagringsplats
1. Konfigurera om git med samma inställningar, men se till att **Importera befintliga data Factory resurser till databasen** är markerat och välj **ny gren**
1. Ta bort alla resurser från samarbets grenen
1. Skapa en pull-begäran för att slå samman ändringarna i samarbets grenen 

## <a name="provide-feedback"></a>Ge feedback
Välj **feedback** för att kommentera om funktioner eller meddela Microsoft om problem med verktyget:

![Feedback](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om övervakning och hantering av pipelines finns i [övervaka och hantera pipelines program mässigt](monitor-programmatically.md).
* För att implementera kontinuerlig integrering och distribution, se [kontinuerlig integrering och leverans (CI/CD) i Azure Data Factory](continuous-integration-deployment.md).
