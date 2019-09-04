---
title: Onboarding-guide för Microsoft Security code Analysis
description: Den här artikeln beskriver hur du installerar tillägget Microsoft Security code Analysis
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
ms.openlocfilehash: 8936bc21af951cbabb90188b6a31d610caf80395
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241763"
---
# <a name="onboarding-and-installing"></a>Onboarding och installation

Krav för att komma igång med Microsofts säkerhets kod analys:

- Ett berättigat Microsoft Unified Support erbjudande, enligt beskrivningen i följande avsnitt.
- En Azure DevOps-organisation.
- Behörighet att installera tillägg i Azure DevOps-organisationen.
- Källkod som kan synkroniseras till en Azure-värdbaserad Azure DevOps-pipeline.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Onboarding The Microsoft Security code Analysis extension

- Om du har något av följande Support erbjudanden kontaktar du din tekniska konto ansvarig för att köpa eller byta ut befintliga timmar för att få åtkomst till tillägget:
   - Enhetlig support avancerad nivå
   - Enhetlig support prestanda nivå
   - Premier Support för utvecklare
   - Premier Support för partner
   - Premier Support för företag
- Om du har någon av följande Support tjänster eller saknar Microsoft support avtal, måste du uppgradera till ett berättigat support erbjudande:
   - Azure-support för partners
   - Azure Basic-support
   - Azure-Developer Support
   - Azure-Standard Support
   - Azure-Professional Direct
   - Enhetlig support kärn nivå
- Om du vill köpa ett berättigat support erbjudande går du till [Start sidan för Support tjänster](https://www.microsoft.com/enterprise/services/support).
- När ett Support kontrakt är på plats kontaktar du din teknik konto ansvarig för att hjälpa dig att komma igång och hjälpa dig att samla in all nödvändig information.

>[!NOTE]
> Om du är en partner som är registrerad i Microsoft Partner Network, är du berättigad att köpa Premier Support för partner. Annars måste du köpa ett av de stödberättigade Support erbjudanden som nämns ovan.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Installera tillägget Microsoft Security code Analysis

1. När tillägget har delats med din Azure DevOps-organisation går du till din Azure DevOps-organisations sida. En exempel-URL för en sådan sida http://dev.azure.com/contoso är.
1. Välj shopping bag-ikonen i det övre högra hörnet bredvid ditt namn och välj sedan **Hantera tillägg**.
1. Välj tillägget Microsoft säkerhets kod analys och öppna sedan guiden Azure DevOps UI för att starta installationen.
1. I list rutan väljer du Azure DevOps-organisationen för att installera tillägget på.
1. Välj **Installera**. När installationen är klar kan du börja använda tillägget.

>[!NOTE]
> Även om du inte har åtkomst för att installera tillägget fortsätter du med installations stegen. Du kan begära åtkomst från din Azure DevOps-organisations administratör under installations processen.

När du har installerat tillägget visas de säkra utvecklings uppgifterna och de är tillgängliga för att läggas till i dina Azure-pipeliner.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Lägga till specifika bygg uppgifter till din Azure DevOps-pipeline

1. Öppna ditt team projekt från din Azure DevOps-organisation.
1. Välj **pipeline** > -**versioner**.
1. Välj den pipeline som du vill lägga till tillägg för build-uppgifter för:
   - Ny pipeline: Välj **ny** och följ stegen som beskrivs för att skapa en ny pipeline.
   - Redigera pipeline: Välj en befintlig pipeline och välj sedan **Redigera** för att börja redigera pipelinen.
1. Välj **+** och gå till fönstret **Lägg till aktiviteter** .
1. Leta upp den build-uppgift som du vill lägga till från antingen listan eller med hjälp av sökrutan. Välj **Lägg till**.
1. Ange de parametrar som krävs för uppgiften.
1. Köa en ny version.
   >[!NOTE]
   >Fil-och mappsökvägar är relativa i förhållande till käll lagrings platsens rot. Om du anger utdatafiler och mappar som parametrar ersätts de med den gemensamma plats som vi har definierat på Build-agenten.

> [!TIP]
>
> - Om du vill köra en analys efter din version kan du placera Microsoft Security code Analysis build-uppgifter efter steget publicera build-artefakter i din version. På så sätt kan din build slutföra och publicera resultat innan du kör statiska analys verktyg.
> - Välj alltid **Fortsätt vid fel** för bygg uppgifter med säker utveckling. Även om ett verktyg Miss lyckas kan de andra köra. Det finns inga beroenden mellan verktyg.
> - Microsoft Security code Analysis build-uppgifter Miss lyckas bara om ett verktyg inte kan köras utan problem. Men de lyckas även om ett verktyg identifierar problem i koden. Genom att använda åtgärden för att bygga efter analyser kan du konfigurera din version så att den inte fungerar när ett verktyg identifierar problem i koden.
> - Vissa Azure DevOps build-uppgifter stöds inte när de körs via en versions pipeline. Mer specifikt stöder Azure DevOps inte uppgifter som publicerar artefakter inifrån en versions pipeline.
> - En lista över fördefinierade variabler i Azure DevOps team build som du kan ange som parametrar finns i [Azure DevOps build-variabler](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar build-aktiviteter finns i vår [konfigurations guide](security-code-analysis-customize.md).

Om du har fler frågor om tillägget och de verktyg som erbjuds kan du kolla in vår [vanliga frågor och svar](security-code-analysis-faq.md).
