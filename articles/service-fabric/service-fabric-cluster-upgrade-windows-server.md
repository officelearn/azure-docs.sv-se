---
title: Uppgradera en fristående Azure Service Fabric-kluster i Windows Server | Microsoft Docs
description: Uppgradera Azure Service Fabric-kod och/eller konfiguration som kan köras fristående Service Fabric-kluster, inklusive att ställa in uppdateringsläget för klustret.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 93b79b7adacdec18912d28bb9725e2dc77737d59
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43050771"
---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Uppgradera fristående Azure Service Fabric-kluster på Windows Server 
> [!div class="op_single_selector"]
> * [Azure-kluster](service-fabric-cluster-upgrade.md)
> * [Fristående klustret](service-fabric-cluster-upgrade-windows-server.md)
>
>

Möjlighet att uppgradera är nyckeln till din produkt långsiktig framgång för alla moderna system. Ett Azure Service Fabric-kluster är en resurs som du äger. Den här artikeln beskrivs hur du kan kontrollera att klustret alltid körs versioner av Service Fabric-kod och konfigurationer som stöds.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Kontrollera Service Fabric-version som körs på klustret
Om du vill ange att hämta uppdateringar av Service Fabric när Microsoft publicerar en ny version, inställd klusterkonfigurationen fabricClusterAutoupgradeEnabled *SANT*. Om du vill välja en version som stöds av Service Fabric som du vill att klustret ska vara på inställd klusterkonfigurationen fabricClusterAutoupgradeEnabled *FALSKT*.

> [!NOTE]
> Kontrollera att klustret alltid körs en Service Fabric-version som stöds. När Microsoft presenterar lanseringen av en ny version av Service Fabric, markeras den tidigare versionen för support upphör när du har minst 60 dagar från datumet då meddelandet. Nya versioner tillkännages [på Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/). Den nya versionen är kan väljas vid den tidpunkten.
>
>

Du kan uppgradera klustret till den nya versionen bara om du använder en nodkonfiguration för produktion-format, där varje Service Fabric-nod har allokerats på en separat fysisk eller virtuell dator. Om du har ett kluster för utveckling, där mer än en Service Fabric-noden är på en enda fysisk eller virtuell dator, måste du återskapa klustret med den nya versionen.

Två olika arbetsflöden kan uppgradera klustret till den senaste versionen eller en Service Fabric-version som stöds. Ett arbetsflöde är för kluster som är ansluten till den senaste versionen automatiskt. Andra arbetsflödet är för kluster som inte har någon anslutning till att hämta den senaste versionen av Service Fabric.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Uppgradera kluster som har anslutning att hämta senaste kod och konfiguration
Följ dessa steg för att uppgradera klustret till en version som stöds om klusternoderna är anslutna till internet till den [Microsoft Download Center](http://download.microsoft.com).

För kluster som är ansluten till den [Microsoft Download Center](http://download.microsoft.com), Microsoft söker regelbundet efter tillgängligheten för nya Service Fabric-versioner.

När en ny Service Fabric-version är tillgänglig, har paketet laddat ned lokalt till klustret och etableras för uppgradering. Dessutom visar systemet för att informera kunden om den nya versionen, en explicit kluster hälsotillstånd varning som liknar följande:

”Den aktuella klusterversionen [version #] stödet upphör [date]”.

När klustret kör den senaste versionen, försvinner varningen.

#### <a name="cluster-upgrade-workflow"></a>Arbetsflöde för uppgradering av kluster
När klustret hälsotillstånd varning visas gör du följande:

1. Ansluta till klustret från valfri dator som har administratörsåtkomst till alla datorer som listas som noder i klustret. Den dator som skriptet körs på har inte ingå i klustret.

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

2. Hämta listan över Service Fabric-versioner som du kan uppgradera till.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Du bör få utdata som liknar detta:

    ![Hämta Service Fabric-versioner][getfabversions]
3. Starta en uppgradering av klustret till en tillgänglig version med hjälp av den [Start ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell-kommando.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Du kan använda Service Fabric Explorer eller kör följande PowerShell-kommando för att övervaka förloppet för uppgraderingen:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Uppgraderingen återställs om hälsoprinciper klustret inte uppfylls. Om du vill ange anpassade hälsoprinciper för kommandot Start-ServiceFabricClusterUpgrade finns i dokumentationen för [Start ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    När du har åtgärdat problemen som resulterade i återställningen kan du initiera uppgraderingen igen genom att följa samma steg som tidigare beskrivna.

### <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Uppgradera kluster som har *ingen anslutning* att ladda ned den senaste kod och konfiguration
Följ dessa steg för att uppgradera klustret till en version som stöds om klusternoderna inte har Internetanslutning för att den [Microsoft Download Center](http://download.microsoft.com).

> [!NOTE]
> Om du kör ett kluster som inte är ansluten till internet, måste du övervaka Service Fabric-teamets blogg vill veta mer om nya versioner. Systemet visar inte en varning visas hälsotillstånd kluster för att varna dig om nya versioner.  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatisk etablering jämfört med manuella etablering
Om du vill aktivera automatisk överföring och registrering för den senaste versionen av koden, ställa in Service Fabric Update-tjänst. Instruktioner finns i Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt inuti den [fristående paketet](service-fabric-cluster-standalone-package-contents.md).
Följ dessa instruktioner för manuell process.

Ändra din klusterkonfiguration för att ange följande egenskap för *FALSKT* innan du påbörjar en uppgradering av konfiguration:

        "fabricClusterAutoupgradeEnabled": false,

Mer information om användning finns i den [Start ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell-kommando. Se till att uppdatera clusterConfigurationVersion om du i din JSON innan du börjar uppgradera konfigurationen.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Arbetsflöde för uppgradering av kluster

1. Kör [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) från någon av noderna i klustret och anteckna den *TargetCodeVersion*.

2. Kör följande från en Internetansluten dator att lista alla uppgraderingen-kompatibla versioner med den aktuella versionen och hämta motsvarande paketet från de associera länkarna:

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Ansluta till klustret från valfri dator som har administratörsåtkomst till alla datorer som listas som noder i klustret. Den dator som skriptet körs på har inte ingå i klustret.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. Kopiera det Hämta paketet till avbildningsarkivet kluster.

5. Registrera kopierade paketet.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Starta en uppgradering av klustret till en tillgänglig version.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer eller så kan du köra följande PowerShell-kommando:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Uppgraderingen återställs om hälsoprinciper klustret inte uppfylls. Om du vill ange anpassade hälsoprinciper för kommandot Start-ServiceFabricClusterUpgrade finns i dokumentationen för [Start ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    När du har åtgärdat problemen som resulterade i återställningen kan du initiera uppgraderingen igen genom att följa samma steg som tidigare beskrivna.


## <a name="upgrade-the-cluster-configuration"></a>Uppgradera klusterkonfigurationen
Innan du startar uppgraderingen konfiguration kan du testa nya klusterkonfigurationen JSON genom att köra följande PowerShell-skript i det fristående paketet:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
Eller Använd det här skriptet:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

Vissa konfigurationer kan inte uppgraderas, till exempel slutpunkter, klusternamnet och nod-IP, osv. Den nya klusterkonfigurationen JSON har testats mot den gamla servern och genererar fel i PowerShell-fönstret om det uppstår problem.

Om du vill uppgradera configuration klusteruppgraderingen kör [Start ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Konfiguration av uppgraderingen är bearbetade uppgraderingsdomän per uppgraderingsdomän.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Certifikatet config uppgradering av kluster  
Ett klustercertifikat används för autentisering mellan klusternoderna. Förnya certifikatet ska utföras med extra försiktig eftersom fel blockerar kommunikation mellan klusternoder.

Tekniskt sett stöds fyra alternativen:  

* Enkel certifikatuppgraderingen: sökvägen för uppgraderingen är certifikat (primär) -> certifikatet B (primär) -> certifikatet C (primär) ->...

* Dubbla certifikatuppgraderingen: sökvägen för uppgraderingen är certifikat (primär) -> certifikat (primär) och B (sekundär) -> certifikatet B (primär) > certifikatet B (primär) och C (sekundär) -> certifikatet C (primär) ->...

* Av Typuppgradering av certifikat: tumavtryck för certifikat configuration <>--Vanligtnamn-baserade Certifikatkonfiguration. Till exempel certifikatets tumavtryck (primär) och tumavtryck B (sekundär) -> certifikatet CommonName C.

* Utfärdaren tumavtryck för uppgradering av certifikat: sökvägen för uppgraderingen är certifikat-CN = A, IssuerThumbprint = IT1 (primär) certifikat-CN -> = A, IssuerThumbprint = IT1 IT2 (primär) certifikat-CN -> = A, IssuerThumbprint = IT2 (primär).


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du anpassar några [inställningar för Service Fabric-klustret](service-fabric-cluster-fabric-settings.md).
* Lär dig hur du [skala ditt kluster in och ut](service-fabric-cluster-scale-up-down.md).
* Lär dig mer om [programuppgraderingar](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
