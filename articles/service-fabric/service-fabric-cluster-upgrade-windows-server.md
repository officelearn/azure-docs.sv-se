---
title: "Uppgradera en fristående Azure Service Fabric-kluster i Windows Server | Microsoft Docs"
description: "Uppgradera Azure Service Fabric-kod och/eller konfiguration som kör ett fristående Service Fabric-klustret, inklusive inställning uppdateringsläget klustret."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: c95c1827d0433dcb61eace34e7a905a5610c7781
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Uppgradera fristående Azure Service Fabric-kluster i Windows Server 
> [!div class="op_single_selector"]
> * [Azure-kluster](service-fabric-cluster-upgrade.md)
> * [Fristående kluster](service-fabric-cluster-upgrade-windows-server.md)
>
>

För alla moderna system är kan uppgraderas nyckeln till långsiktig framgång för en produkt. Ett Azure Service Fabric-kluster är en resurs som du äger. Den här artikeln beskriver hur du kan kontrollera att klustret alltid körs versioner av Service Fabric-kod och konfigurationer som stöds.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Kontrollera Service Fabric-version som körs på klustret
För att ange ditt kluster ska ladda ned uppdateringar av Service Fabric när Microsoft publicerar en ny version, tilldelas klusterkonfigurationen fabricClusterAutoupgradeEnabled *SANT*. Om du vill välja en version av Service Fabric som du vill att klustret ska finnas på stöds sägs klusterkonfigurationen fabricClusterAutoupgradeEnabled *FALSKT*.

> [!NOTE]
> Kontrollera att klustret alltid kör en Service Fabric-version som stöds. När Microsoft Beskriver utgivningen av en ny version av Service Fabric, markeras den tidigare versionen för slutet av stödet efter minst 60 dagar från datumet då meddelandet. Nya versioner tillkännages [på Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/). Den nya versionen finns att välja vid den punkten.
>
>

Du kan uppgradera klustret till den nya versionen endast om du använder en konfiguration av noden produktions-format, där varje Service Fabric-nod har allokerats på en separat fysisk eller virtuell dator. Om du har ett kluster för utveckling, där fler än en Service Fabric-nod är på en enda fysisk eller virtuell dator, måste du återskapa klustret med den nya versionen.

