---
title: Integrering av Azure DevTest Labs och DevOps | Microsoft Docs
description: Lär dig hur du använder labb i Azure DevTest Labs inom en kontinuerlig integrering (CI) / kontinuerlig leverans (CD) pipelines i en företagsmiljö.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078929"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integrering av Azure DevTest Labs och Azure DevOps
DevOps är en utvecklingsmetodiken för programvara som kan integreras programutveckling (utveckling) med åtgärder (Ops) för ett system. Det här systemet kan leverera nya funktioner, uppdateringar och korrigeringar i linje med affärsmål. Den här metoden omfattar allt från utforma nya funktioner baserat på mål, användningsmönster och feedback från kunder; att åtgärda, återställa och Härdning av systemet när ett problem uppstår. Ett enkelt identifierade komponenten i den här metoden är kontinuerlig integrering (CI) / pipeline för kontinuerlig leverans (CD). En CI/CD-pipeline tar information, kod och resurser från en allokering via en serie steg som omfattar att skapa, testa och distribution för att producera systemet. Den här artikeln fokuserar på olika sätt att använda effektivt labs inom en pipeline i en företagsmiljö. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Fördelarna med att använda labs i DevOps-arbetsflöde 

### <a name="focused-access"></a>Fokuserad åtkomst 
Om du använder ett labb som en komponent kan ett specifikt ekosystem ska associeras med en begränsad grupp användare. Vanligtvis är har ett team eller en grupp som fungerar i ett gemensamt område eller en specifik funktion ett labb som tilldelats.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replikering av infrastruktur i molnet 
Utvecklare kan snabbt ställa in ett ekosystem för utveckling som innehåller en maskin med källkoden och verktyg. Utvecklaren kan också skapa en miljö som är nästan identisk i produktion-konfigurationen. Det underlättar utvecklingen snabbare inre slingan. 

### <a name="pre-production"></a>Förproduktion 
Att ha ett labb i CI/CD-pipeline gör det enklare att ha flera olika miljöer för Förproduktion eller datorer som körs på samma gång för asynkron testning. Olika parallella infrastrukturer, till exempel versionsagenter kan distribueras och hanteras i ett laboratorium. 

## <a name="devops-with-devtest-labs"></a>DevOps med DevTest Labs 

### <a name="development--operation"></a>Utveckling / åtgärd 
Ett labb bör vara fokuserar på ett team som arbetar i ett funktionsområde. Den här vanliga fokus möjliggör delning av området-specifika resurser, t.ex. verktyg, skript eller Resource Manager-mallar. Det gör snabbare ändringar och begränsa de negativa effekterna till en mindre grupp. Dessa delade resurser kan utvecklare skapa virtuella datorer med alla nödvändiga kod, verktyg och konfiguration. De kan skapas dynamiskt eller ha ett system som skapar Källavbildningen med anpassningarna. Inte bara kan utvecklare skapa virtuella datorer, men de kan också skapa labb miljöer baserat på mallarna som krävs för att skapa rätt Azure-resurser i laboratoriet. Ändringar eller destruktiva arbetet kan göras mot laboratoriemiljön utan att påverka andra. Föreställ dig ett scenario där produkten är ett fristående system som är installerad på kundens dator. I det här scenariot har DevTest Labs förbättrat skapa en virtuell dator som innehåller installera ytterligare programvara med hjälp av artefakter och förväg bygga kundkonfigurationer för snabbare inre slingan testning av koden. 
  
## <a name="cicd-pipeline"></a>CI/CD-pipeline 
CI/CD-pipeline är en av de viktigaste komponenterna i DevOps som flyttar kod från en pull-begäran för utvecklare, integrerar med befintlig kod och distribuerar den till produktion-ekosystemet. Alla resurser behöver inte vara i ett laboratorium. Till exempel kan en Jenkins-värd ställas in utanför labb som en mer permanent resurs. Här följer några specifika exempel på att integrera labb i pipelinen. 

### <a name="build"></a>Utveckla 
Skapa pipelinen fokuserar om hur du skapar ett paket med komponenter som ska testas tillsammans för att lämnas till versionspipelinen. Labs kan inte ingå i build-pipeline som platsen för versionsagenter och andra supportresurser. Du har möjlighet att dynamiskt skapa infrastrukturen kan för bättre kontroll. Med möjligheten att ha flera miljöer i ett labb kan körs varje version asynkront när build-ID som en del av miljöinformationen om för att unikt identifiera resurserna till den specifika versionen.   

Build-agenter, den testmiljön möjlighet att begränsa åtkomsten ökar säkerheten och minskar risken för oavsiktliga skador.  

### <a name="test"></a>Testa 
DevTest Labs kan en CI/CD-pipeline att automatisera skapandet av Azure-resurs (virtuella datorer, miljöer) som kan användas för för automatisk och manuell testning. De virtuella datorerna skapas med hjälp av artefakter eller formler som använder information från skapandeprocessen för att skapa olika anpassade konfigurationer som krävs för att testa.   

### <a name="release"></a>Frisläpp 
DevTest Labs används ofta för verifiering i avsnittet versionen innan koden har distribuerats. Det liknar testning i avsnittet Skapa. Produktionsresurser bör inte distribueras i DevTest Labs. 

### <a name="customization"></a>Anpassning 
Det finns befintliga aktiviteter som tillåter manipulering av virtuella datorer och miljöer inom specifika labb i Azure DevOps. Azure DevOps-tjänsterna är ett sätt att hantera CI/CD-pipeline, kan du integrera labbet till alla system som stöder möjligheten att anropa REST API: er, Kör PowerShell-skript eller använda Azure CLI. 

Även om vissa CI/CD-pipeline chefer har befintliga öppen källkod-plugin-program som kan hantera resurser i Azure och labb. Du kan behöva använda vissa anpassade skript för att passa de specifika behoven i pipelinen.  Med det i åtanke när du kör en uppgift används ett huvudnamn för tjänsten med rätt roll för att få åtkomst till labbet. Tjänstens huvudnamn måste vanligtvis rollen deltagaråtkomst till labbet. Mer information finns i [integrera Azure DevTests Labs i din Azure DevOps kontinuerlig integrering och leverans-pipeline](devtest-lab-integrate-ci-cd-vsts.md). 
 