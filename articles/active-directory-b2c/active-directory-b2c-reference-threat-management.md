---
title: Insamling av hantering i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om identifiering och skyddsteknik tekniker för DOS-attacker och lösenord attacker i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1801fe9695aa15850d600300b957df2c7d7cd9ef
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060954"
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C: Hothantering

Hothantering innehåller planering för skydd mot attacker mot dina system och nätverk. Denial of service-attacker kan göra resurser tillgängliga för avsedda användarna. Lösenord attacker leda till obehörig åtkomst till resurser. Azure Active Directory B2C (Azure AD B2C) har inbyggda funktioner som kan hjälpa dig att skydda dina data mot dessa hot på flera olika sätt.

## <a name="denial-of-service-attacks"></a>Denial of service-attacker

Azure AD B2C använder identifiering och skyddsteknik tekniker som SYN cookies och begränsningar för hastighet och anslutningen att skydda underliggande resurser mot DOS-attacker.

## <a name="password-attacks"></a>Lösenord attacker

Azure AD B2C har också minskning tekniker för lösenord attacker. Minskning omfattar lösenord brute force-attacker och ordlisteattacker för lösenord. Lösenord som anges av användare krävs för att vara förhållandevis komplexa. Med hjälp av olika signaler analyserar Azure AD B2C integriteten för begäranden. Azure AD B2C har utformats för att skilja smart avsedda användarna från hackare och botnät. Azure AD B2C tillhandahåller en avancerad strategi för att låsa konton baserat på det lösenord som anges i sannolikheten för en attack.

Mer information finns i [Microsoft Trust Center](https://www.microsoft.com/trustcenter/default.aspx).
