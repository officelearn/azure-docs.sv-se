---
title: Komma igång med Cloud Foundry på Microsoft Azure | Microsoft Docs
description: Kör OSS eller spela en central molnet Foundry på Microsoft Azure
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
ms.openlocfilehash: 42910675bcf512a3d6c76369adc9f41215420a78
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry på Azure

Molnet Foundry är en öppen källkod plattform som en-tjänst (PaaS) för att skapa, distribuera och använda 12-factor-program som utvecklats i olika språk och ramverk. Det här dokumentet beskrivs de alternativ som du har för att köra molnet Foundry på Azure och hur du kan komma igång.

## <a name="cloud-foundry-offerings"></a>Foundry molntjänster

Det finns två typer av molnet Foundry tillgänglig för körning på Azure: öppen källkod molnet Foundry (OSS CF) och spela en central molnet Foundry (PCF). OSS CF är en helt [öppen källkod](https://github.com/cloudfoundry) version av Cloud Foundry hanteras av molnet Foundry Foundation. Spela en central molnet Foundry är en enterprise-distribution för molnet från spela en central Software Inc. Vi titta på några av skillnaderna mellan de två erbjudandena.

### <a name="open-source-cloud-foundry"></a>Öppen källkod molnet Foundry

Du kan distribuera OSS molnet Foundry i Azure genom att först distribuera en BOSH director och distribuera moln Foundry med hjälp av den [instruktionerna på GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Mer information om hur du använder OSS CF finns i [dokumentationen](https://docs.cloudfoundry.org/) från molnet Foundry Foundation.

Microsoft ger bästa stöd för OSS CF via följande community kanaler:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>bosh-azure-cpi kanal på [moln Foundry Slack](https://slack.cloudfoundry.org/)
- [CF bosh postlistan](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub problem för den [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) och [service broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Stödet för Azure-resurser, till exempel de virtuella datorerna där du kör molnet Foundry baseras på ditt Azure-supportavtal. Bästa community-support gäller bara för molnet Foundry-specifika komponenter.

### <a name="pivotal-cloud-foundry"></a>Spela en central molnet Foundry

Spela en central molnet Foundry innehåller samma core plattform som OSS distribution, tillsammans med en uppsättning egna hanteringsverktyg och enterprise-stöd. Om du vill köra PCF på Azure, måste du skaffa en licens från Pivotal. PCF erbjudandet från Azure marketplace innehåller en 90-dagars testversion.

Verktygen innehåller [spela en central Operations Manager](http://docs.pivotal.io/pivotalcf/customizing/), ett webbprogram som förenklar distribution och hantering av ett moln Foundry foundation och [spela en central appar Manager](https://docs.pivotal.io/pivotalcf/console/), ett webbprogram för att hantera användare och program.

Förutom supportkanaler som anges för OSS CF ovan, kan en PCF-licens du kontakta Pivotal för support. Microsoft och Pivotal har också aktiverat stöd för arbetsflöden som gör att du kan kontakta någon av parterna för hjälp och låta din förfrågan dirigeras korrekt beroende på var problemet ligger.

## <a name="azure-service-broker"></a>Azure Service Broker

Molnet Foundry uppmanar den [”tolv faktor app”](https://12factor.net/) metod som främjar en ren avgränsning av tillståndslösa programprocesser och stateful säkerhetskopiering tjänster. [Tjänsten mäklare](https://docs.cloudfoundry.org/services/api.html) erbjuder ett konsekvent sätt att etablera och binda stödjande tjänster till program. Den [Azure service broker](https://github.com/Azure/meta-azure-service-broker) innehåller några viktiga Azure-tjänster via den här kanalen, inklusive Azure storage och SQL Azure.

Om du använder spela en central molnet Foundry service broker är också [tillgänglig som en panel](https://docs.pivotal.io/azure-sb/installing.html) från spela en central nätverket.

## <a name="related-resources"></a>Relaterade resurser

### <a name="visual-studio-team-services-plugin"></a>Visual Studio Team Services plugin-program

Molnet Foundry passar bra om flexibel programvaruutveckling, inklusive användning av kontinuerlig integration (KO) och kontinuerlig leverans (CD). Om du använder Visual Studio Team Services VSTS () för att hantera dina projekt och vill ange pipeline in CI/CD mål moln Foundry, kan du använda den [VSTS molnet Foundry build-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Plugin-programmet gör det enkelt att konfigurera och automatisera distributioner till molnet Foundry om körs i Azure eller en annan miljö.

## <a name="next-steps"></a>Nästa steg

- [Distribuera spela en central molnet Foundry från Azure Marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Distribuera en app till molnet Foundry i Azure](./cloudfoundry-deploy-your-first-app.md)
