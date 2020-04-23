---
title: Orkestrera implementering av Azure DevTest Labs
description: Den här artikeln innehåller vägledning för att samordna implementeringen av Azure DevTest Labs i din organisation.
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
ms.openlocfilehash: fc6cbbd0aa9cb0750e497c7cc7edbd42f21bda55
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025007"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Dirigera implementeringen av Azure DevTest Labs
Den här artikeln innehåller en rekommenderad metod för snabb distribution och implementering av Azure DevTest Labs. Följande bild betonar den övergripande processen som normativ vägledning samtidigt som flexibiliteten iakttas för att stödja olika branschkrav och scenarier.

![Steg för att implementera Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Antaganden
Den här artikeln förutsätter att du har följande objekt på plats innan du implementerar en DevTest Labs-pilot:

- **Azure-prenumeration:** Pilotteamet har åtkomst till distribution av resurser till en Azure-prenumeration. Om arbetsbelastningarna bara är utveckling och testning rekommenderar vi att du väljer Enterprise DevTest-erbjudandet för ytterligare tillgängliga avbildningar och lägre priser på virtuella Windows-datorer.
- **Lokal åtkomst**: Vid behov har lokal åtkomst redan konfigurerats. Den lokala åtkomsten kan ske via en VPN-anslutning från plats till plats eller via Express Route. Anslutning via Express Route kan vanligtvis ta många veckor att upprätta, det rekommenderas att ha Express Route på plats innan du startar projektet.
- **Pilotteam:** De inledande utvecklingsprojektteamen som använder DevTest Labs har identifierats tillsammans med tillämpliga utvecklings- eller testaktiviteter och fastställer krav/mål/mål för dessa team.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Milstolpe 1: Upprätta inledande nätverkstopologi och design
Det första fokusområdet när du distribuerar en Azure DevTest Labs-lösning är att upprätta den planerade anslutningen för de virtuella datorerna. I följande steg beskrivs de nödvändiga förfarandena:

1. Definiera **inledande IP-adressintervall** som har tilldelats DevTest Labs-prenumerationen i Azure. Det här steget kräver att du prognostisera den förväntade användningen i antal virtuella datorer så att du kan tillhandahålla ett tillräckligt stort block för framtida expansion.
2. Identifiera metoder för **önskad åtkomst** till DevTest Labs (till exempel extern/ intern åtkomst). En viktig punkt i det här steget är att avgöra om virtuella datorer har offentliga IP-adresser (det vill ha direkt tillgängligt från Internet).
3. Identifiera och upprätta **metoder för anslutning** med resten av Azure-molnmiljön och lokala. Om den påtvingade routningen med Express Route är aktiverad är det troligt att de virtuella datorerna behöver lämpliga proxykonfigurationer för att gå igenom företagets brandvägg.
4. Om virtuella datorer ska **domänanslutas**bestämmer du om de ansluter till en molnbaserad domän (AAD Directory Services till exempel) eller en lokal domän. För lokala, bestämma vilken organisationsenhet (OU) inom active directory som de virtuella datorerna ansluter. Bekräfta dessutom att användare har åtkomst till att gå med (eller upprätta ett tjänstkonto som har möjlighet att skapa maskinposter i domänen)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Milstolpe 2: Distribuera pilotlabbet
När nätverkstopologin är på plats kan det första/pilotlabbet skapas genom att vidta följande åtgärder:

1. Skapa en första DevTest Labs-miljö.
2. Bestäm tillåtna VM-avbildningar och storlekar för användning med labb. Bestäm om anpassade avbildningar kan överföras till Azure för användning med DevTest Labs.
3. Säker åtkomst till labbet genom att skapa inledande rollbasåtkomstkontroller (RBAC) för labbet (labbägare och labbanvändare). Vi rekommenderar att du använder synkroniserade active directory-konton med Azure Active Directory för identitet med DevTest Labs.
4. Konfigurera DevTest Labs så att principer som scheman, kostnadshantering, anspråksbara virtuella datorer, anpassade avbildningar eller formler används.
5. Upprätta en onlinedatabas som Azure Repos/Git.
6. Besluta om användningen av offentliga eller privata förråd eller kombination av båda. Organisera JSON-mallar för distributioner och långsiktig underhåll.
7. Skapa anpassade artefakter om det behövs. Det här steget är valfritt. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Milstolpe 3: Dokumentation, support, lära och förbättra
De första pilotteamen kan behöva ingående stöd för att komma igång. Använd deras upplevelser för att säkerställa att rätt dokumentation och support finns på plats för fortsatt distribution av Azure DevTest Labs.

1. Introducera pilotteamen till deras nya DevTest Labs-resurser (demos, dokumentation)
2. Baserat på pilotteamens erfarenheter, planera och leverera dokumentation efter behov
3. Formalisera processen för introduktion av nya team (skapa och konfigurera labb, ge åtkomst, etc)Formalize process for onboarding new teams (creating and configuring labs, providing access, etc)
4. Baserat på det första upptaget, kontrollera ursprungliga prognosen för IP-adressutrymme är fortfarande rimligt och korrekt
5. Se till att lämpliga efterlevnads- och säkerhetsgranskningar har slutförts

## <a name="next-steps"></a>Nästa steg
Se nästa artikel i den här serien: [Styrning av Azure DevTest Labs-infrastruktur](devtest-lab-guidance-governance-resources.md)
