---
title: Beredskapsöversikt för Azure Security Center | Microsoft Docs
description: I det här dokumentet finns en beredskapsöversikt för att komma igång med Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: yurid
ms.openlocfilehash: b2b4dc1aa9486e432fb2fae48343c21fba620221
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996691"
---
# <a name="azure-security-center-readiness-roadmap"></a>Beredskapsöversikt för Azure Security Center
I det här dokumentet får du en beredskapsöversikt som hjälper dig att komma igång med Azure Security Center.

## <a name="understanding-security-center"></a>Förstå Security Center
Azure Security Center har en enhetlig säkerhetshantering och avancerat skydd mot hot för arbetsbelastningar som körs i Azure, lokalt och i andra moln. 

Använd följande resurser för att komma igång med Security Center.

Artiklar
* [Introduktion till Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Snabbstartsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started)

Videor
* [Snabb introduktionsvideo](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [Översikt över Security Center-funktioner för skydd, identifiering och svar](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planering och drift
För att få ut mesta möjliga av Security Center är det viktigt att veta hur olika medarbetare och avdelningar i organisationen kommer att använda tjänsten så att kraven på säkerhet vid drift, övervakning, styrning och incidenthantering uppfylls.

Med följande resurser får du hjälp under planerings- och driftprocesserna.


Artikel
* [Planerings- och bruksanvisning för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

Video
* [Skydd för hybridmolns arbetsbelastningar med Security Center](https://mva.microsoft.com/training-courses/hybrid-cloud-workload-protection-with-azure-security-center-18173?l=X4WqTA3jE_1106218965)

### <a name="onboarding-computers-to-security-center"></a>Registrering av datorer till Security Center
Security Center identifierar automatiskt eventuella Azure-prenumerationer eller arbetsytor som inte är aktiverade för Security Center Standard. Det inkluderar Azure-prenumerationer med Security Center – Kostnadsfri och arbetsytor som inte har någon aktiverad säkerhetslösning.

Med följande resurser får du hjälp under registreringsprocesserna.

Artikel
* [Registrering till Azure Security Center Standard för ökad säkerhet](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

Video
* [Azure Security Center Hybrid – Översikt](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Minimera säkerhetsproblem med Security Center
Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera verkliga hot och minimera antalet falska positiva identifieringar.

Följande resurser hjälper dig att hantera säkerhetsaviseringar och skydda dina resurser.

Artiklar    
* [Övervakning av säkerhetshälsa i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Skydda nätverket i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Skydda Azure SQL-tjänsten och data i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Video   
* [Minimera säkerhetsproblem med Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Security Center för svar om incidenter
För att minska kostnaderna och skadorna är det viktigt att ha en incidenthanteringsplan på plats innan en attack äger rum. Azure Security Center kan användas i olika faser av en incidenthantering.

Med följande resurser kan du lära dig hur Security Center kan införlivas i processen för incidenthantering.

Videor  
* [Incidenthantering i Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Reagera snabbt på hot med nästa generations säkerhetsåtgärder och undersökning](https://youtu.be/e8iFCz5RM4g)

Artiklar    
* [Använda Azure Security Center vid incidenthantering](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Automatisera svar med säkerhetsspelbok](https://docs.microsoft.com/azure/security-center/security-center-playbooks)

## <a name="advanced-cloud-defense"></a>Avancerat molnskydd

Virtuella Azure-datorer kan dra nytta av funktioner för avancerat molnskydd i Security Center. Dessa funktioner omfattar just-in-Time-åtkomst till virtuella datorer och anpassningsbara program kontroller.

Använd följande resurser för att lära dig mer om hur du använder funktionerna i Security Center.

Videor  
* [Azure Security Center – just-in-Time VM-åtkomst](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center – Anpassningsbara programkontroller](https://youtu.be/wWWekI1Y9ck)

Artiklar    
* [Hantera åtkomst till virtuella datorer med just-in-Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Anpassningsbara programkontroller i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Praktiska aktiviteter

* [Praktiska övningar i Security Center](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Rekommendationsspelbok för brandvägg för webbaserade program (WAF) i Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Playbook för Azure Security Center: Säkerhets aviseringar @ no__t-0

## <a name="additional-resources"></a>Ytterligare resurser
* [Dokumentationssidan om Security Center](https://docs.microsoft.com/azure/security-center/)
* [Dokumentationssidan om REST-API för Security Center](https://msdn.microsoft.com/library/mt704034.aspx)
* [Vanliga frågor och svar om Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Prissättningssidan för Security Center](https://azure.microsoft.com/pricing/details/security-center/)
* [Metodtips för identitetssäkerhet](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)
* [Metodtips för nätverkssäkerhet](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices)
* [PaaS-rekommendationer](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Efterlevnad](https://www.microsoft.com/en-us/trustcenter/compliance/due-diligence-checklist)
* [Log Analytics-kunder kan nu använda Azure Security Center för att skydda sina hybrid moln arbets belastningar](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>Community-resurser

* [Security Center UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [Community-forum för Security Center](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



