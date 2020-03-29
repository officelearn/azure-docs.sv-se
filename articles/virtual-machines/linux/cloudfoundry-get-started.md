---
title: Komma igång med molngjuteri på Microsoft Azure
description: Kör OSS- eller Pivotal Cloud Foundry på Microsoft Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: ba0b4a636d8f785f1c169e4066c9cf6d25ba81db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970056"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry på Azure

Cloud Foundry är en plattform som en tjänst (PaaS) med öppen källkod som finns för att skapa, distribuera och använda 12-faktorsprogram som utvecklats i olika språk och ramverk. I det här dokumentet beskrivs de alternativ du har för att köra Cloud Foundry på Azure och hur du kan komma igång.

## <a name="cloud-foundry-offerings"></a>Molngjuteri erbjudanden

Det finns två former av cloud foundry som kan köras på Azure: molngjuteri (OSS CF) med öppen källkod (OSS CF) och Pivotal Cloud Foundry (PCF). OSS CF är en helt [öppen källkod](https://github.com/cloudfoundry) version av Cloud Foundry förvaltas av Cloud Foundry Foundation. Pivotal Cloud Foundry är en företagsdistribution av Cloud Foundry från Pivotal Software Inc. Vi tittar på några av skillnaderna mellan de två erbjudandena.

### <a name="open-source-cloud-foundry"></a>Molngjuteri med öppen källkod

Du kan distribuera OSS Cloud Foundry på Azure genom att först distribuera en BOSH-chef och sedan distribuera Cloud Foundry, med hjälp av [instruktionerna på GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Mer information om hur du använder OSS CF finns i [dokumentationen](https://docs.cloudfoundry.org/) från Cloud Foundry Foundation.

Microsoft ger stöd för OSS CF med bäst arbete via följande communitykanaler:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>bosh-azure-cpi kanal på [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [cf-bosh e-postlista](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub-problem för [KPI-](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) och [servicemäklaren](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Supportnivån för dina Azure-resurser, till exempel de virtuella datorer där du kör Cloud Foundry, baseras på ditt Azure-supportavtal. Community-support för bästa arbete gäller endast för molngjuterispecifika komponenter.

### <a name="pivotal-cloud-foundry"></a>Pivotal molngjuteri

Pivotal Cloud Foundry innehåller samma kärnplattform som OSS-distributionen, tillsammans med en uppsättning egna hanteringsverktyg och företagssupport. Om du vill köra PCF på Azure måste du skaffa en licens från Pivotal. PCF-erbjudandet från Azure-marknadsplatsen innehåller en 90-dagars utvärderingslicens.

Verktygen inkluderar [Pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), ett webbprogram som förenklar distribution och hantering av en Cloud Foundry foundation och Pivotal Apps [Manager](https://docs.pivotal.io/pivotalcf/console/), ett webbprogram för hantering av användare och program.

Förutom de supportkanaler som anges för OSS CF ovan ger en PCF-licens dig rätt att kontakta Pivotal för support. Microsoft och Pivotal har också aktiverat supportarbetsflöden som gör att du kan kontakta endera parten för att få hjälp och få din förfrågan dirigerad på rätt sätt beroende på var problemet ligger.

## <a name="azure-service-broker"></a>Azure-tjänstmäklare

Cloud Foundry uppmuntrar ["tolvfaktorapp"-metoden,](https://12factor.net/) som främjar en ren separation av tillståndslösa programprocesser och tillståndskänsliga stödtjänster. [Servicemäklare](https://docs.cloudfoundry.org/services/api.html) erbjuder ett konsekvent sätt att etablera och binda stödtjänster till program. [Azure-tjänstmäklaren](https://github.com/Azure/meta-azure-service-broker) tillhandahåller några av de viktigaste Azure-tjänsterna via den här kanalen, inklusive Azure-lagring och Azure SQL.

Om du använder Pivotal Cloud Foundry är servicemäklaren också [tillgänglig som en panel](https://docs.pivotal.io/azure-sb/installing.html) från Pivotal Network.

## <a name="related-resources"></a>Relaterade resurser

### <a name="azure-devops-services-plugin"></a>Plugin-program för Azure DevOps Services

Cloud Foundry är väl lämpad för agil mjukvaruutveckling, inklusive användning av kontinuerlig integration (CI) och kontinuerlig leverans (CD). Om du använder Azure DevOps Services för att hantera dina projekt och vill konfigurera en CI/CD-pipeline inriktning Cloud Foundry, kan du använda [Azure DevOps Services Cloud Foundry build-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Insticksprogrammet gör det enkelt att konfigurera och automatisera distributioner till Cloud Foundry, oavsett om den körs i Azure eller en annan miljö.

## <a name="next-steps"></a>Nästa steg

- [Distribuera pivotala molngjuteri från Azure Marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Distribuera en app till molngjuteri i Azure](./cloudfoundry-deploy-your-first-app.md)
