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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460544"
---
## <a name="protect-your-access-keys"></a>Skydda dina åtkomstnycklar

Åtkomstnycklarna för lagringskonto liknar ett rotlösenord för ditt lagringskonto. Var alltid noga med att skydda dina åtkomstnycklar. Använd Azure Key Vault för att hantera och rotera dina nycklar på ett säkert sätt. Undvik att distribuera åtkomstnycklar till andra användare, hårdkoda dem eller spara dem var som helst i oformaterad text som är tillgänglig för andra. Rotera tangenterna om du tror att de kan ha komprometterats.

Om möjligt kan du använda Azure Active Directory (Azure AD) för att auktorisera begäranden till Blob- och kölagring i stället för delad nyckel. Azure AD ger överlägsen säkerhet och användarvänlighet över delad nyckel. Mer information om hur du auktoriserar åtkomst till data med Azure AD finns i [Auktorisera åtkomst till Azure-blobbar och köer med Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
