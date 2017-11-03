---
title: "Azure Active Directory hybrid identity designöverväganden - översikt | Microsoft Docs"
description: "Översikt och innehåll karta över designguide Hybrid Identity"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: e2a70f2474298618dd8ee11c583f8f445d7eba7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory Hybrid Identity designöverväganden
Konsumenten-baserade enheter proliferating företagets världen och molnbaserade program för programvara som en tjänst (SaaS) är lätta att vidta. Därför kan behålla kontrollen över användarnas programåtkomst plattformar interna datacenter och moln en utmaning.  

Microsofts identitetslösningar omfattar lokala och molnbaserade funktioner, skapar en enda användaridentitet för autentisering och auktorisering för alla resurser, oavsett plats. Vi kallar detta Hybrididentitet. Det finns olika design och konfigurationsalternativ för hybrididentitet med hjälp av Microsoft solutions och i vissa fall kan det vara svårt att avgöra vilken kombination bästa uppfyller behoven för din organisation. 

Den här Designguide för Hybrid Identity hjälper dig att förstå hur för att utforma en hybrididentitetslösning som passar bäst för verksamheten och teknikbehov för din organisation.  Den här guiden innehåller en rad steg och aktiviteter som du kan följa för att hjälpa dig att utforma en hybrididentitetslösning som uppfyller organisationens unika krav. I steg och aktiviteter, kommer guiden presentera relevanta tekniker och funktioner finns tillgängliga för organisationer att uppfylla funktions- och kvalitetskrav (till exempel tillgänglighet, skalbarhet, prestanda, hantering och säkerhet) nivå krav. 

Mer specifikt är hybrid identity överväganden guiden designmålen att besvara följande frågor: 

* Vilka frågor behöver jag be och besvara för att hitta en identity-specifika hybridutformning för en domän med teknik- eller problemdomän som bäst uppfyller Mina krav?
* Vilka sekvensen av aktiviteter bör slutföras för att utforma en hybrididentitetslösning för domänen teknik eller problem? 
* Vilka hybrid identity-teknik och vilka konfigurationsalternativ finns tillgängliga för att hjälpa mig uppfylla kraven? Vad är avvägningarna mellan dessa alternativ så att jag kan välja det bästa alternativet för mitt företag?

## <a name="who-is-this-guide-intended-for"></a>Vem riktar den här handboken till?
 CHEF, CITO, chefen identitet arkitekter, Enterprise-arkitekter och IT-arkitekter som ansvarar för att utforma en hybrididentitetslösning för medelstora eller stora organisationer.

## <a name="how-can-this-guide-help-you"></a>Hur kan den här handboken hjälpa dig?
Du kan använda den här guiden för att förstå hur du utformar en hybrididentitetslösning som går att integrera en molnbaserad identity management-systemet med din aktuella lokal identitetslösning. 

Följande bild visar ett exempel en hybrididentitetslösning som gör att IT-administratörer att hantera deras aktuella Windows Server Active Directory lösningen som finns lokalt integrera med Microsoft Azure Active Directory så att användarna kan använda enkel inloggning ( SSO) över program som finns i molnet och lokalt.

![](./media/hybrid-id-design-considerations/hybridID-example.png)

På bilden ovan är ett exempel på en hybrididentitetslösning som använder molntjänster till att integrera med lokala funktioner för att ge en enda upplevelse för slutanvändaren autentiseringsprocessen och för att underlätta IT hanterar de resurser. Även om detta kan vara ett mycket vanligt scenario, kommer varje organisations identitet hybridutformning skiljer sig från exemplet som illustreras i bild 1 på grund av olika krav. 

Den här guiden innehåller en serie steg och aktiviteter som du kan följa om du vill skapa en hybrididentitetslösning som uppfyller organisationens unika krav. I följande steg och aktiviteter presenterar handboken relevanta tekniker och Funktionsalternativ för att uppfylla funktions- och tjänsten kvalitetsnivån som krävs för din organisation.

**Antaganden**: du har viss erfarenhet av Windows Server, Active Directory Domain Services och Azure Active Directory. I det här dokumentet förutsätter vi att du söker efter hur lösningarna kan uppfylla företagets behov på egen hand eller i en integrerad lösning.

## <a name="design-considerations-overview"></a>Översikt över design-överväganden
Det här dokumentet innehåller en uppsättning steg och aktiviteter som du kan följa om du vill skapa en hybrididentitetslösning som bäst uppfyller dina krav. Stegen visas i tur och ordning. Överväganden vid utformning av du lär dig i senare steg kan kräva att du ändrar beslut som du tog tidigare, men på grund av konflikter som uppstår. Varje försök görs att varna dig om eventuella designkonflikter i hela dokumentet. 

Du kommer till vilken design som bäst motsvarar dina krav förrän du har gått igenom stegen så många gånger som behövs för att alla tänkbara överväganden i dokumentet. 

| Hybrid Identity fas | Avsnittet innehåller en listan |
| --- | --- |
| Ange identitetskrav för |[Fastställa affärsbehov](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Ange krav för directory-synkronisering](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Ange krav för multifaktorautentisering](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definiera en strategi för införandet en hybrid identity](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Planera för att förbättra datasäkerhet via starkt identitetslösning |[Fastställa kraven på dataskydd](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Ange krav för innehållshantering](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Fastställa krav på åtkomstkontroll](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Fastställa kraven på incidentsvar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definiera en strategi för skydd av data](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Planera för hybrid identity livscykel |[Fastställa hanteringsuppgifter för hybrid identity](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Hantering av datasynkronisering](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Fastställa införandestrategin hybrid identity](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="download-this-guide"></a>Hämta denna guide
Du kan hämta en pdf-version av guiden överväganden vid utformning av Hybrid från den [Technet-galleriet](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288). 

