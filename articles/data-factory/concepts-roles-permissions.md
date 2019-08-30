---
title: Roller och behörigheter för Azure Data Factory | Microsoft Docs
description: Beskriver de roller och behörigheter som krävs för att skapa data fabriker och arbeta med underordnade resurser.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: 9df65322958bffd3182aaa8d734e8b29717d939d
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142530"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Roller och behörigheter för Azure Data Factory

Den här artikeln beskriver de roller som krävs för att skapa och hantera Azure Data Factory-resurser och de behörigheter som har beviljats av dessa roller.

## <a name="roles-and-requirements"></a>Roller och krav

Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna *deltagare* eller *ägare*, eller vara *administratör* för Azure-prenumerationen. Om du vill visa vilka behörigheter du har i prenumerationen öppnar du Azure-portalen, väljer användarnamnet i det övre högra hörnet och väljer sedan **Behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. 

För att skapa och hantera underordnade resurser för Data Factory – inklusive datauppsättningar, länkade tjänster, pipelines, utlösare och integreringskörningar – gäller följande krav:
- För att kunna skapa och hantera underordnade resurser i Azure-portalen måste du tillhöra rollen **Data Factory-deltagare** på resursgruppsnivå eller högre.
- För att skapa och hantera underordnade resurser med PowerShell eller SDK räcker det att du har rollen som **deltagare** på resursnivå eller högre.

För exempel på instruktioner om hur du lägger till en användare till en roll läser du artikeln [Lägg till roller](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="set-up-permissions"></a>Konfigurera behörigheter

När du har skapat en Data Factory kanske du vill låta andra användare arbeta med data fabriken. För att ge den här åtkomsten till andra användare måste du lägga till dem i den inbyggda **Data Factory deltagar** rollen i resurs gruppen som innehåller data fabriken.

### <a name="scope-of-the-data-factory-contributor-role"></a>Omfattningen av rollen Data Factory Contributor

Medlemskap i rollen **Data Factory Contributor** låter användare göra följande:
- Skapa, redigera och ta bort data fabriker och underordnade resurser, inklusive data uppsättningar, länkade tjänster, pipeliner, utlösare och integrerings körningar.
- Distribuera Resource Manager-mallar. Resource Manager-distribution är den distributions metod som används av Data Factory i Azure Portal.
- Hantera App Insights-aviseringar för en data fabrik.
- Skapa support biljetter.

Mer information om den här rollen finns i [Data Factory Contributor-rollen](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Resource Manager för malldistribution

Rollen **Data Factory Contributor** , på resurs grupps nivå eller över, låter användare distribuera Resource Manager-mallar. Till följd av detta kan medlemmar i rollen använda Resource Manager-mallar för att distribuera både data fabriker och deras underordnade resurser, inklusive data uppsättningar, länkade tjänster, pipelines, utlösare och integrerings körningar. Medlemskap i den här rollen låter inte användaren skapa andra resurser, men.

Behörigheter för Azure databaser och GitHub är oberoende av Data Factory behörigheter. Därför kan en användare med lagrings platsen-behörigheter som bara är en medlem i rollen läsare redigera Data Factory underordnade resurser och genomföra ändringar i lagrings platsen, men inte publicera ändringarna.

> [!IMPORTANT]
> Distribution av Resource Manager-mallar med rollen **Data Factorys medarbetare** höjer inte dina behörigheter. Om du till exempel distribuerar en mall som skapar en virtuell Azure-dator och du inte har behörighet att skapa virtuella datorer, Miss lyckas distributionen med ett auktoriseringsfel.

### <a name="custom-scenarios-and-custom-roles"></a>Anpassade scenarier och anpassade roller

Ibland kan du behöva ge olika åtkomst nivåer för olika data fabriks användare. Exempel:
- Du kan behöva en grupp där användare bara har behörighet för en speciell data fabrik.
- Eller så kan du behöva en grupp där användare bara kan övervaka en data fabrik (eller fabriker), men inte ändra den.

Du kan uppnå dessa anpassade scenarier genom att skapa anpassade roller och tilldela användare till dessa roller. Mer information om anpassade roller finns [i anpassade roller i Azure](..//role-based-access-control/custom-roles.md).

Här följer några exempel som visar vad du kan uppnå med anpassade roller:

- Låt en användare skapa, redigera eller ta bort data fabriker i en resurs grupp från Azure Portal.

  Tilldela den inbyggda rollen **Data Factory Contributor** på resurs grupps nivå för användaren. Om du vill tillåta åtkomst till en data fabrik i en prenumeration tilldelar du rollen på prenumerations nivå.

- Låt en användare Visa (läsa) och övervaka en data fabrik, men inte redigera eller ändra den.

  Tilldela den inbyggda rollen **läsare** för användarens data Factory-resurs.

- Låt en användare redigera en enskild data fabrik i Azure Portal.

  Det här scenariot kräver två roll tilldelningar.

  1. Tilldela den inbyggda rollen **deltagare** på Data Factory-nivån.
  2. Skapa en anpassad roll med behörigheten **Microsoft.** Resources/distributions/. Tilldela den här anpassade rollen till användaren på resurs grupps nivå.

- Låt en användare bara kunna testa anslutningen i en länkad tjänst

    Skapa en anpassad roll roll med behörigheter för följande åtgärder: **Microsoft. DataFactory/factors/getFeatureValue/Read** och **Microsoft. DataFactory/factors/getDataPlaneAccess/Read**. Tilldela den här anpassade rollen till användarens data Factory-resurs.

- Låt en användare uppdatera en data fabrik från PowerShell eller SDK, men inte i Azure Portal.

  Tilldela den inbyggda rollen **deltagare** på Data Factory-resursen för användaren. Med den här rollen kan användaren se resurserna i Azure Portal, men användaren kan inte komma åt knapparna **publicera** och **publicera alla** .

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om roller i Azure – [förstå roll definitioner](../role-based-access-control/role-definitions.md)

- Läs mer om rollen **Data Factory Contributor** - [Data Factory deltagar roll](../role-based-access-control/built-in-roles.md#data-factory-contributor).
