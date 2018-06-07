---
title: Hantera användardata finns i en undersökning av Azure Security Center | Microsoft Docs
description: " Lär dig mer om att hantera användardata i Azure Security Center undersökningen funktionen. "
services: operations-management-suite
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: 6685db4eeda72928753c74c64b4b26539ccb1eb9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655718"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Hantera användardata i Azure Security Center undersökning
Den här artikeln innehåller information om hur du hanterar användardata i Azure Security Center undersökningen funktionen. Undersökningen data lagras i [Azure logganalys](../log-analytics/log-analytics-overview.md) och visas i Security Center. Hantera användardata omfattar möjligheten att ta bort eller exportera data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Söker efter och identifiera personliga data
I Azure-portalen kan du använda Security Center [undersökningen funktionen](../security-center/security-center-investigation.md) att söka efter personlig data. Funktionen undersökningen finns under **säkerhetsaviseringar**.

Funktionen undersökningen visar alla entiteter användarinformation och data under den **entiteter** fliken.

## <a name="securing-and-controlling-access-to-personal-information"></a>Skydda och kontrollera åtkomst till personlig information
En Security Center-användare som tilldelats rollen Läsare, ägare, deltagare eller kontoadministratör kan komma åt kundens data i verktyget.

Se [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../role-based-access-control/built-in-roles.md) lära dig mer om rollerna Reader, ägare och deltagare. Se [Azure-prenumerationsadministratörer](../billing/billing-add-change-azure-subscription-administrator.md) vill veta mer om rollen kontoadministratör.

## <a name="deleting-personal-data"></a>Ta bort personliga data
Security Center användaren tilldelas rollen som ägare, deltagare, eller kontoadministratör kan ta bort informationen om undersökningen.

Om du vill ta bort en undersökning, kan du skicka en `DELETE` begäran till Azure Resource Manager REST API:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

Den `incidentName` indata kan hittas genom att lista alla incidenter med hjälp av en `GET` begäran:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Exportera personliga data
Security Center användaren tilldelas rollen som ägare, deltagare, eller kontoadministratör exportera undersökningen information. Om du vill exportera undersökningen information går du till den **entiteter** att kopiera och klistra in relevant information.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du hanterar användardata finns [hanterar användardata i Azure Security Center](security-center-privacy.md).
