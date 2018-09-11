---
title: Komma igång med Cloud Foundry på Microsoft Azure | Microsoft Docs
description: Köra OSS eller Pivotal Cloud Foundry på Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 18aa5ab16a1690fdcead46ebc15d72837f62612d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302477"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry på Azure

Cloud Foundry är en plattform som en tjänst (PaaS) med öppen källkod som finns för att skapa, distribuera och använda 12-faktorsprogram som utvecklats i olika språk och ramverk. Det här dokumentet beskrivs de alternativ som du har för att köra Cloud Foundry på Azure och hur du kan komma igång.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry-erbjudanden

Det finns två typer av Cloud Foundry tillgänglig för körning på Azure: open source-Cloud Foundry (OSS CF) och Pivotal Cloud Foundry (PCF). OSS CF är en helt [öppen källkod](https://github.com/cloudfoundry) version av Cloud Foundry hanteras av Cloud Foundry Foundation. Pivotal Cloud Foundry är en företagsdistribution för Cloud Foundry från Pivotal Software Inc. Vi tittar på några av skillnaderna mellan de två erbjudandena.

### <a name="open-source-cloud-foundry"></a>Öppen källkod Cloud Foundry

Du kan distribuera OSS Cloud Foundry på Azure genom att distribuera en BOSH director först och sedan distribuera Cloud Foundry, med hjälp av den [instruktionerna på GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Mer information om hur du använder OSS CF finns den [dokumentation](https://docs.cloudfoundry.org/) tillhandahålls av Cloud Foundry Foundation.

Microsoft tillhandahåller mån support för OSS CF via följande community-kanaler:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>bosh-azure-cpi-kanal på [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [CF bosh distributionslista](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub-problem för den [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) och [service broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Stödet för dina Azure-resurser, till exempel virtuella datorer där du kör Cloud Foundry är baserad på ditt Azure-support-avtal. Mån community-support gäller endast för Cloud Foundry-specifika komponenter.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Pivotal Cloud Foundry innehåller samma core plattform som OSS-distribution, tillsammans med en uppsättning upphovsrättsskyddad hanteringsverktyg och företagssupport. Om du vill köra PCF på Azure, måste du skaffa en licens från Pivotal. PCF-erbjudande från Azure marketplace innehåller en 90-dagars utvärderingslicens.

Verktygen innehåller [Pivotal Operations Manager](http://docs.pivotal.io/pivotalcf/customizing/), ett webbprogram som förenklar distribution och hantering av en Cloud Foundry foundation och [Pivotal appar Manager](https://docs.pivotal.io/pivotalcf/console/), ett webbprogram för att hantera användare och program.

Förutom supportkanaler som visas för OSS CF ovan, kan en PCF-licens du kontakta Pivotal för support. Microsoft och Pivotal har också aktiverat stöd arbetsflöden så att du kan kontakta part för att få hjälp och har din förfrågan vidarebefordras på rätt sätt beroende på var problemet ligger.

## <a name="azure-service-broker"></a>Azure Service Broker

Cloud Foundry uppmuntrar den [”tolv-factor-app”](https://12factor.net/) metodik, som främjar en ren inkapsling av tillståndslösa program bearbetar och tillståndskänslig säkerhetskopiering tjänster. [Service Broker](https://docs.cloudfoundry.org/services/api.html) erbjuder ett konsekvent sätt att etablera och binda stödtjänster till program. Den [Azure service broker](https://github.com/Azure/meta-azure-service-broker) innehåller några av de viktiga Azure-tjänsterna via den här kanalen, inklusive Azure storage och Azure SQL.

Om du använder Pivotal Cloud Foundry, service broker är också [tillgängliga som en panel](https://docs.pivotal.io/azure-sb/installing.html) från Pivotal nätverket.

## <a name="related-resources"></a>Relaterade resurser

### <a name="azure-devops-services-plugin"></a>Plugin-programmet med Azure DevOps-tjänsterna

Cloud Foundry är utmärkt för snabb programutveckling, inklusive användning av kontinuerlig integrering (CI) och kontinuerlig leverans (CD). Om du använder Azure DevOps-tjänster för att hantera dina projekt och vill använda som in en CI/CD i pipeline riktar in sig på Cloud Foundry, kan du använda den [Azure DevOps tjänster Cloud Foundry skapa tillägg för](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Plugin-programmet gör det enkelt att konfigurera och automatisera distributioner till Cloud Foundry, oavsett om körs i Azure eller någon annan miljö.

## <a name="next-steps"></a>Nästa steg

- [Distribuera Pivotal Cloud Foundry på Azure Marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Distribuera en app till Cloud Foundry på Azure](./cloudfoundry-deploy-your-first-app.md)
