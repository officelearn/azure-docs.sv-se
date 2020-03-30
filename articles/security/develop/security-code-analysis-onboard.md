---
title: Onboarding-guide för Microsoft Security Code Analysis
description: I den här artikeln beskrivs hur du installerar tillägget Microsoft Security Code Analysis
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 6132aab98cc8145cb99cf153c64f20fbac00131c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197013"
---
# <a name="onboarding-and-installing"></a>Introduktion och installation

Förutsättningar för att komma igång med Analys av Microsofts säkerhetskod:

- Ett kvalificerat Microsoft Unified-supporterbjudande, som beskrivs i följande avsnitt.
- En Azure DevOps-organisation.
- Behörighet att installera tillägg till Azure DevOps-organisationen.
- Källkod som kan synkroniseras till en molnbaserad Azure DevOps-pipeline.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Introduktion till tillägget Microsoft Security Code Analysis

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Är du intresserad av att köpa tillägget Microsoft Security Code Analysis?

Om du har något av följande supporterbjudanden kontaktar du din tekniska kontohanterare för att köpa eller byta befintliga timmar för att få tillgång till tillägget:

- Avancerad nivå med enhetlig support
- Enhetlig supportprestandanivå
- Premier Support för utvecklare
- Premier Support för partners
- Premier Support för företag

Om du inte har något av ovanstående supportavtal kan du köpa tillägget från en av våra Partners.

**Nästa steg:**

Kontakta en partner från listan nedan och be om att få köpa tillägget Microsoft Security Code Analysis.

>**Partner:**

- Zoner - Kontaktuppgifter:cloudsupport@zones.com

### <a name="become-a-partner"></a>Bli partner

Microsoft Security Code Analysis-teamet söker dig till partner ombord med ett Premier Support for Partners-avtal. Partner hjälper Azure DevOps-kunder att utvecklas säkrare genom att sälja tillägget till kunder som vill köpa det, men inte har ett Enterprise Support-avtal med Microsoft. Intresserade partners kan registrera sig [här](http://www.microsoftpartnersupport.com/msrd/opin).

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Installera tillägget Microsoft Security Code Analysis

1. När tillägget har delats med din Azure DevOps-organisation går du till din Azure DevOps-organisationssida. En exempel-URL för `https://dev.azure.com/contoso`en sådan sida är .
1. Välj ikonen för shoppingväskan i det övre högra hörnet bredvid ditt namn och välj sedan **Hantera tillägg**.
1. Välj **Delad**.
1. Välj tillägget Microsoft Security Code Analysis, välj **installera**.
1. Välj Azure DevOps-organisationen i listrutan för att installera tillägget på.
1. Välj **Installera**. När installationen är klar kan du börja använda tillägget.

>[!NOTE]
> Även om du inte har åtkomst för att installera tillägget fortsätter du med installationsstegen. Du kan begära åtkomst från din Azure DevOps-organisationsadministratör under installationsprocessen.

När du har installerat tillägget är de säkra utvecklingsversionsuppgifterna synliga och tillgängliga för att lägga till dina Azure Pipelines.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Lägga till specifika bygguppgifter i din Azure DevOps-pipeline

1. Öppna ditt teamprojekt från din Azure DevOps-organisation.
1. Välj **Pipelines** > **Builds**.
1. Välj den pipeline som du vill lägga till tilläggsversionsuppgifterna i:
   - Ny pipeline: Välj **Ny** och följ stegen som beskrivs för att skapa en ny pipeline.
   - Redigera pipeline: Välj en befintlig pipeline och välj sedan **Redigera** för att börja redigera pipelinen.
1. Markera **+** och gå till fönstret **Lägg till uppgifter.**
1. Leta reda på den bygguppgift som du vill lägga till i listan eller genom att använda sökrutan. Välj **Lägg till**.
1. Ange de parametrar som behövs för aktiviteten.
1. Köa ett nytt bygge.
   >[!NOTE]
   >Fil- och mappsökvägarna är relativa till källdatabasens rot. Om du anger utdatafiler och mappar som parametrar ersätts de med den gemensamma plats som vi har definierat på byggagenten.

> [!TIP]
>
> - Om du vill köra en analys efter din version placerar du bygguppgifterna för Microsoft Security Code Analysis efter steget Publicera byggartefakter i din version. På så sätt kan din version avsluta och publicera resultat innan du kör statiska analysverktyg.
> - Välj alltid **Fortsätt med fel** för bygguppgifter för säker utveckling. Även om ett verktyg misslyckas, kan de andra köras. Det finns inga ömsesidiga beroenden mellan verktyg.
> - Byggfunktionerna i Microsoft Security Code Analysis misslyckas bara om ett verktyg inte kan köras. Men de lyckas även om ett verktyg identifierar problem i koden. Genom att använda aktiviteten Skapa efter analys kan du konfigurera bygget så att den misslyckas när ett verktyg identifierar problem i koden.
> - Vissa Azure DevOps-bygguppgifter stöds inte när de körs via en versionspipeline. Mer specifikt stöder Azure DevOps inte uppgifter som publicerar artefakter från en versionspipeline.
> - En lista över fördefinierade variabler i Azure DevOps Team Build som du kan ange som parametrar finns i [Azure DevOps Build Variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar bygguppgifterna finns i vår [konfigurationsguide](security-code-analysis-customize.md) eller [YAML-konfigurationsguide](yaml-configuration.md).

Om du har fler frågor om tillägget och de verktyg som erbjuds, kolla in vår [FAQ sida](security-code-analysis-faq.md).
