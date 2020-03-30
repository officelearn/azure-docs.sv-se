---
title: Uppgradera versionen av ett fristående kluster
description: Uppgradera Azure Service Fabric-koden som kör ett fristående Service Fabric-kluster.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 489a90180454e2b4a9dad34730fbd3c4f235a2ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598110"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Uppgradera service fabric-versionen som körs i klustret 

För alla moderna system, förmågan att uppgradera är nyckeln till långsiktig framgång för din produkt. Ett Azure Service Fabric-kluster är en resurs som du äger. I den här artikeln beskrivs hur du uppgraderar den version av Service Fabric som körs på ditt fristående kluster.

> [!NOTE]
> Kontrollera att klustret alltid kör en Service Fabric-version som stöds. När Microsoft meddelar att en ny version av Service Fabric släpps är den tidigare versionen markerad för supportens efter minst 60 dagar från datumet för tillkännagivandet. Nya utgåvor tillkännages [på Service Fabric team blogg](https://blogs.msdn.microsoft.com/azureservicefabric/). Den nya versionen är tillgänglig att välja vid den tidpunkten.
>
>

Du kan bara uppgradera klustret till den nya versionen om du använder en nodkonfiguration i produktionsstil, där varje Service Fabric-nod allokeras på en separat fysisk eller virtuell dator. Om du har ett utvecklingskluster, där mer än en Service Fabric-nod finns på en enda fysisk eller virtuell dator, måste du återskapa klustret med den nya versionen.

Två olika arbetsflöden kan uppgradera klustret till den senaste versionen eller en Service Fabric-version som stöds. Ett arbetsflöde är för kluster som har anslutning för att hämta den senaste versionen automatiskt. Det andra arbetsflödet är för kluster som inte har anslutning för att hämta den senaste Service Fabric-versionen.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Aktivera automatisk uppgradering av Service Fabric-versionen av klustret
Om du vill att klustret ska hämta uppdateringar av `fabricClusterAutoupgradeEnabled` Service Fabric när Microsoft släpper en ny version anger du klusterkonfigurationen till *true*. Om du vill att en version av Service Fabric som stöds `fabricClusterAutoupgradeEnabled` ska vara på manuellt anger du klusterkonfigurationen till *false*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Uppgradera kluster som har anslutning för att hämta den senaste koden och konfigurationen
Så här uppgraderar du klustret till en version som stöds om klusternoderna har internetanslutning till [Microsoft Download Center](https://download.microsoft.com).

För kluster som har anslutning till [Microsoft Download Center](https://download.microsoft.com)söker Microsoft regelbundet efter tillgängligheten för nya Service Fabric-versioner.

När en ny Service Fabric-version är tillgänglig hämtas paketet lokalt till klustret och etableras för uppgradering. Dessutom, för att informera kunden om den nya versionen, visar systemet en explicit kluster hälsovarning som liknar följande:

"Det aktuella klusterversionen [version #] -stöd slutar [datum]."

När klustret har kört den senaste versionen försvinner varningen.

När du ser hälsovarningen för klustret uppgraderar du klustret:

1. Anslut till klustret från alla datorer som har administratörsbehörighet till alla datorer som visas som noder i klustret. Datorn som skriptet körs på behöver inte vara en del av klustret.

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

    Du bör få en utdata som liknar denna:

    ![Hämta Service Fabric-versioner][getfabversions]
3. Starta en klusteruppgradering till en tillgänglig version med kommandot [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Om du vill övervaka hur uppgraderingen fortskrider kan du använda Service Fabric Explorer eller köra följande PowerShell-kommando:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Om klusterhälsoprinciperna inte uppfylls återställs uppgraderingen. Om du vill ange anpassade hälsoprinciper för kommandot Start-ServiceFabricClusterUpgrade läser du dokumentationen för [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    När du har åtgärdat problemen som resulterade i återställningen initierar du uppgraderingen igen genom att följa samma steg som tidigare beskrivits.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Uppgradera kluster som inte har *någon anslutning* för att hämta den senaste koden och konfigurationen
Så här uppgraderar du klustret till en version som stöds om klusternoderna inte har internetanslutning till [Microsoft Download Center](https://download.microsoft.com).

> [!NOTE]
> Om du kör ett kluster som inte är anslutet till internet måste du övervaka [Teambloggen för Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) för att lära dig mer om nya versioner. Systemet visar inte en klusterhälsovarning för att varna dig om nya versioner.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatisk etablering jämfört med manuell etablering
Om du vill aktivera automatisk hämtning och registrering för den senaste kodversionen ställer du in servicetjänsten För att uppdatera och registrera den senaste kodversionen. Instruktioner finns i *Verktyg\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* i det [fristående paketet](service-fabric-cluster-standalone-package-contents.md).

För den manuella processen, följ dessa instruktioner.

Ändra klusterkonfigurationen så att följande egenskap ska *false* innan du startar en konfigurationsuppgradering:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Information om användning finns i kommandot [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell. Se till att uppdatera "clusterConfigurationVersion" i din JSON innan du startar konfigurationsuppgraderingen.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Arbetsflöde för klusteruppgradering

1. Kör [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) från en av noderna i klustret och notera *TargetCodeVersion*.

2. Kör följande från en internetansluten dator för att lista alla uppgraderingskompatibla versioner med den aktuella versionen och hämta motsvarande paket från tillhörande nedladdningslänkar:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Anslut till klustret från alla datorer som har administratörsbehörighet till alla datorer som visas som noder i klustret. Datorn som skriptet körs på behöver inte vara en del av klustret.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Kopiera det hämtade paketet till klusteravbildningsarkivet.

5. Registrera det kopierade paketet.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Starta en klusteruppgradering till en tillgänglig version.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer eller köra följande PowerShell-kommando:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Om klusterhälsoprinciperna inte uppfylls återställs uppgraderingen. Om du vill ange anpassade hälsoprinciper för kommandot Start-ServiceFabricClusterUpgrade läser du dokumentationen för [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    När du har åtgärdat problemen som resulterade i återställningen initierar du uppgraderingen igen genom att följa samma steg som tidigare beskrivits.

## <a name="next-steps"></a>Nästa steg
* [Uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md)
* Anpassa vissa [service fabric-klusterinställningar](service-fabric-cluster-fabric-settings.md).
* [Skala klustret in och ut](service-fabric-cluster-scale-up-down.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
