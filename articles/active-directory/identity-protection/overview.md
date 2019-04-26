---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur Azure AD Identity Protection kan du begränsa möjligheten för en angripare att utnyttja en komprometterad identitet eller en enhet och skyddar en identitet eller en enhet som har tidigare eller misstänks äventyras.
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 304e23ec76a2f79d0ab3852efe2a483cf86c51b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453037"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Vad är Azure Active Directory Identity Protection?

Azure Active Directory Identity Protection är en funktion i Azure AD Premium P2-versionen som gör att du kan:

- Identifiera potentiella problem som påverkar organisationens identiteter

- Konfigurera automatiska svar till identifierade misstänkta åtgärder som är relaterade till din organisations identiteter  

- Undersöka misstänkta incidenter och vidta lämpliga åtgärder för att lösa dem   


## <a name="get-started"></a>Kom igång

Microsoft har skyddat molnbaserade identiteter för fler än tio. Med Azure Active Directory Identity Protection använda i din miljö kan du samma skyddssystem som Microsoft använder för att skydda identiteter.

Merparten av säkerhetsbrott sker när angripare får tillgång till en miljö genom att stjäla en användares identitet. Angripare har blivit allt mer effektivt att utnyttja från tredje part överträdelser och använda avancerade nätfiskeattacker genom åren. När en angripare får åtkomst till och med låg Privilegierade konton, är det relativt enkelt att få åtkomst till viktiga företagets resurser via lateral förflyttning.

Till följd av detta måste du:

- Skydda alla identiteter oavsett deras behörighetsnivå

- Proaktivt förhindra komprometterade identiteter som missbrukat

Identifiera komprometterade identiteter är ingen enkelt uppgift. Azure Active Directory använder anpassningsbara maskininlärningsalgoritmer och heuristik för att identifiera avvikelser och misstänkta incidenter som tyder på potentiellt komprometterade identiteter. Med dessa data genererar Identity Protection rapporter och aviseringar så att du kan utvärdera upptäckta problem och vidta lämpliga mitigerings- eller åtgärder.

Azure Active Directory Identity Protection är mer än en övervakning och rapportering verktyget. För att skydda din organisations identiteter kan konfigurera du riskbaserade principer som automatiskt svarar på identifierade problem när en angiven risknivå har uppnåtts. Dessa principer, utöver andra kontroller för villkorlig åtkomst som tillhandahålls av Azure Active Directory och [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS), kan automatiskt blockera eller initiera anpassningsbar åtgärder inklusive lösenordsåterställning och multifaktorautentisering tvingande.


#### <a name="identity-protection-capabilities"></a>Identity Protection-funktioner

**Identifiera säkerhetsrisker och riskfyllda konton:**  

* Att tillhandahålla anpassade rekommendationer för att förbättra övergripande säkerhetspositionen genom att markera sårbarheter
* Beräkna risknivåer för inloggning
* Beräkna risknivåer för användare


**Undersöka riskhändelser:**

* Skicka meddelanden för riskhändelser
* Undersöka riskhändelser med relevant och sammanhangsbaserad information
* Att tillhandahålla grundläggande arbetsflöden för att spåra undersökningar
* Enkel åtkomst till åtgärder som till exempel återställning av lösenord

**Principer för riskbaserad villkorlig åtkomst:**

* Princip för att minimera riskfyllda inloggningar genom att blockera inloggningar eller att kräva multifaktorautentisering utmaningar
* För att blockera eller säker riskfyllda användarkonton
* Princip som kräver att användare registrerar sig för multifaktorautentisering



## <a name="identity-protection-roles"></a>Identity Protection roller

För att belastningsutjämna hanteringsaktiviteter runt implementeringen Identity Protection kan du tilldela flera roller. Azure AD Identity Protection har stöd för 3 katalogroller:

| Roll                         | Kan göra                          | Det går inte att göra
| :--                          | ---                                |  ---   |
| Global administratör         | Fullständig åtkomst till Identity Protection, publicera Identity Protection| |
| Säkerhetsadministratör       | Fullständig åtkomst till Identity Protection | Publicera Identity Protection kan återställa lösenord för en användare |
| Säkerhetsläsare              | Skrivskyddad åtkomst till Identity Protection | Publicera Identity Protection, åtgärda användare, konfigurera principer, återställa lösenord |




Mer information finns i [Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)



## <a name="detection"></a>Detection (Identifiering)

### <a name="vulnerabilities"></a>Sårbarheter

Azure Active Directory Identity Protection analyserar din konfiguration och identifierar sårbarheter som kan påverka din användaridentitet. Mer information finns i [sårbarheter som identifieras av Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-events"></a>Riskhändelser

Azure Active Directory använder anpassningsbara maskininlärningsalgoritmer och heuristik för att identifiera misstänkta åtgärder relaterade till dina användares identiteter. Systemet skapar en post för varje identifierad misstänkt åtgärd. Dessa poster kallas även riskhändelser.  
Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](../active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Undersökning

Resan via Identity Protection börjar vanligtvis med Identity Protection-instrumentpanelen.

![Reparation](./media/overview/1000.png "reparation")

På instrumentpanelen har du åtkomst till:

* Rapporter som **användare som har flaggats för risk**, **riskhändelser** och **sårbarheter**
* Inställningar, till exempel konfigurationen av din **säkerhetsprinciper**, **meddelanden** och **registrering för multifaktorautentisering**

Det är vanligtvis din startpunkt för undersökningar, som du kan granska aktiviteter, loggar och annan relevant information som rör en riskhändelse att bestämma om reparation eller minimering steg är nödvändiga, och hur identiteten har drabbade och förstå hur komprometterade identiteten har använts.

Du kan koppla din undersökning aktiviteter till den [meddelanden](notifications.md) Azure Active Directory Protection skickar per e-postmeddelande.



## <a name="policies"></a>Principer

Om du vill implementera automatiska svar, tillhandahåller Azure Active Directory Identity Protection tre principer:

- [Registreringsprincip för multi-Factor authentication](howto-mfa-policy.md)

- [Princip för användarrisk](howto-user-risk-policy.md)

- [Riskprincip för inloggning](howto-sign-in-risk-policy.md)


## <a name="next-steps"></a>Nästa steg

- [Channel 9: Azure AD och Identity visa: Förhandsversionen av Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

- [Aktivera Azure Active Directory Identity Protection](enable.md)

