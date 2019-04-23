---
title: Mer information om supportalternativ för Azure Service Fabric | Microsoft Docs
description: Azure Service Fabric-kluster-versioner som stöds och länkar till filen supportärenden
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: a931de8be07d41cf4daab63aa7691973ee158452
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005051"
---
# <a name="azure-service-fabric-support-options"></a>Supportalternativ för Azure Service Fabric

För att ge stöd för dina Service Fabric-kluster som du kör dina arbetsbelastningar för program på har vi konfigurerat olika alternativ för dig. Beroende på supportnivå som behövs och problemets allvarlighetsgrad, får du att välja rätt alternativ. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Rapportera produktionsproblem eller begära betald support för Azure

Öppna en biljett för stöd för rapportering problem på ditt Service Fabric-kluster som distribueras på Azure, [på Azure-portalen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) eller [Microsoft support portal](https://support.microsoft.com/oas/default.aspx?prid=16146).

Läs mer om:
 
- [Support från Microsoft för Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft premier support](https://support.microsoft.com/en-us/premier).

> [!Note]
> Kluster som körs på en Brons tillförlitlighetsnivån kan du köra test-arbetsbelastningar. Om det uppstår problem med ett kluster som körs på Brons tillförlitlighet Microsoft support-teamet hjälper dig att åtgärda problemet, men kommer inte att utföra en Rotorsaksanalys. Se [tillförlitlighetsegenskaper för klustret](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) för mer information.
>
> Mer information om vad som krävs för ett produktionsklart kluster finns i den [produktion beredskap checklista](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Rapportera produktionsproblem eller begära betald support för fristående Service Fabric-kluster

För att rapportera problem med Service Fabric-klustret distribuerat lokalt eller på andra moln, öppna en biljett för professionell support på [Microsoft support portal](https://support.microsoft.com/oas/default.aspx?prid=16146).

Läs mer om:

- [Professionell Support från Microsoft för den lokala](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft premier support](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Rapporten Azure Service Fabric-problem

Vi har ställt in en GitHub-lagringsplatsen för reporting Service Fabric-problem.  Vi övervakar också aktivt följande forum.

### <a name="github-repo"></a>GitHub-lagringsplats 

Rapportera problem som Azure Service Fabric på [Service-Fabric-problem med git-lagringsplats](https://github.com/Azure/service-fabric-issues). Den här lagringsplatsen är avsedd för rapportering och spåra problem med Azure Service Fabric och för att göra små funktionsförfrågningar. **Använd detta inte att rapportera live-webbplatser problem**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow och MSDN-forum

Den [Service Fabric-tagg på StackOverflow] [ stackoverflow] och [Service Fabric-forum på MSDN] [ msdn-forum] är bästa används för att ställa frågor om hur plattformen fungerar och hur du kan utföra vissa aktiviteter med den.

### <a name="azure-feedback-forum"></a>Azure-Feedbackforum

Den [Azure Feedback-Forum för Service Fabric] [ uservoice-forum] är den bästa platsen för att skicka stora funktionsidéer som du har för produkten när vi går igenom de mest populära förfrågningarna är en del av vår medelstora och långsiktig planering. Vi rekommenderar att du rally stöd för dina förslag inom gruppen.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric Preview-versioner – stöds inte för användning i produktion

Vi släpper från tid till annan-versioner som har viktiga funktioner som vi vill ha feedback, som blir tillgängliga som förhandsversioner. Dessa förhandsversioner bör endast användas för testning. Din produktionskluster bör alltid köra en stöds, stabil, Service Fabric-version. En förhandsversion börjar alltid med ett högre och den lägre versionsnummer 255. Till exempel om du ser ett Service Fabric version 255.255.5703.949 den utgivna versionen ska bara användas i testkluster och är en förhandsversion. Dessa förhandsversioner tillkännages också på den [Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric) och kommer att innehålla information om de funktioner som ingår.
Det finns inget alternativ för betald support för dessa förhandsversioner. Använd någon av de alternativ som visas [rapporten Azure Service Fabric utfärdar](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) ställa frågor eller lämna kommentarer.

## <a name="next-steps"></a>Nästa steg

[Service Fabric-versioner som stöds](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples