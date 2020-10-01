---
title: Metod tips för Azure RBAC
description: Metod tips för att använda rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.openlocfilehash: 4cb3e1fe0275c676e2ce54ff9201502fc3595937
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91595481"
---
# <a name="best-practices-for-azure-rbac"></a>Metod tips för Azure RBAC

I den här artikeln beskrivs några metod tips för hur du använder rollbaserad åtkomst kontroll i Azure (Azure RBAC). Dessa bästa metoder är härledda från vår erfarenhet av Azure RBAC och kundernas upplevelser som du själv.

## <a name="only-grant-the-access-users-need"></a>Bevilja endast åtkomst användare behov

Med hjälp av Azure RBAC kan du hålla isär uppgifter i ditt team och bevilja endast den omfattning av åtkomst till användare som de behöver för att utföra sitt arbete. I stället för att ge alla obegränsad behörighet i din Azure-prenumeration eller dina resurser kan du tillåta enbart vissa åtgärder i ett visst omfång.

När du planerar din strategi för åtkomstkontroll är det en bra idé att bevilja användare den lägsta behörighet som krävs för att de ska kunna utföra sitt arbete. Undvik att tilldela bredare roller i bredare omfång, även om det i första skedet verkar vara bekvämare att göra det. Genom att begränsa roller och omfattningar begränsar du vilka resurser som är utsatta för risker om säkerhets objekts skyddet aldrig äventyras.

Följande diagram visar ett föreslaget mönster för att använda Azure RBAC.

![Azure RBAC och minsta behörighet](./media/best-practices/rbac-least-privilege.png)

Information om hur du lägger till roll tilldelningar finns i [lägga till eller ta bort Azure roll tilldelningar med hjälp av Azure Portal](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Begränsa antalet prenumerations ägare

Du bör ha högst tre prenumerations ägare för att minska risken för brott mot en komprometterad ägare. Den här rekommendationen kan övervakas i Azure Security Center. För andra identitets-och åtkomst rekommendationer i Security Center, se [säkerhets rekommendationer – en referens guide](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Använd Azure AD Privileged Identity Management

För att skydda privilegierade konton från skadliga cyberhot-attacker kan du använda Azure Active Directory Privileged Identity Management (PIM) för att minska exponerings tiden för privilegier och öka din insyn i användningen genom rapporter och aviseringar. PIM skyddar privilegierade konton genom att tillhandahålla just-in-Time-åtkomst till Azure AD och Azure-resurser. Åtkomst kan vara en tids gräns efter vilken privilegierna återkallas automatiskt. 

Mer information finns i [Vad är Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="next-steps"></a>Nästa steg

- [Felsöka Azure RBAC](troubleshooting.md)