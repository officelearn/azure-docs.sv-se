---
title: Sårbarheter som identifieras av Azure Active Directory Identity Protection
description: Översikt över sårbarheter som identifieras av Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e792551f4cac857f56454c67d527e01cb9c4281
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113130"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Sårbarheter som identifieras av Azure Active Directory Identity Protection

Sårbarheter är svagheter i en miljö som kan utnyttjas av en angripare. Vi rekommenderar att administratörer åtgärda dessa sårbarheter för att förbättra säkerhetspositionen för organisationen.

![Sårbarheter som rapporterats av Identity Protection](./media/vulnerabilities/identity-protection-vulnerabilities.png)

I följande avsnitt ger dig en översikt över sårbarheter som rapporterats av Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registrering för Multifaktorautentisering inte har konfigurerats

Denna säkerhetsrisk kan utvärdera distributionen av Azure Multi-Factor Authentication i din organisation.

Azure Multi-Factor Authentication ger ett andra säkerhetslager för autentisering av användare. Det hjälper dig att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål för en enkel inloggningsprocess. Azure Multi-Factor Authentication ger lättanvända verifieringsalternativ som:

* Telefonsamtal
* Textmeddelande
* Mobilapp-meddelande
* OTP-Verifieringskod

Vi rekommenderar att du kräver Azure Multi-Factor Authentication för användarinloggningar. Multifaktorautentisering spelar en viktig roll i principer för riskbaserad villkorlig åtkomst som är tillgängliga via Identity Protection.

Mer information finns i [Vad är Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Ohanterade molnappar

Denna säkerhetsrisk kan du identifiera ohanterade molnappar i din organisation.

IT-personal är ofta ovetande om alla molnprogram i deras organisation. Det är enkelt att se varför administratörer skulle är orolig för obehörig åtkomst till företagsdata, möjliga dataläckage och andra säkerhetsrisker.

Vi rekommenderar att du distribuerar Cloud Discovery att identifiera ohanterade molnprogram och hantera dessa program med Azure Active Directory.

Mer information finns i [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Säkerhetsaviseringar från Privileged Identity Management

Denna säkerhetsrisk kan du identifiera och Lös aviseringar om Privilegierade identiteter i din organisation.  

Om du vill att användarna ska utföra Privilegierade åtgärder, organisationer behöver ge användare tillfälligt eller permanent privilegierad åtkomst i Azure AD resurser för Azure eller Office 365 eller andra SaaS-appar. Var och en av dessa Privilegierade användare ökar risken för angrepp på din organisation. Denna säkerhetsrisk kan du identifiera användare med onödiga privilegierad åtkomst och vidta lämpliga åtgärder för att minska eller eliminera risken de utgör.

Vi rekommenderar organisationer att använda Azure AD Privileged Identity Management för att hantera, kontrollera och övervaka Privilegierade identiteter i Azure AD, samt andra Microsoft onlinetjänster som Office 365 eller Microsoft Intune.

Mer information finns i [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Se också

[Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)
