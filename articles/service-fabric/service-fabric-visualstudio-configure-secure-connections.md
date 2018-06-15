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
ms.openlocfilehash: e2772cc2c59b93c7e523eaa0127dcf4ea0bc589e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208693"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Konfigurera säkra anslutningar till ett Service Fabric-kluster från Visual Studio
Lär dig hur du använder Visual Studio säker åtkomst till ett Azure Service Fabric-kluster med principer för åtkomstkontroll konfigurerats.

## <a name="cluster-connection-types"></a>Kluster-anslutningstyper
Två typer av anslutningar som stöds av Azure Service Fabric-kluster: **inte är säkra** anslutningar och **x509 certifikatbaserad** säkra anslutningar. (För Service Fabric-kluster finns lokalt, **Windows** och **dSTS** autentiseringar stöds också.) Du måste konfigurera anslutningstypen klustret när klustret skapas. Anslutningstypen kan inte ändras när den har skapats.

Verktyg för Visual Studio Service Fabric stöder alla typer av autentisering för att ansluta till ett kluster för publicering. Se [ställa in ett Service Fabric-kluster från Azure portal](service-fabric-cluster-creation-via-portal.md) instruktioner om hur du ställer in en säker Service Fabric-klustret.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Konfigurera klustret anslutningar i Publicera profiler
Om du publicerar ett Service Fabric-projekt från Visual Studio kan du använda den **publicera Fabric tjänstprogrammet** dialogrutan för att välja ett Azure Service Fabric-kluster. Under **Anslutningens slutpunkt**, Välj ett befintligt kluster under din prenumeration.

![Den ** dialogrutan Publicera Service Fabric Application ** används för att konfigurera en Service Fabric-anslutning.][publishdialog]

Den **publicera Fabric tjänstprogrammet** dialogrutan verifieras automatiskt kluster-anslutningen. Om du uppmanas logga in på ditt Azure-konto. Om valideringen lyckas, innebär det att systemet har rätt certifikat installerat för att ansluta till klustret på ett säkert sätt eller klustret är inte säker. Verifieringsfel kan orsakas av problem med nätverket eller genom att inte låta systemet korrekt konfigurerade för att ansluta till en säker kluster.

![Den ** publicera Service Fabric Application ** dialogrutan verifierar en befintlig konfigurerad anslutning för Service Fabric-klustret.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Att ansluta till en säker kluster
1. Kontrollera att du kan komma åt ett klientcertifikat som litar på målklustret. Certifikatet delas vanligtvis som en Personal Information Exchange (.pfx)-fil. Se [ställa in ett Service Fabric-kluster från Azure portal](service-fabric-cluster-creation-via-portal.md) att konfigurera servern för att bevilja åtkomst till en klient.
2. Installera det betrodda certifikatet. Dubbelklicka på PFX-filen för att göra detta, eller använda PowerShell-skript importera PfxCertificate för att importera certifikaten. Installera certifikatet till **Cert: \LocalMachine\My**. Det är OK att acceptera alla standardinställningar vid import av certifikatet.
3. Välj den **publicera...**  på snabbmenyn för projektet för att öppna den **publicera Azure-programmet** dialogrutan och välj sedan målklustret. Verktyget matchar anslutningen automatiskt och sparar säker anslutningsparametrar i profilen.
4. Valfritt: Du kan redigera profilen som om du vill ange en säker kluster-anslutning.
   
   Eftersom du redigerar manuellt publicera profil-XML-filen om du vill ange certifikatinformationen anteckna Certifikatarkivets namn kan lagra plats och tumavtrycket för certifikatet. Du måste ange dessa värden för certifikatets store namn och plats. Se [så här: hämta en certifikatets tumavtryck](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) för mer information.
   
   Du kan använda den *ClusterConnectionParameters* parametrar för att ange PowerShell-parametrar som ska användas vid anslutning till Service Fabric-klustret. Parametrar som är giltiga som accepteras av Connect-ServiceFabricCluster cmdlet. Se [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) en lista över tillgängliga parametrar.
   
   Om du ska publicera till ett kluster, måste du ange lämpliga parametrar för specifika klustret. Följande är ett exempel på ansluter till en icke-säker kluster:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Här är ett exempel för att ansluta till x509 certifikatbaserad säker klustret:
   
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
5. Redigera andra nödvändiga inställningar, till exempel uppgradera parametrar och programmet parametern plats, och publicera programmet från den **publicera Fabric tjänstprogrammet** dialogrutan i Visual Studio.

## <a name="next-steps"></a>Nästa steg
Mer information om åtkomst till Service Fabric-kluster finns [visualisera ditt kluster med hjälp av Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
