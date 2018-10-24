---
title: Visuell redigering i Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder visuell redigering i Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: shlo
ms.openlocfilehash: 3db32dfe2d7b65535f7d30f76241b33ecca96c15
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957963"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visuell redigering i Azure Data Factory
Azure Data Factory användargränssnittet användarupplevelsen (UX) kan du visuellt skapa och distribuera resurser för din datafabrik utan att behöva skriva någon kod. Du kan dra aktiviteter till en rityta för pipelinen, utför testkörningar, felsöker stegvis och distribuerar och övervakar dina pipelinekörningar. Det finns två metoder för att utföra visuell redigering med UX-Gränssnittet:

- Författare direkt med Data Factory-tjänsten.
- Författare med Azure-lagringsplatser Git-integrering för samarbete, källkontroll och versionshantering.

## <a name="author-directly-with-the-data-factory-service"></a>Författare direkt med Data Factory-tjänsten
Visuell redigering med Data Factory-tjänsten skiljer sig från visuell redigering med Git-integrering på två sätt:

- Data Factory-tjänsten innehåller inte en lagringsplats för att lagra JSON-entiteter för dina ändringar.
- Data Factory-tjänsten inte är optimerad för samarbete eller versionskontroll.

![Konfigurera Data Factory-tjänsten ](media/author-visually/configure-data-factory.png)

När du använder UX **Redigeringsyta** skriva direkt med Data Factory-tjänsten, endast den **publicera alla** läget är tillgängligt. Alla ändringar du gör publiceras direkt till Data Factory-tjänsten.

