---
title: Redigera visuellt Azure datafabriker | Microsoft Docs
description: "Lär dig hur du skapar visuellt Azure datafabriker"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: 3e67665facba78c4ca8e2317f0323b4c5c02a49c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="visually-author-data-factories"></a>Visuellt författare datafabriker
Azure Data Factory UX-upplevelse användare visuellt skapa och distribuera resurser i sina data factory utan att skriva en enda rad kod. Det här gränssnittet utan kod kan du dra och släppa aktiviteter på en pipeline-arbetsyta, utföra testkörningar, felsöka upprepade gånger, och distribuera och övervaka din pipeline-körs. Du kan välja att använda verktyget ADF UX på två sätt:

1. Arbeta direkt med Data Factory-tjänsten
2. Konfigurera VSTS Git-integrering för samarbete, källkontrollen eller versionshantering

## <a name="authoring-with-data-factory"></a>Redigering med Data Factory
Det första alternativet redigering direkt med Data Factory-läge. Den här metoden skiljer sig från redigering VSTS kod databasen att lagra JSON-entiteter av ändringarna databas eller optimerat för samarbete eller versionskontroll.

![Konfigurera Data Factory](media/author-visually/configure-data-factory.png)

Det finns Data Factory-läge ”publicera” läge. Alla ändringar du gör publiceras direkt till Data Factory-tjänsten.

![Publicera data Factory](media/author-visually/data-factory-publish.png)

## <a name="authoring-with-vsts-git-integration"></a>Redigering med VSTS Git-integrering
Redigering med VSTS Git-integrering kan användas för källkontroll och samarbete vid skapande av din data factory pipelines. Användare har möjlighet att koppla en datafabrik till en VSTS Git konto lagringsplats för källkontroll, samarbete och versionshantering osv. Ett enda VSTS GIT-konto kan ha flera databaser. Men kan en VSTS Git-lagringsplats bara associeras med en enda data factory. Om du inte har ett konto för VSTS och databasen redan kan skapa en [här](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student).

### <a name="configure-vsts-git-repo-with-azure-data-factory"></a>Konfigurera VSTS Git Repo med Azure Data Factory
Användare kan konfigurera en VSTS GIT-lagringsplatsen med en datafabrik via två metoder.

#### <a name="method-1-lets-get-started-page"></a>Metod 1: ”Vi börjar med' sida

Gå till sidan 'Vi börjar med ”och klicka på” Konfigurera databasen'

![Konfigurera databasen](media/author-visually/configure-repo.png)

Därifrån visas en sida för att konfigurera inställningar för databasen.

![Konfigurera inställningar för databasen](media/author-visually/repo-settings.png)
* **Databasen typen**: Visual Studio Team Services Git (för närvarande Github stöds inte.)
* **Visual Studio Team Services-konto**: kontonamnet finns från https://{account namn}. visualstudio.com. Logga in på ditt konto i VSTS [här](https://www.visualstudio.com/team-services/git/) och åtkomst till din Visual Studio-profil om du vill visa dina databaser och projekt
* **Projektnamn:** projektnamnet hittar från https://{account name}.visualstudio.com/{project namn}
* **RepositoryName:** databasnamnet. VSTS projekt innehålla Git databaser för att hantera din källkod när projektet växer. Skapa en ny databas eller använda en befintlig databas redan finns i projektet.
* **Importera befintliga Data Factory-resurser till databasen**: genom att markera den här rutan kan du importera dina aktuella data factory resurser som skapats på UX-arbetsytan till den associerade VSTS GIT-lagringsplatsen i JSON-format. Den här åtgärden exporterar varje resurs individuellt (som är länkade tjänster och datauppsättningar exporteras till separata JSONs).    Om du avmarkerar den här kryssrutan, importeras inte befintliga resurser till på Git-lagringsplatsen.

#### <a name="method-2-from-authoring-canvas"></a>Metod 2: Från arbetsytan redigering

Klicka på listrutan Data Factory under din data factory-namnet i 'redigering arbetsytan'. Klicka på ”Konfigurera databasen”. Liknar **metod 1**, en sida som visas för att konfigurera inställningar för databasen. Information om inställningarna för föregående avsnitt finns.

![Konfigurera databasen 2](media/author-visually/configure-repo-2.png)

### <a name="version-control"></a>Versionskontroll
Versionskontroll, kallas även att som källkontroll, system gör att utvecklare kan samarbeta på koden och spåra ändringar i koden grundläggande. Källkontrollen är ett viktigt verktyg för flera utvecklare projekt.

Varje VSTS Git-lagringsplats som kopplats till en datafabrik har en mastergrenen. Därifrån kan varje användare som har åtkomst till VSTS Git-lagringsplatsen har två alternativ när du ändrar: synkronisera och publicera.

![Publicera synkronisering](media/author-visually/sync-publish.png)

#### <a name="sync"></a>Sync

När du klickar på 'sync' du hämtar ändringar från mastergrenen i din lokala gren eller skicka ändringar från din lokala gren till mastergrenen.

![Synkroniserar ändringar](media/author-visually/sync-change.png)

#### <a name="publish"></a>Publicera
 Publicera ändringar i mastergrenen till Data Factory-tjänsten.

> [!NOTE]
> Den **mastergrenen är inte representerar vad distribueras i Data Factory-tjänsten.** Mastergrenen *måste* publiceras manuellt till Data Factory-tjänsten.




## <a name="expression-language"></a>Uttrycksspråk

Användarna kan ange uttryck för att definiera egenskapsvärden med hjälp av Uttrycksspråk som stöds av Azure Data Factory. Se [uttryck och funktioner i Azure Data Factory](control-flow-expression-language-functions.md) för mer om vilka uttryck som stöds.

Ange uttryck i egenskapsvärden i UX t.ex.

![Uttrycksspråk](media/author-visually/expression-language.png)

## <a name="parameters"></a>Parametrar
Användare kan ange parametrar för Pipelines och datauppsättningar, på fliken-Parametrar'. Dessutom Använd parametrar i egenskaperna för enkelt genom att trycka på ”Lägg till dynamiskt innehåll”.

![Dynamiskt innehåll](media/author-visually/dynamic-content.png)

Därifrån kan du använda en befintlig parameter eller ange en ny parameter i egenskapens värde.

![Parametrar](media/author-visually/parameters.png)

## <a name="feedback"></a>Feedback
Klicka på ikonen ”Feedback” om du vill ge oss feedback (Microsoft) på olika funktioner eller eventuella problem som du kan hantera.

![Feedback](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Nästa steg

Mer information om att övervaka och hantera pipelines, se [övervaka och hantera pipelines programmässigt](monitor-programmatically.md) artikel
