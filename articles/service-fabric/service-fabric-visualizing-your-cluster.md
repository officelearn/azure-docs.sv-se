---
title: Visualisera klustret med Azure Service Fabric Explorer | Microsoft Docs
description: Service Fabric Explorer är ett program för att granska och hantera molnprogram och noder i ett Microsoft Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: mikhegn
ms.openlocfilehash: 459dd86fd614cb185801b074cea70c36dc7f6ccb
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972340"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualisera ditt kluster med Service Fabric Explorer

Service Fabric Explorer (SFX) är ett verktyg med öppen källkod för att granska och hantera Azure Service Fabric-kluster. Service Fabric Explorer är ett skrivbordsprogram för Windows, macOS och Linux.

## <a name="service-fabric-explorer-download"></a>Ladda ned Service Fabric Explorer

Använd följande länkar för att ladda ned Service Fabric Explorer som ett skrivbordsprogram:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Skrivbordsversionen av Service Fabric Explorer kan ha fler eller färre funktioner än stöd för kluster. Du kan återgå till den Service Fabric Explorer-versionen som distribueras till klustret för att garantera kompatibilitet med fullständiga funktioner.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Köra Service Fabric Explorer från klustret

Service Fabric Explorer finns också i ett Service Fabric-kluster hanteringsslutpunkten för HTTP. Om du vill starta SFX i en webbläsare, bläddra till klustrets HTTP-hanteringsslutpunkten från valfri webbläsare – till exempel https://clusterFQDN:19080.

För konfiguration för utvecklare arbetsstation, kan du starta Service Fabric Explorer i ditt lokala kluster genom att gå till https://localhost:19080/Explorer. Titta på den här artikeln att [Förbered din utvecklingsmiljö](service-fabric-get-started.md).

## <a name="connect-to-a-service-fabric-cluster"></a>Ansluta till ett Service Fabric-kluster
Om du vill ansluta till ett Service Fabric-kluster, behöver du hanteringsslutpunkten för kluster (IP/FQDN) och HTTP-slutpunkt Hanteringsport (19080 som standard). Till exempel https://mysfcluster.westus.cloudapp.azure.com:19080. Använd kryssrutan ”Anslut till localhost” för att ansluta till ett lokalt kluster på din arbetsstation.

### <a name="connect-to-a-secure-cluster"></a>Ansluta till ett säkert kluster
Du kan styra klientåtkomst till ditt Service Fabric-kluster med certifikat eller med hjälp av Azure Active Directory (AAD).

Om du försöker ansluta till ett säkert kluster blir sedan beroende på klustrets konfiguration du ombedd att ange ett klientcertifikat eller logga in med AAD.

## <a name="video-tutorial"></a>Videosjälvstudie

Titta på följande Microsoft Virtual Academy-video om du vill veta hur du använder Service Fabric Explorer:

> [!NOTE]
> Den här videon visar Service Fabric Explorer finns i ett Service Fabric-kluster, inte skrivbordsversionen.
>
>

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="understand-the-service-fabric-explorer-layout"></a>Förstå Service Fabric Explorer-layout
Du kan navigera genom Service Fabric Explorer med hjälp av trädvyn till vänster. Instrumentpanelen för klustret innehåller en översikt över klustret, inklusive en sammanfattning av program- och nodhälsan i roten för trädet.

![Service Fabric Explorer klusterinstrumentpanel][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Visa klustrets layout
Noder i ett Service Fabric-kluster är placerade i ett tvådimensionellt rutnät feldomäner och uppgraderingsdomäner. Den här placering säkerställer att programmen är tillgängliga när det finns maskinvarufel och programuppgraderingar. Du kan visa hur det aktuella klustret ser ut med hjälp av kluster-kartan.

![Service Fabric Explorer klusterkarta][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Visa program och tjänster
Klustret innehåller två underträd: en för program och en annan för noder.

Du kan använda vyn program kan navigera i Service Fabric logiska hierarkin: program, tjänster, partitioner och repliker.

I exemplet nedan programmet **MyApp** består av två tjänster **MyStatefulService** och **webbtjänsten**. Eftersom **MyStatefulService** är tillståndskänslig så den innehåller en partition med en primär och två sekundära repliker. Däremot WebSvcService är tillståndslösa och innehåller en enda instans.

![Service Fabric Explorer programvy][sfx-application-tree]

På varje nivå i trädet visas i huvudrutan viktig information om objektet. Du kan till exempel se hälsostatus och version för en viss tjänst.

![Informationsfönster för Service Fabric Explorer][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Visa klustrets noder
Nodvyn visar klustrets fysiska layout. För en viss nod kan du inspektera vilka program som har kod distribuerad på noden. Mer specifikt kan du se vilka repliker för närvarande körs det.

## <a name="actions"></a>Åtgärder
Service Fabric Explorer erbjuder ett snabbt sätt att anropa åtgärder på noder, program och tjänster i klustret.

Till exempel för att ta bort en programinstans väljer programmet från trädet till vänster och välj sedan **åtgärder** > **ta bort programmet**.

![Tar bort ett program i Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Du kan utföra samma åtgärder genom att klicka på ellipsen bredvid varje element.
>
> Varje åtgärd som kan utföras via Service Fabric Explorer kan också utföras via PowerShell eller REST-API för att aktivera automatisering.
>
>

Du kan också använda Service Fabric Explorer för att skapa instanser av programmet för en viss programtypen och versionen. Välj vilken typ av program i trädvyn och klicka sedan på den **skapa app-instansen** länken bredvid den version som du vill ha i den högra rutan.

![Skapa en programinstans i Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer stöder inte parametrar när du skapar instanser av programmet. Instanser av programmet använder standardvärden för parametern.
>
>

## <a name="next-steps"></a>Nästa steg
* [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Distribution av Service Fabric med hjälp av PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png