![Publicera läge](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>Redigera med Azure-lagringsplatser Git-integrering
Visuell redigering med Azure-lagringsplatser Git-integrering har stöd för källkontroll och samarbete for work på din data factory-pipelines. Du kan associera en datafabrik med en organisationens databas i Azure-lagringsplatser Git för källkontroll, samarbete, versionshantering och så vidare. En enda Azure-lagringsplatser Git-organisation kan ha flera databaser, men en Azure-lagringsplatser Git-lagringsplatsen kan associeras med endast en data factory. Om du inte har en Azure-lagringsplatser organisation eller databasen, Följ [instruktionerna](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) att skapa dina resurser.

> [!NOTE]
> Du kan lagra aktivitetsskript och datafiler i en Azure-lagringsplatser Git-lagringsplats. Du kan dock överföra filerna manuellt till Azure Storage. Data Factory-pipeline överför inte skript eller datafiler som lagras i en Azure-lagringsplatser Git-databas till Azure Storage automatiskt.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Konfigurera en Azure-lagringsplatser Git-lagringsplats med Azure Data Factory
Du kan konfigurera en Azure-lagringsplatser Git-lagringsplats med en datafabrik med två metoder.

#### <a name="method1"></a> Metod 1 (Azure lagringsplatser Git-lagringsplats): vi sidan Kom igång

I Azure Data Factory går du till den **nu sätter vi igång** sidan. Välj **konfigurera Kodlagringsplats**:

![Konfigurera en kodlagringsplats för Azure-lagringsplatser](media/author-visually/configure-repo.png)

Den **inställningar för lagringsplats** konfigurationsruta visas:

![Konfigurera inställningar för kod lagringsplats](media/author-visually/repo-settings.png)

Fönstret visar följande kod i Azure-lagringsplatser inställningar för lagringsplats:

| Inställning | Beskrivning | Värde |
|:--- |:--- |:--- |
| **Typ av lagringsplats** | Typ av kodlagringsplatsen Azure lagringsplatser.<br/>**Obs**: GitHub stöds inte för närvarande. | Azure-lagringsplatser Git |
| **Azure Active Directory** | Ditt klientnamn för Azure AD. | <your tenant name> |
| **Azure-lagringsplatser organisation** | Organisationsnamnet Azure lagringsplatser. Du kan hitta Azure-lagringsplatser organisationens namn på `https://{organization name}.visualstudio.com`. Du kan [logga in på din Azure-lagringsplatser organisation](https://www.visualstudio.com/team-services/git/) att komma åt din Visual Studio-profil och se dina databaser och projekt. | <your organization name> |
| **Projektnamn** | Projektnamnet Azure lagringsplatser. Du kan hitta din Azure-lagringsplatser projektnamn på `https://{organization name}.visualstudio.com/{project name}`. | <your Azure Repos project name> |
| **RepositoryName** | Din Azure-lagringsplatser kod databasnamn. Projekt för Azure lagringsplatser innehålla Git-lagringsplatser för att hantera din källkod när projektet växer. Du kan skapa en ny databas eller använda en befintlig databas som redan finns i projektet. | <your Azure Repos code repository name> |
| **Samarbete gren** | Din Azure-lagringsplatser samarbete gren som ska användas för publicering. Som standard är det `master`. Ändra den här inställningen om du vill publicera resurser från en annan gren. | <your collaboration branch name> |
| **Rotmapp för vyer** | Rotmappen i din Azure-lagringsplatser samarbete gren. | <your root folder name> |
| **Importera befintlig Data Factory-resurser till databasen** | Anger om du vill importera befintliga data factory-resurser från UX **Redigeringsyta** till en Azure-lagringsplatser Git-lagringsplats. Markera kryssrutan för att importera din data factory-resurser till den associerade Git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt (det vill säga länkade tjänster och datauppsättningar exporteras till separata för). När den här rutan inte har valts, importera inte befintliga resurser. | Valda (standard) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>Metod 2 (Azure lagringsplatser Git-lagringsplats): UX Redigeringsyta
I Azure Data Factory UX **Redigeringsyta**, leta upp din data factory. Välj den **Data Factory** nedrullningsbara menyn och välj sedan **konfigurera Kodlagringsplats**.

En konfigurationsruta visas. Mer information om inställningarna se beskrivningarna i <a href="#method1">konfigurationsmetoden 1</a>.

![Konfigurera inställningar för kod lagringsplats för redigering av UX](media/author-visually/configure-repo-2.png)

## <a name="use-a-different-azure-active-directory-tenant"></a>Använd en annan Azure Active Directory-klient

Du kan skapa en Azure-lagringsplatser Git-lagringsplats i en annan Azure Active Directory-klient. Ange en annan Azure AD-klient du måste ha administratörsbehörighet för den prenumeration som du använder.

## <a name="use-your-personal-microsoft-account"></a>Använd ditt personliga Microsoft-konto

Om du vill använda ett personligt microsoftkonto för Git-integrering kan du länka din personliga Azure-lagringsplats till din organisations Active Directory.

1. Lägg till ditt personliga Microsoft-konto till din organisations Active Directory som gäst. Mer information finns i [användare i Lägg till Azure Active Directory B2B-samarbetet i Azure-portalen](../active-directory/b2b/add-users-administrator.md).

2. Logga in på Azure Portal med ditt personliga Microsoft-konto. Växla sedan till din organisations Active Directory.

3. Gå till avsnittet Azure DevOps, där du nu se ditt personliga lager. Välj lagringsplatsen och Anslut med Active Directory.

Efter de här konfigurationsstegen ditt personliga lager är tillgänglig när du ställer in Git-integrering i Användargränssnittet för Data Factory.

Mer information om att ansluta Azure-databaser till din organisations Active Directory finns i [ansluta din Azure DevOps-organisation till Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="switch-to-a-different-git-repo"></a>Växla till en annan Git-lagringsplats

Du växlar till en annan Git-lagringsplats genom att leta upp ikonen i det övre högra hörnet av sidan för Data Factory-översikt, enligt följande skärmbild. Om du inte ser ikonen, rensa webbläsarens lokala cacheminnet. Välj ikonen Ta bort kopplingen till den aktuella lagringsplatsen.

När du tar bort kopplingen till den aktuella lagringsplatsen kan konfigurera du inställningarna Git om du vill använda en annan lagringsplats. Du kan sedan importera befintliga Data Factory-resurser till den nya lagringsplatsen.

![Ta bort kopplingen till den aktuella Git-lagringsplatsen](media/author-visually/remove-repo.png)

## <a name="use-version-control"></a>Använda versionskontroll
Versionskontrollsystem (även kallat _källkontroll_) möjligt för utvecklare att samarbeta med kod och spåra ändringar som görs i koden grundläggande. Källkontroll är ett viktigt verktyg för flera utvecklare projekt.

Varje Azure-lagringsplatser Git-lagringsplats som är associerat med en data factory har en gren för samarbete. (`master` är samarbete standardgrenen). Användarna kan också skapa funktionen grenar genom att klicka på **+ ny gren** och göra utvecklingen i funktionen grenar.

![Ändra koden genom att synkronisera eller publicera](media/author-visually/sync-publish.png)

När du är klar med utvecklingen nya funktioner i din gren med funktionen kan du klicka på **skapa pull-begäran**. Den här åtgärden tar du Azure lagringsplatser Git där du kan öka pull-begäranden gör code granskningar och slå samman ändringar i din gren för samarbete. (`master` är standard). Du kan bara publicera till Data Factory-tjänsten från din gren för samarbete. 

![Skapa en ny pullbegäran](media/author-visually/create-pull-request.png)

## <a name="configure-publishing-settings"></a>Konfigurera inställningar för publicering

Konfigurera grenen publicera – det vill säga grenen där sparas Resource Manager-mallar – Lägg till en `publish_config.json` filen till rotmappen i grenen samarbete. Data Factory läser den här filen, söker efter fältet `publishBranch`, och skapar en ny gren (om den inte redan finns) med det angivna värdet. Det sparar alla Resource Manager-mallar i den angivna platsen. Exempel:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

När du publicerar från Git-läge kan bekräfta du att Data Factory använder publish-gren som du förväntar dig enligt följande skärmbild:

![Bekräfta rätt publicera gren](media/author-visually/configure-publish-branch.png)

När du anger en ny gren publicera Data Factory inte ta bort den tidigare publicera grenen. Om du vill remote grenen tidigare publicera kan ta du bort den manuellt.

Data Factory läser bara den `publish_config.json` filen när den har lästs in fabriken. Om du redan har den fabriken som lästs in i portalen kan du uppdatera webbläsaren för att ändringarna ska börja gälla.

## <a name="publish-code-changes"></a>Publicera ändringar i koden
När du har en sammanfogad ändringar i grenen samarbete (`master` är standard), Välj **publicera** manuellt publicera kodändringarna i huvudgrenen till Data Factory-tjänsten.

![Publicera ändringar i Data Factory-tjänsten](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> Huvudgrenen är inte representerar det distribueras i Data Factory-tjänsten. Huvudgrenen *måste* publiceras manuellt till Data Factory-tjänsten.

## <a name="author-with-github-integration"></a>Redigera med GitHub-integrering

Visuell redigering med GitHub-integreringen har stöd för källkontroll och samarbete for work på din data factory-pipelines. Du kan associera en datafabrik med en GitHub-lagringsplats för kontot för källkontroll, samarbete, versionskontroll. Ett enda GitHub-konto kan ha flera databaser, men en GitHub-lagringsplats kan associeras med endast en data factory. Om du inte har en GitHub-konto eller följer [instruktionerna](https://github.com/join) att skapa dina resurser. GitHub-integreringen med Data Factory har stöd för såväl offentliga GitHub som GitHub Enterprise.

Du måste ha administratörsbehörighet för den prenumeration som du använder för att konfigurera en GitHub-lagringsplatsen.

Titta på följande videoklipp för en nio minuters introduktion och demonstration av den här funktionen:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>Begränsningar

- Du kan lagra aktivitetsskript och datafiler i en GitHub-lagringsplats. Du kan dock överföra filerna manuellt till Azure Storage. Data Factory-pipeline överför inte skript eller datafiler som lagras i en GitHub-databas till Azure Storage automatiskt.

- GitHub Enterprise med en version som är äldre än 2.14.0 fungerar inte i Microsoft Edge-webbläsaren.

- GitHub-integreringen med Data faktor visuella redigering verktyg fungerar bara i den allmänt tillgängliga versionen av Data Factory.

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>Konfigurera en offentlig GitHub-lagringsplats med Azure Data Factory

Du kan konfigurera en GitHub-lagringsplats med en datafabrik med två metoder.

**Metod 1 (offentliga lagringsplats): vi sidan Kom igång**

I Azure Data Factory går du till den **nu sätter vi igång** sidan. Välj **konfigurera Kodlagringsplats**:

![Sidan för data Factory Kom igång](media/author-visually/github-integration-image1.png)

Den **inställningar för lagringsplats** konfigurationsruta visas:

![Inställningar för GitHub-lagringsplats](media/author-visually/github-integration-image2.png)

Fönstret visar följande kod i Azure-lagringsplatser inställningar för lagringsplats:

| **Inställning**                                              | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Värde**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Typ av lagringsplats**                                      | Typ av kodlagringsplatsen Azure lagringsplatser.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **GitHub-konto**                                       | Namnet på ditt GitHub. Det här namnet kan hitta https://github.com/{account namn} / {databasnamn}. Navigera till den här sidan uppmanas du att ange autentiseringsuppgifter för GitHub OAuth till ditt GitHub-konto.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | Din GitHub-lagringsplatsen kodnamnet. GitHub-konton innehåller Git-lagringsplatser för att hantera din källkod. Du kan skapa en ny databas eller använda en befintlig databas som redan finns i ditt konto.                                                                                                                                                                                                                              |                    |
| **Samarbete gren**                                 | Din GitHub samarbete gren som ska användas för publicering. Som standard är det master. Ändra den här inställningen om du vill publicera resurser från en annan gren.                                                                                                                                                                                                                                                               |                    |
| **Rotmapp för vyer**                                          | Rotmappen i din GitHub samarbete gren.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Importera befintlig Data Factory-resurser till databasen** | Anger om du vill importera befintliga data factory-resurser från UX **Redigeringsyta** i GitHub-lagringsplatsen. Markera kryssrutan för att importera din data factory-resurser till den associerade Git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt (det vill säga länkade tjänster och datauppsättningar exporteras till separata för). När den här rutan inte har valts, importera inte befintliga resurser. | Valda (standard) |
| **Gren att importera resurs i**                       | Anger vilken gren som data factory-resurser (pipelines, datauppsättningar, länkade tjänster osv) importeras. Du kan importera resurser till en av följande grenar: en. Samarbete b. Skapa ny c. Använd befintlig                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>Metod 2 (offentliga lagringsplats): UX Redigeringsyta

I Azure Data Factory UX **Redigeringsyta**, leta upp din data factory. Välj den **Data Factory** nedrullningsbara menyn och välj sedan **konfigurera Kodlagringsplats**.

En konfigurationsruta visas. Mer information om inställningarna se beskrivningarna i *konfigurationsmetoden 1* ovan.

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>Konfigurera en lagringsplats med GitHub Enterprise med Azure Data Factory

Du kan konfigurera en lagringsplats med GitHub Enterprise med en datafabrik med två metoder.

 #### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>Metod 1 (Enterprise lagringsplats): vi sidan Kom igång

I Azure Data Factory går du till den **nu sätter vi igång** sidan. Välj **konfigurera Kodlagringsplats**:

![Sidan för data Factory Kom igång](media/author-visually/github-integration-image1.png)

Den **inställningar för lagringsplats** konfigurationsruta visas:

![Inställningar för GitHub-lagringsplats](media/author-visually/github-integration-image3.png)

Fönstret visar följande kod i Azure-lagringsplatser inställningar för lagringsplats:

| **Inställning**                                              | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Värde**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Typ av lagringsplats**                                      | Typ av kodlagringsplatsen Azure lagringsplatser.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Använd GitHub Enterprise**                                | Kryssruta för att välja GitHub Enterprise                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **GitHub Enterprise-URL**                                | GitHub Enterprise rot-URL. Exempel: https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **GitHub-konto**                                       | Namnet på ditt GitHub. Det här namnet kan hitta https://github.com/{account namn} / {databasnamn}. Navigera till den här sidan uppmanas du att ange autentiseringsuppgifter för GitHub OAuth till ditt GitHub-konto.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | Din GitHub-lagringsplatsen kodnamnet. GitHub-konton innehåller Git-lagringsplatser för att hantera din källkod. Du kan skapa en ny databas eller använda en befintlig databas som redan finns i ditt konto.                                                                                                                                                                                                                              |                    |
| **Samarbete gren**                                 | Din GitHub samarbete gren som ska användas för publicering. Som standard är det master. Ändra den här inställningen om du vill publicera resurser från en annan gren.                                                                                                                                                                                                                                                               |                    |
| **Rotmapp för vyer**                                          | Rotmappen i din GitHub samarbete gren.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Importera befintlig Data Factory-resurser till databasen** | Anger om du vill importera befintliga data factory-resurser från UX **Redigeringsyta** i GitHub-lagringsplatsen. Markera kryssrutan för att importera din data factory-resurser till den associerade Git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt (det vill säga länkade tjänster och datauppsättningar exporteras till separata för). När den här rutan inte har valts, importera inte befintliga resurser. | Valda (standard) |
| **Gren att importera resurs i**                       | Anger vilken gren som data factory-resurser (pipelines, datauppsättningar, länkade tjänster osv) importeras. Du kan importera resurser till en av följande grenar: en. Samarbete b. Skapa ny c. Använd befintlig                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>Metod 2 (Enterprise lagringsplats): UX Redigeringsyta

I Azure Data Factory UX **Redigeringsyta**, leta upp din data factory. Välj den **Data Factory** nedrullningsbara menyn och välj sedan **konfigurera Kodlagringsplats**.

En konfigurationsruta visas. Mer information om inställningarna se beskrivningarna i *konfigurationsmetoden 1* ovan.

## <a name="use-the-expression-language"></a>Använd språket du uttryck
Du kan ange uttryck för egenskapsvärden med hjälp av Uttrycksspråk som stöds av Azure Data Factory.

Ange uttryck för egenskapsvärdena genom att välja **Lägg till dynamiskt innehåll**:

![Använd språket du uttryck](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Använd funktioner och parametrar

Du kan använda funktioner eller ange parametrar för pipelines och datauppsättningar i Data Factoryn **Uttrycksverktyget**:

Information om uttryck som stöds finns i [uttryck och funktioner i Azure Data Factory](control-flow-expression-language-functions.md).

![Lägg till dynamiskt innehåll](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Ge feedback
Välj **Feedback** att kommentera om funktioner eller för att meddela Microsoft om problem med verktyget:

![Feedback](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Nästa steg
Läs mer om övervakning och hantera pipelines i [övervaka och hantera pipelines programmässigt](monitor-programmatically.md).
