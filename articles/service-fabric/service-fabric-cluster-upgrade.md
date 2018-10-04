---
title: Uppgradera ett Azure Service Fabric-kluster | Microsoft Docs
description: Uppgradera Service Fabric-kod och/eller konfiguration som kör Service Fabric-kluster, inklusive att ställa in kluster uppdateringsläge, uppgradera certifikat, att lägga till programmet portar, göra korrigeringsfiler för operativsystem, och så vidare. Vad kan du förvänta dig när uppgraderingen utförs?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/10/2017
ms.author: aljo
ms.openlocfilehash: 2fd62f8709bddfd981f4b1358c97d0acbaf7f12d
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269114"
---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Uppgradera ett Azure Service Fabric-kluster
> [!div class="op_single_selector"]
> * [Azure-kluster](service-fabric-cluster-upgrade.md)
> * [Fristående kluster](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Utformning av möjligheterna är avgörande för att uppnå långsiktig framgång av din produkt för alla moderna system. Ett Azure Service Fabric-kluster är en resurs som du äger, men delvis hanteras av Microsoft. Den här artikeln beskriver vad hanteras automatiskt och vad du kan konfigurera själv.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Kontrollera den fabric-versionen som körs på klustret
Du kan ställa in klustret för att ta emot automatiska infrastrukturuppgraderingar när de blir tillgängliga av Microsoft eller du kan välja en stöds fabric-versionen som du vill att klustret ska vara på.

Du kan göra detta genom att ange ”upgradeMode” klusterkonfigurationen på portalen eller med hjälp av Resource Manager vid tidpunkten för skapandet eller senare på ett aktivt kluster 

> [!NOTE]
> Se till att hålla ditt kluster som kör en version stöds fabric alltid. Och när vi presentera en ny version av service fabric, markeras den tidigare versionen för support upphör efter 60 dagar efter att minst. De nya versionerna tillkännages [på service fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/). Den nya versionen är tillgänglig för att välja sedan. 
> 
> 

14 dagar före utgången av den versionen som klustret körs en hälsotillståndshändelse genereras som placerar klustret i ett varningshälsotillstånd. Klustret förblir i ett varningstillstånd förrän du har uppgraderat till en version som stöds fabric.

### <a name="setting-the-upgrade-mode-via-portal"></a>Ange Uppgraderingsläge via portalen
Du kan ange klustret att automatiskt eller manuellt när du skapar klustret.

![Create_Manualmode][Create_Manualmode]

Du kan ange klustret till automatisk eller manuell när på ett aktivt kluster med hjälp av hantera upplevelsen. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Uppgradera till en ny version av ett kluster som är inställd på manuell läge via portalen.
Om du vill uppgradera till en ny version, allt du behöver göra är väljer du den tillgängliga versionen i listrutan och sparar. Fabric-uppgraderingen hämtar startats automatiskt. Kluster-hälsoprinciper (en kombination av nodhälsan och hälsotillstånd alla program som körs i klustret) följs under uppgraderingen.

Om klustret hälsoprinciper inte uppfylls, återställs uppgraderingen. Rulla ned det här dokumentet om du vill veta mer om hur du anger dessa anpassade hälsoprinciper. 

När du har åtgärdat problemen som resulterade i återställningen måste du initiera uppgraderingen igen genom att följa samma steg som innan.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Ange Uppgraderingsläge via Resource Manager-mall
Lägga till ”upgradeMode”-konfiguration för resursdefinitionen Microsoft.ServiceFabric/clusters och ange ”clusterCodeVersion” till någon av versionerna som stöds fabric enligt nedan och därefter distribuera mallen. Giltiga värden för ”upgradeMode” är ”manuell” eller ”automatisk”

![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Uppgradera till en ny version av ett kluster som är inställd på manuell läge via Resource Manager-mall.
När klustret är i läget för manuella, för att uppgradera till en ny version, ändra ”clusterCodeVersion” till en version som stöds och distribuera den. Distribution av mallen, sparkar av Fabric-uppgraderingen hämtar startats automatiskt. Kluster-hälsoprinciper (en kombination av nodhälsan och hälsotillstånd alla program som körs i klustret) följs under uppgraderingen.

Om klustret hälsoprinciper inte uppfylls, återställs uppgraderingen. Rulla ned det här dokumentet om du vill veta mer om hur du anger dessa anpassade hälsoprinciper. 

När du har åtgärdat problemen som resulterade i återställningen måste du initiera uppgraderingen igen genom att följa samma steg som innan.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Hämta lista över alla tillgängliga versionen för alla miljöer för en viss prenumeration
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

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Fabric-uppgraderingar när klustret uppgradera läge är automatisk
Microsoft har fabric-kod och konfiguration som körs i ett Azure-kluster. Vi utföra automatiska övervakade uppgraderingar av programvaran som det behövs. Uppgraderingarna kan kod, konfiguration, eller båda. För att säkerställa att ditt program har ingen inverkan eller minimal påverkan på grund av dessa uppdateringar, uppgraderar vi i följande faser:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fas 1: En uppgradering utförs med hjälp av alla hälsoprinciper för kluster
Under den här fasen uppgraderingarna fortsätter en uppgraderingsdomän i taget och de program som körs i klustret fortsätter att köras utan någon avbrottstid. Kluster-hälsoprinciper (en kombination av nodhälsan och hälsotillstånd alla program som körs i klustret) följs under uppgraderingen.

Om klustret hälsoprinciper inte uppfylls, återställs uppgraderingen. Sedan skickas ett e-postmeddelande till Prenumerationens ägare. E-postmeddelandet innehåller följande information:

* Meddelande om att vi var tvungen att återställa en uppgradering av klustret.
* Föreslås korrigerande åtgärder, om sådana.
* Antal dagar (n) tills vi kör fas 2.

Vi försöker köra samma uppgradering några gånger om eventuella uppgraderingar misslyckades av orsaker som infrastruktur. Efter n dagar från det datum då e-postmeddelandet har skickats, vi gå vidare till fas 2.

Om klustret hälsoprinciper uppfylls som genomförd uppgraderingen och markerat som slutförda. Detta kan inträffa under den första uppgraderingen eller någon av uppgradering repriser i det här steget. Det finns inga e-postbekräftelse på en lyckad körning. Det här är att undvika att skicka dig för många e-postmeddelanden--e-post som bör ses som ett undantag till normalt läge. Vi räknar med de flesta av klusteruppgradering ska lyckas utan att påverka programmets tillgänglighet.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fas 2: En uppgradering utförs med hjälp av standard hälsoprinciper endast
Hälsoprinciper i den här fasen är inställda så att antalet program som har felfri i början av uppgraderingen är densamma under uppgraderingsprocessen. Fas 2-uppgraderingar fortsätter en uppgraderingsdomän i taget som i fas 1, och de program som körs i klustret fortsätter att köras utan någon avbrottstid. Kluster-hälsoprinciper (en kombination av nodhälsan och hälsotillstånd alla program som körs i klustret) följs under uppgraderingen.

Om klustret hälsoprinciper gäller inte uppfylls, återställs uppgraderingen. Sedan skickas ett e-postmeddelande till Prenumerationens ägare. E-postmeddelandet innehåller följande information:

* Meddelande om att vi var tvungen att återställa en uppgradering av klustret.
* Föreslås korrigerande åtgärder, om sådana.
* Antal dagar (n) tills vi kör fas 3.

Vi försöker köra samma uppgradering några gånger om eventuella uppgraderingar misslyckades av orsaker som infrastruktur. En e-postmeddelande skickas ett par dagar innan n dagar är igång. Vi fortsätter du till fas 3 efter n dagar från det datum då det e-postmeddelandet skickades. E-postmeddelanden som vi skickar dig i fas 2 måste du vara noggrann allvarligt och måste du vidta korrigerande åtgärder.

Om klustret hälsoprinciper uppfylls som genomförd uppgraderingen och markerat som slutförda. Detta kan inträffa under den första uppgraderingen eller någon av uppgradering repriser i det här steget. Det finns inga e-postbekräftelse på en lyckad körning.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fas 3: En uppgradering utförs med hjälp av aggressiva hälsoprinciper
Dessa hälsoprinciper i den här fasen är avsedda för uppgraderingen är färdig i stället för hälsotillståndet för programmen. Mycket få klusteruppgradering som hamnar i det här steget. Om ditt kluster kommer till den här fasen, är det troligt att ditt program blir ohälsosamt och/eller förlora tillgänglighet.

Liknar de andra två faserna, fas 3 uppgraderingar fortsätta en uppgraderingsdomän i taget.

Om klustret hälsoprinciper inte uppfylls, återställs uppgraderingen. Vi försöker köra samma uppgradering några gånger om eventuella uppgraderingar misslyckades av orsaker som infrastruktur. Efter det fästs klustret, så att den får inte längre stöd och/eller uppgraderingar.

Ett e-postmeddelande med den här informationen skickas till Prenumerationens ägare, tillsammans med de korrigerande åtgärderna. Vi förväntar oss inte alla kluster komma in i ett tillstånd där fas 3 har misslyckats.

Om klustret hälsoprinciper uppfylls som genomförd uppgraderingen och markerat som slutförda. Detta kan inträffa under den första uppgraderingen eller någon av uppgradering repriser i det här steget. Det finns inga e-postbekräftelse på en lyckad körning.

## <a name="cluster-configurations-that-you-control"></a>Klusterkonfigurationer som du kontrollerar
Utöver möjligheten att ange kluster Uppgraderingsläge, här är de konfigurationer som du kan ändra på ett aktivt kluster.

### <a name="certificates"></a>Certifikat
Du kan lägga till nya eller ta enkelt bort certifikaten för klustret och klienten via portalen. Referera till [det här dokumentet för detaljerade anvisningar](service-fabric-cluster-security-update-certs-azure.md)

![Skärmbild som visar certifikattumavtryck i Azure-portalen.][CertificateUpgrade]

### <a name="application-ports"></a>Programportar
Du kan ändra programportar genom att ändra belastningsutjämnaren resursegenskaper som är associerade med nodtyp. Du kan använda portalen eller du kan använda PowerShell och Resource Manager direkt.

För att öppna en ny port på alla virtuella datorer i en nodtyp, gör du följande:

1. Lägg till en ny avsökning i lämplig belastningsutjämnare.
   
    Om du har distribuerat ditt kluster med hjälp av portalen namnges belastningsutjämnarna ”LB-namnet på resursen grupp-NodeTypename”, en för varje nodtyp. Eftersom load balancer namnen är unika endast inom en resursgrupp, är det bäst om du söker efter dem under en viss resursgrupp.
   
    ![Skärmbild som visar att lägga till en avsökning i en belastningsutjämnare i portalen.][AddingProbes]
2. Lägg till en ny regel till belastningsutjämnaren.
   
    Lägg till en ny regel i samma belastningsutjämnare med avsökningen som du skapade i föregående steg.
   
    ![Lägger till en ny regel till en belastningsutjämnare i portalen.][AddingLBRules]

### <a name="placement-properties"></a>Placeringsegenskaper
För var och en nodtyperna kan du lägga till anpassade placeringsegenskaper som du vill använda i dina program. NodeType är en standardegenskap som du kan använda utan att lägga till den uttryckligen.

> [!NOTE]
> Mer information om användning av placeringsbegränsningar nodegenskaper och hur du definierar dem finns i avsnittet ”placering begränsningar och nodegenskaper” i Service Fabric Cluster Resource Manager-dokumentet på [som beskriver ditt kluster](service-fabric-cluster-resource-manager-cluster-description.md).
> 
> 

### <a name="capacity-metrics"></a>Kapacitet mått
Du kan lägga till anpassade kapacitetsmått som du vill använda i dina program att rapportera belastning för var och en nodtyperna. Information om användning av kapacitet att rapportera belastning, i Service Fabric Cluster Resource Manager-dokument på [som beskriver ditt kluster](service-fabric-cluster-resource-manager-cluster-description.md) och [mått och Load](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Inställningar för fabric - hälsoprinciper
Du kan ange anpassade hälsoprinciper för uppgraderingen av fabric. Om du har angett ditt kluster till automatisk infrastrukturuppgraderingar, hämta principerna tillämpas på fas 1 av de automatiska infrastrukturuppgraderingar.
Om du har angett ditt kluster för manuell fabric uppgraderingar, hämta principerna tillämpas varje gång som du väljer en ny version som utlöser systemet för att sätta igång fabric-uppgraderingen i klustret. Om du inte åsidosätter principerna kan används standardvärdena.

Du kan ange anpassade hälsoprinciper eller granska de aktuella inställningarna under bladet ”uppgraderingen av fabric” genom att välja de avancerade inställningarna. Granska följande bild på hur du. 

![Hantera anpassade hälsoprinciper][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Anpassa infrastrukturinställningarna för klustret
Referera till [service fabric-kluster infrastrukturinställningarna](service-fabric-cluster-fabric-settings.md) på vad och hur du kan anpassa dem.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Korrigeringsfiler för operativsystem på de virtuella datorer som ingår i klustret
Referera till [Patch Orchestration Application](service-fabric-patch-orchestration-application.md) som kan distribueras på klustret för att installera uppdateringar från Windows Update på ett dirigerat sätt, att behålla tjänsterna som är tillgänglig hela tiden. 

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>OS-uppgraderingar på de virtuella datorer som ingår i klustret
Om du måste uppgradera den OS-avbildningen på de virtuella datorerna i klustret, måste du göra det en virtuell dator i taget. Du ansvarar för den här uppgraderingen--det finns för närvarande ingen automatisering för detta.

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
