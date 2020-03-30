---
title: Distributionsplaner – Azure Active Directory | Microsoft-dokument
description: Heltäckande vägledning om hur du distribuerar många Azure Active Directory-funktioner.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17e6708225262349d56c6e261895882e9c31677f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262222"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-distributionsplaner
Letar du efter heltäckande vägledning om distribution av Azure Active Directory-funktioner (Azure AD)? Azure AD-distributionsplaner går igenom affärsvärdet, planeringsöverväganden och operativa procedurer som krävs för att distribuera vanliga Azure AD-funktioner.

Från någon av plansidorna använder du webbläsarens utskrift till PDF-kapacitet för att skapa en uppdaterad offlineversion av dokumentationen.
## <a name="include-the-right-stakeholders"></a>Inkludera rätt intressenter

När du påbörjar distributionsplaneringen för en ny funktion är det viktigt att inkludera viktiga intressenter i hela organisationen. Vi rekommenderar att du identifierar och dokumenterar den eller de personer som fyller i var och en av följande roller och arbetar med dem för att fastställa deras deltagande i projektet.  

Roller kan omfatta följande 

|Roll |Beskrivning |
|-|-|
|Slutanvändaren|En representativ grupp användare för vilka funktionen kommer att implementeras. Ofta förhandsgranskas ändringarna i ett pilotprogram.
|IT-supporthanterare|IT-supportorganisationsrepresentant som kan ge input om supporten av den här ändringen ur ett helpdesk-perspektiv.  
|Identitetsarkitekt eller Global Azure-administratör|Representant för identitetshanteringsgruppen som ansvarar för att definiera hur den här ändringen är anpassad till den grundläggande identitetshanteringsinfrastrukturen i organisationen.|
|ProgramFöretagsägare |Den övergripande företagsägaren av de berörda programen, vilket kan innefatta hantering av åtkomst.Kan också ge synpunkter på användarupplevelsen och nyttan av denna förändring från en slutanvändares perspektiv.
|Säkerhetsägare|En representant från säkerhetsteamet som kan signera att planen uppfyller organisationens säkerhetskrav.|
|Compliance Manager (Efterlevnadshanteraren)|Den person inom organisationen som ansvarar för att säkerställa efterlevnad av företagets, branschens eller myndigheters krav.|

**Graden av engagemang kan omfatta:**

- **R**esponsible för genomförande av projektplan och utfall 

- **Ett**pproval av projektplan och utfall 

- **C**ontributor till projektplan och utfall 

- **Jag**har inte formade projektplan och utfall


## <a name="best-practices-for-a-pilot"></a>Bästa praxis för en pilot
En pilot kan du testa med en liten grupp innan du slår på en kapacitet för alla. Se till att varje användningsfall i organisationen testas noggrant som en del av testningen. Det är bäst att rikta in sig på en viss grupp pilotanvändare innan du distribuerar det här till organisationen som helhet.

I din första våg, mål IT, användbarhet och andra lämpliga användare som kan testa och ge feedback. Den här feedbacken bör användas för att vidareutveckla den kommunikation och de instruktioner du skickar till användarna och för att ge insikter om vilka typer av problem som supportpersonalen kan se. 

En utvidgning av utbyggnaden till större användargrupper bör ske genom att omfattningen av den eller de målgrupper som är avsedda öka. Detta kan göras genom [dynamiskt gruppmedlemskap](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)eller genom att manuellt lägga till användare i målgruppen/målgrupperna.


## <a name="deploy-authentication"></a>Distribuera autentisering

