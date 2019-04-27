---
title: Uppgradera ett Azure Service Fabric-kluster | Microsoft Docs
description: Uppgradera Service Fabric-kod och/eller konfiguration som kör Service Fabric-kluster, inklusive att ställa in kluster uppdateringsläge, uppgradera certifikat, att lägga till programmet portar, göra korrigeringsfiler för operativsystem, och så vidare. Vad kan du förvänta dig när uppgraderingen utförs?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 234bff5049babf0c4b1d036b40201720b2736228
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714735"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Uppgradera Service Fabric-versionen av ett kluster

Utformning av möjligheterna är avgörande för att uppnå långsiktig framgång av din produkt för alla moderna system. Ett Azure Service Fabric-kluster är en resurs som du äger, men delvis hanteras av Microsoft. Den här artikeln beskriver hur du uppgraderar versionen av Service Fabric som körs i din Azure-kluster.

Du kan ställa in klustret för att ta emot automatiska infrastrukturuppgraderingar när de blir tillgängliga av Microsoft eller du kan välja en stöds fabric-versionen som du vill att klustret ska vara på.

Du kan göra detta genom att ange ”upgradeMode” klusterkonfigurationen på portalen eller med hjälp av Resource Manager vid tidpunkten för skapandet eller senare på ett aktivt kluster 

> [!NOTE]
> Se till att hålla ditt kluster som kör en version stöds fabric alltid. Och när vi presentera en ny version av service fabric, markeras den tidigare versionen för support upphör efter 60 dagar efter att minst. De nya versionerna tillkännages [på service fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/). Den nya versionen är tillgänglig för att välja sedan. 
> 
> 

14 dagar före utgången av den versionen som klustret körs en hälsotillståndshändelse genereras som placerar klustret i ett varningshälsotillstånd. Klustret förblir i ett varningstillstånd förrän du har uppgraderat till en version som stöds fabric.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Ange uppgraderingsläget i Azure portal
Du kan ange klustret att automatiskt eller manuellt när du skapar klustret.

![Create_Manualmode][Create_Manualmode]

Du kan ange klustret till automatisk eller manuell när på ett aktivt kluster med hjälp av hantera upplevelsen. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Uppgradera till en ny version av ett kluster som är inställd på manuell läge via portalen.
Om du vill uppgradera till en ny version, allt du behöver göra är väljer du den tillgängliga versionen i listrutan och sparar. Fabric-uppgraderingen hämtar startats automatiskt. Kluster-hälsoprinciper (en kombination av nodhälsan och hälsotillstånd alla program som körs i klustret) följs under uppgraderingen.

Om klustret hälsoprinciper inte uppfylls, återställs uppgraderingen. Rulla ned det här dokumentet om du vill veta mer om hur du anger dessa anpassade hälsoprinciper. 

När du har åtgärdat problemen som resulterade i återställningen måste du initiera uppgraderingen igen genom att följa samma steg som innan.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Ange den Uppgraderingsläge med en Resource Manager-mall
Lägga till ”upgradeMode”-konfiguration för resursdefinitionen Microsoft.ServiceFabric/clusters och ange ”clusterCodeVersion” till någon av versionerna som stöds fabric enligt nedan och därefter distribuera mallen. Giltiga värden för ”upgradeMode” är ”manuell” eller ”automatisk”

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Uppgradera till en ny version av ett kluster som är inställd på manuell läge via Resource Manager-mall.
När klustret är i läget för manuella, för att uppgradera till en ny version, ändra ”clusterCodeVersion” till en version som stöds och distribuera den. Distribution av mallen, sparkar av Fabric-uppgraderingen hämtar startats automatiskt. Kluster-hälsoprinciper (en kombination av nodhälsan och hälsotillstånd alla program som körs i klustret) följs under uppgraderingen.

Om klustret hälsoprinciper inte uppfylls, återställs uppgraderingen.  

När du har åtgärdat problemen som resulterade i återställningen måste du initiera uppgraderingen igen genom att följa samma steg som innan.

## <a name="set-custom-health-polices-for-upgrades"></a>Ange hälsoprinciper anpassade för uppgradering
Du kan ange anpassade hälsoprinciper för uppgraderingen av fabric. Om du har angett ditt kluster till automatisk infrastrukturuppgraderingar kommer dessa principer gäller för den [fas 1 av de automatiska infrastrukturuppgraderingar](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Om du har angett ditt kluster för manuell fabric uppgraderingar, hämta principerna tillämpas varje gång som du väljer en ny version som utlöser systemet för att sätta igång fabric-uppgraderingen i klustret. Om du inte åsidosätter principerna kan används standardvärdena.

Du kan ange anpassade hälsoprinciper eller granska de aktuella inställningarna under bladet ”uppgraderingen av fabric” genom att välja de avancerade inställningarna. Granska följande bild på hur du. 

![Hantera anpassade hälsoprinciper][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Lista över alla tillgängliga versioner för alla miljöer för en viss prenumeration
Kör följande kommando och du bör få utdata som liknar det här.

”supportExpiryUtc” talar om när en viss version upphör att gälla eller har upphört att gälla. Den senaste versionen har inte ett giltigt datum – det har värdet ”9999-12-31T23:59:59.9999999”, vilket innebär bara att utgångsdatumet inte ännu har angetts.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du anpassar några av de [service fabric-kluster fabric-inställningar](service-fabric-cluster-fabric-settings.md)
* Lär dig hur du [skala ditt kluster in och ut](service-fabric-cluster-scale-up-down.md)
* Lär dig mer om [programuppgraderingar](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
