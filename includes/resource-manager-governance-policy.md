---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c1e490aacdc9f712c6e186e56fe35fd5872fb575
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
[Azure principer](/azure/azure-policy/) hjälper dig att fatta till alla resurser i prenumerationen uppfyller företagets krav. Använda principer för att minska kostnaderna genom att begränsa distributionsalternativen för dessa typer av resurser och SKU: er som är godkända. Du definierar regler och åtgärder för dina resurser och dessa regler tillämpas automatiskt under distributionen. Du kan till exempel styra vilka typer av resurser som har distribuerats. Eller så kan du begränsa godkända platserna för resurser. Vissa principer neka en åtgärd och vissa principer konfigurera granskning av en åtgärd.

Principen är kompletterar rollbaserad åtkomstkontroll (RBAC). RBAC fokuserar på användaråtkomst och är en standard Neka och explicit Tillåt system. Principen fokuserar på resursegenskaper under och efter distributionen. Det är en standard Tillåt och explicit neka system.

Det finns två begrepp att förstå med principer - principdefinitioner och principtilldelningar. En principdefinition beskrivs management-villkor som du vill tillämpa. En principtilldelning placerar en principdefinition i åtgärden för ett visst område.

![Tilldela principer](./media/resource-manager-governance-policy/policy-concepts.png)

Azure tillhandahåller flera inbyggda principdefinitioner som du kan använda utan ändringar. Du skickar parametervärden för att ange vilka värden som tillåts i omfattningen. Om inbyggda principdefinitionen inte uppfyller dina krav, kan du [skapa definitioner för anpassad princip](../articles/azure-policy/create-manage-policy.md).

### <a name="who-can-create-and-assign-policies"></a>Vem som kan skapa och tilldela principer

Om du vill använda principer måste du autentiseras via RBAC. Mer specifikt måste ditt konto ha detta:

* `Microsoft.Authorization/policydefinitions/write`-behörighet för att definiera en princip.
* `Microsoft.Authorization/policyassignments/write`-behörighet för att tilldela en princip.

Dessa behörigheter ingår inte i den **deltagare** roll.
