---
title: Lär dig mer om Azure Service Fabric-supportalternativ | Microsoft Docs
description: Azure Service Fabric-kluster-versioner som stöds och länkar till filen supportärenden
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/14/2018
ms.author: pkc
ms.openlocfilehash: 503bc07ff904dcb5952a13f12296f14fdd1999c0
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric-supportalternativ

Vi har ställt in olika alternativ för att ge stöd för ditt Service Fabric-kluster som du kör ditt program arbetsbelastningar på. Beroende på nivån av stöd som behövs och problemets allvarlighetsgrad, får du välja rätt alternativ. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Rapportera problem som produktion eller be betald stöd för Azure

Skapa ett ärende för support för att rapportera problem på ditt Service Fabric-kluster som distribueras på Azure [på Azure-portalen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) eller [Microsoft support-portalen](http://support.microsoft.com/oas/default.aspx?prid=16146).

Läs mer om:
 
- [Support från Microsoft för Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft premier support](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Rapportera problem som produktion eller be betald stöd för fristående Service Fabric-kluster

För att rapportera problem på Service Fabric-klustret distribueras lokalt eller i andra moln, skapa ett ärende för professionell support på [Microsoft support-portalen](http://support.microsoft.com/oas/default.aspx?prid=16146).

Läs mer om:

- [Professional Support från Microsoft för lokalt](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft premier support](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Rapporten Azure Service Fabric-problem 
Vi har skapat en GitHub-lagringsplatsen för att rapportera problem med Service Fabric.  Vi övervakar också aktivt följande forum.

### <a name="github-repo"></a>GitHub-repo 
Rapportera problem med Azure Service Fabric [problem för Service Fabric git repo](https://github.com/Azure/service-fabric-issues). Den här lagringsplatsen är avsedd för rapportering och uppföljning av problem med Azure Service Fabric och för att göra små funktionsförfrågningar. **Använd detta inte att rapporten live-plats problem**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow och MSDN-forum
Den [Service Fabric-tagg på StackOverflow] [ stackoverflow] och [Service Fabric-forum på MSDN] [ msdn-forum] är bästa används för att ställa frågor om hur plattform fungerar och hur du kan utföra vissa uppgifter med den.

### <a name="azure-feedback-forum"></a>Azure Feedback-forum
Den [Azure Feedback Forum för Service Fabric] [ uservoice-forum] är den bästa platsen för att skicka stora funktionen idéer som du har för produkten som vi går igenom de mest populära begärandena är en del av vår medelstora och långsiktig planering. Vi rekommenderar att du rally stöd för dina förslag inom gruppen.


## <a name="supported-service-fabric-versions"></a>Service Fabric-versioner som stöds.

Kontrollera att klustret är alltid en Service Fabric-version som stöds. Och när vi meddela lanseringen av en ny version av Service Fabric, markeras den tidigare versionen för slutet av stödet efter 60 dagar från det datum då minst. De nya versionerna meddelas [på Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/).

Referera till följande dokument på information om hur du kan skydda ditt kluster som kör en Service Fabric-version som stöds.

- [Uppgradera Service Fabric-versionen på ett Azure-kluster ](service-fabric-cluster-upgrade.md)
- [Uppgradera Service Fabric-versionen på en fristående windows server-kluster ](service-fabric-cluster-upgrade-windows-server.md)
 
Här är listan över Service Fabric-versioner som stöds och deras stöd slutdatum.

| **Service Fabric runtime i klustret** | **Kan uppgradera direkt från klustret version** |**Kompatibel SDK / NuGet-paketet versioner** | **Slutet av Support datum** |
| --- | --- |--- | --- |
| Kluster-versioner före 5.3.121 | 5.1.158* |Mindre än eller lika med version 2.3 |20 januari 2017 |
| 5.3.* | 5.1.158.* |Mindre än eller lika med version 2.3 |24 februari 2017 |
| 5.4.* | 5.1.158.* |Mindre än eller lika med version 2.4 |Kan 10,2017       |
| 5.5.* | 5.4.164.* |Mindre än eller lika med version 2.5 |Augusti 10,2017    |
| 5.6.* | 5.4.164.* |Mindre än eller lika med version 2.6 |Oktober 13,2017   |
| 5.7.* | 5.4.164.* |Mindre än eller lika med version 2.7 |December 15,2017  |
| 6.0.* | 5.6.205.* |Mindre än eller lika med version 2.8 |Mars 30,2018     | 
| 6.1. * | 5.7.221.* |Mindre än eller lika med version 3.0 |Juli 15,2018      |
| 6.2. * | 6.0.232.* |Mindre än eller lika med version 3.1 |Aktuell version och därför inget slutdatum |

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric förhandsversioner - stöds inte för produktion.
Då, släpp vi-versioner som har viktiga funktioner som vi vill feedback, som släpps som förhandsgranskningar. Dessa förhandsversioner bör endast användas för testning. Klustret produktion bör alltid köra en stöds, stabil, Service Fabric-version. En förhandsversion börjar alltid med ett högre och lägre versionsnummer 255. Till exempel om du ser ett Service Fabric version 255.255.5703.949 som versionen ska bara användas i testkluster och är en förhandsversion. Dessa förhandsvisningarna också tillkännages i den [Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric) och kommer att ha information på funktioner som ingår.

Det finns inget stöd för betald alternativ för dessa förhandsvisningarna. Använd någon av de alternativ som beskrivs [rapporten Azure Service Fabric utfärdar](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) att ställa frågor eller lämna feedback.

## <a name="next-steps"></a>Nästa steg

- [Uppgradera service fabric-versionen på ett Azure-kluster ](service-fabric-cluster-upgrade.md)
- [Uppgradera Service Fabric-versionen på en fristående windows server-kluster ](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples
