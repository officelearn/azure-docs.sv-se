---
title: Sårbarheter som identifieras av Azure Active Directory Identity Protection | Microsoft Docs
description: Översikt över sårbarheter som identifieras av Azure Active Directory Identity Protection.
services: active-directory
keywords: Azure active directory identitetsskydd, cloud discovery, hantera program, säkerhet, risk, risknivå, säkerhetsproblem och säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 534384f8e8fde17c2330831b346d87c50ea24183
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084016"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Sårbarheter som identifieras av Azure Active Directory Identity Protection
Säkerhetsrisker är svagheter i din miljö som kan utnyttjas av en angripare. Vi rekommenderar att åtgärda dessa problem för att förbättra säkerhetsläget i din organisation och förhindrar att angripare utnyttjar dem.


![säkerhetsproblem](./media/active-directory-identityprotection-vulnerabilities/101.png "säkerhetsrisker")



Följande avsnitt ger dig en översikt över de säkerhetsrisker som rapporterats av Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registreringen för multifaktorautentisering är inte konfigurerad
Den här säkerhetsrisken hjälper dig att styra distributionen av Azure Multi-Factor Authentication i din organisation. 

Azure Multi-Factor authentication ger ett andra säkerhetslager för autentisering av användare. Det hjälper dig att skydda åtkomst till data och program och uppfyller efterfrågan från användarna för en process för enkel inloggning. Den ger stark autentisering via en mängd alternativ för enkel verifiering – telefonsamtal, textmeddelande eller mobilapp meddelande eller verifiering kod och tredjeparts OATH-token.

Vi rekommenderar att du behöver Azure Multi-Factor Authentication för användarinloggningar. Multifaktorautentisering spelar en viktig roll i risk-baserade villkorliga åtkomstprinciper tillgängliga via Identity Protection.

Mer information finns i [vad är Azure Multi-Factor Authentication?](authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Ohanterade molnappar
Det här problemet kan du identifiera ohanterade molnappar i din organisation.

I moderna företag inte IT-avdelningar ofta känner till alla molnprogram som användare i organisationen använder för att utföra sitt arbete. Det är enkelt att se varför administratörer skulle oroar otillåten åtkomst till företagsdata, möjliga dataläckage och andra säkerhetsrisker. 

Vi rekommenderar för att distribuera Cloud Discovery att identifiera ohanterade molnprogram och hantera dessa program med Azure Active Directory.

Mer information finns i [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Säkerhetsaviseringar från Privileged Identity Management
Den här säkerhetsrisken hjälper dig att identifiera och lösa aviseringar om Privilegierade identiteter i din organisation.  

Om du vill att användare ska kunna utföra Privilegierade åtgärder organisationer behöver ge användare tillfälligt eller permanent privilegierad åtkomst i Azure AD Azure eller Office 365 resurser eller andra SaaS-appar. Var och en av dessa Privilegierade användare ökar risken för angrepp på din organisation. Den här säkerhetsrisken hjälper dig att identifiera användare med onödiga privilegierad åtkomst och vidta lämpliga åtgärder för att minska eller eliminera risken de utgör. 

Vi rekommenderar att din organisation använder Azure AD Privileged Identity Management för att hantera, styr och övervaka Privilegierade identiteter och deras åtkomst till resurser i Azure AD samt andra Microsoft online services som Office 365 eller Microsoft Intune.

Mer information finns i [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="see-also"></a>Se också
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

