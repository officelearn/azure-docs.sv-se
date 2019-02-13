---
title: Sårbarheter som identifieras av Azure Active Directory Identity Protection | Microsoft Docs
description: Översikt över sårbarheter som identifieras av Azure Active Directory Identity Protection.
services: active-directory
keywords: Azure active directory identity protection kan molnidentifiering, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: markvi
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5968731981e9ba602a27b6b66a53e7b67e8fa7f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187329"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Sårbarheter som identifieras av Azure Active Directory Identity Protection
Sårbarheter är svagheter i din miljö som kan utnyttjas av en angripare. Vi rekommenderar att du åtgärda dessa sårbarheter för att förbättra säkerhetspositionen för din organisation och förhindra att angripare utnyttjar dem.


![sårbarheter](./media/vulnerabilities/101.png "sårbarheter")



I följande avsnitt ger dig en översikt över sårbarheter som rapporterats av Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registreringen för multifaktorautentisering är inte konfigurerad
Denna säkerhetsrisk kan du styra distributionen av Azure Multi-Factor Authentication i din organisation. 

Azure Multi-Factor authentication ger ett andra säkerhetslager för autentisering av användare. Det hjälper dig att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål för en enkel inloggningsprocess. Du får stark autentisering via en rad enkla verifieringsalternativ – telefonsamtal, SMS eller mobilapp-meddelande eller verifieringen kod och andra leverantörers OATH-token.

Vi rekommenderar att du kräver Azure Multi-Factor Authentication för användarinloggningar. Multifaktorautentisering spelar en viktig roll i principer för riskbaserad villkorlig åtkomst som är tillgängliga via Identity Protection.

Mer information finns i [Vad är Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Ohanterade molnappar
Denna säkerhetsrisk kan du identifiera ohanterade molnappar i din organisation.

I moderna företag inte IT-avdelningar ofta känner till alla molnprogram som användare i organisationen använder för att utföra sitt arbete. Det är enkelt att se varför administratörer skulle är orolig för obehörig åtkomst till företagsdata, möjliga dataläckage och andra säkerhetsrisker. 

Vi rekommenderar för att distribuera Cloud Discovery att identifiera ohanterade molnprogram och hantera dessa program med Azure Active Directory.

Mer information finns i [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Säkerhetsaviseringar från Privileged Identity Management
Denna säkerhetsrisk kan du identifiera och Lös aviseringar om Privilegierade identiteter i din organisation.  

Om du vill att användarna ska utföra Privilegierade åtgärder, organisationer behöver ge användare tillfälligt eller permanent privilegierad åtkomst i Azure AD resurser för Azure eller Office 365 eller andra SaaS-appar. Var och en av dessa Privilegierade användare ökar risken för angrepp på din organisation. Denna säkerhetsrisk kan du identifiera användare med onödiga privilegierad åtkomst och vidta lämpliga åtgärder för att minska eller eliminera risken de utgör. 

Vi rekommenderar att din organisation använder Azure AD Privileged Identity Management för att hantera, kontrollera och övervaka Privilegierade identiteter och deras åtkomst till resurser i Azure AD samt andra Microsoft onlinetjänster som Office 365 eller Microsoft Intune.

Mer information finns i [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md). 

## <a name="see-also"></a>Se också

[Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)

