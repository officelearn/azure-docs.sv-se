---
title: "Visualisera ditt kluster med hjälp av Service Fabric Explorer | Microsoft Docs"
description: "Service Fabric Explorer är ett webbaserat verktyg för att kontrollera och hantera molnprogram och noder i ett Microsoft Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: ryanwi
ms.openlocfilehash: 965ffc0f8cec26cccbe6e6459731afc234111f4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualisera ditt kluster med Service Fabric Explorer
Service Fabric Explorer är ett webbaserat verktyg för att kontrollera och hantera program och noderna i Azure Service Fabric-klustret. Service Fabric Explorer ligger direkt på klustret, så att det alltid är tillgängliga, oavsett om klustret körs.

## <a name="video-tutorial"></a>Videosjälvstudie

Information om hur du använder Service Fabric Explorer, se följande Microsoft Virtual Academy video:

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="connect-to-service-fabric-explorer"></a>Ansluta till Service Fabric Explorer
Om du har följt anvisningarna för att [förbereda din utvecklingsmiljö](service-fabric-get-started.md), kan du starta Service Fabric-Utforskaren på din lokala klustret genom att gå till http://localhost:19080/Explorer.

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
>

I följande tabell visas åtgärderna som är tillgängliga för varje entitet:

| **Entitet** | **Åtgärd** | **Beskrivning** |
| --- | --- | --- |
| Programtyp |Avetablera typ |Tar bort programpaketet från avbildningsarkivet i klustret. Kräver att alla program av den typen som ska tas bort först. |
| Program |Ta bort program |Ta bort program, inklusive alla tjänster och deras tillstånd (eventuella). |
| Tjänst |Ta bort tjänsten |Ta bort tjänsten och dess tillstånd (eventuella). |
| Node |Aktivera |Aktivera noden. |
| Node | Inaktivera (paus) | Pausa noden i det aktuella tillståndet. Tjänster fortsätter att köras men Service Fabric flyttas proaktivt inte något på eller inaktivera den om det är nödvändigt för att förhindra att en inkonsekvens nätverksavbrott eller om data. Den här åtgärden används vanligtvis för att aktivera felsökning services på en viss nod så att de inte flytta under kontroll. | |
| Node | Inaktivera (omstart) | Flytta alla InMemory-tjänster av en nod och Stäng beständiga tjänster på ett säkert sätt. Används vanligtvis när värdprocesser eller datorn måste startas om. | |
| Node | Inaktivera (ta bort data) | Stäng alla tjänster som körs på noden när du har skapat tillräckligt ledig repliker på ett säkert sätt. Används vanligtvis när en nod (eller åtminstone dess lagring) som permanent tas utanför kommissionen. | |
| Node | Ta bort nodens tillstånd | Ta bort kunskap om repliker för en nod från klustret. Används vanligtvis när en redan felaktiga noden bedöms oåterkalleligt. | |
| Node | Starta om | Simulera ett nodfel genom att starta om noden. Mer information [här](/powershell/module/servicefabric/restart-servicefabricnode?view=azureservicefabricps) | |

Eftersom många åtgärder är skadliga, kan du bli ombedd att bekräfta din avsikt innan åtgärden har slutförts.

> [!TIP]
> Varje åtgärd som kan utföras via Service Fabric Explorer kan också utföras via PowerShell eller REST-API för att aktivera automatisering.
>
>

Du kan också använda Service Fabric Explorer för att skapa instanser av programmet för en viss programtypen och versionen. Välj programtyp av i trädvyn och klicka sedan på den **skapa app-instansen** länken bredvid den version som du vill ha i den högra rutan.

![Skapa en programinstans i Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Programinstanser som skapats via Service Fabric Explorer kan för närvarande parameteriseras. De skapas med hjälp av standardparametervärden.
>
>

## <a name="connect-to-a-remote-service-fabric-cluster"></a>Ansluta till en fjärransluten Service Fabric-kluster
Om du känner klustrets slutpunkt och den behörighet som du har åtkomst till Service Fabric Explorer från en webbläsare. Detta beror på att Service Fabric Explorer är en tjänst som körs i klustret.

### <a name="discover-the-service-fabric-explorer-endpoint-for-a-remote-cluster"></a>Identifiera Service Fabric Explorer-slutpunkt för ett kluster
Peka webbläsaren för att nå Service Fabric Explorer för ett kluster:

http://&lt;din klusterslutpunkten&gt;: 19080/Explorer

För Azure-kluster finns också hela Webbadressen i rutan klustret essentials i Azure-portalen.

### <a name="connect-to-a-secure-cluster"></a>Ansluta till ett säkert kluster
Du kan kontrollera klientåtkomst till Service Fabric-kluster med certifikat eller med hjälp av Azure Active Directory (AAD).

Om du försöker ansluta till Service Fabric-Utforskaren på ett säkert kluster ska sedan beroende på klustrets konfiguration du behöva ange ett klientcertifikat eller logga in med AAD.

## <a name="next-steps"></a>Nästa steg
* [Möjlighet att testa översikt](service-fabric-testability-overview.md)
* [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric-programdistribution med hjälp av PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png
