---
title: Dirigera implementering av Azure DevTest Labs
description: Den här artikeln innehåller riktlinjer för dirigera implementering av Azure DevTest Labs i din organisation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e0ac09a68bda539fe7abd05fce1739d1a58a3c99
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127352"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Dirigera implementeringen av Azure DevTest Labs
Den här artikeln innehåller en rekommenderad metod för snabb utveckling och implementering av Azure DevTest Labs. Följande bild visar den övergripande processen som vägledning när du får flexibilitet för att stödja olika branschspecifika krav och scenarier.

![Steg för att implementera Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Antaganden
Den här artikeln förutsätter att du har följande objekt på plats innan du implementerar ett pilotprojekt Labb:

- **Azure-prenumeration**: Pilotteamet har åtkomst till att distribuera resurser till en Azure-prenumeration. Om arbetsbelastningarna som är endast utveckling och testning, rekommenderar vi att du väljer Enterprise DevTest-erbjudande för ytterligare tillgängliga avbildningar och lägre priser på Windows-datorer.
- **Lokal åtkomst**: Om det behövs har åtkomst till lokala redan konfigurerats. Lokal åtkomst kan utföras via en plats-till-plats-VPN-anslutning eller via Express Route. Anslutning via Express Route kan vanligtvis ta flera veckor att upprätta, vi rekommenderar att du har Express-Route på plats innan du startar projektet.
- **Pilotteam team**: Ett inledande utvecklingsarbete projekt eller flera team som använder labb har identifierats tillsammans med tillämpliga utveckling eller testning aktiviteter och upprätta krav/mål/strategier för dessa grupper.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Milstolpe 1: Upprätta inledande nätverkets topologi och design
Det första området i fokus när du distribuerar en Azure DevTest Labs-lösning är att upprätta en planerad anslutning för virtuella datorer. Följande steg beskriver nödvändiga procedurer:

1. Definiera **inledande IP-adressintervall** som har tilldelats DevTest Labs-prenumeration i Azure. Det här steget kräver prognoser förväntad användning i många virtuella datorer så att du kan ange en stor tillräckligt med block för framtida expansioner.
2. Identifiera **metoder för att lägga till** i DevTest Labs (till exempel externa / internt åtkomst). En viktig aspekt i det här steget är att avgöra om virtuella datorer som har offentliga IP-adresser (det vill säga kan nås från internet, direkt).
3. Identifiera och upprätta **metoderna av anslutning** med resten av Azure cloud-miljö och lokalt. Om Tvingad routning med Expressroute är aktiverad, är det troligt att de virtuella datorerna ha lämpliga proxy-konfigurationer för att bläddra i företagets brandvägg.
4. Om virtuella datorer som ska vara **domänanslutna**, avgöra om de ansluta till en molnbaserad-domän (AAD Directory Services till exempel) eller en lokal domän. Lokalt, avgör i vilken organisationsenhet (OU) i active directory som de virtuella datorerna ansluter till. Kontrollera också att användarna har åtkomst för att ansluta till (eller upprätta ett tjänstkonto som har möjlighet att skapa datorn poster i domänen)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Milstolpe 2: Distribuera pilot labbet
När nätverkstopologin är på plats, första/pilot-labb kan skapas genom att utföra följande stegen:

1. Skapa en första labb-miljö (stegvisa instruktioner hittar [här](https://github.com/Azure/fta-devops/blob/master/devtest-labs/articles/devtest-labs-walkthrough-it.md))
2. Fastställa tillåten VM-avbildningar och storlekar för användning med lab. Bestäm om anpassade avbildningar kan överföras till Azure för användning med DevTest Labs.
3. Säker åtkomst till labbet genom att skapa inledande rollbaserad Base åtkomst kontroller (RBAC) för labbet (labbägare och labbanvändare). Vi rekommenderar att du använder synkroniserad active directory-konton med Azure Active Directory för Identitetshantering med DevTest Labs.
4. Konfigurera labb för att använda principer, till exempel scheman, kostnaden hantering, tillgängliga virtuella datorer, anpassade avbildningar eller formler.
5. Upprätta en online, till exempel Azure lagringsplatser/Git-lagringsplatsen.
6. Bestäm om användning av offentliga eller privata lagringsplatser eller kombination av båda. Organisera JSON-mallar för distributioner och långsiktig sustainment.
7. Om det behövs kan du skapa anpassade artefakter. Det här steget är valfritt. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Milstolpe 3: Dokumentation, support, Läs och förbättra
De första pilotprojekt teamen kan kräva djupgående stöd för att komma igång. Använda deras upplevelser för att se till att relevant information och support finns på plats för fortsatt distribution av Azure DevTest Labs.

1. Introducera pilot team till sina nya DevTest Labs-resurser (demonstrationer, dokumentation)
2. Baserat på pilot team upplevelser, planera och leverera dokumentation efter behov
3. Formalisera process för onboarding nya team (skapa och konfigurera labs, vilket ger åtkomst, osv)
4. Baserat på inledande upptaget, kontrollera ursprungliga prognoser för IP-adressutrymme är fortfarande rimligt och korrekt
5. Se till att lämpliga granskningar för efterlevnad och säkerhet har slutförts

## <a name="next-steps"></a>Nästa steg
Se nästa artikel i den här serien: [Styrning i Azure DevTest Labs-infrastruktur](devtest-lab-guidance-governance-resources.md)
