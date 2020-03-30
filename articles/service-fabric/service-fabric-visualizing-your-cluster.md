---
title: Visualisera klustret med Hjälp av Azure Service Fabric Explorer
description: Service Fabric Explorer är ett program för att inspektera och hantera molnprogram och noder i ett Microsoft Azure Service Fabric-kluster.
author: mikkelhegn
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 80e3d990b6e8026c57ffff0048d0447a95529564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258192"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualisera ditt kluster med Service Fabric Explorer

Service Fabric Explorer (SFX) är ett verktyg med öppen källkod för att inspektera och hantera Azure Service Fabric-kluster. Service Fabric Explorer är ett skrivbordsprogram för Windows, macOS och Linux.

## <a name="service-fabric-explorer-download"></a>Hämta Hämtning av Service Fabric Explorer

Använd följande länkar för att hämta Service Fabric Explorer som ett skrivbordsprogram:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Skrivbordsversionen av Service Fabric Explorer kan ha fler eller färre funktioner än klusterstödet. Du kan återgå till den Version av Service Fabric Explorer som distribueras till klustret för att säkerställa fullständig funktionskompatibilitet.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Köra Service Fabric Explorer från klustret

Service Fabric Explorer finns också i ett service fabric-klusters HTTP-hanteringsslutpunkt. Om du vill starta SFX i en webbläsare bläddrar du till klustrets HTTP-hanteringsslutpunkt från valfri webbläsare , till exempel https:\//clusterFQDN:19080.

För installation av utvecklararbetsstation kan du starta Service Fabric https://localhost:19080/ExplorerExplorer i det lokala klustret genom att navigera till . Titta på den här artikeln för att [förbereda din utvecklingsmiljö](service-fabric-get-started.md).

> [!NOTE]
> Om ditt kluster skyddas av ett självsignerat certifikat får du ett felmeddelande från webbläsaren "Den här webbplatsen är inte säker". Du kan helt enkelt gå igenom de flesta moderna webbläsare genom att åsidosätta varningen. I en produktionsmiljö bör klustret skyddas med vanligt namn och ett certifikatutfärdarcertifikat. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Ansluta till ett Service Fabric-kluster
Om du vill ansluta till ett Service Fabric-kluster behöver du slutpunkten för klusterhantering (FQDN/IP) och HTTP-hanteringsslutpunktsporten (19080 som standard). Till exempel\:https //mysfcluster.westus.cloudapp.azure.com:19080. Använd kryssrutan Anslut till localhost för att ansluta till ett lokalt kluster på din arbetsstation.

### <a name="connect-to-a-secure-cluster"></a>Ansluta till ett säkert kluster
Du kan styra klientåtkomsten till ditt Service Fabric-kluster antingen med certifikat eller med Azure Active Directory (AAD).

Om du försöker ansluta till ett säkert kluster måste du, beroende på klustrets konfiguration, visa upp ett klientcertifikat eller logga in med AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>Förstå layouten Service Fabric Explorer
Du kan navigera genom Service Fabric Explorer med hjälp av trädet till vänster. Vid trädets rot ger klusterinstrumentpanelen en översikt över klustret, inklusive en sammanfattning av programmets och nodhälsan.

