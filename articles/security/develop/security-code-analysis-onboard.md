---
title: Onboarding-guide för Microsoft Azure säkerhets kod analys
description: Den här artikeln är att installera tillägg för säkerhets kod analys
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
ms.openlocfilehash: 8a8eca73205d4f8f33d4d069fda72638af61d355
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718306"
---
# <a name="how-to-onboarding-and-installing"></a>Instruktioner: Onboarding och installation

För hands krav för att komma igång med Microsofts säkerhets kod analys
  - Kvalificerat Microsoft Unified Support Services-erbjudande (information nedan)
  - En Azure DevOps-organisation
  - Behörigheter för att installera tillägg i Azure DevOps-organisationen
  - Källkod som kan synkroniseras till en Azure-värdbaserad Azure DevOps-pipeline


## <a name="onboarding-microsoft-security-code-analysis-extension"></a>Onboarding-tillägg för Microsoft Security code Analysis

- Om du redan har något av följande Support erbjudanden kontaktar du bara din tekniska konto ansvarig och purchase\swap befintliga timmar för att få åtkomst till tillägget.
   - Enhetlig support – avancerad och prestanda nivå, Premier Support för utvecklare, Premier Support för partner eller Premier Support för företag.
- Om du har någon av följande Support tjänster eller inte har någon Support plan hos Microsoft, måste du uppgradera till ett berättigat support erbjudande:
   - Azure-Support för partners, Azure Basic, Azure Developer, Azure standard, Azure Professional Direct eller Unified support – core-nivå
- Om du vill köpa ett berättigat support erbjudande går du till [Start sidan för Support tjänster](https://www.microsoft.com/enterprise/services/support)
- När ett Support kontrakt är på plats kan du kontakta din tekniska konto ansvarig som kan hjälpa dig att komma igång och samla in all nödvändig information.
 
>[!NOTE]
> Endast registrerade partner i Microsoft Partner Network är berättigade till köp Premier Support för partner, annars köper ett av de berättigade Support erbjudanden som nämnts tidigare

## <a name="installing-microsoft-security-code-analysis-extension"></a>Installera Microsoft Security code Analysis-tillägg

1. När tillägget har delats med din Azure DevOps-organisation går du till din Azure DevOps-organisations sida (till exempel http://dev.azure.com/contoso)
2. Klicka på shopping bag-ikonen i det övre högra hörnet bredvid ditt namn och klicka sedan på Hantera tillägg 
3. Klicka på tillägget Microsoft säkerhets kod analys och starta installationen genom att starta guiden Azure DevOps UI.
4. Välj Azure DevOps-organisation för att installera tillägget på från List rutan
5. Klicka på installera. När den är klar kan du fortsätta att använda tillägget

>[!NOTE]
> Även om du inte har åtkomst kan du fortsätta med installations stegen och du kan begära åtkomst från din Azure DevOps-organisations administratör under processen.
>
När tillägget har installerats visas Bygg aktiviteterna för säker utveckling som är tillgängliga för att läggas till i dina Azure-pipeliner.

## <a name="adding-specific-build-tasks-to-your-devops-pipeline"></a>Lägga till specifika bygg uppgifter till din DevOps-pipeline

1. Öppna ditt grup projekt från din Azure DevOps-organisation.
2. Gå till fliken **versioner** under **pipelines** 
3. Välj den pipeline som du vill lägga till tilläggets Bygg aktiviteter i. 
   - Nytt – Klicka på **nytt** och följ stegen som beskrivs för att skapa en ny pipeline.
   - Redigera – Välj pipelinen och klicka på **Redigera** för att börja redigera en befintlig pipeline.
4. Klicka på + för att navigera till fönstret **Lägg till aktiviteter** .
5. Hitta den build-uppgift som du vill lägga till antingen från listan eller med hjälp av sökrutan och klicka sedan på **Lägg till**. 
6. Nu kan du fortsätta att ange de parametrar som behövs för aktiviteten.
>[!NOTE]
>Fil-eller katalog Sök vägar är relativa till roten för din käll lagring och parametrar som anger att mappen utdata/Files ersätts med den gemensamma plats som vi har definierat på Build-agenten.

7. Köa en ny version.
> [!TIP]
>  - Om du vill köra analysen efter din version kan du placera Microsoft Security code Analysis build-uppgifter efter steget publicera build-artefakter i din version. På så sätt kan din build slutföra och publicera resultat innan du kör statiska analys verktyg.
>  - Kontrol lera alltid alternativet **"Fortsätt vid fel"** för säker utveckling av bygg uppgifter. Även om ett av verktygen Miss lyckas kan de andra köra. Det finns inga beroenden.
>  - Build-aktiviteter för Microsoft Security code Analysis fungerar bara om verktyget inte kan köras utan problem, men de Miss lyckas inte om och när verktyget identifierar problem i koden. Du kan konfigurera din version så att den slutar när ett verktyg identifierar problem i koden med hjälp av aktiviteten **efter analys** .
>  - Vissa Azure DevOps build-uppgifter stöds inte vid körning via en "Release"-pipeline. Detta är en begränsning i Azure-DevOps. De stöder inte uppgifter som publicerar artefakter inifrån en versions pipeline.
>  - En lista över fördefinierade variabler i Azure DevOps team build, som du kan ange som parametrar, finns i [Azure DevOps build-variabler](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar build-aktiviteter finns i vår [konfigurations guide](security-code-analysis-customize.md)

Om du har fler frågor om tillägget och de verktyg som erbjuds [kontrollerar du sidan med vanliga frågor och svar.](security-code-analysis-faq.md)


