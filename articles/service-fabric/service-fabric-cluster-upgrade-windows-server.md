---
title: Uppgraderar versionen av ett fristående Azure Service Fabric-kluster | Microsoft Docs
description: Uppgradera Azure Service Fabric-kod som kör ett fristående Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 29d034be5999d0bc3f0a244cfa7a5658a4ecce32
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661470"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Uppgradera Service Fabric-versionen som körs på klustret 

Möjlighet att uppgradera är nyckeln till din produkt långsiktig framgång för alla moderna system. Ett Azure Service Fabric-kluster är en resurs som du äger. Den här artikeln beskriver hur du uppgraderar versionen av Service Fabric som körs på ditt fristående kluster.

> [!NOTE]
> Kontrollera att klustret alltid körs en Service Fabric-version som stöds. När Microsoft presenterar lanseringen av en ny version av Service Fabric, markeras den tidigare versionen för support upphör när du har minst 60 dagar från datumet då meddelandet. Nya versioner tillkännages [på Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/). Den nya versionen är kan väljas vid den tidpunkten.
>
>

Du kan uppgradera klustret till den nya versionen bara om du använder en nodkonfiguration för produktion-format, där varje Service Fabric-nod har allokerats på en separat fysisk eller virtuell dator. Om du har ett kluster för utveckling, där mer än en Service Fabric-noden är på en enda fysisk eller virtuell dator, måste du återskapa klustret med den nya versionen.

Två olika arbetsflöden kan uppgradera klustret till den senaste versionen eller en Service Fabric-version som stöds. Ett arbetsflöde är för kluster som är ansluten till den senaste versionen automatiskt. Andra arbetsflödet är för kluster som inte har någon anslutning till att hämta den senaste versionen av Service Fabric.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Aktivera automatiska uppgraderingar för Service Fabric-versionen av ditt kluster
Att ställa in klustret för att hämta uppdateringar av Service Fabric när Microsoft publicerar en ny version i `fabricClusterAutoupgradeEnabled` klusterkonfigurationen och *SANT*. För att manuellt välja en version av Service Fabric som du vill att klustret ska vara på stöds, ange den `fabricClusterAutoupgradeEnabled` klusterkonfigurationen och *FALSKT*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Uppgradera kluster som har anslutning att hämta senaste kod och konfiguration
Följ dessa steg för att uppgradera klustret till en version som stöds om klusternoderna är anslutna till internet till den [Microsoft Download Center](https://download.microsoft.com).

För kluster som är ansluten till den [Microsoft Download Center](https://download.microsoft.com), Microsoft söker regelbundet efter tillgängligheten för nya Service Fabric-versioner.

När en ny Service Fabric-version är tillgänglig, har paketet laddat ned lokalt till klustret och etableras för uppgradering. Dessutom visar systemet för att informera kunden om den nya versionen, en explicit kluster hälsotillstånd varning som liknar följande:

”Den aktuella klusterversionen [version #] stödet upphör [date]”.

När klustret kör den senaste versionen, försvinner varningen.

Uppgradera klustret när klustret hälsotillstånd varning visas:

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

    ```powershell
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

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Uppgradera kluster som har *ingen anslutning* att ladda ned den senaste kod och konfiguration
Följ dessa steg för att uppgradera klustret till en version som stöds om klusternoderna inte har Internetanslutning för att den [Microsoft Download Center](https://download.microsoft.com).

> [!NOTE]
> Om du kör ett kluster som inte är ansluten till internet, måste du övervaka den [Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/) att lära dig om nya versioner. Systemet visar inte en varning visas hälsotillstånd kluster för att varna dig om nya versioner.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatisk etablering jämfört med manuella etablering
Om du vill aktivera automatisk överföring och registrering för den senaste versionen av koden, ställa in Service Fabric Update-tjänst. Anvisningar finns i *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* i den [fristående paketet](service-fabric-cluster-standalone-package-contents.md).

Följ dessa instruktioner för manuell process.

Ändra din klusterkonfiguration för att ange följande egenskap för *FALSKT* innan du påbörjar en uppgradering av konfiguration:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Mer information om användning finns i den [Start ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell-kommando. Se till att uppdatera clusterConfigurationVersion om du i din JSON innan du börjar uppgradera konfigurationen.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Arbetsflöde för uppgradering av kluster

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

    ```powershell
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

## <a name="next-steps"></a>Nästa steg
* [Uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md)
* Anpassa vissa [inställningar för Service Fabric-klustret](service-fabric-cluster-fabric-settings.md).
* [Skala ditt kluster in och ut](service-fabric-cluster-scale-up-down.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
