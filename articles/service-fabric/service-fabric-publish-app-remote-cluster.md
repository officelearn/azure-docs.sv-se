---
title: "Publicera en app för ett kluster med Visual Studio | Microsoft Docs"
description: "Lär dig hur du publicerar ett program till en fjärransluten service fabric-kluster med hjälp av Visual Studio."
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: faecd892-eb54-4d9c-8023-c67442afb8e8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/29/2016
ms.author: cawa
ms.openlocfilehash: c440c520d84fc503ff9e705555449e92555d4721
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-and-remove-applications-using-visual-studio"></a>Distribuera och ta bort program med Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [FabricClient-API:er](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Azure Service Fabric-tillägget för Visual Studio är ett enkelt, repeterbara och skriptbara sätt att publicera ett program till ett Service Fabric-kluster.

## <a name="the-artifacts-required-for-publishing"></a>Artefakter som krävs för publicering
### <a name="deploy-fabricapplicationps1"></a>Distribuera FabricApplication.ps1
Det här är ett PowerShell-skript som använder en publiceringssökväg som en parameter för publishing Service Fabric-program. Eftersom det här skriptet ingår i ditt program, är du Välkommen att ändra den efter behov för ditt program.

### <a name="publish-profiles"></a>Publicera profiler
En mapp i Service Fabric-programprojekt kallas **PublishProfiles** innehåller XML-filer som lagrar viktig information för att publicera ett program, exempelvis:

* Anslutningsparametrar för Service Fabric-kluster
* Sökväg till en parameterfil för programmet
* Inställningar för uppgradering

Som standard ditt program innehåller tre publicera profiler: Local.1Node.xml, Local.5Node.xml och Cloud.xml. Du kan lägga till fler profiler genom att kopiera och klistra in en av standardfilerna som.

### <a name="application-parameter-files"></a>Parametern programfiler
En mapp i Service Fabric-programprojekt kallas **ApplicationParameters** innehåller XML-filer för användardefinierade application manifest parametervärden. Application manifest-filer kan parameteriseras så att du kan använda olika värden för distributionsinställningar. Läs mer om Parameterisera ditt program i [hantera flera miljöer i Service Fabric](service-fabric-manage-multiple-environment-app-configuration.md).

> [!NOTE]
> Du bör skapa projektet innan du utför att redigera filen i en textredigerare eller via dialogrutan Publicera för aktörstjänster. Det beror på att en del av manifest-filer kommer att skapas under genereringen.

## <a name="to-publish-an-application-using-the-publish-service-fabric-application-dialog-box"></a>Publicera ett program med hjälp av dialogrutan Publicera programmet för Service Fabric
Följande steg visar hur du publicerar ett program som använder den **publicera Fabric tjänstprogrammet** dialogrutan som tillhandahålls av Visual Studio Fabric-Tools.

1. Välj på snabbmenyn för Service Fabric Application-projekt **publicera...** Visa den **publicera Fabric tjänstprogrammet** dialogrutan.
   
    ![Den ** dialogrutan Publicera Service Fabric Application **][0]
   
    Filen som markerats i den **mål profil** nedrullningsbara listrutan är där alla inställningar, förutom **Manifest versioner**, sparas. Du kan återanvända en befintlig profil eller skapa en ny genom att välja **<... hantera profiler >** i den **mål profil** nedrullningsbara listrutan. När du väljer en publiceringsprofil visas innehållet i motsvarande fält i dialogrutan. Spara dina ändringar när som helst genom att välja den **spara profil** länk.    
2. I den **Anslutningens slutpunkt** , anger en lokal eller fjärransluten Service Fabric klustrets publishing slutpunkt. Om du vill lägga till eller ändra anslutningens slutpunkt, klicka på den **Anslutningens slutpunkt** listrutan. I listan visas tillgängliga Service Fabric-klustret anslutningens slutpunkter som du kan publicera baserat på dina Azure-abonnemang. Observera att om du inte är redan inloggad Visual Studio, du uppmanas att göra detta.
   
    Använd dialogrutan för val av klustret för att välja från uppsättningen med tillgängliga prenumerationer och -kluster.
   
    ![Den ** dialogrutan Välj Service Fabric klustret **][1]
   
   > [!NOTE]
   > Om du vill publicera till en valfri slutpunkt (t.ex en part kluster) finns på **publicering till en godtycklig klusterslutpunkten** nedan.
   > 
   > 
   
    När du väljer en slutpunkt, verifierar Visual Studio anslutningen till det valda Service Fabric-klustret. Om klustret inte är säker kan Visual Studio ansluta till den direkt. Om klustret är skyddad, måste du dock installera ett certifikat på den lokala datorn innan du fortsätter. Se [hur du konfigurerar säkra anslutningar](service-fabric-visualstudio-configure-secure-connections.md) för mer information. När du är klar väljer du den **OK** knappen. Det markerade klustret visas i den **publicera Fabric tjänstprogrammet** dialogrutan.
