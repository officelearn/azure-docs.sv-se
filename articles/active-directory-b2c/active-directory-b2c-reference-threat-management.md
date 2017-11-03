---
title: 'Azure Active Directory B2C: Hot management | Microsoft Docs'
description: "Läs mer om identifiering och minskning tekniker för denial of service-attacker och lösenord attacker i Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: Ajith Alexander
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
ms.openlocfilehash: 9472cb01eb713e297053727b1a314293574bb657
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C: Threat management

Threat management innehåller planering för skydd mot attacker mot ditt system och nätverk. Denial of service-attacker kan göra resurser tillgängliga för avsedda användarna. Lösenord attacker leda till obehörig åtkomst till resurser. Azure Active Directory B2C (Azure AD B2C) har inbyggda funktioner som kan hjälpa dig att skydda dina data mot dessa hot på flera olika sätt.

## <a name="denial-of-service-attacks"></a>Denial of service-attacker

Azure AD B2C använder identifiering och minskning tekniker som SYN cookies och hastighet och anslutningen gränserna för att skydda underliggande resurserna mot DOS-attacker.

## <a name="password-attacks"></a>Lösenord attacker

Azure AD B2C har också minskning tekniker för lösenord attacker. Lösning innehåller lösenord brute force-attacker och lösenord för ordlisteattacker. Lösenord som anges av användare krävs för att vara rimligen komplex. Med hjälp av olika signaler analyserar Azure AD B2C integriteten för begäranden. Azure AD B2C är utformat för att särskilja Intelligent avsedda användarna mot hackare och botnät. Azure AD B2C innehåller en avancerad strategi för att låsa ett konto baserat på de lösenord som angetts i sannolikheten för en attack.

Mer information finns i [Microsoft Trust Center](https://www.microsoft.com/trustcenter/security/threatmanagement).
