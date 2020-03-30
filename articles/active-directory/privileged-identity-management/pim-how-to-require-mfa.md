---
title: MFA eller 2FA och privilegierad identitetshantering – Azure AD | Microsoft-dokument
description: Lär dig hur Azure AD Privileged Identity Management (PIM) validerar MFA (Multi Factor Authentication).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6df593909c3ae5962e413eb84e64196fade0326b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022143"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Multifaktorautentisering och privilegierad identitetshantering

Vi rekommenderar att du kräver MFA (Multi Factor Authentication) för alla administratörer. Detta minskar risken för en attack på grund av ett komprometterat lösenord.

Du kan kräva att användarna slutför en multifaktorautentiseringsutmaning när de loggar in. Du kan också kräva att användare slutför en multifaktorautentiseringsutmaning när de aktiverar en roll i Azure Active Directory (Azure AD) Privileged Identity Management (PIM). På så sätt, om användaren inte slutförde en multifaktorautentiseringsutmaning när de loggade in, uppmanas de att göra det av Privilegierad identitetshantering.

> [!IMPORTANT]
> Just nu fungerar Azure Multi-Factor Authentication endast med arbets- eller skolkonton, inte Microsofts personliga konton (vanligtvis ett personligt konto som används för att logga in på Microsoft-tjänster som Skype, Xbox eller Outlook.com). På grund av detta kan alla som använder ett personligt konto inte vara en kvalificerad administratör eftersom de inte kan använda multifaktorautentisering för att aktivera sina roller. Om dessa användare behöver fortsätta hantera arbetsbelastningar med ett Microsoft-konto höjer du dem till permanenta administratörer för tillfället.

## <a name="how-pim-validates-mfa"></a>Så här validerar PIM MFA

Det finns två alternativ för att validera multifaktorautentisering när en användare aktiverar en roll.

Det enklaste alternativet är att förlita sig på Azure Multi-Factor Autentisering för användare som aktiverar en privilegierad roll. För att göra detta, kontrollera först att dessa användare är licensierade, om det behövs, och har registrerats för Azure Multi-Factor Authentication. Mer information om hur du distribuerar Azure Multi-Factor-autentisering finns i [Distribuera molnbaserad Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md). Vi rekommenderar, men inte obligatoriskt, att du konfigurerar Azure AD för att framtvinga multifaktorautentisering för dessa användare när de loggar in. Detta beror på att multifaktorautentiseringskontrollerna kommer att göras av privilegierad identitetshantering själv.

Alternativt, om användare autentiserar lokalt kan du låta din identitetsleverantör ansvara för multifaktorautentisering. Om du till exempel har konfigurerat AD Federation Services för att kräva smartcard-baserad autentisering innan du öppnar Azure AD, innehåller [Skydda molnresurser med Azure Multi-Factor Authentication och AD FS](../authentication/howto-mfa-adfs.md) instruktioner för att konfigurera AD FS för att skicka anspråk till Azure AD. När en användare försöker aktivera en roll accepterar privilegierad identitetshantering att multifaktorautentisering redan har validerats för användaren när den tar emot lämpliga anspråk.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure AD-rollinställningar i privilegierad identitetshantering](pim-how-to-change-default-settings.md)
- [Konfigurera Azure-resursrollinställningar i Privilegierad identitetshantering](pim-resource-roles-configure-role-settings.md)
