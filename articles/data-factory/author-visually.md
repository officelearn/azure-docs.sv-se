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
ms.date: 06/01/2018
ms.author: shlo
ms.openlocfilehash: 655a6ab2960047cde50bec2953015283ca8577f0
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214870"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visuell redigering i Azure Data Factory
Azure Data Factory användargränssnittet användarupplevelsen (UX) kan du visuellt skapa och distribuera resurser för din datafabrik utan att behöva skriva någon kod. Du kan dra aktiviteter till en rityta för pipelinen, utför testkörningar, felsöker stegvis och distribuerar och övervakar dina pipelinekörningar. Det finns två metoder för att utföra visuell redigering med UX-Gränssnittet:

- Författare direkt med Data Factory-tjänsten.
- Redigera med Visual Studio Team Services (VSTS) Git-integrering för samarbete, källkontroll eller versionshantering.

## <a name="author-directly-with-the-data-factory-service"></a>Författare direkt med Data Factory-tjänsten
Visuell redigering med Data Factory-tjänsten skiljer sig från visuell redigering med VSTS på två sätt:

- Data Factory-tjänsten innehåller inte en lagringsplats för att lagra JSON-entiteter för dina ändringar.
- Data Factory-tjänsten inte är optimerad för samarbete eller versionskontroll.

![Konfigurera Data Factory-tjänsten ](media/author-visually/configure-data-factory.png)

När du använder UX **Redigeringsyta** skriva direkt med Data Factory-tjänsten, endast den **publicera alla** läget är tillgängligt. Alla ändringar du gör publiceras direkt till Data Factory-tjänsten.

