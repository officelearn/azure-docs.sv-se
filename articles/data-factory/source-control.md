---
title: Källkontroll
description: Lär dig hur du konfigurerar källkontroll i Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/09/2019
ms.openlocfilehash: 3007865c15ceb03b104282c29179ec59a8196b38
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604588"
---
# <a name="source-control-in-azure-data-factory"></a>Källkontroll i Azure Data Factory
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory-användargränssnittsupplevelsen (UX) har två upplevelser tillgängliga för visuell redigering:

- Författare direkt med datafabriken
- Författare med Azure Repos Git- eller GitHub-integrering

> [!NOTE]
> Endast redigering direkt med datafabrikstjänsten stöds i Azure Government Cloud.

## <a name="author-directly-with-the-data-factory-service"></a>Författare direkt med datafabriken

När du redigerar direkt med datafabrikstjänsten är det enda sättet att spara ändringar via knappen **Publicera alla.** När du har klickat publiceras alla ändringar som du har gjort direkt i datafabrikstjänsten. 

![Publiceringsläge](media/author-visually/data-factory-publish.png)

Att redigera direkt med datafabrikstjänsten har följande begränsningar:

- Tjänsten Data Factory innehåller inte en databas för lagring av JSON-entiteterna för dina ändringar.
- Data Factory-tjänsten är inte optimerad för samarbete eller versionskontroll.

> [!NOTE]
> Redigering direkt med datafabrikstjänsten inaktiveras i Azure Data Factory UX när en Git-databas har konfigurerats. Ändringar kan göras direkt i tjänsten via PowerShell eller en SDK.

## <a name="author-with-azure-repos-git-integration"></a>Skapa med Git-integrering för Azure-lagringsplatser

Visuell redigering med Azure Repos Git-integrering stöder källkontroll och samarbete för arbete med dina datafabrikspipelor. Du kan associera en datafabrik med en Azure Repos Git-organisationsdatabas för källkontroll, samarbete, versionshantering och så vidare. En enda Azure Repos Git-organisation kan ha flera databaser, men en Azure Repos Git-databas kan bara associeras med en datafabrik. Om du inte har en Azure Repos-organisation eller databas följer du [dessa instruktioner](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) för att skapa dina resurser.

> [!NOTE]
> Du kan lagra skript- och datafiler i en Azure Repos Git-databas. Du måste dock ladda upp filerna manuellt till Azure Storage. En Data Factory-pipeline överför inte automatiskt skript- eller datafiler som lagras i en Azure Repos Git-databas till Azure Storage.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Konfigurera en Azure Repos Git-databas med Azure Data Factory

Du kan konfigurera en Azure Repos Git-databas med en datafabrik med två metoder.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Konfigurationsmetod 1: Startsida för Azure Data Factory

På startsidan för Azure Data Factory väljer du **Konfigurera koddatabas**.

