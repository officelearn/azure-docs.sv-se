---
title: Uppgradera ett Azure Service Fabric-kluster
description: Lär dig mer om hur du uppgraderar versionen eller konfigurationen av ett Azure Service Fabric-kluster – ange klusteruppdateringsläge, uppgradera certifikat, lägga till programportar, göra OS-korrigeringar och vad du kan förvänta dig när uppgraderingarna utförs.
ms.topic: conceptual
ms.date: 11/12/2018
ms.custom: sfrev
ms.openlocfilehash: 6897854820339fc78dd9083c82147dce95ab68b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258660"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Uppgradera och uppdatera ett Azure Service Fabric-kluster

För alla moderna system, design för uppgradering är nyckeln till att uppnå långsiktig framgång för din produkt. Ett Azure Service Fabric-kluster är en resurs som du äger, men hanteras delvis av Microsoft. I den här artikeln beskrivs vad som hanteras automatiskt och vad du kan konfigurera själv.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Styra tygversionen som körs i klustret

Kontrollera att klustret alltid kör en [tygversion som stöds](service-fabric-versions.md). Varje gång Microsoft tillkännager lanseringen av en ny version av Service Fabric markeras den tidigare versionen för supportens efter minst 60 dagar från det datumet. Nya utgåvor tillkännages på [Service Fabric team blogg](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

14 dagar innan frisläppningen som klustret körs genereras en hälsohändelse som placerar klustret i ett hälsotillstånd för varning. Klustret förblir i ett varningstillstånd tills du uppgraderar till en fabric-version som stöds.

Du kan ange att klustret ska ta emot automatiska infrastrukturuppgraderingar när de släpps av Microsoft eller så kan du välja en tygversion som stöds som du vill att klustret ska vara på.  Mer information finns [i Uppgradera Service Fabric-versionen av klustret](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Fabric uppgradering beteende under automatiska uppgraderingar

Microsoft underhåller den infrastrukturkod och konfiguration som körs i ett Azure-kluster. Vi utför automatiska övervakade uppgraderingar till programvaran efter behov. Dessa uppgraderingar kan vara kod, konfiguration eller båda. För att säkerställa att ditt program inte drabbas av någon påverkan eller minimal påverkan på grund av dessa uppgraderingar utförs uppgraderingar i följande faser:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fas 1: En uppgradering utförs med hjälp av alla klusterhälsoprinciper

Under den här fasen fortsätter uppgraderingarna en uppgraderingsdomän i taget och de program som kördes i klustret fortsätter att köras utan avbrott. Klusterhälsoprinciperna (för nodhälsa och programhälsa) följs under uppgraderingen.

Om klusterhälsoprinciperna inte uppfylls återställs uppgraderingen och ett e-postmeddelande skickas till prenumerationens ägare. E-postmeddelandet innehåller följande information:

* Meddelande om att vi var tvungna att återställa en klusteruppgradering.
* Föreslagna avhjälpande åtgärder, om några.
* Antalet dagar *(n*) tills vi kör fas 2.

Vi försöker köra samma uppgradering några gånger till om några uppgraderingar misslyckades av infrastrukturskäl. Efter *n* dagar från det datum då e-postmeddelandet skickades fortsätter vi till fas 2.

Om klusterhälsoprinciperna uppfylls anses uppgraderingen vara lyckad och markerad som slutförd. Detta kan inträffa under den första uppgraderingen eller någon av uppgraderingskörningarna i den här fasen. Det finns ingen e-postbekräftelse för en lyckad körning. Detta för att undvika att skicka för många e-postmeddelanden till dig. att få ett e-postmeddelande bör ses som ett undantag från det normala. Vi förväntar oss att de flesta klusteruppgraderingar lyckas utan att påverka programmets tillgänglighet.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fas 2: En uppgradering utförs med hjälp av endast standardhälsoprinciper

Hälsoprinciperna i den här fasen anges på ett sådant sätt att antalet program som var felfria i början av uppgraderingen förblir detsamma under hela uppgraderingsprocessen. Precis som i fas 1 fortsätter fas 2-uppgraderingarna en uppgraderingsdomän i taget och de program som kördes i klustret fortsätter att köras utan avbrott. Klusterhälsoprinciperna (en kombination av nodhälsa och hälsotillstånd som alla program som körs i klustret) följs under hela uppgraderingen.

Om klusterhälsoprinciperna i kraft inte uppfylls återställs uppgraderingen. Sedan skickas ett e-postmeddelande till prenumerationens ägare. E-postmeddelandet innehåller följande information:

* Meddelande om att vi var tvungna att återställa en klusteruppgradering.
* Föreslagna avhjälpande åtgärder, om några.
* Antalet dagar (n) tills vi kör fas 3.

Vi försöker köra samma uppgradering några gånger till om några uppgraderingar misslyckades av infrastrukturskäl. Ett påminnelsemeddelande skickas ett par dagar innan n dagar är. Efter n dagar från det datum då e-postmeddelandet skickades fortsätter vi till fas 3. De e-postmeddelanden vi skickar till dig i fas 2 måste tas på allvar och korrigerande åtgärder måste vidtas.

Om klusterhälsoprinciperna uppfylls anses uppgraderingen vara lyckad och markerad som slutförd. Detta kan inträffa under den första uppgraderingen eller någon av uppgraderingskörningarna i den här fasen. Det finns ingen e-postbekräftelse för en lyckad körning.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fas 3: En uppgradering utförs med hjälp av aggressiva hälsopolicyer

Dessa hälsoprinciper i denna fas är inriktade på att slutföra uppgraderingen snarare än på programmens hälsa. Få klusteruppgraderingar hamnar i den här fasen. Om klustret kommer till den här fasen finns det en god chans att ditt program blir ohälsosamt och/eller förlorar tillgänglighet.

I likhet med de andra två faserna fortsätter fas 3-uppgraderingar en uppgraderingsdomän i taget.

Om klusterhälsoprinciperna inte uppfylls återställs uppgraderingen. Vi försöker köra samma uppgradering några gånger till om några uppgraderingar misslyckades av infrastrukturskäl. Därefter är klustret fäst, så att det inte längre får stöd och/eller uppgraderingar.

Ett e-postmeddelande med den här informationen skickas till prenumerationsägaren, tillsammans med de korrigerande åtgärderna. Vi förväntar oss inte att några kluster ska hamna i ett tillstånd där fas 3 har misslyckats.

Om klusterhälsoprinciperna uppfylls anses uppgraderingen vara lyckad och markerad som slutförd. Detta kan inträffa under den första uppgraderingen eller någon av uppgraderingskörningarna i den här fasen. Det finns ingen e-postbekräftelse för en lyckad körning.

## <a name="manage-certificates"></a>Hantera certifikat

Service Fabric använder [X.509-servercertifikat](service-fabric-cluster-security.md) som du anger när du skapar ett kluster för att skydda kommunikationen mellan klusternoder och autentisera klienter. Du kan lägga till, uppdatera eller ta bort certifikat för klustret och klienten i [Azure-portalen](https://portal.azure.com) eller använda PowerShell/Azure CLI.  Mer information finns [i lägga till eller ta bort certifikat](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Öppna programportar

Du kan ändra programportar genom att ändra resursegenskaperna Load Balancer som är associerade med nodtypen. Du kan använda Azure-portalen eller använda PowerShell/Azure CLI. Mer information finns i [Öppna programportar för ett kluster](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definiera nodegenskaper

Ibland kanske du vill se till att vissa arbetsbelastningar körs endast på vissa typer av noder i klustret. Vissa arbetsbelastningar kan till exempel kräva GPU:er eller SSD-enheter medan andra kanske inte gör det. För var och en av nodtyperna i ett kluster kan du lägga till anpassade nodegenskaper i klusternoder. Placeringsbegränsningar är de uttryck som är kopplade till enskilda tjänster som väljer för en eller flera nodegenskaper. Placeringsbegränsningar definierar var tjänster ska köras.

Mer information om hur du använder placeringsbegränsningar, nodegenskaper och hur du definierar dem läser du [nodegenskaper och placeringsbegränsningar](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Lägga till kapacitetsmått

För var och en av nodtyperna kan du lägga till anpassade kapacitetsmått som du vill använda i dina program för att rapportera inläsning. Mer information om hur du använder kapacitetsmått för att rapportera inläsning finns i Service Fabric Cluster Resource Manager-dokumenten för [att beskriva ditt kluster](service-fabric-cluster-resource-manager-cluster-description.md) och mått och läs [in](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Ange hälsoprinciper för automatiska uppgraderingar

Du kan ange anpassade hälsoprinciper för uppgradering av fabric. Om du har ställt in klustret på Automatiska infrastrukturuppgraderingar tillämpas dessa principer på fas 1 av de automatiska infrastrukturuppgraderingarna.
Om du har ställt in klustret för manuella infrastrukturuppgraderingar tillämpas dessa principer varje gång du väljer en ny version som utlöser systemet för att starta infrastrukturuppgraderingen i klustret. Om du inte åsidosätter principerna används standardinställningarna.

Du kan ange anpassade hälsoprinciper eller granska de aktuella inställningarna under bladet "tyguppgradering" genom att välja de avancerade uppgraderingsinställningarna. Läs igenom följande bild om hur man gör.

![Hantera anpassade hälsoprinciper][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Anpassa fabric-inställningar för ditt kluster

Många olika konfigurationsinställningar kan anpassas i ett kluster, till exempel tillförlitlighetsnivån för kluster- och nodegenskaperna. Mer information finns i [Inställningarna för Klusterinfrastruktur för Service Fabric](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Korrigering av operativsystemet i klusternoderna

PA (Patch Orchestration Application) är ett Service Fabric-program som automatiserar operativsystemkorrigering på ett Service Fabric-kluster utan driftstopp. [Patch Orchestration Application for Windows](service-fabric-patch-orchestration-application.md) kan distribueras i klustret för att installera korrigeringar på ett orkestrerat sätt samtidigt som tjänsterna är tillgängliga hela tiden.

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
