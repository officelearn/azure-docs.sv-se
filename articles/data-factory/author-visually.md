---
title: Visual redigering i Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder visual redigering i Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: bce74c80f53eda654bb0289a1ff8a6cb88fd13f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visual redigering i Azure Data Factory
Azure Data Factory användargränssnittet experience (UX) kan du visuellt skapa och distribuera resurser för din data factory utan att behöva skriva någon kod. Du kan dra aktiviteter till en pipeline-arbetsyta, utföra testkörningar, felsöka upprepade gånger, och distribuera och övervaka din pipeline-körs. Det finns två tillvägagångssätt för att utföra visual redigering med UX:

- Författare direkt med Data Factory-tjänsten.
- Redigera med Visual Studio Team Services VSTS () Git-integrering för samarbete, källkontrollen eller versionshantering.

## <a name="author-directly-with-the-data-factory-service"></a>Författare direkt med Data Factory-tjänsten
Visual redigering med Data Factory-tjänsten skiljer sig från visual redigering med VSTS på två sätt:

- Data Factory-tjänsten innehåller en databas för att lagra JSON-entiteter för dina ändringar.
- Data Factory-tjänsten inte är optimerad för samarbete eller versionskontroll.

![Konfigurera tjänsten Data Factory ](media/author-visually/configure-data-factory.png)

När du använder UX **redigering arbetsytan** att skapa direkt med Data Factory-tjänsten, endast den **publicera** läget är tillgängligt. Alla ändringar du gör publiceras direkt till Data Factory-tjänsten.

![Publicera läge](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Författare med VSTS Git-integrering
Visual redigering med VSTS Git-integration stöder källkontrollen och samarbete for work på din data factory pipelines. Du kan associera en datafabrik med en VSTS Git-lagringsplats för kontot för källkontroll, samarbete, versionshantering och så vidare. Ett enda VSTS Git-konto kan ha flera databaser, men en VSTS Git-lagringsplats kan associeras med en enda data factory. Om du inte har ett konto för VSTS eller databasen, Följ [instruktionerna](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) att skapa dina resurser.

> [!NOTE]
> Du kan lagra skript och datafiler i en VSTS GIT-lagringsplats. Du måste dock överför filerna manuellt till Azure Storage. Data Factory-pipelinen hämtar inte skript eller datafiler som lagras i en VSTS GIT-databas till Azure Storage automatiskt.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>Konfigurera en VSTS Git-lagringsplats med Azure Data Factory
Du kan konfigurera en VSTS GIT-lagringsplats med en datafabrik via två metoder.

<a name="method1"></a>
#### <a name="configuration-method-1-lets-get-started-page"></a>Konfigurationen för 1: ska få igång sida
I Azure Data Factory går du till den **nu sätter vi igång** sidan. Välj **konfigurera databasen**:

![Konfigurera en lagringsplats för VSTS kod](media/author-visually/configure-repo.png)

Den **databasen inställningar** konfigurationen visas:

![Konfigurera inställningar för databasen](media/author-visually/repo-settings.png)

I rutan visas följande kod i VSTS databasen inställningar:

| Inställning | Beskrivning | Värde |
|:--- |:--- |:--- |
| **Typ av databasen** | Typ av VSTS kod databasen.<br/>**Obs**: GitHub stöds inte för närvarande. | Visual Studio Team Services Git |
| **Visual Studio Team Services Account** | VSTS namnet på ditt konto. Du kan hitta kontonamnet VSTS på `https://{account name}.visualstudio.com`. Du kan [logga in på ditt konto i VSTS](https://www.visualstudio.com/team-services/git/) att komma åt din Visual Studio-profil och se dina databaser och projekt. | \<namnet på ditt konto > |
| **ProjectName** | Ditt VSTS projektnamn. Du kan hitta din VSTS projektnamn på `https://{account name}.visualstudio.com/{project name}`. | \<ditt VSTS projektnamn > |
| **RepositoryName** | Din VSTS kod databasens namn. VSTS projekt innehålla Git databaser för att hantera din källkod när projektet växer. Du kan skapa en ny databas eller använda en befintlig databas som redan ingår i projektet. | \<databasen för VSTS kodnamnet > |
| **Importera befintliga Data Factory-resurser till databasen** | Anger om du vill importera befintliga data factory-resurser från UX **redigering arbetsytan** i en VSTS Git-lagringsplats. Markera kryssrutan för att importera dina data factory-resurser till den associera Git-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt (det vill säga de länkade tjänsterna och datauppsättningar exporteras till separata JSONs). När rutan inte är markerad importeras inte befintliga resurser. | Valda (standard) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Konfigurationsmetoden 2: UX arbetsytan redigering
I Azure Data Factory UX **redigering arbetsytan**, leta upp din data factory. Välj den **Datafabriken** listrutan och välj sedan **konfigurera databasen**.

En konfiguration visas. Mer information om inställningarna finns beskrivningar i <a href="#method1">konfigurationsmetoden 1</a>.

![Konfigurera inställningar för databasen koden för UX redigering](media/author-visually/configure-repo-2.png)

### <a name="use-version-control"></a>Använda versionskontroll
System version (även kallat _kontroll_) kan utvecklare samarbeta på koden och spåra ändringar som görs i koden bas. Källkontrollen är ett viktigt verktyg för flera utvecklare projekt.

Varje VSTS Git-lagringsplats som är kopplad till en datafabrik har en mastergrenen. När du har åtkomst till en VSTS Git-lagringsplatsen, kan du ändra koden genom att välja **Sync** eller **publicera**:

![Ändra koden genom att synkronisera eller publicera](media/author-visually/sync-publish.png)

#### <a name="sync-code-changes"></a>Synkronisera kodändringar
När du har valt **Sync**, du kan dra ändras från mastergrenen till din lokala gren eller push ändras från din lokala gren till mastergrenen.

![Synkronisera kodändringar](media/author-visually/sync-change.png)

#### <a name="publish-code-changes"></a>Publicera ändringar i koden
Välj **publicera** manuellt publiceras kodändringarna i mastergrenen till tjänsten Data Factory.

> [!IMPORTANT]
> Mastergrenen är inte representerar vad distribueras i Data Factory-tjänsten. Mastergrenen *måste* publiceras manuellt till Data Factory-tjänsten.

## <a name="use-the-expression-language"></a>Använd språket du uttryck
Du kan ange uttryck för egenskapsvärden med hjälp av Uttrycksspråk som stöds av Azure Data Factory. Information om uttryck som stöds finns [uttryck och funktioner i Azure Data Factory](control-flow-expression-language-functions.md).

Ange uttryck för egenskapsvärden med hjälp av UX **redigering arbetsytan**:

![Använd språket du uttryck](media/author-visually/expression-language.png)

## <a name="specify-parameters"></a>Ange parametrar
Du kan ange parametrar för pipelines och datauppsättningar i Azure Data Factory **parametrar** fliken. Du kan enkelt använda parametrarna i egenskaper genom att välja **lägga till dynamiskt innehåll**:

![Lägga till dynamiskt innehåll](media/author-visually/dynamic-content.png)

Du kan använda befintliga parametrar eller ange nya parametrar för värden för egenskaper:

![Ange parametrar för egenskapsvärden](media/author-visually/parameters.png)

## <a name="provide-feedback"></a>Ge feedback
Välj **Feedback** att kommentera om funktioner eller för att meddela Microsoft om problem med verktyget:

![Feedback](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Nästa steg
Läs mer om övervakning och hantering av pipelines i [övervaka och hantera pipelines programmässigt](monitor-programmatically.md).
