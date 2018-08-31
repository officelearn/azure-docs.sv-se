---
title: Säkerhetsguiden i PIM - Azure | Microsoft Docs
description: Beskriver säkerhetsguiden som visas första gången du använder Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 02/27/2017
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: 178a4c5e978075f2a59b22a1cccf462138527964
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189087"
---
# <a name="security-wizard-in-pim"></a>Säkerhetsguiden i PIM
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

- [Börja använda PIM](pim-getting-started.md)
- [Tilldela Azure AD-katalogroller i PIM](pim-how-to-add-role-to-user.md)
- [Bevilja åtkomst till andra administratörer att hantera PIM](pim-how-to-give-access-to-pim.md)
