---
title: Konfigurera säkra anslutningar i Azure Service Fabric-kluster | Microsoft Docs
description: Lär dig hur du använder Visual Studio för att konfigurera säkra anslutningar som stöds av Azure Service Fabric-klustret.
services: service-fabric
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: tglee
ms.assetid: 80501867-dd7a-4648-8bd6-d4f26b68402d
ms.service: multiple
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 8d76a2144234591792359ed8dd4a0779e6a2fc5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628310"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Konfigurera säkra anslutningar till ett Service Fabric-kluster från Visual Studio
Lär dig hur du använder Visual Studio för att få säker åtkomst till ett Azure Service Fabric-kluster med principer för åtkomstkontroll konfigureras.

## <a name="cluster-connection-types"></a>Kluster-anslutningstyper
Två typer av anslutningar som stöds av Azure Service Fabric-klustret: **icke-säker** anslutningar och **x509 certifikatbaserad** säkra anslutningar. (För Service Fabric-kluster finns lokalt, **Windows** och **dSTS** autentiseringar stöds också.) Du måste konfigurera anslutningstypen klustret när klustret skapas. Anslutningstypen kan inte ändras när den har skapats.

Visual Studio Service Fabric-verktygen stöder alla typer av autentisering för att ansluta till ett kluster för publicering. Se [konfigurera ett Service Fabric-kluster från Azure portal](service-fabric-cluster-creation-via-portal.md) för anvisningar om hur du ställer in ett säkert Service Fabric-kluster.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Konfigurera klusteranslutningar i Publicera profiler
Om du har publicerat ett Service Fabric-projekt från Visual Studio kan du använda den **publicera Service Fabric-program** dialogrutan för att välja ett Azure Service Fabric-kluster. Under **anslutningsslutpunkten**, Välj ett befintligt kluster i din prenumeration.

![Den ** dialogrutan Publicera Service Fabric Application ** används för att konfigurera en Service Fabric-anslutning.][publishdialog]

Den **publicera Service Fabric-program** dialogrutan verifierar automatiskt klusteranslutningen. Om du uppmanas logga in på ditt Azure-konto. Om valideringen passerar innebär det att systemet har rätt certifikat installerat för att ansluta till klustret på ett säkert sätt eller klustret är inte säker. Verifieringsfel kan orsakas av problem i nätverket eller genom att inte låta systemet korrekt konfigurerad för att ansluta till ett säkert kluster.

![Den ** publicera Service Fabric Application ** dialogrutan verifierar en befintlig korrekt konfigurerad anslutning för Service Fabric-kluster.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Att ansluta till ett säkert kluster
1. Kontrollera att du kan komma åt ett klientcertifikat som litar på målklustret. Certifikatet delas vanligtvis som en Personal Information Exchange (.pfx)-fil. Se [konfigurera ett Service Fabric-kluster från Azure portal](service-fabric-cluster-creation-via-portal.md) att konfigurera servern för att bevilja åtkomst till en klient.
2. Installera det betrodda certifikatet. Dubbelklicka på PFX-filen om du vill göra detta måste eller Använd PowerShell-skriptet Import-PfxCertificate för att importera certifikaten. Installera certifikatet till **Cert: \LocalMachine\My**. Det är OK om du vill acceptera alla standardinställningar medan du importerar certifikatet.
3. Välj den **publicera...**  på snabbmenyn för projektet för att öppna den **publicera Azure-program** dialogrutan och välj sedan målklustret. Verktyget löser anslutningen och sparar säker anslutningsparametrarna i profilen som automatiskt.
4. Valfritt: Du kan redigera profilen som du anger en anslutning till ett säkert kluster.
   
   Eftersom du redigerar manuellt publicera profil-XML-fil se till att notera Certifikatarkivets namn om du vill ange certifikatinformationen kan lagra plats och tumavtrycket för certifikatet. Du måste ange dessa värden för certifikatets store namn och plats. Se [Anvisningar: Hämta tumavtrycket för ett certifikat](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) för mer information.
   
   Du kan använda den *ClusterConnectionParameters* parametrar för att ange PowerShell-parametrar som ska användas vid anslutning till Service Fabric-klustret. Giltiga parametrar är någon som accepteras av cmdleten Connect-ServiceFabricCluster. Se [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) en lista över tillgängliga parametrar.
   
   Om du ska publicera till ett fjärrkluster, måste du ange lämpliga parametrar för specifika klustret. Följande är ett exempel för att ansluta till ett icke-säker kluster:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Här är ett exempel för att ansluta till en x509 certifikatbaserad säkert kluster:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Redigera andra nödvändiga inställningar, till exempel Uppgraderingsparametrar och programmet parametern filplats, och sedan publicera dina program från den **publicera Service Fabric-program** dialogrutan i Visual Studio.

## <a name="next-steps"></a>Nästa steg
Mer information om åtkomst till Service Fabric-kluster finns i [visualisera klustret med hjälp av Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
