---
title: "Visualisera ditt kluster med hjälp av Azure Service Fabric Explorer | Microsoft Docs"
description: "Service Fabric Explorer är ett program för att kontrollera och hantera molnprogram och noder i ett Microsoft Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: mikhegn
ms.openlocfilehash: 34e00058591bc5a0a02bc408cfc3fcc11010f17c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualisera ditt kluster med Service Fabric Explorer

Service Fabric Explorer (SFX) är ett verktyg med öppen källkod för att kontrollera och hantera Azure Service Fabric-kluster. Service Fabric Explorer är ett skrivbordsprogram för Windows och Linux. Stöd för MacOS kommer snart.

## <a name="service-fabric-explorer-download"></a>Service Fabric Explorer hämtning

Använd följande länkar för att hämta Service Fabric Explorer som ett skrivbordsprogram:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

> [!NOTE]
> Skrivbordsversionen av Service Fabric Explorer kan ha fler eller färre funktioner än stöd för kluster. Du kan återgå till Service Fabric Explorer-versionen som distribuerats till klustret för att säkerställa kompatibilitet för alla funktioner.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Kör Service Fabric Explorer från klustret

Service Fabric Explorer finns också i Service Fabric-klustret HTTP management-slutpunkten. Bläddra till klustrets HTTP-slutpunkt för hantering i valfri webbläsare – till exempel https://clusterFQDN:19080 om du vill starta SFX i en webbläsare.

För utvecklare arbetsstation installationen kan du starta Service Fabric-Utforskaren på din lokala klustret genom att gå till http://localhost:19080/Explorer. Titta på den här artikeln [förbereda din utvecklingsmiljö](service-fabric-get-started.md).

## <a name="connect-to-a-service-fabric-cluster"></a>Ansluta till ett Service Fabric-kluster
Om du vill ansluta till ett Service Fabric-kluster, behöver du hanteringsslutpunkten kluster (IP/FQDN) och HTTP-slutpunkt Hanteringsport (19080 som standard). For example https://mysfcluster.westus.cloudapp.azure.com:19080. Använd kryssrutan ”ansluta till localhost” för att ansluta till det lokala klustret på din arbetsstation.

### <a name="connect-to-a-secure-cluster"></a>Ansluta till ett säkert kluster
Du kan kontrollera klientåtkomst till Service Fabric-kluster med certifikat eller med hjälp av Azure Active Directory (AAD).

Om du försöker ansluta till en säker kluster blir sedan beroende på klustrets konfiguration du ombedd att ange ett klientcertifikat eller logga in med AAD.

## <a name="video-tutorial"></a>Videosjälvstudie

Information om hur du använder Service Fabric Explorer, se följande Microsoft Virtual Academy video:

> [!NOTE]
> Den här videon visar Service Fabric Explorer finns i ett Service Fabric-kluster, inte klientversionen.
>
>

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="understand-the-service-fabric-explorer-layout"></a>Förstå Service Fabric Explorer-layout
Du kan bläddra igenom Service Fabric Explorer med hjälp av trädet till vänster. Roten i trädet innehåller en översikt över klustret, inklusive en sammanfattning av program och noden hälsa i instrumentpanelen för klustret.

![Instrumentpanelen för Service Fabric Explorer-klustret][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Visa klustrets layout
Noder i ett Service Fabric-kluster är placerade över en tvådimensionell rutnät för feldomäner och uppgradera domäner. Den här placering garanterar att dina program finns tillgängliga med maskinvarufel och programuppgraderingar. Du kan se hur det aktuella klustret är placerade med hjälp av listan över lediga kluster.

![Service Fabric Explorer över lediga kluster][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Visa program och tjänster
Klustret innehåller två underträd: en för program och en annan för noder.

Du kan använda vyn program kan navigera i Service Fabric logiska hierarkin: program, tjänster, partitioner och repliker.

I exemplet nedan programmet **MyApp** består av två tjänster **MyStatefulService** och **WebService**. Eftersom **MyStatefulService** är tillståndskänslig så innehåller en partition med en primär och två sekundära repliker. Däremot WebSvcService är tillståndslösa och innehåller en enda instans.

![Service Fabric Explorer programvy][sfx-application-tree]

På varje nivå i trädet visar i huvudfönstret viktig information om objektet. Du kan till exempel se hälsostatus och version för en viss tjänst.

![Service Fabric Explorer essentials fönstret][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Visa noder i klustret
Nodvyn visar klustrets fysiska layout. För en viss nod kan du inspektera vilka program som har kod distribuerad på noden. Mer specifikt kan du se vilka repliker körs det.

## <a name="actions"></a>Åtgärder
Service Fabric Explorer erbjuder ett snabbt sätt att anropa åtgärder på noder, program och tjänster inom klustret.

Exempelvis för att ta bort en instans av programmet, Välj programmet från trädet till vänster och välj sedan **åtgärder** > **ta bort programmet**.

![Om du tar bort ett program i Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Du kan utföra samma åtgärder genom att klicka på knappen bredvid varje element.
>
> Varje åtgärd som kan utföras via Service Fabric Explorer kan också utföras via PowerShell eller REST-API för att aktivera automatisering.
>
>

Du kan också använda Service Fabric Explorer för att skapa instanser av programmet för en viss programtypen och versionen. Välj programtyp av i trädvyn och klicka sedan på den **skapa app-instansen** länken bredvid den version som du vill ha i den högra rutan.

![Skapa en programinstans i Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer stöder inte parametrar när du skapar instanser av programmet. Programinstanser använder standardvärden för parametern.
>
>

## <a name="next-steps"></a>Nästa steg
* [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric-programdistribution med hjälp av PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png