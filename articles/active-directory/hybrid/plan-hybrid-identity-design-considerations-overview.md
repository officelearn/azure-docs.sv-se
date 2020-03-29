---
title: Azure Active Directory hybrid identitetsdesign överväganden - översikt | Microsoft-dokument
description: Översikts- och innehållskarta över guide för hybrididentitetsdesign
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381489"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory Hybrid Identity designöverväganden
Konsumentbaserade enheter sprider sig i företagsvärlden, och molnbaserade SaaS-program (Software-as-a-Service) är lätta att använda. Därför är det svårt att behålla kontrollen över användarnas programåtkomst över interna datacenter och molnplattformar.  

Microsofts identitetslösningar omfattar både lokala och molnbaserade funktioner, skapar en enda användaridentitet för autentisering och auktorisering till alla resurser, oavsett plats. Detta begrepp kallas Hybrid Identity. Det finns olika design- och konfigurationsalternativ för hybrididentitet med hjälp av Microsoft-lösningar, och i vissa fall kan det vara svårt att avgöra vilken kombination som bäst uppfyller organisationens behov. 

Denna Hybrid Identity Design Considerations Guide hjälper dig att förstå hur du utformar en hybrididentitetslösning som bäst passar företagets och teknikens behov för din organisation.  Den här guiden beskriver en rad steg och uppgifter som du kan följa för att hjälpa dig att utforma en hybrididentitetslösning som uppfyller organisationens unika krav. Under hela stegen och uppgifterna kommer guiden att presentera relevanta tekniker och funktionsalternativ som är tillgängliga för organisationer för att uppfylla funktions- och tjänstkvalitet (t.ex. tillgänglighet, skalbarhet, prestanda, hanterbarhet och säkerhet) Krav. 

Närmare bestämt är hybrid identitetsdesign överväganden guide mål att svara på följande frågor: 

* Vilka frågor behöver jag ställa och svara för att driva en hybrididentitetsspecifik design för en teknik- eller problemdomän som bäst uppfyller mina krav?
* Vilken sekvens av aktiviteter ska jag slutföra för att utforma en hybrididentitetslösning för teknik- eller problemdomänen? 
* Vilka hybrididentitetsteknik och konfigurationsalternativ finns tillgängliga för att hjälpa mig att uppfylla mina krav? Vilka är kompromisserna mellan dessa alternativ så att jag kan välja det bästa alternativet för mitt företag?

## <a name="who-is-this-guide-intended-for"></a>Vem riktar sig den här handboken till?
 CIO, CITO, Chief Identity Architects, Enterprise Architects och IT Architects med ansvar för att utforma en hybrididentitetslösning för medelstora eller stora organisationer.

## <a name="how-can-this-guide-help-you"></a>Hur kan den här handboken hjälpa dig?
Du kan använda den här guiden för att förstå hur du utformar en hybrididentitetslösning som kan integrera ett molnbaserat identitetshanteringssystem med din aktuella lokala identitetslösning. 

Följande bild visar ett exempel på en hybrididentitetslösning som gör det möjligt för IT-administratörer att hantera att integrera sin nuvarande Windows Server Active Directory-lösning som finns lokalt med Microsoft Azure Active Directory så att användare kan använda Enkel inloggning ( SSO) över program som finns i molnet och lokalt.

![Exempel](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

Ovanstående illustration är ett exempel på en hybrididentitetslösning som utnyttjar molntjänster för att integrera med lokala funktioner för att ge en enda upplevelse till slutanvändarens autentiseringsprocess och för att underlätta IT-hantering av dessa Resurser. Även om det här exemplet kan vara ett vanligt scenario, är varje organisations hybrididentitetsdesign sannolikt annorlunda än det exempel som visas i figur 1 på grund av olika krav. 

Den här guiden innehåller en serie steg och uppgifter som du kan följa för att utforma en hybrididentitetslösning som uppfyller organisationens unika krav. Under de följande stegen och uppgifterna visar guiden relevanta tekniker och funktionsalternativ som är tillgängliga för att uppfylla kraven på funktionell och servicekvalitetsnivå för din organisation.

**Antaganden:** Du har viss erfarenhet av Windows Server, Active Directory Domain Services och Azure Active Directory. I det här dokumentet antas att du letar efter hur dessa lösningar kan uppfylla dina affärsbehov på egen hand, eller i en integrerad lösning.

## <a name="design-considerations-overview"></a>Översikt över designöverväganden
Det här dokumentet innehåller en uppsättning steg och uppgifter som du kan följa för att utforma en hybrididentitetslösning som bäst uppfyller dina krav. Stegen visas i tur och ordning. Designöverväganden du lär dig i senare steg kan kräva att du ändrar beslut som du har fattat i tidigare steg, men på grund av motstridiga designval. Varje försök görs att varna dig för potentiella designkonflikter i hela dokumentet. 

Du kommer fram till den design som bäst uppfyller dina krav först efter iterering genom stegen så många gånger som behövs för att införliva alla överväganden i dokumentet. 

| Hybrid identitetsfas | Ämneslista |
| --- | --- |
| Fastställa identitetskrav |[Bestäm affärsbehov](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Fastställ krav på katalogsynkronisering](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Fastställa autentiseringskrav för flera faktorer](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definiera en strategi för antagande av hybrididentitet](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Planera för att förbättra datasäkerheten genom stark identitetslösning |[Bestäm dataskyddskrav](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Fastställa krav för innehållshantering](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Fastställa åtkomstkontrollkrav](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Fastställa krav för incidenthantering](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definiera dataskyddsstrategi](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Planera för hybrididentitetslivscykeln |[Ta reda på hybrididentitetshanteringsuppgifter](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Hantering av synkronisering](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Ta reda på strategi för antagande av hybrididentitetshantering](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Efterföljande moment
[Fastställa identitetskrav](plan-hybrid-identity-design-considerations-business-needs.md)

