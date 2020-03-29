---
title: Integrering av Azure DevTest Labs och DevOps | Microsoft-dokument
description: Lär dig hur du använder labb av Azure DevTest Labs inom en kontinuerlig integrering (CI) / kontinuerlig leverans (CD) pipelines i en företagsmiljö.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67078929"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integrering av Azure DevTest Labs och Azure DevOps
DevOps är en mjukvaruutvecklingsmetod som integrerar mjukvaruutveckling (Dev) med drift (Ops) för ett system. Det här systemet kan leverera nya funktioner, uppdateringar och korrigeringar i linje med affärsmål. Denna metod omfattar allt från att utforma nya funktioner baserat på mål, användningsmönster och kundfeedback; att fästa, återställa och härda systemet när problem uppstår. En lätt identifierad komponent i denna metod är rörledningen för kontinuerlig integrering (CI)/ kontinuerlig leverans (CD). En CI/CD-pipeline tar information, kod och resurser från en commit genom en serie steg som inkluderar att skapa, testa och distribuera för att producera systemet. Den här artikeln fokuserar på olika sätt att effektivt använda labb i en pipeline i en företagsmiljö. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Fördelar med att använda labb i DevOps-arbetsflödet 

### <a name="focused-access"></a>Fokuserad åtkomst 
Med hjälp av ett labb som en komponent kan ett specifikt ekosystem associera med en begränsad grupp människor. Vanligtvis har ett team eller en grupp som arbetar i ett gemensamt område eller en viss funktion ett labb tilldelat till dem.   

### <a name="infrastructure-replication-in-the-cloud"></a>Infrastrukturreplikering i molnet 
En utvecklare kan snabbt skapa ett utvecklingsekosystem som innehåller en utvecklingsruta med källkod och verktyg. Utvecklaren kan också skapa en miljö som är nästan identisk med produktionskonfigurationen. Det hjälper till med snabbare inre slinga utveckling. 

### <a name="pre-production"></a>Förproduktion 
Att ha ett labb i CI/CD-pipelinen gör det enklare att ha flera olika förproduktionsmiljöer eller maskiner som körs samtidigt för asynkrona tester. Olika supportinfrastrukturer som byggagenter kan distribueras och hanteras i ett labb. 

## <a name="devops-with-devtest-labs"></a>DevOps med DevTest Labs 

### <a name="development--operation"></a>Utveckling / Drift 
Ett labb bör fokuseras på ett team som arbetar i ett funktionsområde. Med det här gemensamma fokuset kan du dela områdesspecifika resurser, till exempel verktyg, skript eller Resource Manager-mallar. Det möjliggör snabbare ändringar samtidigt som de negativa effekterna begränsas till en mindre grupp. Med de här delade resurserna kan utvecklaren skapa virtuella utvecklings-datorer med all nödvändig kod, verktyg och konfiguration. De kan skapas antingen dynamiskt eller ha ett system som skapar basavbildningar med anpassningarna. Utvecklaren kan inte bara skapa virtuella datorer, utan de kan också skapa DevTest Labs-miljöer baserat på nödvändiga mallar för att skapa lämpliga Azure-resurser i labbet. Alla förändringar eller destruktiva arbete kan göras mot labbmiljön utan att påverka någon annan. Tänk på scenariot där produkten är ett fristående system som är installerat på kundens dator. I det här fallet har DevTest Labs förbättrat skapande av virtuella datorer som inkluderar att installera ytterligare programvara med artefakter och förbygga kundkonfigurationer för snabbare inre loop testning av koden. 
  
## <a name="cicd-pipeline"></a>CI/CD-pipeline 
CI/CD-pipelinen är en av de kritiska komponenterna i DevOps som flyttar kod från en pull-begäran av utvecklaren, integrerar den med den befintliga koden och distribuerar den till produktionsekosystemet. Alla resurser behöver inte vara i ett labb. En Jenkins-värd kan till exempel ställas in utanför labbet som en mer beständig resurs. Här är några specifika exempel på att integrera labb i pipelinen. 

### <a name="build"></a>Utveckla 
Byggpipelinen fokuserar på att skapa ett paket med komponenter som kommer att testas tillsammans för att överlämnas till versionspipelinen. Labb kan vara en del av byggpipelinen som plats för byggagenter och andra supportresurser. Att ha möjlighet att dynamiskt bygga infrastrukturen möjliggör större kontroll. Med möjligheten att ha flera miljöer i ett labb kan varje version köras asynkront när du använder bygg-ID som en del av miljöinformationen för att unikt identifiera resurserna till den specifika versionen.   

För byggagenter ökar labbets förmåga att begränsa åtkomsten säkerheten och minskar risken för oavsiktlig korruption.  

### <a name="test"></a>Testa 
DevTest Labs tillåter en CI/CD-pipeline för att automatisera skapandet av Azure Resource (virtuella datorer, miljöer) som kan användas för automatisk och manuell testning. De virtuella datorerna skulle skapas med artefakter eller formler som använder information från byggprocessen för att skapa de olika anpassade konfigurationer som krävs för testning.   

### <a name="release"></a>Frisläpp 
DevTest Labs används ofta för verifiering i utgivningsavsnittet innan koden distribueras. Det liknar testning i avsnittet Bygg. Produktionsresurser bör inte distribueras i DevTest Labs. 

### <a name="customization"></a>Anpassning 
I Azure DevOps finns det befintliga uppgifter som tillåter manipulering av virtuella datorer och miljöer i specifika labb. Azure DevOps Services är ett sätt att hantera CI/CD-pipelinen, men du kan integrera labbet i alla system som stöder möjligheten att anropa REST-API:er, köra PowerShell-skript eller använda Azure CLI. 

Medan vissa CI/CD-pipelinehanterare har befintliga plugins med öppen källkod som kan hantera resurser i Azure och DevTest Labs. Du kan behöva använda vissa anpassade skript för att passa de specifika behoven hos pipelinen.  Med detta i åtanke, när du utför en uppgift, en tjänst huvudnamn används med lämplig roll för att få tillgång till labbet. Tjänstens huvudnamn behöver vanligtvis deltagarrollens åtkomst till labbet. Mer information finns i [Integrera Azure DevTests Labs i din Azure DevOps kontinuerlig integrering och leveranspipeline](devtest-lab-integrate-ci-cd-vsts.md). 
 