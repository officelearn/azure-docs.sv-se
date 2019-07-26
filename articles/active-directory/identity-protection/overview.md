---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur Azure AD Identity Protection ger dig möjlighet att begränsa möjligheten för en angripare att utnyttja en komprometterad identitet eller enhet och att skydda en identitet eller en enhet som tidigare misstänkts eller var känd för att bli komprometterad.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99a542d3208d5871d88c966fffc65cf16e0fbeee
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335387"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Vad är Azure Active Directory Identity Protection?

Azure Active Directory [Identity](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology) Protection gör det möjligt för organisationer att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter.

## <a name="get-started"></a>Kom igång

Microsoft har skyddat molnbaserade identiteter i mer än tio år. Med Azure Active Directory Identity Protection i din miljö kan du använda samma skydds system som Microsoft använder för att skydda identiteter.

De flesta säkerhets överträdelser sker när angripare får åtkomst till en miljö genom att stjäla en användares identitet. Under åren har angriparen blivit alltmer effektiv i att utnyttja överträdelser från tredje part och använda avancerade phishing-attacker. Så snart en angripare får åtkomst till ännu låg privilegie rad användar konton, är det relativt enkelt att få åtkomst till viktiga företags resurser genom lateral förflyttning.

Som en följd av detta måste du:

- Skydda alla identiteter oavsett behörighets nivå
- Förhindra att komprometterade identiteter missbrukas proaktivt

Det är ingen enkel uppgift att identifiera komprometterade identiteter. Azure Active Directory använder anpassningsbara algoritmer för maskin inlärning och heuristik för att identifiera avvikelser och misstänkta incidenter som indikerar potentiellt komprometterade identiteter. Med hjälp av dessa data genererar identitets skydd rapporter och aviseringar som gör att du kan utvärdera de identifierade problemen och vidta lämpliga åtgärder för att åtgärda problemet.

Azure Active Directory Identity Protection är mer än ett övervaknings-och rapporterings verktyg. För att skydda din organisations identiteter kan du konfigurera riskfyllda principer som automatiskt svarar på identifierade problem när en angiven risk nivå har uppnåtts. Dessa principer, förutom andra villkorliga åtkomst kontroller som tillhandahålls av Azure Active Directory och [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS), kan antingen automatiskt blockera eller initiera anpassningsbara åtgärds åtgärder, inklusive lösen ords återställning och tillämpning av Multi-Factor Authentication.

### <a name="identity-protection-capabilities"></a>Funktioner för identitets skydd

**Identifiera sårbarheter och riskfyllda konton:**  

- Tillhandahålla anpassade rekommendationer för att förbättra den övergripande säkerhets position genom att markera sårbarheter
- Beräkning av inloggnings risk nivåer
- Beräkna risk nivåer för användare

**Undersöka risk händelser:**

- Skicka meddelanden för risk händelser
- Undersöka risk händelser med hjälp av relevant och sammanhangsbaserad information
- Tillhandahålla grundläggande arbets flöden för att spåra utredningar
- Ge enkel åtkomst till åtgärds åtgärder, till exempel återställning av lösen ord

**Riskfyllda principer för villkorlig åtkomst:**

- Princip för att minimera riskfyllda inloggningar genom att blockera inloggningar eller kräva Multi-Factor Authentication-utmaningar
- Princip för att blockera eller skydda riskfyllda användar konton
- Princip för att kräva att användare registrerar sig för Multi-Factor Authentication

## <a name="identity-protection-roles"></a>Identitets skydds roller

Om du vill belastningsutjämna hanterings aktiviteterna kring implementeringen av identitets skyddet kan du tilldela flera roller. Azure AD Identity Protection stöder tre katalog roller:

| Role | Kan göra | Kan inte göra |
| :-- | --- | --- |
| Global administratör | Fullständig åtkomst till identitets skydd, inbyggt identitets skydd| |
| Säkerhetsadministratör | Fullständig åtkomst till identitets skydd | Återställa identitets skydd, återställa lösen ord för en användare |
| Säkerhetsläsare | Skrivskyddad åtkomst till identitets skydd | Identifiera skydd av identiteter, reparera användare, konfigurera principer, återställa lösen ord |

Mer information finns i [tilldela administratörs roller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

## <a name="detection"></a>Identifiering

### <a name="vulnerabilities"></a>Sårbarheter

Azure Active Directory Identity Protection analyserar konfigurationen och identifierar sårbarheter som kan påverka användarens identiteter. Mer information finns i [sårbarheter som upptäckts av Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-events"></a>Riskhändelser

Azure Active Directory använder anpassningsbara algoritmer för maskin inlärning och heuristik för att identifiera misstänkta åtgärder som är relaterade till användarens identiteter. Systemet skapar en post för varje identifierad misstänkt åtgärd. Dessa poster kallas även risk händelser.  
Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](../active-directory-identity-protection-risk-events.md).

## <a name="investigation"></a>Undersökning

Din resa genom identitets skydd börjar normalt med instrument panelen för identitets skydd.

![Reparation](./media/overview/1000.png "Reparation")

På instrumentpanelen har du åtkomst till:

- Rapporter som användare som har **flaggats för risk**, **risk händelser** och **sårbarheter**
- Inställningar som konfiguration av dina **säkerhets principer**, **meddelanden** och **Multi-Factor Authentication-registrering**

Det är vanligt vis din utgångs punkt för undersökning, som är en process för att granska aktiviteter, loggar och annan relevant information som rör en risk händelse för att avgöra om åtgärder för reparation eller minskning är nödvändiga och hur identiteten var komprometterad och förstå hur den komprometterade identiteten användes.

Du kan knyta dina undersöknings aktiviteter till [meddelanden](notifications.md) Azure Active Directory skydd som skickas per e-post.

## <a name="policies"></a>Principer

Azure Active Directory Identity Protection har tre principer för att implementera automatiska svar:

- [Registrerings princip för Multi-Factor Authentication](howto-mfa-policy.md)
- [Användar risk princip](howto-user-risk-policy.md)
- [Princip för inloggnings risk](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Nästa steg

- [Kanal 9: Azure AD och identitet visar: För hands version av identitets skydd](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Aktivera Azure Active Directory Identity Protection](enable.md)
