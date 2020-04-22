---
title: Metodtips för Azure RBAC
description: Metodtips för att använda Azure-rollbaserad åtkomstkontroll (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc86dd488ff9e8649ae80f4768941791dd37fce6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726779"
---
# <a name="best-practices-for-azure-rbac"></a>Metodtips för Azure RBAC

I den här artikeln beskrivs några metodtips för att använda Azure-rollbaserad åtkomstkontroll (Azure RBAC). Dessa metodtips härleds från vår erfarenhet av Azure RBAC och erfarenheter från kunder som du själv.

## <a name="only-grant-the-access-users-need"></a>Bevilja endast den åtkomst som användarna behöver

Med Azure RBAC kan du segregera uppgifter inom ditt team och endast bevilja åtkomst till användare som de behöver för att utföra sina jobb. I stället för att ge alla obegränsad behörighet i din Azure-prenumeration eller dina resurser kan du tillåta enbart vissa åtgärder i ett visst omfång.

När du planerar din strategi för åtkomstkontroll är det en bra idé att bevilja användare den lägsta behörighet som krävs för att de ska kunna utföra sitt arbete. I följande diagram visas ett föreslaget mönster för att använda RBAC.

![RBAC och lägsta behörighet](./media/best-practices/rbac-least-privilege.png)

Information om hur du lägger till rolltilldelningar finns i [Lägga till eller ta bort rolltilldelningar](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Begränsa antalet abonnemangsägare

Du bör ha högst 3 prenumerationsägare för att minska risken för intrång av en komprometterad ägare. Den här rekommendationen kan övervakas i Azure Security Center. Andra identitets- och åtkomstrekommendationer i Security Center finns i [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Använda Azure AD-privilegierad identitetshantering

För att skydda privilegierade konton från skadliga cyberattacker kan du använda AZURE Active Directory Privileged Identity Management (PIM) för att sänka exponeringstiden för privilegier och öka din insyn i deras användning via rapporter och aviseringar. PIM hjälper till att skydda privilegierade konton genom att tillhandahålla privilegierad åtkomst till Azure AD- och Azure-resurser. Åtkomst kan vara tidsbunden efter vilken privilegier återkallas automatiskt. 

Mer information finns i [Vad är Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="next-steps"></a>Nästa steg

- [Felsöka Azure RBAC](troubleshooting.md)