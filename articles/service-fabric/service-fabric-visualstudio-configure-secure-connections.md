---
title: Konfigurera säkra Azure Service Fabric-klusteranslutningar
description: Lär dig hur du använder Visual Studio för att konfigurera säkra anslutningar som stöds av Azure Service Fabric-klustret.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 11f76153726d3fc92118fb46cc61b4627ab6a1b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464090"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Konfigurera säkra anslutningar till ett Service Fabric-kluster från Visual Studio
Lär dig hur du använder Visual Studio för att på ett säkert sätt komma åt ett Azure Service Fabric-kluster med konfigurerade principer för åtkomstkontroll.

## <a name="cluster-connection-types"></a>Klusteranslutningstyper
Två typer av anslutningar stöds av Azure Service Fabric-klustret: **icke-säkra** anslutningar och **x509 certifikatbaserade** säkra anslutningar. (För Service Fabric-kluster som finns lokalt stöds även **Windows-** och **dSTS-autentiseringar.)** Du måste konfigurera klusteranslutningstypen när klustret skapas. När den har skapats kan inte anslutningstypen ändras.

Verktygen Visual Studio Service Fabric stöder alla autentiseringstyper för anslutning till ett kluster för publicering. Se [Konfigurera ett Service Fabric-kluster från Azure-portalen](service-fabric-cluster-creation-via-portal.md) för instruktioner om hur du konfigurerar ett säkert Service Fabric-kluster.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Konfigurera klusteranslutningar i publiceringsprofiler
Om du publicerar ett Service Fabric-projekt från Visual Studio använder du dialogrutan **Publicera tjänst fabric-program** för att välja ett Azure Service Fabric-kluster. Under **Anslutningsslutpunkt**väljer du ett befintligt kluster under din prenumeration.

![Dialogrutan **Publicera program för tjänstinfrastruktur** används för att konfigurera en Service Fabric-anslutning.][publishdialog]

Dialogrutan **Program för publiceringstjänst fabric** validerar klusteranslutningen automatiskt. Logga in på ditt Azure-konto om du uppmanas att göra det. Om valideringen går innebär det att systemet har rätt certifikat installerade för att ansluta till klustret på ett säkert sätt, eller så är klustret inte säkert. Valideringsfel kan orsakas av nätverksproblem eller genom att systemet inte är korrekt konfigurerat för att ansluta till ett säkert kluster.

![Dialogrutan **Publicera program för tjänstinfrastruktur** validerar en befintlig, korrekt konfigurerad Service Fabric-klusteranslutning.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Så här ansluter du till ett säkert kluster
1. Kontrollera att du kan komma åt ett av klientcertifikaten som målklustret litar på. Certifikatet delas vanligtvis som en fil för utbyte av personlig information (.pfx). Se [Konfigurera ett Service Fabric-kluster från Azure-portalen](service-fabric-cluster-creation-via-portal.md) för hur du konfigurerar servern för att bevilja åtkomst till en klient.
2. Installera det betrodda certifikatet. Det gör du genom att dubbelklicka på PFX-filen eller använda PowerShell-skriptet Import-PfxCertificate för att importera certifikaten. Installera certifikatet på **Cert:\LocalMachine\My**. Det är OK att acceptera alla standardinställningar när du importerar certifikatet.
3. Välj kommandot **Publicera...** på snabbmenyn i projektet för att öppna dialogrutan **Publicera Azure-program** och välj sedan målklustret. Verktyget löser anslutningen automatiskt och sparar de säkra anslutningsparametrarna i publiceringsprofilen.
4. Valfritt: Du kan redigera publiceringsprofilen för att ange en säker klusteranslutning.
   
   Eftersom du redigerar XML-filen Publicera profil manuellt för att ange certifikatinformation måste du notera certifikatarkivets namn, arkivplats och certifikattumavtryck. Du måste ange dessa värden för certifikatets butiksnamn och butiksplats. Mer information finns i Så här hämtar du [tumavtrycket](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) för ett certifikat.
   
   Du kan använda parametrarna *ClusterConnectionParameters* för att ange de PowerShell-parametrar som ska användas vid anslutning till Service Fabric-klustret. Giltiga parametrar är alla som accepteras av Cmdlet Connect-ServiceFabricCluster. Se [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) för en lista över tillgängliga parametrar.
   
   Om du publicerar till ett fjärrkluster måste du ange lämpliga parametrar för det specifika klustret. Följande är ett exempel på anslutning till ett icke-säkert kluster:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Här är ett exempel på hur du ansluter till ett x509-certifikatbaserat säkert kluster:
   
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
5. Redigera alla andra nödvändiga inställningar, till exempel uppgraderingsparametrar och programparameterfilsplatsen, och publicera sedan programmet från dialogrutan **Publicera tjänstinfrastrukturprogram** i Visual Studio.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder Service Fabric-kluster finns i [Visualisera klustret med hjälp av Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
