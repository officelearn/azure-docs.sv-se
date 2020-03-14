---
title: Läs mer om support alternativ för Azure Service Fabric
description: Azure Service Fabric-kluster versioner stöds och länkar till fil support biljetter
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 7494f0072f27f2c9b00db7070f19dfc05627eacf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282099"
---
# <a name="azure-service-fabric-support-options"></a>Support alternativ för Azure Service Fabric

För att leverera lämpligt stöd för dina Service Fabric-kluster som du kör program arbets belastningen på, har vi ställt in olika alternativ åt dig. Beroende på den support nivå som krävs och problemets allvarlighets grad får du välja rätt alternativ. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Rapportera produktions problem eller begär betald support för Azure

Om du vill rapportera problem med ditt Service Fabric-kluster som har distribuerats på Azure öppnar du en biljett för support [på Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) eller [Microsoft Support Portal](https://support.microsoft.com/oas/default.aspx?prid=16146).

Läs mer om:
 
- [Support från Microsoft för Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft Premier-Support](https://support.microsoft.com/en-us/premier).

> [!Note]
> Kluster som körs på en brons Tillförlitlighets nivå eller ett kluster med en nod kan bara köra test arbets belastningar. Om du får problem med ett kluster som körs på brons tillförlitlighet eller ett kluster med en enda nod, kommer Microsoft support team att hjälpa dig att lösa problemet, men det kommer inte att utföra en rotor Saks analys. Mer information finns i [klustrets Tillförlitlighets egenskaper](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) .
>
> Mer information om vad som krävs för ett produktions klart kluster finns i [Check listan för produktions beredskap](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Rapportera produktions problem eller begär betald support för fristående Service Fabric kluster

För rapporterings problem på ditt Service Fabric-kluster som har distribuerats lokalt eller i andra moln öppnar du en biljett för professionell support på [Microsofts support Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Läs mer om:

- [Professional support från Microsoft för lokal](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft Premier-Support](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Rapportera problem med Azure-Service Fabric

Vi har konfigurerat en GitHub-lagrings platsen för rapportering Service Fabric problem.  Vi arbetar också aktivt med att övervaka följande forum.

### <a name="github-repo"></a>GitHub lagrings platsen 

Rapportera Azure Service Fabric problem på [Service-Fabric-problem git-lagrings platsen](https://github.com/Azure/service-fabric-issues). Den här lagrings platsen är avsedd för rapportering och spårning av problem med Azure Service Fabric och för att göra små funktions förfrågningar. **Använd inte detta för att rapportera problem med Live-webbplatser**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow-och MSDN-forum

[Taggen Service Fabric på StackOverflow][stackoverflow] och [Service Fabric forum på MSDN][msdn-forum] används bäst för att ställa frågor om hur plattformen fungerar och hur du kan utföra vissa uppgifter med den.

### <a name="azure-feedback-forum"></a>Azure feedback-forum

[Azure feedback-forumet för Service Fabric][uservoice-forum] är den bästa platsen för att skicka stora funktions idéer för produkten när vi går igenom de populäraste förfrågningarna är en del av vårt medel stora och långsiktiga planer. Vi rekommenderar att du Rally support för dina förslag i communityn.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric för hands versioner – stöds inte för produktions användning

Från och med tiden kommer vi att lansera versioner som har viktiga funktioner som vi vill ha feedback om, vilket publiceras som för hands versioner. Dessa för hands versioner bör endast användas i test syfte. Ditt produktions kluster bör alltid köra en stabil, stabil Service Fabric version som stöds. En för hands version börjar alltid med ett större och lägre versions nummer på 255. Om du till exempel ser en Service Fabric version 255.255.5703.949 är den versionen bara att användas i test kluster och är en för hands version. De här för hands versionerna visas också i [Service Fabric teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric) och innehåller information om de funktioner som ingår.
Det finns inget betalt support alternativ för dessa för hands versioner. Använd något av alternativen som anges under [rapportera Azure-Service Fabric problem](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) för att ställa frågor eller ge feedback.

## <a name="next-steps"></a>Nästa steg

[Service Fabric versioner som stöds](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
