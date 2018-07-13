---
title: Azure AD Privileged Identity Management-säkerhetsguiden
description: Första gången du använder Azure Active Directory Privileged Identity Management-tillägget visas med en säkerhetsguiden. Den här artikeln beskriver steg för att använda guiden.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 02/27/2017
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: 6d600afea67d2703c796b403b3e509383f7171a5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590291"
---
# <a name="using-the-security-wizard-in-azure-ad-privileged-identity-management"></a>Guiden Säkerhet i Azure AD Privileged Identity Management 
Om du är den första personen som kör Azure Privileged Identity Management (PIM) för din organisation kan visas med en guide. Guiden hjälper dig att förstå säkerhetsriskerna med Privilegierade identiteter och hur du använder PIM för att minska riskerna. Du behöver inte göra ändringar i befintliga rolltilldelningar i guiden om du vill göra det senare.

## <a name="what-to-expect"></a>Vad du kan förvänta dig
Innan din organisation kan börja använda PIM, alla rolltilldelningar vara permanenta: användarna är alltid i dessa roller även om de för närvarande inte behöver sina privilegier.  Det första steget i guiden visar en lista över privilegierad roller och hur många användare som för närvarande i dessa roller. Du kan öka detaljnivån i en viss roll och Läs mer om användare om en eller flera av dem är okänd.

Det andra steget i guiden ger dig möjlighet att ändra administratörens rolltilldelningar.  

> [!WARNING]
> Det är viktigt att du har minst en global administratör och mer än en privilegierad rolladministratör med ett organisationskonto (inte ett Microsoft-konto). Om det finns bara en administratör av Privilegierade roller måste organisationen inte kan hantera PIM om kontot tas bort.
> Håll också nere rolltilldelningar permanent om en användare har ett Microsoft-konto (ett konto som de använder för att logga in på Microsoft-tjänster som Skype och Outlook.com). Om du planerar att kräva MFA för aktivering för rollen, kommer användaren utelåst.
> 
> 

När du har gjort ändringar, visas inte längre i guiden. Nästa gång du eller en annan administratör av Privilegierade roller kan du använda PIM, visas PIM-instrumentpanel.  

* Om du vill lägga till eller ta bort användare från roller eller ändra tilldelningar från permanent berättigad, Läs mer i [lägga till eller ta bort en användarroll](pim-how-to-add-role-to-user.md).
* Om du vill ge fler användare åtkomst till att hantera PIM Läs mer i [ge åtkomst till att hantera i PIM](pim-how-to-give-access-to-pim.md).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