![Konfigurera en Azure Repos-koddatabas](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Konfigurationsmetod 2: Arbetsyta för UX-redigering
På den azure data factory UX-redigeringsarbetsplatsen väljer du den **nedrullningsbara** menyn Data Factory och väljer sedan **Konfigurera koddatabas**.

![Konfigurera inställningarna för koddatabasen för UX-redigering](media/author-visually/configure-repo-2.png)

Båda metoderna öppnar konfigurationsfönstret för databasinställningar.

![Konfigurera inställningarna för koddatabasen](media/author-visually/repo-settings.png)

Konfigurationsfönstret visar följande azure repos-koddatabasinställningar:

| Inställning | Beskrivning | Värde |
|:--- |:--- |:--- |
| **Databastyp** | Typ av Azure Repos-kodlagringsplats.<br/> | Azure DevOps Git eller GitHub |
| **Azure Active Directory** | Ditt Azure AD-klientnamn. | `<your tenant name>` |
| **Azure Repos Organisation** | Organisationsnamnet för Azure Repos. Du kan hitta ditt Azure `https://{organization name}.visualstudio.com`Repos-organisationsnamn på . Du kan [logga in på din Azure-databaser organisation](https://www.visualstudio.com/team-services/git/) för att komma åt din Visual Studio-profil och se dina databaser och projekt. | `<your organization name>` |
| **Projektnamn** | Ditt Azure Repos-projektnamn. Du kan hitta ditt Azure `https://{organization name}.visualstudio.com/{project name}`Repos-projektnamn på . | `<your Azure Repos project name>` |
| **RepositoryName (RepositoryName)** | Ditt Azure Repos-koddatabasnamn. Azure-databaser innehåller Git-databaser för att hantera källkoden när projektet växer. Du kan skapa en ny databas eller använda en befintlig databas som redan finns i projektet. | `<your Azure Repos code repository name>` |
| **Samarbetsgren** | Din Azure Repos-samarbetsgren som används för publicering. Som standard är `master`det . Ändra den här inställningen om du vill publicera resurser från en annan gren. | `<your collaboration branch name>` |
| **Rotmappen** | Din rotmapp i din Azure Repos-samarbetsgren. | `<your root folder name>` |
| **Importera befintliga datafabriksresurser till databas** | Anger om befintliga datafabriksresurser ska importeras från **UX-redigeringsarbetsplatsen** till en Azure Repos Git-databas. Markera rutan om du vill importera datafabriksresurserna till den associerade Git-databasen i JSON-format. Den här åtgärden exporterar varje resurs individuellt (det vill än de länkade tjänsterna och datauppsättningarna exporteras till separata JSON). När den här rutan inte är markerad importeras inte de befintliga resurserna. | Markerat (standard) |
| **Gren att importera resurs till** | Anger i vilken gren datafabriksresurserna (pipelines, datauppsättningar, länkade tjänster etc.) importeras. Du kan importera resurser till en av följande grenar: a. Samarbete b. Skapa nya c. Använd befintliga |  |

> [!NOTE]
> Om du använder Microsoft Edge och inte ser några värden i listrutan Azure DevOps-konto lägger du till https://*.visualstudio.com i listan över betrodda platser.

### <a name="use-a-different-azure-active-directory-tenant"></a>Använda en annan Azure Active Directory-klient

Du kan skapa en Azure Repos eller Git-lagringsplats i en annan Azure Active Directory-klientorganisation. För att kunna ange en annan Azure AD-klientorganisation måste du ha administratörsbehörighet för den prenumeration som du använder.

### <a name="use-your-personal-microsoft-account"></a>Använda ditt personliga Microsoft-konto

Om du vill använda ett personligt Microsoft-konto för Git-integrering kan du länka din personliga Azure-repo till organisationens Active Directory.

1. Lägg till ditt personliga Microsoft-konto i organisationens Active Directory som gäst. Mer information finns [i Lägga till Azure Active Directory B2B-samarbetsanvändare i Azure-portalen](../active-directory/b2b/add-users-administrator.md).

2. Logga in på Azure-portalen med ditt personliga Microsoft-konto. Växla sedan till organisationens Active Directory.

3. Gå till avsnittet Azure DevOps, där du nu ser din personliga repo. Markera repo och anslut med Active Directory.

Efter de här konfigurationsstegen är din personliga repo tillgänglig när du konfigurerar Git-integrering i datafabrikens användargränssnitt.

Mer information om hur du ansluter Azure Repos till organisationens Active Directory finns i [Ansluta din Azure DevOps-organisation till Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Skapa med GitHub-integrering

Visuell redigering med GitHub-integrering stöder källkontroll och samarbete för arbete med dina datafabrikspipelor. Du kan associera en datafabrik med en GitHub-kontodatabas för källkontroll, samarbete, versionshantering. Ett enda GitHub-konto kan ha flera databaser, men en GitHub-databas kan bara associeras med en datafabrik. Om du inte har ett GitHub-konto eller en databas följer du [dessa instruktioner](https://github.com/join) för att skapa dina resurser.

GitHub-integreringen med Data Factory stöder både offentliga [https://github.com](https://github.com)GitHub (det vill ha) och GitHub Enterprise. Du kan använda både offentliga och privata GitHub-databaser med Data Factory så länge du har läs- och skrivbehörighet till databasen i GitHub.

Om du vill konfigurera en GitHub-repo måste du ha administratörsbehörighet för Den Azure-prenumeration som du använder.

För en nio minuters introduktion och demonstration av den här funktionen, titta på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Konfigurera en GitHub-databas med Azure Data Factory

Du kan konfigurera en GitHub-databas med en datafabrik med två metoder.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Konfigurationsmetod 1: Startsida för Azure Data Factory

På startsidan för Azure Data Factory väljer du **Konfigurera koddatabas**.

![Konfigurera en Azure Repos-koddatabas](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Konfigurationsmetod 2: Arbetsyta för UX-redigering

På den azure data factory UX-redigeringsarbetsplatsen väljer du den **nedrullningsbara** menyn Data Factory och väljer sedan **Konfigurera koddatabas**.

![Konfigurera inställningarna för koddatabasen för UX-redigering](media/author-visually/configure-repo-2.png)

Båda metoderna öppnar konfigurationsfönstret för databasinställningar.

![GitHub-databasinställningar](media/author-visually/github-integration-image2.png)

Konfigurationsfönstret visar följande GitHub-databasinställningar:

| **Inställning** | **Beskrivning**  | **Värde**  |
|:--- |:--- |:--- |
| **Databastyp** | Typ av Azure Repos-kodlagringsplats. | GitHub |
| **Använda GitHub Enterprise** | Kryssrutan för att välja GitHub Enterprise | inte har valts (standard) |
| **GitHub Enterprise-URL** | GitHub Enterprise-rot-URL:en (måste vara HTTPS för lokal GitHub Enterprise-server). Till exempel: https://github.mydomain.com. Krävs endast om **Använd GitHub Enterprise** är markerat | `<your GitHub enterprise url>` |                                                           
| **GitHub-konto** | Ditt GitHub-kontonamn. Det här namnet finns\/från https: /github.com/{account name}/{repository name}. Om du navigerar till den här sidan uppmanas du att ange GitHub OAuth-autentiseringsuppgifter i ditt GitHub-konto. | `<your GitHub account name>` |
| **Databasnamn**  | Ditt GitHub-koddatabasnamn. GitHub-konton innehåller Git-databaser för att hantera källkoden. Du kan skapa en ny databas eller använda en befintlig databas som redan finns i ditt konto. | `<your repository name>` |
| **Samarbetsgren** | Din GitHub-samarbetsgren som används för publicering. Som standard är det master. Ändra den här inställningen om du vill publicera resurser från en annan gren. | `<your collaboration branch>` |
| **Rotmappen** | Din rotmapp i GitHub-samarbetsgrenen. |`<your root folder name>` |
| **Importera befintliga datafabriksresurser till databas** | Anger om befintliga datafabriksresurser ska importeras från UX-redigeringsarbetsytan till en GitHub-databas. Markera rutan om du vill importera datafabriksresurserna till den associerade Git-databasen i JSON-format. Den här åtgärden exporterar varje resurs individuellt (det vill än de länkade tjänsterna och datauppsättningarna exporteras till separata JSON). När den här rutan inte är markerad importeras inte de befintliga resurserna. | Markerat (standard) |
| **Gren att importera resurs till** | Anger i vilken gren datafabriksresurserna (pipelines, datauppsättningar, länkade tjänster etc.) importeras. Du kan importera resurser till en av följande grenar: a. Samarbete b. Skapa nya c. Använd befintliga |  |

### <a name="known-github-limitations"></a>Kända GitHub-begränsningar

- Du kan lagra skript- och datafiler i en GitHub-databas. Du måste dock ladda upp filerna manuellt till Azure Storage. En Data Factory-pipeline överför inte automatiskt skript- eller datafiler som lagras i en GitHub-databas till Azure Storage.

- GitHub Enterprise med en version som är äldre än 2.14.0 fungerar inte i webbläsaren Microsoft Edge.

- GitHub-integrering med datafabrikens visuella redigeringsverktyg fungerar bara i den allmänt tillgängliga versionen av Data Factory.

- Högst 1 000 entiteter per resurstyp (till exempel pipelines och datauppsättningar) kan hämtas från en enda GitHub-gren. Om den här gränsen nås föreslås att dina resurser delas upp i separata fabriker. Azure DevOps Git har inte den här begränsningen.

## <a name="switch-to-a-different-git-repo"></a>Växla till en annan Git-repo

Om du vill växla till en annan Git-repo klickar du på ikonen **Git Repo Settings** i det övre högra hörnet på översiktssidan för Data Factory. Om ikonen inte kan visas avmarkerar du den lokala webbläsarens cacheminne. Välj ikonen om du vill ta bort kopplingen till den aktuella repoen.

![Ikon för Git](media/author-visually/remove-repo.png)

När fönstret Databasinställningar visas väljer du **Ta bort Git**. Ange ditt datafabriksnamn och klicka på **Bekräfta** om du vill ta bort Git-databasen som är associerad med datafabriken.

![Ta bort kopplingen till den aktuella Git-reporäntan](media/author-visually/remove-repo2.png)

När du har tagit bort kopplingen till den aktuella reporäntan kan du konfigurera Git-inställningarna så att de använder en annan repo och sedan importera befintliga datafabriksresurser till den nya reporäntan. 

## <a name="version-control"></a>Versionskontroll

Versionskontrollsystem (kallas även _källkontroll)_ gör det att utvecklare kan samarbeta om kod- och spåra ändringar som görs i kodbasen. Källkontroll är ett viktigt verktyg för projekt med flera utvecklare.

### <a name="creating-feature-branches"></a>Skapa funktionsgrenar

Varje Azure Repos Git-databas som är associerad med en datafabrik har en samarbetsgren. (är`master` standardsamarbetsgrenen). Användare kan också skapa funktionsgrenar genom att klicka på **+ Ny gren** i grenens listruta. När det nya grenfönstret visas anger du namnet på funktionsgrenen.

![Skapa en ny gren](media/author-visually/new-branch.png)

När du är redo att slå samman ändringarna från funktionsgrenen till din samarbetsgren klickar du på grenens listruta och väljer **Skapa pull-begäran**. Den här åtgärden tar dig till Azure Repos Git där du kan höja pull-begäranden, göra kodgranskningar och koppla ändringar till din samarbetsgren. (är`master` standard). Du får bara publicera till Data Factory-tjänsten från din samarbetsfilial. 

![Skapa en ny pull-begäran](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurera publiceringsinställningar

Om du vill konfigurera publiceringsgrenen , det vill än `publish_config.json` den gren där Resource Manager-mallar sparas, lägger du till en fil i rotmappen i samarbetsgrenen. Data Factory läser den här `publishBranch`filen, söker efter fältet och skapar en ny gren (om den inte redan finns) med det angivna värdet. Sedan sparas alla Resource Manager-mallar på den angivna platsen. Ett exempel:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

När du anger en ny publiceringsgren tar Data Factory inte bort den tidigare publiceringsgrenen. Om du vill ta bort den tidigare publiceringsgrenen tar du bort den manuellt.

> [!NOTE]
> Data Factory läser `publish_config.json` bara filen när den läser in fabriken. Om du redan har fabriken laddad i portalen uppdaterar du webbläsaren så att ändringarna börjar gälla.

### <a name="publish-code-changes"></a>Publicera kodändringar

När du har sammanfogat ändringar`master` i samarbetsgrenen ( är standard) klickar du på **Publicera** om du manuellt vill publicera kodändringarna i huvudgrenen till datafabrikstjänsten.

![Publicera ändringar i tjänsten Data Factory](media/author-visually/publish-changes.png)

En sidoruta öppnas där du bekräftar att publiceringsgrenen och väntande ändringar är korrekta. När du har verifierat ändringarna klickar du på **OK** för att bekräfta publiceringen.

![Bekräfta rätt publiceringsgren](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Huvudgrenen är inte representativ för vad som distribueras i datafabrikstjänsten. Huvudgrenen *måste* publiceras manuellt till datafabrikstjänsten.

## <a name="advantages-of-git-integration"></a>Fördelar med Git-integrering

-   **Källkontroll**. När dina datafabriksarbetsbelastningar blir avgörande vill du integrera din fabrik med Git för att utnyttja flera fördelar med källkontroll som följande:
    -   Förmåga att spåra/granska ändringar.
    -   Möjlighet att återställa ändringar som introducerade buggar.
-   **Partiella sparningar**. När du gör många ändringar i din fabrik, kommer du att inse att i det vanliga LIVE-läget, kan du inte spara dina ändringar som utkast, eftersom du inte är redo, eller om du inte vill förlora dina ändringar om datorn kraschar. Med Git-integrering kan du fortsätta spara ändringarna stegvis och publicera till fabriken endast när du är redo. Git fungerar som en mellanlagringsplats för ditt arbete, tills du har testat dina ändringar till din belåtenhet.
-   **Samarbete och kontroll**. Om du har flera gruppmedlemmar som deltar i samma fabrik kanske du vill låta dina lagkamrater samarbeta med varandra via en kodgranskningsprocess. Du kan också ställa in din fabrik så att inte alla deltagare till fabriken har behörighet att distribuera till fabriken. Gruppmedlemmar kan bara tillåtas att göra ändringar via Git, men endast vissa personer i teamet får "Publicera" ändringarna i fabriken.
-   **Visar diffs**. I Git-läge får du se en fin diff av nyttolasten som är på väg att få publiceras till fabriken. Den här diff visar alla resurser / entiteter som har ändrats / lagts till / tas bort sedan den senaste gången du publicerade till din fabrik. Baserat på den här diffen kan du antingen fortsätta med publiceringen eller gå tillbaka och kontrollera dina ändringar och sedan komma tillbaka senare.
-   **Bättre CI/ CD**. Om du använder Git-läge kan du konfigurera versionspipelinen så att den utlöses automatiskt så snart det görs några ändringar i utvecklingsfabriken. Du får också anpassa egenskaperna i fabriken som är tillgängliga som parametrar i resource manager-mallen. Det kan vara användbart att bara behålla den obligatoriska uppsättningen egenskaper som parametrar och ha allt annat hårt kodat.
-   **Bättre prestanda**. En genomsnittlig fabrik laddas tio gånger snabbare i Git-läge än i vanligt LIVE-läge, eftersom resurserna hämtas via Git.

## <a name="best-practices-for-git-integration"></a>Metodtips för Git-integrering

### <a name="permissions"></a>Behörigheter

Vanligtvis vill du inte att alla gruppmedlemmar ska ha behörighet att uppdatera fabriken. Följande behörighetsinställningar rekommenderas:

*   Alla gruppmedlemmar bör ha läsbehörighet till datafabriken.
*   Endast en utvald uppsättning personer bör tillåtas att publicera till fabriken. För att kunna göra det måste de ha rollen **Data Factory-deltagare** i resursgruppen som fabriken befinner sig i. Mer information om behörigheter finns i [Roller och behörigheter för Azure Data Factory](concepts-roles-permissions.md).
   
Vi rekommenderar att du inte tillåter direkt incheckning till samarbetsgrenen. Den här begränsningen kan förhindra buggar eftersom varje incheckning kommer att gå igenom en pull request review process som beskrivs i [Skapa funktionsgrenar](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Använda lösenord från Azure Key Vault

Vi rekommenderar att du använder Azure Key Vault för att lagra anslutningssträngar eller lösenord för Data Factory Linked Services. Av säkerhetsskäl lagrar vi inte sådan hemlig information i Git, så alla ändringar av länkade tjänster publiceras omedelbart till Azure Data Factory-tjänsten.

Om du använder Key Vault blir det också enklare att kontinuerlig integrering och distribution eftersom du inte behöver ange dessa hemligheter under resurshanterarens malldistribution.

## <a name="troubleshooting-git-integration"></a>Felsöka Git-integrering

### <a name="stale-publish-branch"></a>Inaktuell publiceringsgren

Om publiceringsgrenen inte är synkroniserad med huvudgrenen och innehåller inaktuella resurser trots en nyligen genomförd publicering kan du prova att följa dessa steg:

1. Ta bort din nuvarande Git-databas
1. Konfigurera om Git med samma inställningar, men se till **att importera befintliga datafabriksresurser till databasen** är markerat och välj Ny **gren**
1. Skapa en pull-begäran om att koppla ändringarna till samarbetsgrenen 

Nedan följer några exempel på situationer som kan orsaka en inaktuell publiceringsgren:
- En användare har flera grenar. I en funktionsgren raderade de en länkad tjänst som inte är AKV-associerad (icke AKV-länkade tjänster publiceras omedelbart oavsett om de finns i Git eller inte) och sammanfogade aldrig funktionsgrenen till samarbetsbrnach.
- En användare ändrade datafabriken med hjälp av SDK eller PowerShell
- En användare flyttade alla resurser till en ny gren och försökte publicera för första gången. Länkade tjänster bör skapas manuellt när resurser importeras.
- En användare laddar upp en icke-AKV-länkad tjänst eller en Integration Runtime JSON manuellt. De refererar till resursen från en annan resurs, till exempel en datauppsättning, en länkad tjänst eller en pipeline. En icke-AKV-länkad tjänst som skapas via användarupplevelsen publiceras omedelbart eftersom autentiseringsuppgifterna måste krypteras. Om du överför en datauppsättning som refererar till den länkade tjänsten och försöker publicera tillåter användarupplevelsen den eftersom den finns i git-miljön. Det kommer att avvisas vid publiceringstillfället eftersom det inte finns i datafabrikstjänsten.

## <a name="provide-feedback"></a>Ge feedback
Välj **Feedback** om du vill kommentera funktioner eller meddela Microsoft om problem med verktyget:

![Feedback](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om övervakning och hantering av pipelines finns i [Övervaka och hantera pipelines programmässigt.](monitor-programmatically.md)
* Information om hur du implementerar kontinuerlig integrering och distribution finns i [Kontinuerlig integrering och leverans (CI/CD) i Azure Data Factory](continuous-integration-deployment.md).
