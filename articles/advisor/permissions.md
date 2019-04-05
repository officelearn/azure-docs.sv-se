---
title: Behörigheter i Azure Advisor
description: Advisor-behörigheter och hur de kan blockera din möjlighet att konfigurera prenumerationer eller skjuta upp eller stänga rekommendationer.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: cbd2e456c96dbf8ca01387f0c7c17a1541dbfe55
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59052799"
---
# <a name="permissions-in-azure-advisor"></a>Behörigheter i Azure Advisor

Azure Advisor tillhandahåller rekommendationer baserat på användning och konfiguration av ditt Azure-resurser och prenumerationer. Advisor använder den [inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) tillhandahålls av [Role-Based Access Control](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) för att hantera åtkomst till rekommendationer och Advisor-funktioner. 

## <a name="roles-and-their-access"></a>Roller och deras åtkomst

I följande tabell definieras rollerna och den åtkomstbehörighet som de har i Advisor:

| **Roll** | **Visa rekommendationer** | **Redigera regler** | **Redigera konfiguration** | **Redigera konfiguration av resursgrupp**| **Stäng och skjuta upp rekommendationer**|
|---|:---:|:---:|:---:|:---:|:---:|
|Prenumerationens ägare|**X**|**X**|**X**|**X**|**X**|
|Prenumerationsdeltagare|**X**|**X**|**X**|**X**|**X**|
|Läsare i prenumeration för|**X**|--|--|--|--|
|Resursgrupp ägare|**X**|--|--|**X**|**X**|
|Resursgrupp deltagare|**X**|--|--|**X**|**X**|
|Resursgrupp läsare|**X**|--|--|--|--|
|Resursägare|**X**|--|--|--|**X**|
|Resurs-deltagare|**X**|--|--|--|**X**|
|Resurs-läsare|**X**|--|--|--|--|

> [!NOTE]
> Behörighet att visa rekommendationer är beroende av din åtkomst till den rekommendationen resurs som påverkas.

## <a name="permissions-and-unavailable-actions"></a>Behörigheter och otillgänglig åtgärder

Brist på rätt behörighet kan blockera din möjlighet att utföra åtgärder i Advisor. Följande är några vanliga problem.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Det går inte att konfigurera prenumerationer eller resursgrupper

När du försöker konfigurera prenumerationer eller resursgrupper i Advisor kan du se att alternativet att inkludera eller exkludera är inaktiverat. Denna status anger att du inte har tillräcklig behörighet för den resursgrupp eller prenumeration. För att lösa problemet, lär du dig hur du [ge en användaråtkomst](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Det går inte att skjuta upp eller ignorera en rekommendation

Om du får ett fel vid försök att skjuta upp eller ignorera en rekommendation, kan du inte har tillräckliga behörigheter. Se till att du har minst deltagaråtkomst till resursen som påverkas av rekommendation du skjuter upp eller ignorera. För att lösa problemet, lär du dig hur du [ge en användaråtkomst](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Nästa steg

Den här artikeln gav en översikt över hur Advisor använder RBAC för att kontrollera användarbehörighet och hur du löser vanliga problem. Mer information om Advisor finns:

- [Vad är Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Kom igång med Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
