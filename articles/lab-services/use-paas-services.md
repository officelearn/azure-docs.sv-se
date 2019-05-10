---
title: Använda Platform-as-a-Service (PaaS) tjänster i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du använder Platform-as-a-Service (Pass) tjänster i Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 7cdd185cddbd2403b72ff0e06530913af0b031de
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233123"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Använda Platform-as-a-Service (PaaS) tjänster i Azure DevTest Labs
PaaS stöds i DevTest Labs via funktionen miljöer. Miljöer i DevTest Labs stöds av förkonfigurerade Azure Resource Manager-mallar i en Git-lagringsplats. Miljöer kan innehålla både PaaS och IaaS-resurser. De gör att du kan skapa komplexa system som kan inkludera Azure-resurser som virtuella datorer, databaser, virtuella nätverk och Web apps, som är anpassade till samarbete. Dessa mallar kan konsekvent distribution och förbättrad hantering av miljöer med källkodskontroll. 

En korrekt konfigurerad system kan följande scenarier: 

- Utvecklare kan ha oberoende och flera miljöer
- Tester på olika konfigurationer asynkront
- Mellanlagring och produktion pipelines utan malländringar-integrering
- Med både för utveckling av datorer och miljöer inom samma labbet förbättrar enkel hantering och kostnad reporting.  

Resursprovidern DevTest Labs skapar resurserna för labb användarens räkning, så inga ytterligare behörighet måste tilldelas till lab-användare att aktivera användningen av PaaS-resurser. Följande bild visar ett Service Fabric-kluster som en miljö i laboratoriet.

![Service Fabric-kluster som en miljö](./media/create-environment-service-fabric-cluster/cluster-created.png)

Mer information om hur du konfigurerar miljöer finns i [skapa miljöer för flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md). DevTest Labs har en offentlig Azure Resource Manager-mallar som du kan använda för att skapa miljöer utan att behöva ansluta till en extern källa för GitHub själv-lagringsplats. Mer information i offentliga miljöer finns i [konfigurera och använda offentliga miljöer i Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

I stora organisationer tillhandahåller utvecklingsteam vanligtvis miljöer, till exempel anpassade/isolerade testmiljöer. Det kan finnas miljöer som ska användas av alla team inom en affärsenhet eller en division. IT-gruppen kanske vill ge sina miljöer som kan användas av alla grupper i organisationen.  

## <a name="customizations"></a>Anpassningar

#### <a name="sandbox"></a>Begränsat läge 
Labbägare kan anpassa labbmiljöer om du vill ändra användarens roll från **läsare** till **deltagare** i resursgruppen. Den här funktionen är i den **Lab inställningar** sidan den **konfiguration och principer** labbet. Den här ändringen i rollen gör att användaren kan lägga till eller ta bort resurser i miljön. Om du vill begränsa åtkomsten ytterligare kan du använda Azure-principer. Den här funktionen kan du anpassa resurserna eller konfigurationen utan åtkomst på prenumerationsnivå.

#### <a name="custom-tokens"></a>Anpassade token
Det finns vissa anpassat labb-information som ligger utanför resursgruppen och är specifik för miljöer som mallen har åtkomst till. Här följer några av dem: 

- Lab nätverksidentifiering
- Location
- Storage-konto där filer för Resource Manager-mallarna lagras. 
 
#### <a name="lab-virtual-network"></a>Lab virtuellt nätverk
Den [miljöer att ansluta till virtuella labbnätverk](connect-environment-lab-virtual-network.md) artikeln beskrivs hur du ändrar dina Resource Manager-mall som ska användas i `$(LabSubnetId)` token. När en miljö skapas den `$(LabSubnetId)` token ersättas med det första undernät märket där den **användning i virtuell dator skapar** alternativet är inställt på **SANT**. Det gör våra miljö för att använda tidigare skapat nätverk. Om du vill använda samma Resource Manager-mallar i miljöer i testet som mellanlagring och produktion, använda `$(LabSubnetId)` som standard i en mallparameter i Resource Manager. 

#### <a name="environment-storage-account"></a>Miljö Storage-konto
DevTest Labs stöder användning av [kapslade Resource Manager-mallar](../azure-resource-manager/resource-group-linked-templates.md). Den [hur Azure DevTest Labs underlättar kapslade resurshanteraren malldistributioner för testmiljöer](https://azure.microsoft.com/updates/azure-devtest-labs-streamlined-nested-arm-template-deployment-support-for-arm-template-based-environments) artikeln förklarar hur du använder `_artifactsLocation` och `_artifactsLocationSasToken` -tokens för att skapa en URI för en Resource Manager-mall i samma mapp som eller i en kapslad mapp för den huvudsakliga mallen. Mer information om dessa två token finns i den **distribution artefakter** delen av [Azure Resource Manager – Guide för bästa praxis](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Användarupplevelse

## <a name="developer"></a>Utvecklare
Utvecklare använder samma arbetsflöde för att skapa en virtuell dator för att skapa en viss miljö. De väljer du miljön jämfört med avbildningen av datorn och ange nödvändig information som krävs av mallen. Varje utvecklare med en miljö kan distribution av ändringar och förbättrad inre slingan felsökning. Miljön kan skapas när som helst med hjälp av den senaste mallen.  Den här funktionen kan miljöer förstörs och återskapas för att minska avbrottstiden slipper manuellt skapa systemet eller återställa från fel testning.  

### <a name="testing"></a>Testning
DevTest Labs-miljöer möjliggör oberoende testning av specifik kod och konfigurationer asynkront. Den vanligaste metoden är att ställa in miljön med koden från enskilda pull-begäran och starta automatiserad testning. När automatiserade tester har körts klart, kan manuell testning köras mot den specifika miljön. Den här processen görs vanligtvis som en del av CI/CD-pipeline. 

## <a name="management-experience"></a>Grupphantering

### <a name="cost-tracking"></a>Kostnadsuppföljning
Kostnaden spårningsfunktionen inkluderar Azure-resurser i olika miljöer som en del av totala kostnad trend. Kostnad per resurser bryts inte ut olika resurser i miljön, men visar miljön i en enda kostnad.

### <a name="security"></a>Säkerhet
En korrekt konfigurerade Azure-prenumeration med DevTest Labs kan [begränsa åtkomsten till Azure-resurser endast via labbet](devtest-lab-add-devtest-user.md). Miljöer med kan labbägare ge användare åtkomst till PaaS-resurser med godkända konfigurationer utan att tillåta åtkomst till andra Azure-resurser. I scenariot där labbanvändare anpassa miljöer, kan labbägare deltagarbehörighet. Deltagaråtkomst gör att lab användaren att lägga till eller ta bort Azure-resurs i den hanterade resursgruppen. Det gör att enklare spårning och hantering jämfört med ger användaren deltagaråtkomst till prenumerationen.

### <a name="automation"></a>Automation
Automation är en viktig komponent för stor skala, effektiva ekosystem. Automation är nödvändig för att hantera hantera eller spåra flera miljöer i prenumerationer och labb.

### <a name="cicd-pipeline"></a>CI/CD Pipeline
PaaS-tjänster i DevTest Labs kan hjälpa att förbättra CI/CD-pipeline genom att ha fokuserar distributioner där åtkomst styrs av labbet.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för ytterligare information om miljöer: 

- 
- [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md)
- [Konfigurera och använda offentliga miljöer i Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Skapa en miljö med fristående Service Fabric-kluster i Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Ansluta en miljö till ditt labb-nätverk i Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrera miljöer i din Azure DevOps CI/CD-pipelines](integrate-environments-devops-pipeline.md)
 





