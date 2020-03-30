---
title: Läs mer om supportalternativ för Azure Service Fabric
description: Azure Service Fabric klusterversioner stöds och länkar till filsupportbiljetter
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 7494f0072f27f2c9b00db7070f19dfc05627eacf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282099"
---
# <a name="azure-service-fabric-support-options"></a>Supportalternativ för Azure Service Fabric

För att ge lämpligt stöd för dina Service Fabric-kluster som du kör dina programarbetsbelastningar på har vi ställt in olika alternativ för dig. Beroende på vilken supportnivå som behövs och hur allvarlig problemet är får du välja rätt alternativ. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Rapportera produktionsproblem eller begära betald support för Azure

Om du vill rapportera problem i ditt Service Fabric-kluster som distribueras på Azure öppnar du en biljett för support [på Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) eller Microsoft [supportportal](https://support.microsoft.com/oas/default.aspx?prid=16146).

Läs mer om:
 
- [Support från Microsoft för Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft premier-support](https://support.microsoft.com/en-us/premier).

> [!Note]
> Kluster som körs på en bronstillförlitlighetsnivå eller Single Node Cluster gör att du bara kan köra testarbetsbelastningar. Om du får problem med ett kluster som körs på bronstillförlitlighet eller Single Node Cluster hjälper Microsoft-supportteamet dig att minska problemet, men utför ingen grundorsaksanalys. Mer information [finns i klustrets tillförlitlighetsegenskaper.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)
>
> Mer information om vad som krävs för ett produktionsfärdigt kluster finns i [checklistan för produktionsberedskap](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Rapportera produktionsproblem eller begära betalt stöd för fristående Service Fabric-kluster

Om du vill rapportera problem i Service Fabric-klustret som distribueras lokalt eller på andra moln öppnar du en biljett för professionell support på [Microsofts supportportal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Läs mer om:

- [Professionell support från Microsoft för lokalt](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft premier-support](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Rapportera problem med Azure-tjänstinfrastruktur

Vi har skapat en GitHub-repo för rapportering av service fabric-problem.  Vi följer också aktivt följande forum.

### <a name="github-repo"></a>GitHub repo 

Rapportera Problem med Azure-tjänstinfrastruktur på [git repo för Service-Fabric-issues](https://github.com/Azure/service-fabric-issues). Den här repoen är avsedd för rapportering och spårning av problem med Azure Service Fabric och för att göra små funktionsbegäranden. **Använd inte detta för att rapportera problem med live-webbplatsen**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow och MSDN-forum

[Service Fabric-taggen på StackOverflow][stackoverflow] och [Service Fabric-forumet på MSDN][msdn-forum] används bäst för att ställa frågor om hur plattformen fungerar och hur du kan utföra vissa uppgifter med den.

### <a name="azure-feedback-forum"></a>Forum för Azure Feedback

[Azure Feedback Forum for Service Fabric][uservoice-forum] är den bästa platsen för att skicka in stora funktionsidéer du har för produkten när vi granskar de mest populära förfrågningarna är en del av vår planering på medellång till lång sikt. Vi uppmuntrar dig att samla stöd för dina förslag inom samhället.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Förhandsversioner av Service Fabric - stöds inte för produktionsbruk

Från tid till annan, vi släpper versioner som har betydande funktioner som vi vill ha feedback på, som släpps som förhandsvisningar. Dessa förhandsgranskningsversioner bör endast användas för teständamål. Ditt produktionskluster ska alltid köra en service fabric-version som stöds, stabil. En förhandsgranskningsversion börjar alltid med ett större och mindre versionsnummer på 255. Om du till exempel ser en Service Fabric version 255.255.5703.949, ska den versionen endast användas i testkluster och förhandsgranskas. Dessa förhandsversioner meddelas också på [Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric) och kommer att ha information om de funktioner som ingår.
Det finns inget betalt supportalternativ för dessa förhandsversioner. Använd något av alternativen under [Report Azure Service Fabric-problem](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) för att ställa frågor eller ge feedback.

## <a name="next-steps"></a>Nästa steg

[Service Fabric-versioner som stöds](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
