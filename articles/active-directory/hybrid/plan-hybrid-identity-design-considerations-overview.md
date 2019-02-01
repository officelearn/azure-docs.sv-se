---
title: Azure Active Directory hybrid identity designöverväganden - översikt | Microsoft Docs
description: Översikt och innehållskarta av Designguide för Hybrididentitet
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
ms.openlocfilehash: 646c929c3f20f50a52cc8bd29d06522aa608b683
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489843"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory Hybrid Identity designöverväganden
Konsument-baserade enheter proliferating företagets världen och molnbaserade program för programvara som tjänst (SaaS) är lätta att införa. Därför kan underhålla kontroll över användarnas programåtkomst på interna datacenter och plattformar en utmaning.  

Microsofts identitetslösningar omfattar både lokala och molnbaserade funktioner, skapar en enda användaridentitet för autentisering och auktorisering till alla resurser, oavsett plats. Detta begrepp som kallas Hybrid-identitet. Det finns olika design och konfigurationsalternativ för hybrididentitet med lösningar från Microsoft och i vissa fall kan det vara svårt att avgöra vilken kombination bästa uppfylla behoven i din organisation. 

Den här Designguide för Hybrid Identity hjälper dig att förstå hur du utformar en hybrididentitetslösning som passar bäst för verksamheten och teknikbehov för din organisation.  Den här guiden beskriver en serie steg och aktiviteter som du kan följa för att hjälpa dig att utforma en hybrididentitetslösning som uppfyller din organisations unika krav. I steg och aktiviteter, kommer guiden presenterar de relevanta teknikerna och funktioner finns tillgängliga för organisationer att uppfylla funktions- och kvalitetskrav (till exempel tillgänglighet, skalbarhet, prestanda, hanterbarhet och säkerhet) nivå krav. 

Mer specifikt är hybrid identity design överväganden för mål att besvara följande frågor: 

* Vilka frågor behöver jag att ställa och besvara för att hitta en identity-specifika hybridutformning för en domän med teknik- eller problemdomän som bäst uppfyller Mina krav?
* Vilka sekvensen av aktiviteter bör jag slutföra för att utforma en hybrididentitetslösning för domänen teknik- eller problemdomänen? 
* Vilka hybrid identity teknik och vilka konfigurationsalternativ är tillgängliga för att hjälpa mig uppfylla kraven? Vad är avvägningarna mellan dessa alternativ så att jag kan välja det bästa alternativet för mitt företag?

## <a name="who-is-this-guide-intended-for"></a>Vem den här guiden riktar sig till?
 CIO, CITO, Chief identitet arkitekter, Enterprise-arkitekter och IT-arkitekter som ansvarar för att utforma en hybrididentitetslösning för medelstora eller stora organisationer.

## <a name="how-can-this-guide-help-you"></a>Hur kan den här guiden hjälpa dig?
Du kan använda den här guiden för att förstå hur du utformar en hybrididentitetslösning som kan integrera en molnbaserade identitetshanteringssystem med din aktuella lokala identitetslösning. 

Följande bild visar ett exempel en hybrididentitetslösning som gör det möjligt för IT-administratörer att hantera att integrera sina aktuella Windows Server Active Directory enhetshanteringslösning som finns på plats med Microsoft Azure Active Directory så att användarna kan använda enkel inloggning) SSO) mellan program som finns i molnet och lokalt.

![Exempel](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

På bilden ovan är ett exempel på en hybrididentitetslösning som använder molntjänster för att integrera med lokala funktioner för att tillhandahålla en enskild upplevelse för slutanvändaren autentiseringsprocessen och för att underlätta IT hanterar de resurser. Även om det här exemplet kan vara ett vanligt scenario, kommer varje organisations identitet hybridutformning att skilja sig från exemplet som illustreras i bild 1 på grund av olika krav. 

Den här guiden innehåller en serie steg och aktiviteter som du kan följa för att utforma en hybrididentitetslösning som uppfyller din organisations unika krav. I följande steg och aktiviteter presenterar guiden relevanta tekniker och Funktionsalternativ som finns tillgängliga för dig att uppfylla funktions- och service kvalitetsnivån för din organisation.

**Antaganden**: Du har erfarenhet med Windows Server, Active Directory Domain Services och Azure Active Directory. I det här dokumentet förutsätts det du letar efter hur dessa lösningar kan uppfylla företagets behov på egen hand eller i en integrerad lösning.

## <a name="design-considerations-overview"></a>Översikt över design-överväganden
Det här dokumentet innehåller en uppsättning steg och aktiviteter som du kan följa för att utforma en hybrididentitetslösning som bäst uppfyller dina krav. Stegen visas i tur och ordning. Designöverväganden för dig i senare steg kan kräva att du ändrar beslut som du gjorde i tidigare steg, men på grund av konflikter som uppstår. Varje försök görs att varna dig om eventuella designkonflikter i hela dokumentet. 

Du kommer till vilken design som bäst uppfyller dina krav förrän går igenom stegen så många gånger som behövs för att alla tänkbara överväganden i dokumentet. 

| Hybrid Identity fas | Avsnittet lista |
| --- | --- |
| Fastställa identitetskrav |[Fastställa affärsbehov](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Fastställa katalogsynkroniseringskrav](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Fastställa krav för multifaktorautentisering](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definiera en hybrid identity införandestrategi](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Planering för att utöka datasäkerhet via stark identitetslösning |[Fastställa kraven på dataskydd](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Fastställa krav för innehållshantering](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Fastställa krav på åtkomstkontroll](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Fastställa kraven på incidentsvar](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definiera en strategi för skydd av data](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Planera för hybrididentitetslivscykeln |[Fastställa hanteringsuppgifter för hybrid identity](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Hantering av datasynkronisering](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Fastställa införandestrategin för hybrid identity](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Nästa steg
[Fastställa identitetskrav för](plan-hybrid-identity-design-considerations-business-needs.md)

