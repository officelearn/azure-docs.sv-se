---
title: Azure AD Privileged Identity Management-säkerhetsguiden
description: Första gången du använder Azure Active Directory Privileged Identity Management-tillägg visas med en säkerhetsguiden. Den här artikeln beskriver stegen för att med hjälp av guiden.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 02/27/2017
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: cb08e5143814db29a7bec8c46226f07a26e51d25
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233028"
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