![Publicera läge](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Redigera med VSTS Git-integrering
Visuell redigering med VSTS Git-integrering har stöd för källkontroll och samarbete for work på din data factory-pipelines. Du kan associera en datafabrik med en VSTS Git-lagringsplats för kontot för källkontroll, samarbete, versionshantering och så vidare. Ett enda VSTS Git-konto kan ha flera databaser, men en VSTS Git-lagringsplats kan associeras med endast en data factory. Om du inte har en VSTS-konto eller följer [instruktionerna](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) att skapa dina resurser.

> [!NOTE]
> Du kan lagra och datafilerna i VSTS GIT-lagringsplats. Du kan dock överföra filerna manuellt till Azure Storage. Data Factory-pipeline överför inte skript eller datafiler som lagras i en VSTS GIT-databas till Azure Storage automatiskt.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>Konfigurera en VSTS Git-lagringsplats med Azure Data Factory
Du kan konfigurera en VSTS GIT-lagringsplats med en datafabrik med två metoder.

#### <a name="method1"></a> Metod 1: vi sidan Kom igång

I Azure Data Factory går du till den **nu sätter vi igång** sidan. Välj **konfigurera Kodlagringsplats**:

![Konfigurera en VSTS-kodlagringsplatsen](media/author-visually/configure-repo.png)

Den **inställningar för lagringsplats** konfigurationsruta visas:

![Konfigurera inställningar för kod lagringsplats](media/author-visually/repo-settings.png)

Fönstret visar följande kod i VSTS inställningar för lagringsplats:

| Inställning | Beskrivning | Värde |
|:--- |:--- |:--- |
| **Typ av lagringsplats** | Typ av VSTS-kodlagringsplatsen.<br/>**Obs**: GitHub stöds inte för närvarande. | Visual Studio Team Services Git |
| **Azure Active Directory** | Ditt klientnamn för Azure AD. | <your tenant name> |
| **Visual Studio Team Services-konto** | Ditt VSTS-kontonamn. Du hittar namnet på ditt VSTS-konto på `https://{account name}.visualstudio.com`. Du kan [logga in på ditt VSTS-konto](https://www.visualstudio.com/team-services/git/) att komma åt din Visual Studio-profil och se dina databaser och projekt. | <your account name> |
| **Projektnamn** | Ditt VSTS-projektnamn. Du kan hitta din VSTS-projektnamn på `https://{account name}.visualstudio.com/{project name}`. | <your VSTS project name> |
| **RepositoryName** | Din VSTS-kod databasnamn. VSTS-projekt innehålla Git-lagringsplatser för att hantera din källkod när projektet växer. Du kan skapa en ny databas eller använda en befintlig databas som redan finns i projektet. | <your VSTS code repository name> |
| **Samarbete gren** | Din VSTS samarbete gren som ska användas för publicering. Som standard är det `master`. Ändra det här alternativet om du vill publicera resurser från en annan gren. | <your collaboration branch name> |
| **Rotmapp för vyer** | Rotmappen i din VSTS samarbete gren. | <your root folder name> |
| **Importera befintlig Data Factory-resurser till databasen** | Anger om du vill importera befintliga data factory-resurser från UX **Redigeringsyta** till en VSTS Git-lagringsplats. Markera kryssrutan för att importera din data factory-resurser till den associerade Git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt (det vill säga länkade tjänster och datauppsättningar exporteras till separata för). När den här rutan inte har valts, importera inte befintliga resurser. | Valda (standard) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metod 2: UX Redigeringsyta
I Azure Data Factory UX **Redigeringsyta**, leta upp din data factory. Välj den **Data Factory** nedrullningsbara menyn och välj sedan **konfigurera Kodlagringsplats**.

En konfigurationsruta visas. Mer information om inställningarna se beskrivningarna i <a href="#method1">konfigurationsmetoden 1</a>.

![Konfigurera inställningar för kod lagringsplats för redigering av UX](media/author-visually/configure-repo-2.png)

#### <a name="switch-to-a-different-git-repo"></a>Växla till en annan Git-lagringsplats

Du växlar till en annan Git-lagringsplats genom att leta upp ikonen i det övre högra hörnet av sidan för Data Factory-översikt, enligt följande skärmbild. Om du inte ser ikonen, rensa webbläsarens lokala cacheminnet. Välj ikonen Ta bort kopplingen till den aktuella lagringsplatsen.

När du tar bort kopplingen till den aktuella lagringsplatsen kan konfigurera du inställningarna Git om du vill använda en annan lagringsplats. Du kan sedan importera befintliga Data Factory-resurser till den nya lagringsplatsen.

![Ta bort kopplingen till den aktuella Git-lagringsplatsen.](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>Använda versionskontroll
Versionskontrollsystem (även kallat _källkontroll_) möjligt för utvecklare att samarbeta med kod och spåra ändringar som görs i koden grundläggande. Källkontroll är ett viktigt verktyg för flera utvecklare projekt.

Varje VSTS Git-lagringsplats som är associerat med en data factory har en gren för samarbete. (`master` är samarbete standardgrenen). Användarna kan också skapa funktionen grenar genom att klicka på **+ ny gren** och göra utvecklingen i funktionen grenar.

![Ändra koden genom att synkronisera eller publicera](media/author-visually/sync-publish.png)

När du är klar med utvecklingen nya funktioner i din gren med funktionen kan du klicka på **skapa pull-begäran**. Då kommer du till VSTS GIT där du kan öka pull-begäranden, code granskningar och sammanfoga ändringarna i din gren för samarbete. (`master` är standard). Du kan bara publicera till Data Factory-tjänsten från din gren för samarbete. 

![Skapa en ny pullbegäran](media/author-visually/create-pull-request.png)

#### <a name="publish-code-changes"></a>Publicera ändringar i koden
När du har en sammanfogad ändringar i grenen samarbete (`master` är standard), Välj **publicera** manuellt publicera kodändringarna i huvudgrenen till Data Factory-tjänsten.

![Publicera ändringar i Data Factory-tjänsten](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> Huvudgrenen är inte representerar det distribueras i Data Factory-tjänsten. Huvudgrenen *måste* publiceras manuellt till Data Factory-tjänsten.

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
