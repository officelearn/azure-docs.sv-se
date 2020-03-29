---
title: Uppgradera ett klusters Azure Service Fabric-version
description: Uppgradera Service Fabric-koden och/eller konfigurationen som kör ett Service Fabric-kluster, inklusive inställning av klusteruppdateringsläge, uppgradering av certifikat, tillägg av programportar, os-korrigeringar och så vidare. Vad kan du förvänta dig när uppgraderingarna utförs?
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: c3ffcbd4296385623ff5e2c1ee001c27598ff3fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451810"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Uppgradera Service Fabric-versionen av ett kluster

För alla moderna system, design för uppgradering är nyckeln till att uppnå långsiktig framgång för din produkt. Ett Azure Service Fabric-kluster är en resurs som du äger, men hanteras delvis av Microsoft. I den här artikeln beskrivs hur du uppgraderar den version av Service Fabric som körs i Azure-klustret.

Du kan ange att klustret ska ta emot automatiska infrastrukturuppgraderingar när de släpps av Microsoft eller så kan du välja en tygversion som stöds som du vill att klustret ska vara på.

Du gör detta genom att ställa in klusterkonfigurationen "upgradeMode" på portalen eller använda Resource Manager när du skapas eller senare i ett live-kluster 

> [!NOTE]
> Se till att hålla klustret kör en tygversion som stöds alltid. Om och när vi tillkännager lanseringen av en ny version av servicetyg, är den tidigare versionen markerad för slutet av support efter minst 60 dagar från det datumet. De nya utgåvorna tillkännages [på tjänsten tyg team blogg](https://blogs.msdn.microsoft.com/azureservicefabric/). Den nya versionen är tillgänglig att välja då. 
> 
> 

14 dagar innan frisläppningen som klustret körs genereras en hälsohändelse som placerar klustret i ett hälsotillstånd för varning. Klustret förblir i ett varningstillstånd tills du uppgraderar till en fabric-version som stöds.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Ange uppgraderingsläge i Azure-portalen
Du kan ställa in klustret till automatiskt eller manuellt när du skapar klustret.

![Create_Manualmode][Create_Manualmode]

Du kan ställa in klustret till automatiskt eller manuellt när det finns i ett live-kluster med hjälp av hanteringsupplevelsen. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Uppgradera till en ny version i ett kluster som är inställt på manuellt läge via portalen.
Om du vill uppgradera till en ny version behöver du bara välja den tillgängliga versionen i listrutan och spara. Fabric-uppgraderingen sparkas automatiskt. Klusterhälsoprinciperna (en kombination av nodhälsa och hälsotillstånd som alla program som körs i klustret) följs under uppgraderingen.

Om klusterhälsoprinciperna inte uppfylls återställs uppgraderingen. Bläddra ned i det här dokumentet om du vill läsa mer om hur du ställer in de anpassade hälsoprinciperna. 

När du har åtgärdat de problem som resulterade i återställningen måste du initiera uppgraderingen igen genom att följa samma steg som tidigare.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Ange uppgraderingsläge med hjälp av en Resource Manager-mall
Lägg till konfigurationen "upgradeMode" i microsoft.ServiceFabric/clusters-resursdefinitionen och ange "clusterCodeVersion" i en av de fabric-versioner som stöds enligt nedan och distribuera sedan mallen. De giltiga värdena för "upgradeMode" är "Manuell" eller "Automatisk"

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Uppgradera till en ny version i ett kluster som är inställt på manuellt läge via en Resource Manager-mall.
När klustret är i manuellt läge ändrar du "clusterCodeVersion" till en version som stöds och distribuerar den om du vill uppgradera till en ny version. Distributionen av mallen, sparkar av Fabric uppgraderingen blir sparkad igång automatiskt. Klusterhälsoprinciperna (en kombination av nodhälsa och hälsotillstånd som alla program som körs i klustret) följs under uppgraderingen.

Om klusterhälsoprinciperna inte uppfylls återställs uppgraderingen.  

När du har åtgärdat de problem som resulterade i återställningen måste du initiera uppgraderingen igen genom att följa samma steg som tidigare.

## <a name="set-custom-health-polices-for-upgrades"></a>Ange anpassade hälsopoliser för uppgraderingar
Du kan ange anpassade hälso-poliser för tyg uppgradering. Om du har ställt in klustret på Automatiska infrastrukturuppgraderingar tillämpas dessa principer på [fas 1 av de automatiska infrastrukturuppgraderingarna](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Om du har ställt in klustret för manuella infrastrukturuppgraderingar tillämpas dessa principer varje gång du väljer en ny version som utlöser systemet för att starta infrastrukturuppgraderingen i klustret. Om du inte åsidosätter principerna används standardinställningarna.

Du kan ange anpassade hälsoprinciper eller granska de aktuella inställningarna under bladet "tyguppgradering" genom att välja de avancerade uppgraderingsinställningarna. Läs igenom följande bild om hur man gör. 

![Hantera anpassade hälsoprinciper][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Lista alla tillgängliga versioner för alla miljöer för en viss prenumeration
Kör följande kommando och du bör få en utdata som liknar detta.

"supportExpiryUtc" talar om för dig när en viss version löper ut eller har gått ut. Den senaste utgåvan har inte ett giltigt datum - den har ett värde på "9999-12-31T23:59:59.99999999", vilket bara innebär att utgångsdatumet ännu inte är satt.

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
* Lär dig hur du anpassar några av inställningarna för [klusterinfrastruktur för serviceinfrastruktur](service-fabric-cluster-fabric-settings.md)
* Lär dig hur du [skalar klustret in och ut](service-fabric-cluster-scale-up-down.md)
* Läs mer om [programuppgraderingar](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
