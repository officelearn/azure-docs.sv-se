---
title: Behörigheter i Azure Advisor
description: Advisor-behörigheter och hur de kan blockera din möjlighet att konfigurera prenumerationer eller skjuta upp eller avvisa rekommendationer.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422322"
---
# <a name="permissions-in-azure-advisor"></a>Behörigheter i Azure Advisor

Azure Advisor innehåller rekommendationer baserat på användning och konfiguration av dina Azure-resurser och prenumerationer. Advisor använder de [inbyggda rollerna](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) som tillhandahålls av [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) för att hantera din åtkomst till rekommendationer och Advisor-funktioner. 

## <a name="roles-and-their-access"></a>Roller och deras åtkomst

I följande tabell definieras roller och åtkomst de har inom Advisor:

| **Roll** | **Visa rekommendationer** | **Redigera regler** | **Redigera prenumerationskonfiguration** | **Redigera konfiguration av resursgrupp**| **Avvisa och senarelägga rekommendationer**|
|---|:---:|:---:|:---:|:---:|:---:|
|Ägare av prenumeration|**X**|**X**|**X**|**X**|**X**|
|Prenumerationsdeltagare|**X**|**X**|**X**|**X**|**X**|
|Abonnemang Läsare|**X**|--|--|--|--|
|Ägare till resursgrupp|**X**|--|--|**X**|**X**|
|Resursgruppsdeltagare|**X**|--|--|**X**|**X**|
|Läsare av resursgrupp|**X**|--|--|--|--|
|Resursägare|**X**|--|--|--|**X**|
|Resursdeltagare|**X**|--|--|--|**X**|
|Resursläsare|**X**|--|--|--|--|

> [!NOTE]
> Åtkomst för att visa rekommendationer är beroende av din åtkomst till rekommendationens påverkade resurs.

## <a name="permissions-and-unavailable-actions"></a>Behörigheter och åtgärder som inte är tillgängliga

Brist på rätt behörighet kan blockera din förmåga att utföra åtgärder i Advisor. Följande är några vanliga problem.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Det går inte att konfigurera prenumerationer eller resursgrupper

När du försöker konfigurera prenumerationer eller resursgrupper i Advisor kan det hända att alternativet att inkludera eller utesluta är inaktiverat. Den här statusen anger att du inte har tillräcklig behörighetsnivå för resursgruppen eller prenumerationen. LÃ¶s problemet genom att lära dig hur [du beviljar en användare åtkomst](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Det går inte att skjuta upp eller avvisa en rekommendation

Om du får ett felmeddelande när du försöker skjuta upp eller avvisa en rekommendation kanske du inte har tillräcklig behörighet. Se till att du har minst bidragsgivare tillgång till den påverkade resursen för rekommendationen du skjuter upp eller avfärdar. LÃ¶s problemet genom att lära dig hur [du beviljar en användare åtkomst](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Nästa steg

Den här artikeln gav en översikt över hur Advisor använder RBAC för att styra användarbehörigheter och hur du löser vanliga problem. Mer information om Advisor finns i:

- [Vad är Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Kom igång med Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
