---
title: Använda PaaS-tjänster (Platform-as-a-Service) i Azure DevTest Labs
description: Lär dig hur du använder Pass-tjänster (Platform-as-a-Service) i Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88bbf921fedae4bcdba2b6386ce6e08105206cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169196"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Använda PaaS-tjänster (Platform-as-a-Service) i Azure DevTest Labs
PaaS stöds i DevTest Labs via miljöfunktionen. Miljöer i DevTest Labs stöds av förkonfigurerade Azure Resource Manager-mallar i en Git-databas. Miljöer kan innehålla både PaaS- och IaaS-resurser. De gör att du kan skapa komplexa system som kan inkludera Azure-resurser som virtuella datorer, databaser, virtuella nätverk och webbappar, som är anpassade för att fungera tillsammans. Dessa mallar möjliggör konsekvent distribution och förbättrad hantering av miljöer med hjälp av källkodskontroll. 

Ett korrekt konfigurerat system tillåter följande scenarier: 

- Utvecklare att ha oberoende och flera miljöer
- Testning på olika konfigurationer asynkront
- Integrering i mellanlagrings- och produktionspipelledningar utan några malländringar
- Att ha både utvecklingsmaskiner och miljöer inom samma labb förbättrar hanteringen och kostnadsrapporteringen.  

DevTest Labs-resursleverantören skapar resurser för labbanvändarens räkning, så inga extra behörigheter behöver ges till labbanvändaren för att aktivera användning av PaaS-resurserna. Följande bild visar ett Service Fabric-kluster som en miljö i labbet.

![Service Fabric-kluster som miljö](./media/create-environment-service-fabric-cluster/cluster-created.png)

Mer information om hur du konfigurerar miljöer finns i [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md). DevTest Labs har en offentlig databas med Azure Resource Manager-mallar som du kan använda för att skapa miljöer utan att behöva ansluta till en extern GitHub-källa själv. Mer information om offentliga miljöer finns i [Konfigurera och använda offentliga miljöer i Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

I stora organisationer tillhandahåller utvecklingsteam vanligtvis miljöer som anpassade/isolerade testmiljöer. Det kan finnas miljöer som ska användas av alla team inom en affärsenhet eller en division. IT-gruppen kanske vill tillhandahålla sina miljöer som kan användas av alla team i organisationen.  

## <a name="customizations"></a>Anpassningar

#### <a name="sandbox"></a>Begränsat läge 
Labbägaren kan anpassa labbmiljöer för att ändra användarens roll från **läsare** till **deltagare** inom resursgruppen. Den här funktionen finns på sidan **Labbinställningar** under **konfigurationen och principerna** för labbet. Med den här rolländringen kan användaren lägga till eller ta bort resurser i den miljön. Om du vill begränsa åtkomsten ytterligare använder du Azure-principer. Med den här funktionen kan du anpassa resurserna eller konfigurationen utan åtkomst på prenumerationsnivå.

#### <a name="custom-tokens"></a>Anpassade tokens
Det finns en del anpassad labbinformation som ligger utanför resursgruppen och som är specifik för miljöer som mallen kan komma åt. Här är några av dem: 

- Identifiering av labbnätverk
- Location
- Lagringskonto där Resource Manager-mallfilerna lagras. 
 
#### <a name="lab-virtual-network"></a>Lab virtuellt nätverk
I artikeln [Ansluta till labbets virtuella nätverk](connect-environment-lab-virtual-network.md) beskrivs hur du ändrar `$(LabSubnetId)` mallen Resource Manager för att använda token. När en miljö skapas ersätts `$(LabSubnetId)` token av det första undernätsmärket där alternativet för att skapa virtuella **datorer** är inställt på **true**. Det gör att vår miljö kan använda tidigare skapade nätverk. Om du vill använda samma Resource Manager-mallar i miljöer i `$(LabSubnetId)` test som mellanlagring och produktion använder du som standardvärde i en Resource Manager-mallparameter. 

#### <a name="environment-storage-account"></a>Miljölagringskonto
DevTest Labs stöder användning av [kapslade Resource Manager-mallar](../azure-resource-manager/templates/linked-templates.md). I artikeln [[Distribuera kapslade Azure Resource Manager-mallar för testmiljöer beskrivs](deploy-nested-template-environments.md) hur du använder `_artifactsLocation` och `_artifactsLocationSasToken` token för att skapa en URI till en Resource Manager-mall i samma mapp som eller i en kapslad mapp i huvudmallen. Mer information om dessa två token finns i avsnittet **Distributionsartefakter** i [Azure Resource Manager – Guide för metodtips](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Användarupplevelse

## <a name="developer"></a>Developer
Utvecklare använder samma arbetsflöde för att skapa en virtuell dator för att skapa en viss miljö. De väljer miljön jämfört med maskinavbildningen och anger den information som krävs enligt mallen. Varje utvecklare som har en miljö möjliggör distribution av ändringar och förbättrad felsökning av inre loop. Miljön kan skapas när som helst med den senaste mallen.  Den här funktionen gör att miljöer kan förstöras och återskapas för att minska driftstopp från att manuellt behöva skapa systemet eller återhämta sig från feltestning.  

### <a name="testing"></a>Testning
DevTest Labs-miljöer möjliggör oberoende testning av specifik kod och konfigurationer asynkront. Det vanliga är att ställa in miljön med koden från den enskilda pull-begäran och starta alla automatiserade tester. När den automatiserade testningen har slutförts kan alla manuella tester utföras mot den specifika miljön. Vanligtvis görs den här processen som en del av CI/CD-pipelinen. 

## <a name="management-experience"></a>Erfarenhet av hantering

### <a name="cost-tracking"></a>Kostnadsspårning
Kostnadsspårningsfunktionen innehåller Azure-resurser i de olika miljöerna som en del av den övergripande kostnadstrenden. Kostnaden för resurser bryter inte ut de olika resurserna i miljön utan visar miljön som en enda kostnad.

### <a name="security"></a>Säkerhet
En korrekt konfigurerad Azure-prenumeration med DevTest Labs kan [begränsa åtkomsten till Azure-resurser endast via labbet](devtest-lab-add-devtest-user.md). Med miljöer kan en labbägare tillåta användare att komma åt PaaS-resurser med godkända konfigurationer utan att tillåta åtkomst till andra Azure-resurser. I scenariot där labbanvändare anpassar miljöer kan labbägaren tillåta deltagare åtkomst. Deltagaråtkomsten gör det möjligt för labbanvändaren att lägga till eller ta bort Azure-resurs endast inom den hanterade resursgruppen. Det möjliggör enklare spårning och hantering jämfört med att ge användaren åtkomst till prenumerationen.

### <a name="automation"></a>Automation
Automation är en nyckelkomponent för ett storskaligt och effektivt ekosystem. Automatisering är nödvändigt för att hantera hantering eller spårning av flera miljöer över prenumerationer och labb.

### <a name="cicd-pipeline"></a>CI/CD-pipeline
PaaS-tjänster i DevTest Labs kan hjälpa till att förbättra CI/CD-pipelinen genom att ha fokuserade distributioner där åtkomsten styrs av labbet.

## <a name="next-steps"></a>Nästa steg
Mer information om miljöer finns i följande artiklar: 

- 
- [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md)
- [Konfigurera och använda offentliga miljöer i Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Skapa en miljö med självädslutat Service Fabric-kluster i Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Ansluta en miljö till labbets virtuella nätverk i Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrera miljöer i dina Azure DevOps CI/CD-pipelines](integrate-environments-devops-pipeline.md)
 