Två olika arbetsflöden kan uppgradera klustret till den senaste versionen eller en Service Fabric-version som stöds. Ett arbetsflöde är för kluster som har anslutning till den senaste versionen automatiskt. Andra arbetsflödet är för kluster som inte har anslutning till den senaste versionen Service Fabric.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Uppgradera kluster som har anslutning att hämta de senaste kod och konfiguration
Följ dessa steg för att uppgradera ditt kluster till en version som stöds om klusternoderna har internet-anslutning till den [Microsoft Download Center](http://download.microsoft.com).

För kluster som har anslutning till den [Microsoft Download Center](http://download.microsoft.com), Microsoft regelbundet kontrollerar tillgängligheten för nya Service Fabric-versioner.

När en ny Service Fabric-version är tillgänglig, hämtas lokalt till klustret paketet och etableras för uppgradering. För att informera kunden om den här nya versionen visas dessutom systemet en explicit klustret hälsotillstånd varning som liknar följande:

”Den aktuella klusterversionen [version #] stödet upphör [date]”.

När klustret kör den senaste versionen, försvinner varningen.

#### <a name="cluster-upgrade-workflow"></a>Uppgradera arbetsflödet för kluster
När klustret hälsotillstånd varning visas gör du följande:

1. Ansluta till klustret från en dator som har administratörsåtkomst till alla datorer som listas som noder i klustret. Den dator som det här skriptet körs på behöver inte vara en del av klustret.

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Hämta en lista över Service Fabric-versioner som du kan uppgradera till.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Du bör få ett utgående ut ungefär så här:

    ![Hämta Service Fabric-versioner][getfabversions]
3. Starta en klusteruppgradering till en version som är tillgängliga med hjälp av den [Start ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx) Windows PowerShell-kommando.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Du kan använda Service Fabric-Utforskaren eller kör följande PowerShell-kommando för att övervaka förloppet för uppgraderingen:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Om klustret hälsoprinciper inte är uppfyllt, återställs uppgraderingen. Om du vill ange anpassade hälsoprinciper för kommandot Start-ServiceFabricClusterUpgrade finns i dokumentationen för [Start ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

När du har åtgärdat problemen som resulterade i återställningen kan du initiera uppgraderingen igen genom att följa samma steg som beskrivits tidigare.

### <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Uppgradera kluster som har *ingen nätverksanslutning* att ladda ned de senaste kod och konfiguration
Följ dessa steg för att uppgradera ditt kluster till en version som stöds om klusternoderna inte har Internetanslutning till den [Microsoft Download Center](http://download.microsoft.com).

> [!NOTE]
> Om du kör ett kluster som inte är ansluten till internet, måste du övervaka Service Fabric-teamets blogg att lära dig om nya versioner. Systemet visar inte en varning om klustret hälsa att varna dig om nya versioner.  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatisk etablering jämfört med manuella etablering
Konfigurera om du vill aktivera automatisk överföring och registrering för den senaste versionen av koden uppdateringstjänsten Service Fabric. Instruktioner finns i Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt inuti den [fristående paketet](service-fabric-cluster-standalone-package-contents.md).
Följ dessa instruktioner för den manuella processen.

Ändra klusterkonfigurationen för att ange egenskapen följande *FALSKT* innan du påbörjar en uppgradering av konfiguration:

        "fabricClusterAutoupgradeEnabled": false,

Information, se den [Start-ServiceFabricClusterConfigurationUpgrade PowerShell-kommandot](https://msdn.microsoft.com/en-us/library/mt788302.aspx). Se till att uppdatera clusterConfigurationVersion om du i din JSON innan du börjar uppgradera konfiguration.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Uppgradera arbetsflödet för kluster

1. Kör Get-ServiceFabricClusterUpgrade från en av noderna i klustret och notera TargetCodeVersion.

2. Kör följande från en Internetansluten dator att lista alla uppgraderingen-kompatibla versioner med den aktuella versionen och hämta motsvarande paketet från de associera länkarna:

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Ansluta till klustret från en dator som har administratörsåtkomst till alla datorer som listas som noder i klustret. Den dator som det här skriptet körs på behöver inte vara en del av klustret.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. Kopiera det Hämta paketet till klustret image store.

5. Registrera kopierade paketet.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Starta en klusteruppgradering till en version som är tillgängliga.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Du kan övervaka förloppet för uppgraderingen på Service Fabric-Utforskaren eller genom att köra följande PowerShell-kommando:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Om klustret hälsoprinciper inte är uppfyllt, återställs uppgraderingen. Om du vill ange anpassade hälsoprinciper för kommandot Start-ServiceFabricClusterUpgrade finns i dokumentationen för [Start ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

När du har åtgärdat problemen som resulterade i återställningen kan du initiera uppgraderingen igen genom att följa samma steg som beskrivits tidigare.


## <a name="upgrade-the-cluster-configuration"></a>Uppgradera klusterkonfigurationen
Innan du startar uppgraderingen konfiguration kan du testa din nya klusterkonfiguration JSON genom att köra följande PowerShell-skript i fristående paketet:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
Eller Använd det här skriptet:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

Vissa konfigurationer måste uppgraderas, till exempel slutpunkter, klusternamnet, noden IP osv. Nya klusterkonfigurationen JSON har testats mot den gamla servern och genererar fel i PowerShell-fönstret om det uppstår problem.

Om du vill uppgradera configuration klusteruppgradering kör Start-ServiceFabricClusterConfigurationUpgrade. Konfiguration av uppgraderingen är bearbetade uppgraderingsdomän av uppgraderingsdomänen.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Klusteruppgradering certifikat config  
Ett kluster-certifikat används för autentisering mellan noder i klustret. Förnya certifikatet ska utföras med extra försiktig eftersom fel blockerar kommunikation mellan klusternoder.

Tekniskt sett stöds fyra alternativ:  

* Enskilt certifikat uppgraderingen: sökvägen för uppgraderingen är certifikat (primär) -> certifikatet B (primär) -> certifikatet C (primär) ->...

* Dubbla certifikat uppgraderingen: sökvägen för uppgraderingen är B (sekundär) -> certifikatet B (primär) och certifikat (primär) -> certifikat (primär) > certifikatet B (primär) och C (sekundär) -> certifikatet C (primär) ->...

* Av Typuppgradering av certifikat: tumavtryck-baserade certifikat <>--Vanligtnamn-baserade Certifikatkonfiguration. Till exempel certifikatets tumavtryck (primär) och tumavtrycket B (sekundär) -> certifikatet CommonName C.

* Utfärdaren tumavtrycket uppgradering av certifikat: sökvägen för uppgraderingen är certifikatet CN = A, IssuerThumbprint = IT1 (primära) certifikat CN -> = A, IssuerThumbprint = IT1 IT2 (primära) certifikat CN -> = A, IssuerThumbprint = IT2 (primära).


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du anpassar vissa [Service Fabric-klusterinställningar](service-fabric-cluster-fabric-settings.md).
* Lär dig hur du [skala ditt kluster in och ut](service-fabric-cluster-scale-up-down.md).
* Lär dig mer om [programuppgraderingar](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
