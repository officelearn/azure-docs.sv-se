---
title: "Azure AD Privileged Identity Management-säkerhetsguiden"
description: "Första gången du använder Azure Active Directory Privileged Identity Management-tillägg visas med en säkerhetsguiden. Den här artikeln beskriver stegen för att med hjälp av guiden."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: a53a3719-8cc7-4fc7-8164-aafca192871b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: 4a45e1bdbce299dce38a01a17a65024dc41a353f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="using-the-security-wizard-in-azure-ad-privileged-identity-management"></a>Guiden Säkerhet i Azure AD Privileged Identity Management 
Om du är den första personen att köra Azure Privileged Identity Management (PIM) för din organisation, visas en guide. Guiden hjälper dig att förstå säkerhetsriskerna med Privilegierade identiteter och hur du använder PIM för att minska riskerna. Du behöver inte göra ändringar i befintliga rolltilldelningar i guiden om du vill göra det senare.

## <a name="what-to-expect"></a>Vad du kan förvänta dig
Innan din organisation kan börja använda PIM, alla rolltilldelningar är permanenta: användarna är alltid i dessa roller även om de inte för närvarande behöver sina privilegier.  Det första steget i guiden visar en lista över privilegierad roller och hur många användare som för närvarande rollerna. Du kan öka detaljnivån en viss roll för mer information om användare om en eller flera av dem är okänd.

Det andra steget i guiden får du möjlighet att ändra administratörens rolltilldelningar.  

> [!WARNING]
> Det är viktigt att du har minst en global administratör och mer än en administratör av Privilegierade roller med ett organisationskonto (inte ett Microsoft-konto). Om det finns bara en administratör av Privilegierade roller, organisationen inte kan hantera PIM om kontot tas bort.
> Kom också rolltilldelningar permanent om en användare har ett Microsoft-konto (ett konto som de använder för att logga in på Microsoft-tjänster som Skype och Outlook.com). Om du planerar att kräva MFA för aktivering för rollen utelåst användaren.
> 
> 

När du har gjort ändringar i guiden kommer inte längre att visas. Nästa gång du eller en annan administratör av Privilegierade roller använder PIM, visas PIM-instrumentpanelen.  

* Om du vill lägga till eller ta bort användare från roller eller ändra tilldelningar från permanent till berättigade Läs mer på [lägga till eller ta bort en användarroll](active-directory-privileged-identity-management-how-to-add-role-to-user.md).
* Om du vill ge flera användare åtkomst till hantera PIM Läs mer på [ge åtkomst för att hantera i PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

