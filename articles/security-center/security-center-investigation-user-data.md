---
title: Hantera användardata som hittades i en Azure Security Center-undersökning
description: " Lär dig hur du hanterar användardata som finns i Azure Security Centers undersökningsfunktion. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 9e4c6577f0b8b18aff343ac54b31ff292632f5d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979224"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Hantera användardata som hittades i en Azure Security Center-undersökning
Den här artikeln innehåller information om hur du hanterar användardata som finns i Azure Security Centers undersökningsfunktion. Undersökningsdata lagras i [Azure Monitor-loggar](../log-analytics/log-analytics-overview.md) och exponeras i Security Center. Hantering av användardata omfattar möjligheten att ta bort eller exportera data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Söker efter och identifierar personuppgifter
I Azure-portalen kan du använda Security Centers [undersökningsfunktion](../security-center/security-center-investigation.md) för att söka efter personuppgifter. Undersökningsfunktionen är tillgänglig under **Säkerhetsvarningar**.

Undersökningsfunktionen visar alla entiteter, användarinformation och data under fliken **Entiteter.**

## <a name="securing-and-controlling-access-to-personal-information"></a>Skydda och kontrollera åtkomst till personuppgifter
En Security Center-användare som tilldelats rollen som läsare, ägare, deltagare eller kontoadministratör kan komma åt kunddata i verktyget.

Se [Inbyggda roller för Azure-rollbaserad åtkomstkontroll](../role-based-access-control/built-in-roles.md) om du vill veta mer om rollerna Läsare, Ägare och Deltagare. Läs [azure-prenumerationsadministratörer](../cost-management-billing/manage/add-change-subscription-administrator.md) om du vill veta mer om rollen Kontoadministratör.

## <a name="deleting-personal-data"></a>Ta bort personliga data
En Security Center-användare som tilldelats rollen som ägare, deltagare eller kontoadministratör kan ta bort undersökningsinformationen.

Om du vill ta bort `DELETE` en undersökning kan du skicka en begäran till REST-API:et för Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

Indata `incidentName` kan hittas genom att `GET` lista alla incidenter med hjälp av en begäran:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Exportera personliga data
En Security Center-användare som tilldelats rollen som ägare, deltagare eller kontoadministratör kan exportera undersökningsinformationen. Om du vill exportera undersökningsinformation går du till fliken **Entiteter** för att kopiera och klistra in relevant information.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du hanterar användardata finns [i Hantera användardata i Azure Security Center](security-center-privacy.md).
Mer information om hur du tar bort privata data i Azure Monitor-loggar finns i [Så här exporterar och tar du bort privata data](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
