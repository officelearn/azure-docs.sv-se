---
title: Distribution - planer i Azure Active Directory | Microsoft Docs
description: Slutpunkt till slutpunkt vägledning om hur du distribuerar många funktioner i Azure Active Directory.
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
ms.openlocfilehash: 6a335bcbb45b1aac48a3aaea282ffeffe1696f4c
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900070"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-distributionsplaner
Letar du efter slut punkt till slut punkt om hur du distribuerar Azure Active Directory (Azure AD)-funktioner? Distributions planer för Azure AD vägleder dig genom affärs värde, planerings överväganden och operativa procedurer som krävs för att distribuera vanliga funktioner i Azure AD.

Från någon av plan sidorna använder du webbläsarens utskrifts-till-PDF-funktion för att skapa en uppdaterad offline-version av dokumentationen.
## <a name="include-the-right-stakeholders"></a>Ta med rätt intressenter

När du påbörjar distributions planeringen för en ny funktion är det viktigt att inkludera viktiga intressenter i organisationen. Vi rekommenderar att du identifierar och dokumenterar den person eller de personer som uppfyller var och en av följande roller, och arbetar med dem för att fastställa deras medverkan i projektet.  

Roller kan innehålla följande 

|Role |Beskrivning |
|-|-|
|Slutanvändare|En representativ grupp användare för vilka funktionen ska implementeras. Visar ofta ändringarna i ett pilotprogram.
|IT-support ansvarig|IT-avdelningen stöder organisations representant som kan tillhandahålla insikter om den här förändringen från ett support perspektiv.  
|Identitets arkitekt eller Global Azure-administratör|Identitets hanterings team som är ansvarig för att definiera hur den här ändringen justeras med infrastrukturen för kärn identitets hantering i din organisation.|
|Programmets företags ägare |Den övergripande företags ägaren av de berörda programmen, som kan innehålla hanterings åtkomst.  Kan även ge indata om användar upplevelsen och användbarheten för den här ändringen från slutanvändarens perspektiv.
|Säkerhets ägare|En representant från säkerhets teamet som kan godkänna att planen uppfyller organisationens säkerhets krav.|
|Compliance Manager|Den person i organisationen som ansvarar för att säkerställa efterlevnaden av företags-, bransch-eller myndighets krav.|

**Inblandning nivåer kan vara:**

- **R**-esponsible för implementering av projekt plan och resultat 

- **En**pproval av projekt plan och resultat 

- **C**-ontributor till projekt plan och resultat 

- **I**nformed för projekt plan och resultat


## <a name="deploy-authentication"></a>Distribuera autentisering

