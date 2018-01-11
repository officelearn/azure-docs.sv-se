---
title: Uppgradera en Azure Service Fabric-kluster | Microsoft Docs
description: "Uppgradera Service Fabric-kod och/eller konfiguration som kör ett Service Fabric-kluster, inklusive inställning klustret uppdateringsläge, uppgradera certifikat, lägga till programmet portar, göra operativsystemskorrigeringar, och så vidare. Vad kan du förvänta dig när uppgraderingen utförs?"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/10/2017
ms.author: chackdan
ms.openlocfilehash: 7ea71ab891583c51b3c07a4d0a9f0b4f54e56669
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Uppgradera en Azure Service Fabric-kluster
> [!div class="op_single_selector"]
> * [Azure-kluster](service-fabric-cluster-upgrade.md)
> * [Fristående kluster](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

För alla moderna system är designa för möjligheterna avgörande för att uppnå långsiktig framgång för en produkt. Ett Azure Service Fabric-kluster är en resurs som du äger, men delvis hanteras av Microsoft. Den här artikeln beskriver vad hanteras automatiskt och vad du kan konfigurera själv.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Kontrollera fabric-versionen som körs på klustret
Du kan ange att ta emot automatiska fabric uppgraderingar som Microsoft släpper eller så kan du välja en stöds fabric-versionen som du vill att klustret ska finnas på klustret.

Du kan göra detta genom att ange klusterkonfigurationen ”upgradeMode” på portalen eller med hjälp av hanteraren för filserverresurser vid tidpunkten för skapandet eller senare på ett aktivt kluster 

> [!NOTE]
> Se till att hålla ditt kluster som kör en version stöds fabric alltid. Och när vi meddela lanseringen av en ny version av service fabric, markeras den tidigare versionen för slutet av stödet efter 60 dagar från det datum då minst. den nya versioner tillkännages [på service fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/). Den nya versionen är tillgängligt och välj sedan. 
> 
> 

14 dagar före utgången av den versionen klustret körs genereras en hälsohändelse som placerar klustret i ett varningshälsotillstånd. Klustret förblir i ett varningstillstånd förrän du har uppgraderat till en version som stöds fabric.

### <a name="setting-the-upgrade-mode-via-portal"></a>Ange Uppgraderingsläge via portalen
Du kan ange klustret och automatisk eller manuell när du skapar klustret.

![Create_Manualmode][Create_Manualmode]

Du kan ange klustret och automatisk eller manuell på ett aktivt kluster med hjälp av hantera upplevelse. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Uppgradera till en ny version av ett kluster som är inställd på manuell läge via portalen.
Om du vill uppgradera till en ny version är allt du behöver göra markerar den tillgängliga versionen i listrutan och spara. Fabric-uppgraderingen hämtar inletts automatiskt. Kluster-hälsoprinciper (en kombination av noden hälso- och i alla program som körs i klustret) följs under uppgraderingen.

Uppgraderingen återställs om hälsoprinciper klustret inte är uppfyllda. Rulla ned det här dokumentet du vill veta mer om hur du anger dessa anpassade hälsoprinciper. 

När du har åtgärdat problemen som resulterade i återställningen måste du initiera uppgraderingen igen genom att följa samma steg som innan.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Ange Uppgraderingsläge via en Resource Manager-mall
Lägg till ”upgradeMode”-konfigurationen i resursdefinitionen Microsoft.ServiceFabric/clusters och ange någon av versionerna som stöds fabric enligt nedan ”clusterCodeVersion” och sedan distribuera mallen. Giltiga värden för ”upgradeMode” är ”manuell” eller ”automatisk”

![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Uppgradera till en ny version av ett kluster som är inställd på manuell läge via en Resource Manager-mall.
När klustret är i manuellt läge, för att uppgradera till en ny version, ändra ”clusterCodeVersion” till en version som stöds och distribuerar den. Distributionen av mallen sparkar av Fabric-uppgraderingen hämtar inletts automatiskt. Kluster-hälsoprinciper (en kombination av noden hälso- och i alla program som körs i klustret) följs under uppgraderingen.

Uppgraderingen återställs om hälsoprinciper klustret inte är uppfyllda. Rulla ned det här dokumentet du vill veta mer om hur du anger dessa anpassade hälsoprinciper. 

När du har åtgärdat problemen som resulterade i återställningen måste du initiera uppgraderingen igen genom att följa samma steg som innan.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Hämta en lista över alla tillgängliga versionen för alla miljöer för en viss prenumeration
Kör följande kommando och du bör få ett utgående ut ungefär så här.

”supportExpiryUtc” talar om när en viss version upphör att gälla eller har upphört att gälla. Den senaste versionen har inte ett giltigt datum - har ett värde på ”9999-12-31T23:59:59.9999999”, vilket innebär bara att utgångsdatumet inte ännu har angetts.

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
Microsoft har fabric koden och konfigurationen som körs i ett Azure-kluster. Vi utföra automatiska övervakade uppgraderingar till programvaran som det behövs. Uppgraderingarna kan vara koden, konfiguration, eller båda. Kontrollera att ditt program lider ingen påverkan eller minimal påverkan på grund av uppgraderingar, uppgraderar vi i följande faser:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fas 1: En uppgradering utförs med hjälp av alla hälsoprinciper för kluster
Under den här fasen uppgraderingarna fortsätta en domän i taget och program som körs i klustret fortsätter att köras utan driftavbrott. Kluster-hälsoprinciper (en kombination av noden hälso- och i alla program som körs i klustret) följs under uppgraderingen.

Uppgraderingen återställs om hälsoprinciper klustret inte är uppfyllda. Sedan skickas ett e-postmeddelande till ägaren av prenumerationen. E-postmeddelandet innehåller följande information:

* Meddelande om att vi var tvungna att återställa en uppgradering av klustret.
* Föreslagna vidtar åtgärder, om sådana finns.
* Antal dagar (n) förrän vi köra fas 2.

Vi försöker köra samma uppgradering några gånger om eventuella uppgraderingar misslyckades av orsaker som infrastruktur. Efter n dagar från det datum då e-postmeddelandet skickades kan vi gå vidare till fas 2.

Om klustret hälsoprinciper uppfylls som genomförd uppgraderingen och markerat som slutförda. Detta kan inträffa under den första uppgraderingen eller någon av uppgradering repriser i det här steget. Det finns ingen e-bekräftelse av en lyckad körning. Detta är att undvika att skicka du för många e-postmeddelanden--en e-postmeddelanden ska ses som ett undantag till normal. Planerar vi för de flesta av klusteruppgradering ska lyckas utan att påverka programmets tillgänglighet.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fas 2: En uppgradering utförs med hjälp av standard hälsoprinciper endast
Hälsoprinciper i det här steget är inställda så att antalet program som har felfri i början av uppgraderingen förblir oförändrad under uppgraderingsprocessen. Fas 2-uppgraderingar fortsätta en domän samtidigt som fas 1 och program som körs i klustret fortsätter att köras utan driftavbrott. Kluster-hälsoprinciper (en kombination av noden hälso- och i alla program som körs i klustret) följs under uppgraderingen.

Om klustret hälsoprinciper gäller inte uppfylls, återställs uppgraderingen. Sedan skickas ett e-postmeddelande till ägaren av prenumerationen. E-postmeddelandet innehåller följande information:

* Meddelande om att vi var tvungna att återställa en uppgradering av klustret.
* Föreslagna vidtar åtgärder, om sådana finns.
* Antal dagar (n) förrän vi köra fas 3.

Vi försöker köra samma uppgradering några gånger om eventuella uppgraderingar misslyckades av orsaker som infrastruktur. En påminnelse om e-postmeddelande skickas ett par dagar innan n dagar är igång. Efter n dagar från det datum då e-postmeddelandet skickades kan vi gå vidare till fas 3. E-postmeddelanden som vi skickar dig i fas 2 måste du vidta allvarligt och vidtar åtgärder vidtas.

Om klustret hälsoprinciper uppfylls som genomförd uppgraderingen och markerat som slutförda. Detta kan inträffa under den första uppgraderingen eller någon av uppgradering repriser i det här steget. Det finns ingen e-bekräftelse av en lyckad körning.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fas 3: En uppgradering utförs med hjälp av aggressivt hälsoprinciper
Dessa hälsoprinciper i det här steget är inriktad på uppgraderingen är färdig i stället för hälsotillståndet för programmen. Mycket få klusteruppgradering hamna i det här steget. Om klustret kommer till den här fasen, är det troligt att programmet blir ohälsosamt och förlora tillgänglighet.

Precis som i två faser, fas 3 uppgraderingar fortsätta en domän i taget.

Uppgraderingen återställs om hälsoprinciper klustret inte är uppfyllda. Vi försöker köra samma uppgradering några gånger om eventuella uppgraderingar misslyckades av orsaker som infrastruktur. När är klustret Fäst, så att den får inte längre stöd och/eller uppgraderingar.

Ett e-postmeddelande med informationen skickas till prenumerationsägaren tillsammans med vidtar åtgärder. Vi förväntar sig inte några kluster för att hämta i ett tillstånd där fas 3 har misslyckats.

Om klustret hälsoprinciper uppfylls som genomförd uppgraderingen och markerat som slutförda. Detta kan inträffa under den första uppgraderingen eller någon av uppgradering repriser i det här steget. Det finns ingen e-bekräftelse av en lyckad körning.

## <a name="cluster-configurations-that-you-control"></a>Klusterkonfigurationer som du hanterar
Utöver möjligheten att ange kluster Uppgraderingsläge, kan de konfigurationer som du kan ändra på ett aktivt kluster.

### <a name="certificates"></a>Certifikat
Du kan lägga till nya eller ta bort certifikat för klustret och klienten via portalen enkelt. Referera till [det här dokumentet för detaljerade anvisningar](service-fabric-cluster-security-update-certs-azure.md)

![Skärmbild som visar certifikattumavtryck i Azure-portalen.][CertificateUpgrade]

### <a name="application-ports"></a>Programmet portar
Du kan ändra program portar genom att ändra belastningsutjämnaren resursegenskaper som är associerade med nodtypen. Du kan använda portalen eller du kan använda Resource Manager PowerShell direkt.

Om du vill öppna en ny port på alla virtuella datorer i en nodtyp, gör du följande:

1. Lägg till en ny avsökning lämplig belastningsutjämnare.
   
    Om du har distribuerat ditt kluster med hjälp av portalen belastningsutjämnare namnges ”LB-namnet på resursen grupp-NodeTypename”, en för varje nodtyp. Eftersom belastningen belastningsutjämnaren namnen är unika endast inom en resursgrupp, är det bäst om du söker efter dem under en viss resursgrupp.
   
    ![Skärmbild som visar hur du lägger till en avsökning till en belastningsutjämnare i portalen.][AddingProbes]
2. Lägg till en ny regel till belastningsutjämnaren.
   
    Lägga till en ny regel till samma belastningsutjämnare med den avsökningen som du skapade i föregående steg.
   
    ![Lägger till en ny regel till en belastningsutjämnare i portalen.][AddingLBRules]

### <a name="placement-properties"></a>Placeringsegenskaper
För varje nod, kan du lägga till anpassade placeringsegenskaper som du vill använda i dina program. NodeType är en standardegenskap som du kan använda utan att lägga till det explicit.

> [!NOTE]
> Mer information om användning av placeringen nodegenskaper och hur du definierar dem finns i avsnittet ”begränsningar och noden placeringsegenskaper” i dokumentet resurshanteraren för Service Fabric-kluster på [som beskriver ditt kluster](service-fabric-cluster-resource-manager-cluster-description.md).
> 
> 

### <a name="capacity-metrics"></a>Kapacitet mått
Du kan lägga till anpassade kapacitetsmått som du vill använda i dina program att rapportera belastning för varje nodtyper. Mer information om användning av kapacitetsmått för att rapportera belastning, referera till Service Fabric klustret Resource Manager-dokument på [som beskriver ditt kluster](service-fabric-cluster-resource-manager-cluster-description.md) och [mått och Läs in](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Inställningar för fabric - hälsoprinciper
Du kan ange anpassade hälsoprinciper för fabric-uppgraderingen. Om du har angett ditt kluster automatisk fabric uppgraderingar, hämta dessa principer tillämpas på fas 1 av automatisk fabric-uppgraderingar.
Om du har angett ditt kluster för manuell fabric uppgraderingar, hämta principerna tillämpas varje gång du väljer en ny version utlösa att systemet startar fabric-uppgraderingen i klustret. Om du inte åsidosätter principerna används standardvärdena.

Du kan ange anpassade hälsoprinciper eller granska de aktuella inställningarna under bladet ”fabric-uppgraderingen” genom att välja avancerade inställningar för uppgradering. Läs om hur du på bilden nedan. 

![Hantera anpassade hälsoprinciper][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Anpassa infrastrukturinställningarna för klustret
Referera till [service fabric-kluster infrastrukturinställningarna](service-fabric-cluster-fabric-settings.md) på vad och hur du kan anpassa dem.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Operativsystemskorrigeringar på virtuella datorer som ingår i klustret
Referera till [korrigering Orchestration programmet](service-fabric-patch-orchestration-application.md) som kan distribueras på klustret för att installera korrigeringsfiler från Windows Update på ett framförhållning sätt att hålla tjänsterna som är tillgänglig hela tiden. 

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>OS-uppgraderingar på de virtuella datorerna som utgör klustret
Om du måste uppgradera den OS-avbildningen på de virtuella datorerna i klustret måste du göra det en virtuell dator i taget. Du ansvarar för den här uppgraderingen – det finns för närvarande ingen automatisering för det här.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du anpassar några av de [tjänstinställningar fabric-kluster fabric](service-fabric-cluster-fabric-settings.md)
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
