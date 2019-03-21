---
title: Uppgradera ett Azure Service Fabric-kluster | Microsoft Docs
description: Lär dig mer om hur du uppgraderar den version eller konfigurationen av en Azure Service Fabric-klustret.  Den här artikeln beskrivs inställningen kluster uppdateringsläge, uppgradera certifikat, lägga till programportar, korrigeringsfiler för operativsystem och vad du kan förvänta dig när uppgraderingen utförs
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
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: a3778a0b0e5b4b59eb29cb67c0596d9636eb3ccb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58166709"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Uppgradera och uppdaterar ett Azure Service Fabric-kluster

Utformning av möjligheterna är avgörande för att uppnå långsiktig framgång av din produkt för alla moderna system. Ett Azure Service Fabric-kluster är en resurs som du äger, men delvis hanteras av Microsoft. Den här artikeln beskriver vad hanteras automatiskt och vad du kan konfigurera själv.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Kontrollera den fabric-versionen som körs på klustret

Se till att hålla ditt kluster som kör en [stöds infrastrukturversion](service-fabric-versions.md) alltid. Och när vi presentera en ny version av service fabric, markeras den tidigare versionen för support upphör efter 60 dagar efter att minst. De nya versionerna tillkännages i service fabric-teamets blogg. Den nya versionen är tillgänglig för att välja sedan.

14 dagar före utgången av den versionen som klustret körs en hälsotillståndshändelse genereras som placerar klustret i ett varningshälsotillstånd. Klustret förblir i ett varningstillstånd förrän du har uppgraderat till en version som stöds fabric.

Du kan ställa in klustret för att ta emot automatiska infrastrukturuppgraderingar när de blir tillgängliga av Microsoft eller du kan välja en stöds fabric-versionen som du vill att klustret ska vara på.  Mer information, [uppgradera Service Fabric-versionen av ditt kluster](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Fabric uppgradera beteende under automatiska uppgraderingar
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
Dessa hälsoprinciper i den här fasen är avsedda för uppgraderingen är färdig i stället för hälsotillståndet för programmen. Få klusteruppgradering som hamnar i det här steget. Om ditt kluster kommer till den här fasen, är det troligt att ditt program blir ohälsosamt och/eller förlora tillgänglighet.

Liknar de andra två faserna, fas 3 uppgraderingar fortsätta en uppgraderingsdomän i taget.

Om klustret hälsoprinciper inte uppfylls, återställs uppgraderingen. Vi försöker köra samma uppgradering några gånger om eventuella uppgraderingar misslyckades av orsaker som infrastruktur. Efter det fästs klustret, så att den får inte längre stöd och/eller uppgraderingar.

Ett e-postmeddelande med den här informationen skickas till Prenumerationens ägare, tillsammans med de korrigerande åtgärderna. Vi förväntar oss inte alla kluster komma in i ett tillstånd där fas 3 har misslyckats.

Om klustret hälsoprinciper uppfylls som genomförd uppgraderingen och markerat som slutförda. Detta kan inträffa under den första uppgraderingen eller någon av uppgradering repriser i det här steget. Det finns inga e-postbekräftelse på en lyckad körning.

## <a name="manage-certificates"></a>Hantera certifikat
Service Fabric använder [X.509-servercertifikat](service-fabric-cluster-security.md) som du anger när du skapar ett kluster för att skydda kommunikationen mellan klusternoder och autentisera klienter. Du kan lägga till, uppdatera eller ta bort certifikat för klustret och klienten i den [Azure-portalen](https://portal.azure.com) eller med PowerShell/Azure CLI.  Mer information, [lägga till eller ta bort certifikat](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Öppna application portar
Du kan ändra programportar genom att ändra belastningsutjämnaren resursegenskaper som är associerade med nodtyp. Du kan använda Azure-portalen eller du kan använda PowerShell/Azure CLI. Mer information finns i [öppna programmet portar för ett kluster](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definiera nodegenskaper
Ibland kanske du vill se till att vissa arbetsbelastningar endast för vissa typer av noder i klustret. Exempelvis kan vissa arbetsbelastning kräva GPU: er eller SSD-enheter och andra inte. För varje nod i ett kluster, kan du lägga till anpassade nodegenskaper klusternoder. Placeringsbegränsningar är de instruktioner som är kopplade till enskilda tjänster väljer för en eller flera egenskaper för noden. Placeringsbegränsningar definiera där tjänsterna ska köras.

Mer information om användning av placeringsbegränsningar nodegenskaper och hur du definierar dem finns [nodegenskaper och placeringsbegränsningar](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Lägg till kapacitet
Du kan lägga till anpassade kapacitetsmått som du vill använda i dina program att rapportera belastning för var och en nodtyperna. Information om användning av kapacitet att rapportera belastning, i Service Fabric Cluster Resource Manager-dokument på [som beskriver ditt kluster](service-fabric-cluster-resource-manager-cluster-description.md) och [mått och Load](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Ange hälsoprinciper för automatiska uppgraderingar
Du kan ange anpassade hälsoprinciper för uppgraderingen av fabric. Om du har angett ditt kluster till automatisk infrastrukturuppgraderingar, hämta principerna tillämpas på fas 1 av de automatiska infrastrukturuppgraderingar.
Om du har angett ditt kluster för manuell fabric uppgraderingar, hämta principerna tillämpas varje gång som du väljer en ny version som utlöser systemet för att sätta igång fabric-uppgraderingen i klustret. Om du inte åsidosätter principerna kan används standardvärdena.

Du kan ange anpassade hälsoprinciper eller granska de aktuella inställningarna under bladet ”uppgraderingen av fabric” genom att välja de avancerade inställningarna. Granska följande bild på hur du. 

![Hantera anpassade hälsoprinciper][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Anpassa infrastrukturinställningarna för klustret
Många olika konfigurationsinställningar kan anpassas i ett kluster, till exempel tillförlitlighetsnivån för egenskaperna kluster och nod. Mer information finns i [infrastrukturinställningarna för Service Fabric-kluster](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Uppdatera Operativsystemet på klusternoderna
Patch orchestration application (POA) är ett Service Fabric-program som automatiserar operativsystemet uppdatering i Service Fabric-kluster utan avbrott. Den [Patch Orchestration Application för Windows](service-fabric-patch-orchestration-application.md) eller [Patch Orchestration-program för Linux](service-fabric-patch-orchestration-application-linux.md) kan distribueras på klustret för att installera uppdateringar på ett dirigerat sätt samtidigt som tjänsterna tillgänglig hela tiden. 


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