| Funktion | Beskrivning|
| -| -|
| [Multi-Factor Authentication](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-getstarted)| Azure Multi-Factor Authentication (MFA) är Microsofts verifieringslösning i två steg. Med hjälp av godkända autentiseringsmetoder bidrar Azure MFA till att skydda åtkomsten till dina data och program samtidigt som du kan uppfylla behovet av en enkel inloggnings process. |
| [Villkorlig åtkomst](https://aka.ms/deploymentplans/ca)| Med villkorlig åtkomst kan du implementera automatiska åtkomst kontroll beslut för vilka som kan komma åt dina molnappar, baserat på villkor. |
| [Självåterställning av lösenord](https://aka.ms/deploymentplans/sspr)| Lösen ords återställning via självbetjäning hjälper dina användare att återställa sina lösen ord utan att administratören behöver göra det, när och var de behöver. |

## <a name="deploy-application-management"></a>Distribuera program hantering

| Funktion | Beskrivning|
| -| - |
| [Enkel inloggning](https://aka.ms/deploymentplans/sso)| Enkel inloggning hjälper dina användare att komma åt de appar och resurser som de behöver för att göra affärer medan de bara loggar in en gång. När de har loggat in kan de gå från Microsoft Office till SalesForce till Box till interna program utan att behöva ange autentiseringsuppgifterna en andra gång. |
| [Åtkomst panel](https://aka.ms/deploymentplans/accesspanel)| Ger användarna en enkel hubb för att upptäcka och komma åt sina program. Gör det möjligt för dem att bli mer produktiva med självbetjänings funktioner, t. ex. att begära åtkomst till appar och grupper eller att hantera åtkomst till resurser för andras räkning. |


## <a name="deploy-hybrid-scenarios"></a>Distribuera hybrid scenarier

| Funktion | Beskrivning|
| -| -|
| [ADFS till hash-synkronisering av lösenord](https://aka.ms/deploymentplans/adfs2phs)| Med hash-synkronisering av lösen ord synkroniseras hashar av användar lösen ord från lokala Active Directory till Azure AD, vilket gör att Azure AD kan autentisera användare utan interaktion med den lokala Active Directory |
| [ADFS till Pass Through Authentication](https://aka.ms/deploymentplans/adfs2pta) (Passeringsautentisering)| Med Azure AD-direktautentisering kan användarna logga in i både lokala och molnbaserade program med samma lösen ord. Den här funktionen ger användarna en bättre upplevelse – ett mindre lösen ord att komma ihåg – och minskar kostnaderna för IT-supportavdelningen eftersom det är mindre troligt att de glömmer att logga in. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory. |
| [Azure AD-programproxy](https://aka.ms/deploymentplans/appproxy)| I dag vill anställda vara produktiva var som helst, när som helst och från valfri enhet. De behöver åtkomst till SaaS-appar i molnet och i företags program lokalt. Azure AD Application Proxy ger denna robust åtkomst utan kostsamma och komplexa virtuella privata nätverk (VPN) eller demilitariserad zoner (DMZs). |
| [Sömlös enkel inloggning](https://aka.ms/SeamlessSSODPDownload)| Sömlös enkel inloggning med Azure Active Directory (sömlös SSO med Azure AD) loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket. Med den här funktionen behöver användarna inte ange sina lösen ord för att logga in på Azure AD och behöver vanligt vis inte ange sina användar namn. Den här funktionen ger auktoriserade användare enkel åtkomst till dina molnbaserade program utan att behöva ytterligare lokala komponenter. |

## <a name="deploy-user-provisioning"></a>Distribuera användar etablering

| Funktion | Beskrivning|
| -| -|
| [Användaretablering](https://aka.ms/UserProvisioningDPDownload)| Azure AD hjälper dig att automatisera skapande, underhåll och borttagning av användaridentiteter i molnprogram (SaaS), till exempel Dropbox, Salesforce, ServiceNow och mer. |
| [Etableringen av workday-driven inkommande användare](https://aka.ms/WorkdayDeploymentPlan)| Workday-driven inkommande Användaretablering till Active Directory skapar en grund för pågående identitetsstyrning och förbättrar kvaliteten på affärsprocesser som förlitar sig på auktoritativa identitetsdata. Med hjälp av den här funktionen kan du sömlöst hantera identitets livs cykeln för anställda och eventualtillgångar genom att konfigurera regler som mappar till kopplingar – överlåtande processer (t. ex. ny anställning, avsluta, överför) till etablerings åtgärder (till exempel skapa, aktivera, Tillåt |

## <a name="deploy-governance-and-reporting"></a>Distribuera styrning och rapportering

| Funktion | Beskrivning|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) hjälper dig att hantera privilegierade administrativa roller i Azure AD, Azure-resurser och andra Microsoft Online Services. PIM innehåller lösningar som just-in-Time-åtkomst, begär godkännande arbets flöden och fullständigt integrerade åtkomst granskningar, så att du kan identifiera, upptäcka och förhindra illvilliga aktiviteter för privilegierade roller i real tid. |
| [Rapportering och övervakning](https://aka.ms/deploymentplans/reporting)| Utformningen av din Azure AD-rapportering och övervaknings lösning är beroende av dina juridiska, säkerhets-och drift krav samt din befintliga miljö och befintliga processer. Den här artikeln visar de olika design alternativen och vägleder dig till rätt distributions strategi. |
