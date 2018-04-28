---
title: Skydda privilegierad åtkomst i Azure AD | Microsoft Docs
description: Ett avsnitt som förklarar strategier för att skydda privilegierad åtkomst i Azure, Azure Active Directory och Microsoft Online Services.
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: mwahl
ms.assetid: 235a0ce9-1daf-4433-8f65-9c6afcd64d08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: f49d5a629b9643df81253c57ee8e3458b43b0c59
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="securing-privileged-access-in-azure-ad"></a>Skydda privilegierad åtkomst i Azure AD
Skydda privilegierad åtkomst är ett viktigt första steg för att skydda företagets tillgångar i en modern organisation. Privilegierade konton är konton som administrera och hantera IT-system. Cyber angripare mål dessa konton för att få åtkomst till företagets data och system. Om du vill skydda privilegierad åtkomst bör du isolera konton och system från risk att utsättas för en obehörig användare.

Fler användare börjar få privilegierad åtkomst via molntjänster. Detta kan inkludera globala administratörer för Office 365, Azure-prenumerationsadministratörer och användare som har administratörsbehörighet i virtuella datorer eller på SaaS-appar.

Microsoft rekommenderar att du följer den här vägledningen [skydda privilegierad åtkomst](https://technet.microsoft.com/library/mt631194.aspx).

För kunder som använder Azure Active Directory, Office 365 eller andra Microsoft-tjänster och program, tillämpa dessa principer om användarkonton hanteras och har autentiserats av Active Directory eller i Azure Active Directory. Följande avsnitt innehåller mer information om Azure AD-funktioner som stöder skydda privilegierad åtkomst.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication
Du bör kräva tvåstegsverifiering innan du beviljar behörighet för att öka säkerheten för autentisering av administratörer. Tvåstegsverifiering är en metod för att verifiera vem du är som kräver mer än bara ett användarnamn och lösenord. Det ger ett andra säkerhetslager till användarinloggningar och transaktioner.

Azure Multi-Factor Authentication (MFA) är Microsofts tvåstegsverifiering verifiering lösning som hjälper dig att skydda åtkomsten till data och program och uppfyller användaren begära för en process för enkel inloggning. Den ger stark autentisering via en mängd alternativ för enkel verifiering inklusive:

- telefonsamtal
- Textmeddelanden
- mobilapp-meddelanden
- koder för mobilappar
- OATH-token från tredje part

En översikt över hur Azure Multi-Factor Authentication fungerar finns i följande video:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]

Mer information finns i [MFA för Office 365 och Azure MFA](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/).

## <a name="time-bound-privileges"></a>Tidsbundna privilegier
Vissa organisationer kan hitta de har för många användare i mycket Privilegierade roller. En användare kan ha lagts till rollen för en viss aktivitet som du registrerar dig för en tjänst, men använder inte de behörigheterna som ofta efteråt.

För att minska exponeringstid privilegier och öka din insyn i deras användning, begränsa användare till att endast ta med på sina privilegier just-in-time ”(JIT) eller tilldela dessa roller för en kortare varaktighet tryggt behörigheter återkallas automatiskt. Du kan använda för Azure Active Directory, Azure-resurser (förhandsversion) och Microsoft Online Services, [Azure AD Privileged Identity Management (PIM)](https://aka.ms/AzurePIM).

![PIM-instrumentpanelen][2]

## <a name="attack-detection"></a>Angreppsidentifiering
[Azure Active Directory-identitetsskydd](../active-directory-identityprotection.md) ger en samlad vy riskhändelser och potentiella säkerhetsproblem som påverkar din organisations identiteter. Baserat på riskhändelser beräknar identitetsskydd en användare risknivå för varje användare, så att du kan konfigurera risk-baserade principer för att automatiskt skydda identiteter i din organisation. Dessa principer tillsammans med andra kontroller för villkorlig åtkomst som tillhandahålls av Azure Active Directory och EMS, kan automatiskt blockera användaren eller erbjuda förslag som inkluderar lösenordsåterställning och multifaktorautentisering tvingande.

![Azure AD Identity Protection][3]

## <a name="conditional-access"></a>Villkorlig åtkomst
Med villkorlig åtkomstkontroll kontrollerar de särskilda villkor som du väljer att autentisera en användare innan du tillåter åtkomst till ett program i Azure Active Directory. När dessa villkor är uppfyllda, autentiserade användaren och få tillgång till programmet.

## <a name="related-articles"></a>Relaterade artiklar
* Aktivera [Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
* Aktivera [Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-configure.md)
* Aktivera [Azure AD Identity Protection](../active-directory-identityprotection.md)
* Aktivera [villkorlig åtkomstkontroll](../active-directory-conditional-access-azure-portal.md)

Mer information om hur du skapar en plan för fullständig säkerhet finns i avsnittet ”kundens ansvarsområden och översikt över” för den [Microsoft Cloud Security för Enterprise-arkitekter](https://aka.ms/securecustomer) dokumentet. Mer information om att Microsoft-tjänster för att hjälpa till med någon av dessa avsnitt, kontakta din Microsoft-representant eller besök vår [Cybersecurity lösningar sidan](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