3. I den **programmet parameterfilen** listrutan går du till en parameter för programregistrering. Ett program parameterfilen innehåller användardefinierade värden för parametrar i programmanifestfilen. Om du vill lägga till eller ändra en parameter, Välj den **redigera** knappen. Ange eller ändra parameterns värde i den **parametrar** rutnätet. När du är klar väljer du den **spara** knappen.
   
    ![Den ** dialogrutan Redigera parametrar **][2]
4. Använd den **uppgradera programmet** kryssruta för att ange om detta publicera åtgärd är en uppgradering. Uppgraderingen publicera åtgärder skiljer sig från normal publicera åtgärder. Se [Service Fabric Application uppgradera](service-fabric-application-upgrade.md) för en lista över skillnader. Så här konfigurerar du inställningar för den **konfigurera inställningar för uppgradering av** länk. Uppgradera Parameterredigeraren visas. Se [konfigurera uppgraderingen av ett Service Fabric-program](service-fabric-visualstudio-configure-upgrade.md) för mer information om uppgradering parametrar.
5. Välj den **Manifest versioner...** Om du vill visa den **redigera versioner** dialogrutan. Du behöver uppdatera programmet och service-versioner för en uppgradering ska kunna utföras. Se [Service Fabric uppgradera självstudien](service-fabric-application-upgrade-tutorial.md) att lära dig hur programmet och service manifest versioner påverkar en uppgraderingsprocessen.
   
    ![Den ** dialogrutan Redigera versioner **][3]
   
    Om programmet och service versioner använder semantiska versionshantering, till exempel 1.0.0 eller numeriska värden i formatet 1.0.0.0 markerar den **automatiskt uppdatera programmet och service versioner** alternativet. När du väljer det här alternativet tjänsten och versionsnummer för programmet uppdateras automatiskt när koden, konfiguration och data Paketversion uppdateras. Avmarkera kryssrutan om du vill inaktivera den här funktionen om du vill redigera versionerna manuellt.
   
   > [!NOTE]
   > För alla paket-poster ska visas för ett aktören projekt först skapa projektet för att generera posterna i Service Manifest-filer.
   > 
   > 
6. När du är klar att ange alla nödvändiga inställningar, väljer den **publicera** för att publicera programmet till det valda Service Fabric-klustret. De inställningar som du angett tillämpas publiceringsprocessen.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Publicera till en godtycklig kluster-slutpunkt (inklusive part kluster)
Visual Studio publicerar upplevelse är optimerad för publicering till fjärr-kluster som är associerade med en av dina Azure-prenumerationer. Det är dock möjligt att publicera till valfri slutpunkter (till exempel Service Fabric part kluster) genom att redigera profilen som XML. Enligt beskrivningen ovan, tre publicera profiler som tillhandahålls som standard--**Local.1Node.xml**, **Local.5Node.xml**, och **Cloud.xml**-- men är du Välkommen att skapa ytterligare profiler för olika miljöer. Du kanske exempelvis vill skapa en profil för publicering till part kluster, kanske med namnet **Party.xml**.

Om du ansluter till ett oskyddat kluster är allt som krävs för klustret Anslutningens slutpunkt som `partycluster1.eastus.cloudapp.azure.com:19000`. I så fall Anslutningens slutpunkt i profilen som skulle se ut ungefär så här:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Om du ansluter till ett kluster som är skyddade även behöver du ange information om klientens certifikat från det lokala arkivet som ska användas för autentisering. Mer information finns i [Konfigurera säkra anslutningar till ett Service Fabric-kluster](service-fabric-visualstudio-configure-secure-connections.md).

  När din publiceringsprofil har konfigurerats, kan du använda det i dialogrutan Publicera enligt nedan.

  ![Nya publiceringsprofil publicera i dialogrutan][4]

  Observera att i det här fallet den nya profilen pekar på en av de parameter programfilerna. Detta är lämpligt om du vill publicera samma programkonfigurationen till ett antal olika miljöer. Däremot i fall där du vill ha olika konfigurationer för varje miljö som du vill publicera till skulle det vara klokt att skapa en motsvarande program parameterfil.

## <a name="next-steps"></a>Nästa steg
Information om hur du automatiserar publiceringsprocessen i en miljö med kontinuerlig integration finns [Ställ in kontinuerlig integration av Service Fabric](service-fabric-set-up-continuous-integration.md).

[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png