![Instrumentpanelen för Klusterinstrumentpanel för Service Fabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Visa klustrets layout
Noder i ett Service Fabric-kluster placeras över ett tvådimensionellt rutnät av feldomäner och uppgraderingsdomäner. Den här placeringen säkerställer att dina program förblir tillgängliga i närvaro av maskinvarufel och programuppgraderingar. Du kan visa hur det aktuella klustret är utformat med hjälp av klusterkartan.

![Klusterkarta för Service Fabric Explorer][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Visa program och tjänster
Klustret innehåller två underträd: en för program och en annan för noder.

Du kan använda programvyn för att navigera genom Service Fabric:s logiska hierarki: program, tjänster, partitioner och repliker.

I exemplet nedan består programmet **MyApp** av två tjänster, **MyStatefulService** och **WebService**. Eftersom **MyStatefulService** är tillståndskänsligt innehåller den en partition med en primär och två sekundära repliker. WebSvcService är däremot tillståndslöst och innehåller en enda instans.

![Programvy för Service Fabric Explorer][sfx-application-tree]

På varje nivå i trädet visar huvudfönstret relevant information om objektet. Du kan till exempel se hälsostatus och version för en viss tjänst.

![Essentials-fönstret För service fabric Explorer][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Visa klustrets noder
Nodvyn visar klustrets fysiska layout. För en viss nod kan du inspektera vilka program som har kod distribuerad på noden. Mer specifikt kan du se vilka repliker som körs där.

## <a name="actions"></a>Åtgärder
Service Fabric Explorer erbjuder ett snabbt sätt att anropa åtgärder för noder, program och tjänster i klustret.

Om du till exempel vill ta bort en programinstans väljer du programmet från trädet till vänster och väljer sedan **Åtgärder** > **Ta bort program**.

![Ta bort ett program i Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Du kan utföra samma åtgärder genom att klicka på ellipsen bredvid varje element.
>
> Varje åtgärd som kan utföras via Service Fabric Explorer kan också utföras via PowerShell eller ett REST API, för att möjliggöra automatisering.
>
>

Du kan också använda Service Fabric Explorer för att skapa programinstanser för en viss programtyp och version. Välj programtypen i trädvyn och klicka sedan på länken **Skapa appförekomst** bredvid den version du vill använda i den högra rutan.

![Skapa en programinstans i Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer stöder inte parametrar när programinstanser skapas. Programinstanser använder standardparametervärden.
>
>

## <a name="event-store"></a>Händelsebutik
EventStore är en funktion som erbjuds av plattformen som tillhandahåller Plattformshändelser för Service Fabric som är tillgängliga i Service Fabric Explorer och via REST API. Du kan se en ögonblicksbildvy av vad som händer i klustret för varje entitet, t.ex. Du kan också läsa mer om EventStore på [EventStore Översikt](service-fabric-diagnostics-eventstore.md).   

![EventStore (På plats)][sfx-eventstore]

>[!NOTE]
>Från och med Service Fabric version 6.4. EventStore är inte aktiverat som standard och måste vara aktiverat i resurshanterarens mall

>[!NOTE]
>Från och med Service Fabric version 6.4. EventStore-API:erna är endast tillgängliga för Windows-kluster som bara körs på Azure. Vi arbetar med att portera den här funktionen till Linux samt våra fristående kluster.

## <a name="image-store-viewer"></a>Bild store Viewer
Bildlagsvisaren är en funktion som erbjuds om du använder Native Image Store som gör det möjligt att visa det aktuella innehållet i Bildarkivet och hämta fil- och mappinformation, tillsammans med att ta bort filer/mappar.

![Klusterkarta för Service Fabric Explorer][sfx-imagestore]

## <a name="backup-and-restore"></a>Säkerhetskopiera och återställ
Service Fabric Explorer erbjuder möjligheten att samverka med [Backup och Restore](./service-fabric-reliable-services-backup-restore.md). För att kunna se funktionerna för säkerhetskopiering och återställning i SFX måste avancerat läge vara aktiverat.

![Aktivera avancerat läge][0]
 
Följande åtgärder är möjliga:

* Skapa, redigera och ta bort en princip för säkerhetskopiering.
* Aktivera och inaktivera Säkerhetskopiering för ett program, en tjänst eller en partition.
* Pausa och återuppta säkerhetskopiering för ett program, en tjänst eller en partition.
* Utlösare och spåra Säkerhetskopiering av en partition.
* Utlösare och spåra Återställning för en partition.

Mer information om tjänsten Säkerhetskopiering och återställning finns i [REST API-referensen](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore).
## <a name="next-steps"></a>Nästa steg
* [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Distribution av Tjänst Fabric-program med PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png