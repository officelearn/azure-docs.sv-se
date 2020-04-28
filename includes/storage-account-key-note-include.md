---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75460544"
---
## <a name="protect-your-access-keys"></a>Skydda dina åtkomst nycklar

Dina åtkomst nycklar för lagrings kontot liknar ett rot lösen ord för ditt lagrings konto. Var alltid noga med att skydda dina åtkomst nycklar. Använd Azure Key Vault för att hantera och rotera dina nycklar på ett säkert sätt. Undvik att distribuera åtkomst nycklar till andra användare, hårdkoda dem eller spara dem var som helst i oformaterad text som är tillgängliga för andra. Rotera dina nycklar om du misstänker att de kan ha komprometterats.

Använd om möjligt Azure Active Directory (Azure AD) för att auktorisera begär anden till blob och Queue Storage i stället för delad nyckel. Azure AD ger överlägsen säkerhet och enkel användning över delad nyckel. Mer information om hur du auktoriserar åtkomst till data med Azure AD finns i [bevilja åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
