---
title: Multi-Factor Authentication (MFA) och PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur Azure AD Privileged Identity Management (PIM) validerar Multi-Factor Authentication (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 686c39c9fb1f1ff7c0ecf068a6612f530620d5dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804311"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>Multi-Factor Authentication (MFA) och PIM

Vi rekommenderar att du kräver Multi-Factor Authentication (MFA) för alla administratörer. Detta minskar risken för angrepp på grund av ett komprometterat lösen ord.

Du kan kräva att användarna slutför en MFA-utmaning när de loggar in. Du kan också kräva att användarna slutför en MFA-utmaning när de aktiverar en roll i Azure Active Directory (Azure AD) Privileged Identity Management (PIM). På så sätt, om användaren inte slutförde en MFA-utmaning när de loggade in, uppmanas de att göra det av PIM.

> [!IMPORTANT]
> Just nu fungerar Azure MFA bara med arbets-eller skol konton, inte Microsoft-konton (vanligt vis ett personligt konto som används för att logga in på Microsoft-tjänster som Skype, Xbox, Outlook.com osv.). Därför kan någon som använder en Microsoft-konto inte vara en berättigad administratör eftersom de inte kan använda MFA för att aktivera sina roller. Om dessa användare behöver fortsätta hantera arbets belastningar med hjälp av en Microsoft-konto kan du höja dem till permanenta administratörer för tillfället.

## <a name="how-pim-validates-mfa"></a>Hur PIM verifierar MFA

Det finns två alternativ för att verifiera MFA när en användare aktiverar en roll.

Det enklaste alternativet är att förlita dig på Azure MFA för användare som aktiverar en privilegie rad roll. För att göra detta måste du först kontrol lera att dessa användare är licensierade, om det behövs, och har registrerats för Azure MFA. Mer information om hur du distribuerar Azure MFA finns i [distribuera molnbaserad Azure-Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md). Vi rekommenderar, men krävs inte, att du konfigurerar Azure AD för att verkställa MFA för dessa användare när de loggar in. Detta beror på att MFA-kontrollerna utförs av själva PIM.

Alternativt kan du, om användarna autentiseras lokalt, ha din identitets leverantör ansvarig för MFA. Om du till exempel har konfigurerat AD Federation Services för att kräva SmartCard-baserad autentisering innan du får åtkomst till Azure AD, kan du [skydda moln resurser med Azure Multi-Factor Authentication och AD FS](../authentication/howto-mfa-adfs.md) innehåller instruktioner för att konfigurera AD FS till skicka anspråk till Azure AD. När en användare försöker aktivera en roll accepterar PIM att MFA redan har verifierats för användaren när det har tagit emot lämpliga anspråk.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure AD-roller i PIM](pim-how-to-change-default-settings.md)
- [Konfigurera inställningar för Azure-resurs roller i PIM](pim-resource-roles-configure-role-settings.md)
