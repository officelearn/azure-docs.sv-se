---
title: Konfigurera uppgraderingen av ett Service Fabric-program | Microsoft Docs
description: "Lär dig hur du konfigurerar inställningar för att uppgradera ett Service Fabric-program med hjälp av Microsoft Visual Studio."
services: service-fabric
documentationcenter: na
author: mikkelhegn
manager: mfussell
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/29/2017
ms.author: mikkelhegn
ms.openlocfilehash: 314b29a56e4651222822f40a116af97a7372ff2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurera uppgraderingen av ett Service Fabric-program i Visual Studio
Visual Studio tools för Azure Service Fabric stödja uppgradering för publicering till lokal eller fjärransluten kluster. Det finns tre scenarier som du vill uppgradera ditt program till en nyare version i stället för att ersätta program under testning och felsökning:

* Programmet data förloras inte under uppgraderingen.
* Tillgänglighet fortfarande hög så det finns inte några avbrott i tjänsten under uppgraderingen, om det finns tillräckligt med instanser av tjänsten sprids uppgraderingsdomäner.
* Du kan köra testerna mot ett program medan den uppgraderas.

## <a name="parameters-needed-to-upgrade"></a>Parametrar som behövs för att uppgradera
Du kan välja mellan två typer av distribution: regelbundna eller uppgradering. En vanlig distribution raderas alla tidigare distributionsinformation och data på klustret, medan en uppgradering distribution bevarar den. Du måste ange parametrar för uppgradering av program och hälsa kontrollera principer när du uppgraderar ett Service Fabric-program i Visual Studio. Uppgradera parametrar för program för att förhindra uppgraderingen vid kontroll av hälsoprinciper fastställa om uppgraderingen lyckades. Se [uppgradering av Service Fabric-programmet: Uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md) för mer information.

Det finns tre uppgradera lägen: *övervakade*, *UnmonitoredAuto*, och *UnmonitoredManual*.

* En uppgradering av övervakade automatiserar uppgraderingen och hälsokontrollen för programmet.
* Uppgradering UnmonitoredAuto automatiserar uppgraderingen, men hoppar över hälsokontrollen för programmet.
* När du gör en uppgradering UnmonitoredManual som du behöver uppgradera manuellt varje domän.

Varje Uppgraderingsläge kräver olika uppsättningar med parametrar. Se [uppgradera applikationsparametrarna](service-fabric-application-upgrade-parameters.md) vill veta mer om alternativen för uppgradering.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Uppgradera ett Service Fabric-program i Visual Studio
Om du använder Visual Studio Service Fabric-verktyg för att uppgradera ett Service Fabric-program kan du ange en Publicera process för att vara en uppgradering i stället för en vanlig distribution genom att kontrollera den **uppgradera programmet** kryssrutan.

### <a name="to-configure-the-upgrade-parameters"></a>Att konfigurera parametrar för uppgradering
1. Klicka på den **inställningar** knappen bredvid kryssrutan. Den **redigera uppgradera parametrar** dialogrutan visas. Den **redigera uppgradera parametrar** dialogrutan stöder uppgradering lägen övervakade, UnmonitoredAuto och UnmonitoredManual.
2. Välj Uppgraderingsläge som du vill använda och sedan fylla i parametern rutnätet.

    Varje parameter har standardvärden. Den valfria parametern *DefaultServiceTypeHealthPolicy* tar en hash-tabell inmatning. Här är ett exempel på hash-tabell Indataformatet för *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* är en annan valfri parameter som tar en hash-tabell indata i följande format:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Här är en verklig exempel:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Om du väljer UnmonitoredManual Uppgraderingsläge, måste du starta en PowerShell-konsol för att fortsätta och Slutför uppgraderingen manuellt. Referera till [uppgradering av Service Fabric-programmet: avancerade alternativ](service-fabric-application-upgrade-advanced.md) att lära dig hur manuell uppgradering fungerar.

## <a name="upgrade-an-application-by-using-powershell"></a>Uppgradera ett program med hjälp av PowerShell
Du kan använda PowerShell-cmdlets för att uppgradera ett Service Fabric-program. Se [Service Fabric uppgradera självstudien](service-fabric-application-upgrade-tutorial.md) och [Start ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) detaljerad information.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Ange en hälsoprincip för kontrollen i programmanifestfilen
Varje tjänst i ett Service Fabric-program kan ha sin egen parametrar för hälsotillstånd som åsidosätter standardinställningarna. Du kan ange dessa parametervärden i programmanifestfilen.

I följande exempel visar hur du kan tillämpa en princip för kontroll av unikt hälsotillstånd för varje tjänst i programmanifestet.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Nästa steg
Mer information om hur du distribuerar ett program finns [distribuera ett befintligt program i Azure Service Fabric](service-fabric-deploy-existing-app.md).