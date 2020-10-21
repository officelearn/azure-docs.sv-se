---
title: Hantera användar data som finns i en Azure Security Center undersökning
description: " Lär dig hur du hanterar användar data som finns i Azure Security Centers undersöknings funktion. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 88fd707d769c7aed53160a9f22fefd15cce19a4b
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340741"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Hantera användar data som finns i en Azure Security Center undersökning
Den här artikeln innehåller information om hur du hanterar användar data som finns i Azure Security Centers undersöknings funktion. Gransknings data lagras i [Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md) och exponeras i Security Center. Att hantera användar data innebär att du kan ta bort eller exportera data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Söker efter och identifierar personuppgifter
I Azure Portal kan du använda Security Centers gransknings funktion för att söka efter personliga data. Undersöknings funktionen finns i **säkerhets aviseringar**.

I undersöknings funktionen visas alla entiteter, användar information och data på fliken **entiteter** .

## <a name="securing-and-controlling-access-to-personal-information"></a>Skydda och kontrollera åtkomst till personuppgifter
En Security Center användare som har tilldelats rollen läsare, ägare, deltagare eller konto administratör kan komma åt kund information i verktyget.

Se [inbyggda roller för rollbaserad åtkomst kontroll i Azure](../role-based-access-control/built-in-roles.md) om du vill veta mer om rollerna läsare, ägare och deltagare. Se [Azure-prenumerations administratörer](../cost-management-billing/manage/add-change-subscription-administrator.md) för att lära dig mer om rollen konto administratör.

## <a name="deleting-personal-data"></a>Ta bort personliga data
En Security Center användare som tilldelats rollen ägare, deltagare eller konto administratör kan ta bort information om undersökningen.

Om du vill ta bort en undersökning kan du skicka en `DELETE` begäran till Azure Resource Manager REST API:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

Du `incidentName` kan hitta indatamängden genom att visa alla incidenter med en `GET` begäran:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Exportera personliga data
En Security Center användare som tilldelats rollen ägare, deltagare eller konto administratör kan exportera information om undersökningen. Om du vill exportera information om undersökningen går du till fliken **entiteter** för att kopiera och klistra in relevant information.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du hanterar användar data finns [i hantera användar data i Azure Security Center](security-center-privacy.md).
Mer information om hur du tar bort privata data i Azure Monitor loggar finns i [Exportera och ta bort privata data](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).