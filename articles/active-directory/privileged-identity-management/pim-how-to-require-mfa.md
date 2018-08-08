---
title: Så här kräver du multifaktorautentisering | Microsoft Docs
description: Lär dig hur du kan kräva multifaktorautentisering (MFA) för privilegierade identiteter med Azure Active Directory Privileged Identity Management-tillägget.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8e1c0fa212b31c05fcc4559f9f8d42b627f0da0e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622855"
---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Så här kräver du MFA i Azure AD Privileged Identity Management
Vi rekommenderar att du kräver multifaktorautentisering (MFA) för alla dina administratörer. Detta minskar risken för angrepp på grund av ett olämpligt lösenord.

Du kan kräva att användarna slutföra MFA-kontrollen när de loggar in. Det här blogginlägget [MFA för Office 365 och MFA för Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) jämför vad som ingår i Office och Azure-prenumerationer, med följande funktioner i Microsoft Azure Multi-Factor Authentication-erbjudandet.

Du kan också kräva att användarna slutföra MFA-kontrollen när de aktiverar en roll i Azure AD PIM. Det här sättet om användaren inte slutföra MFA-kontrollen när de loggat in, uppmanas de att göra detta av PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Att kräva MFA i Azure AD Privileged Identity Management
När du hanterar identiteter i PIM som en administratör av Privilegierade roller kan du se aviseringar som rekommenderar MFA för privilegierade konton. Klicka på security-avisering i PIM-instrumentpanelen och ett nytt blad öppnas med en lista över administratörskonton som ska kräva MFA.  Du kan kräva MFA genom att välja flera roller och sedan klicka på den **åtgärda** knapp, eller du kan klicka på ellipsen bredvid enskilda roller och klicka sedan på den **åtgärda** knappen.

> [!IMPORTANT]
> Just nu, Azure MFA fungerar bara med arbets- eller skolkonton, inte Microsoft-konton (vanligtvis ett personligt konto som används för att logga in på Microsoft-tjänster som Skype, Xbox, Outlook.com, osv.). Därför kan alla som använder ett Microsoft-konto inte en berättigad administratör eftersom de inte kan använda MFA för att aktivera sina roller. Om dessa användare behöver att fortsätta att hantera arbetsbelastningar med ett Microsoft-konto, gör du dem till permanenta administratörer för tillfället.
> 
> 

Du kan dessutom ändra MFA-kravet för en viss roll genom att klicka på den i avsnittet roller i PIM-instrumentpanelen. Klicka på **inställningar** i rollen bladet och välja **aktivera** under multifaktorautentisering.

## <a name="how-azure-ad-pim-validates-mfa"></a>Hur Azure AD PIM verifierar MFA
Det finns två alternativ för att verifiera MFA när en användare aktiverar rollen.

Det enklaste alternativet är att förlita dig på Azure MFA för användare som aktiverar en privilegierad roll. Om du vill göra detta måste du först kontrollera att dessa användare licensieras, om det behövs och har registrerat dig för Azure MFA. Mer information om hur du gör detta finns i [komma igång med Azure Multi-Factor Authentication i molnet](../authentication/howto-mfa-getstarted.md). Det rekommenderas, men inte krävs att du konfigurerar Azure AD för att införa MFA för dessa användare när de loggar in. Det beror på att MFA-kontroller ska göras av Azure AD PIM själva.

Du kan också ha din identitetsprovider som ansvarar för MFA genom att användare autentiseras lokalt. Om du har konfigurerat AD Federation Services för att kräva smartkortbaserad autentisering innan du använder Azure AD, till exempel [skydda molnresurser med Azure Multi-Factor Authentication och AD FS](../authentication/howto-mfa-adfs.md) innehåller anvisningar för att konfigurera AD FS för att skicka anspråk till Azure AD. När en användare försöker aktivera en roll, godtar Azure AD PIM att MFA har redan verifierats för användaren när den får rätt anspråken.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

