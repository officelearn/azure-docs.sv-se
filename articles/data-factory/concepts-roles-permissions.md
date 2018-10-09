---
title: Roller och behörigheter för Azure Data Factory | Microsoft Docs
description: Beskriver de roller och behörigheter som krävs för att skapa Datafabriker och att arbeta med underordnade resurser.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: 0c915c6b102f3aeedb316048fad1c6dc238fa0ad
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868624"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Roller och behörigheter för Azure Data Factory

Den här artikeln beskrivs de roller som krävs för att skapa och hantera Azure Data Factory-resurser och de behörigheter som beviljats av dessa roller.

## <a name="roles-and-requirements"></a>Roller och krav

Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna *deltagare* eller *ägare*, eller vara *administratör* för Azure-prenumerationen. Om du vill visa de behörigheter som du har i prenumerationen, i Azure-portalen, väljer användarnamnet i det övre högra hörnet och välj sedan **behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. 

För att skapa och hantera underordnade resurser för Data Factory – inklusive datauppsättningar, länkade tjänster, pipelines, utlösare och integration Runtime – följande krav gäller:
- För att skapa och hantera underordnade resurser i Azure-portalen, du måste tillhöra den **Data Factory-deltagare** roll på resursgruppsnivå eller högre.
- Skapa och hantera underordnade resurser med PowerShell eller SDK, den **deltagare** roll på resursnivån eller högre är tillräckliga.

Exempel på instruktioner om hur du lägger till en användare till en roll, finns det [Lägg till roller](../billing/billing-add-change-azure-subscription-administrator.md) artikeln.

## <a name="set-up-permissions"></a>Konfigurera behörigheter

När du har skapat en Data Factory, kanske du vill att andra användare kan arbeta med data factory. Om du vill ge åtkomst till andra användare, du måste lägga till dem till inbyggt **Data Factory-deltagare** -rollen på den resursgrupp som innehåller data factory.

### <a name="scope-of-the-data-factory-contributor-role"></a>Omfånget för Data Factory-deltagarrollen

Medlemskap i den **Data Factory-deltagare** rollen kan användarna göra följande:
- Skapa, redigera och ta bort datafabriker och underordnade resurser inklusive datauppsättningar, länkade tjänster, pipelines, utlösare och integreringskörningar.
- Distribuera Resource Manager-mallar. Resource Manager-distribution är den distributionsmetod som används i Data Factory i Azure-portalen.
- Hantera App Insights aviseringar för en data factory.
- Skapa supportärenden.

Mer information om den här rollen, se [Data Factory-deltagarrollen](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Resource Manager för malldistribution

Den **Data Factory-deltagare** rollen, på resursgruppsnivå eller senare, kan användarna distribuera Resource Manager-mallar. Medlemmar i rollen kan därmed använda Resource Manager-mallar för att distribuera både datafabriker och deras underordnade resurser, inklusive datauppsättningar, länkade tjänster, pipelines, utlösare och integreringskörningar. Medlemskap i den här rollen kan inte skapa andra resurser, men användaren.

> [!WARNING]
> Resource Manager för malldistribution med den **Data Factory-deltagare** rollen inte öka din behörighet. Om du distribuerar en mall som skapar en Azure virtuell dator och du har inte behörighet att skapa virtuella datorer, till exempel misslyckas distributionen med ett auktoriseringsfel.

### <a name="custom-scenarios-and-custom-roles"></a>Anpassade scenarier och anpassade roller

Ibland kan du behöva ge olika åtkomstnivåer för olika data factory-användare. Exempel:
- Du kan behöva en grupp där användarna bara har behörigheter för en specifik data factory.
- Eller så måste en grupp där användare kan endast övervaka en datafabrik (eller datafabriker) men det går inte att ändra den.

Du kan åstadkomma dessa anpassade scenarier genom att skapa anpassade roller och tilldela användare till dessa roller. Mer information om anpassade roller finns i [anpassade roller i Azure](..//role-based-access-control/custom-roles.md).

Här följer några exempel som visar vad du kan uppnå med anpassade roller:

- Låt en användare skapa, redigera eller ta bort alla data factory i en resursgrupp i Azure Portal.

  Tilldela inbyggt **Data Factory-deltagare** roll på resursgruppnivå för användaren. Om du vill tillåta åtkomst till alla data factory i en prenumeration kan du tilldela rollen på prenumerationsnivån.

- Låt en användarvy (läsa) och övervaka en datafabrik, men inte redigera eller ändra den.

  Tilldela inbyggt **läsare** rollen på data factory-resursen för användaren.

- Låt en användare redigerar en enskild data factory i Azure-portalen.

  Det här scenariot kräver två rolltilldelningar.

  1. Tilldela inbyggt **deltagare** roll på nivån för data factory.
  2. Skapa en anpassad roll med behörigheten * Microsoft.Resources/deployments/**. Tilldela den här anpassade rollen för användaren på resursgruppnivå.

- Låt en användare uppdatera en data factory från PowerShell eller SDK: N, men inte i Azure-portalen.

  Tilldela inbyggt **deltagare** rollen på data factory-resursen för användaren. Den här rollen kan användaren se resurser i Azure-portalen, men användaren kan inte komma åt den **publicera** och **publicera alla** knappar.

## <a name="next-steps"></a>Nästa steg

- Läs mer om administratörsroller i Azure - [förstå rolldefinitioner](../role-based-access-control/role-definitions.md)

- Läs mer om den **Data Factory-deltagare** roll – [Data Factory-deltagarrollen](../role-based-access-control/built-in-roles.md#data-factory-contributor).