| Funktion | Beskrivning|
| -| -|
| [Multifaktorautentisering](https://aka.ms/deploymentplans/mfa)| Azure Multi-Factor Authentication (MFA) är Microsofts verifieringslösning i två steg. Med hjälp av administratörsgodkända autentiseringsmetoder hjälper Azure MFA till att skydda åtkomsten till dina data och program samtidigt som du uppfyller kravet på en enkel inloggningsprocess. |
| [Villkorlig åtkomst](https://aka.ms/deploymentplans/ca)| Med villkorlig åtkomst kan du implementera beslut om automatisk åtkomstkontroll för vem som kan komma åt dina molnappar, baserat på villkor. |
| [Självåterställning av lösenord](https://aka.ms/deploymentplans/sspr)| Återställning av lösenord med självbetjäning hjälper användarna att återställa sina lösenord utan administratörsingripande, när och var de behöver. |
| [Lösenordsfri](https://aka.ms/deploymentplans/passwordless) | Implementera lösenordslös autentisering med hjälp av Microsoft Authenticator-appen eller FIDO2-säkerhetsnycklarna i organisationen |

## <a name="deploy-application-management"></a>Distribuera programhantering

| Funktion | Beskrivning|
| -| - |
| [Enkel inloggning](https://aka.ms/deploymentplans/sso)| Enkel inloggning hjälper användarna att komma åt de appar och resurser de behöver för att göra affärer när de bara loggar in en gång. När de har loggat in kan de gå från Microsoft Office till SalesForce till Box till interna program utan att behöva ange autentiseringsuppgifter en andra gång. |
| [Åtkomstpanel](https://aka.ms/deploymentplans/accesspanel)| Erbjud användarna ett enkelt nav för att upptäcka och komma åt alla sina program. Gör det möjligt för dem att bli mer produktiva med självbetjäningsfunktioner, till exempel begära åtkomst till appar och grupper eller hantera åtkomst till resurser för andras räkning. |


## <a name="deploy-hybrid-scenarios"></a>Distribuera hybridscenarier

| Funktion | Beskrivning|
| -| -|
| [ADFS till hash-synkronisering av lösenord](https://aka.ms/deploymentplans/adfs2phs)| Med synkronisering av lösenord hash synkroniseras hashar av användarlösenord från lokala Active Directory till Azure AD, vilket gör att Azure AD autentiserar användare utan interaktion med den lokala Active Directory |
| [ADFS till Pass Through Authentication](https://aka.ms/deploymentplans/adfs2pta) (Passeringsautentisering)| Azure AD Pass-through-autentisering hjälper användarna att logga in på både lokala och molnbaserade program med samma lösenord. Den här funktionen ger användarna en bättre upplevelse - ett lösenord mindre att komma ihåg - och minskar IT-helpdesk-kostnaderna eftersom användarna är mindre benägna att glömma hur de ska logga in. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory. |
| [Azure AD-programproxy](https://aka.ms/deploymentplans/appproxy)| I dag vill anställda vara produktiva var som helst, när som helst och från valfri enhet. De måste komma åt SaaS-appar i molnet och företagsappar lokalt. Azure AD Application proxy möjliggör denna robusta åtkomst utan kostsamma och komplexa virtuella privata nätverk (VPN) eller demilitariserade zoner (DMZs). |
| [Sömlös enkel inloggning](../hybrid/how-to-connect-sso-quick-start.md)| Sömlös enkel inloggning med Azure Active Directory (sömlös SSO med Azure AD) loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket. Med den här funktionen behöver användarna inte skriva in sina lösenord för att logga in på Azure AD och behöver vanligtvis inte ange sina användarnamn. Den här funktionen ger behöriga användare enkel åtkomst till dina molnbaserade program utan att behöva några ytterligare lokala komponenter. |

## <a name="deploy-user-provisioning"></a>Distribuera etablering av användare

| Funktion | Beskrivning|
| -| -|
| [Användaretablering](https://aka.ms/deploymentplans/userprovisioning)| Azure AD hjälper dig att automatisera skapande, underhåll och borttagning av användaridentiteter i molnprogram (SaaS), till exempel Dropbox, Salesforce, ServiceNow och mer. |
| [Etablerande av användare i molnet](https://aka.ms/deploymentplans/cloudhr)| Cloud HR-användare etablering till Active Directory skapar en grund för pågående identitetsstyrning och förbättrar kvaliteten på affärsprocesser som är beroende av auktoritära identitetsdata. Med den här funktionen med din moln-HR-produkt, till exempel Workday eller Successfactors, kan du sömlöst hantera identitetslivscykeln för anställda och villkorade arbetare genom att konfigurera regler som mappar Joiner-Mover-Leaver-processer (till exempel Nyanställda, Avsluta, Överföring) till IT-etableringsåtgärder (till exempel Skapa, Aktivera, Inaktivera) |

## <a name="deploy-governance-and-reporting"></a>Distribuera styrning och rapportering

| Funktion | Beskrivning|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) hjälper dig att hantera privilegierade administrativa roller i Azure AD, Azure-resurser och andra Microsoft Online-tjänster. PIM tillhandahåller lösningar som just-in-time-åtkomst, begäran om godkännandearbetsflöden och fullständigt integrerade åtkomstgranskningar så att du kan identifiera, upptäcka och förhindra skadliga aktiviteter med privilegierade roller i realtid. |
| [Rapportering och övervakning](https://aka.ms/deploymentplans/reporting)| Utformningen av din Azure AD-rapporterings- och övervakningslösning beror på dina juridiska, säkerhets- och driftskrav samt din befintliga miljö och dina befintliga processer. I den här artikeln visas de olika designalternativen och du får rätt distributionsstrategi. |
