---
title: "Hur du kan kräva multifaktorautentisering | Microsoft Docs"
description: "Lär dig att kräva multifaktorautentisering (MFA) för privilegierade identiteter med Azure Active Directory Privileged Identity Management-tillägget."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 1e3dc4ad-3a6a-4a52-8417-3ca4f84ae05c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b778774fa23be8219db3f716d79bac324a7de9d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Så här kräver du MFA i Azure AD Privileged Identity Management
Vi rekommenderar att du kräver multifaktorautentisering (MFA) för alla dina administratörer. Detta minskar risken för angrepp på grund av en komprometterad lösenord.

Du kan kräva att användarna slutföra MFA-kontrollen när de loggar in. Blogginlägget [MFA för Office 365 och Azure MFA](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) jämför vad som ingår i Office och Azure-prenumerationer, med följande funktioner i Microsoft Azure Multi-Factor Authentication-erbjudandet.

Du kan också kräva att användarna slutföra MFA-kontrollen när de aktiverar en roll i Azure AD PIM. Det innebär om användaren inte slutföra MFA-kontrollen när de loggat in, uppmanas de att göra det av PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Kräver MFA i Azure AD Privileged Identity Management
När du hanterar identiteter i PIM som en administratör av Privilegierade roller kan du se aviseringar som rekommenderar MFA för privilegierade konton. Klicka på säkerhetsaviseringen i PIM-instrumentpanelen och öppnas ett nytt blad med en lista över administratörskonton som ska kräva MFA.  Du kan kräva MFA genom att markera flera roller och sedan klicka på den **åtgärda** knappen, eller om du klickar på ellipserna enskilda roller och klicka sedan på den **åtgärda** knappen.

> [!IMPORTANT]
> Just nu Azure MFA fungerar bara med arbets- eller skolkonton, inte Microsoft-konton (vanligtvis ett personligt konto som används för att logga in på Microsoft-tjänster som Skype, Xbox, Outlook.com, etc.). Därför kan inte alla som använder ett Microsoft-konto vara administratör berättigade eftersom de inte kan använda MFA för att aktivera deras roller. Om dessa användare måste fortsätta att hantera arbetsbelastningar som använder ett Microsoft-konto, gör du dem till permanenta administratörer för tillfället.
> 
> 

Du kan dessutom ändra MFA-kravet för en viss roll genom att klicka på den i avsnittet roller i PIM-instrumentpanelen. Klicka på **inställningar** i rollen bladet och sedan välja **aktivera** under multifaktorautentisering.

## <a name="how-azure-ad-pim-validates-mfa"></a>Hur Azure AD PIM verifierar MFA
Det finns två alternativ för att verifiera MFA när en användare aktiverar rollen.

Det enklaste alternativet är att lita på Azure MFA för användare som aktiverar en privilegierad roll. Om du vill göra detta du först kontrollera att dessa användare licensieras om det behövs och har registrerat dig för Azure MFA. Mer information om hur du gör detta finns i [komma igång med Azure Multi-Factor Authentication i molnet](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md). Det rekommenderas, men inte krävs att du konfigurerar Azure AD för att införa MFA för dessa användare när de loggar in. Det beror på att MFA-kontroller ska göras av Azure AD PIM sig själv.

Om användare som autentiseras lokalt kan du ha identitetsprovider ansvara för MFA. Om du har konfigurerat AD Federation Services för att kräva smartkortbaserad autentisering innan du använder Azure AD, till exempel [skydda molnresurser med Azure Multi-Factor Authentication och AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) innehåller anvisningar för att konfigurera AD FS för att skicka anspråk till Azure AD. När en användare försöker aktivera en roll, accepterar Azure AD PIM att MFA har redan verifierats för användaren när den tar emot rätt anspråk.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

