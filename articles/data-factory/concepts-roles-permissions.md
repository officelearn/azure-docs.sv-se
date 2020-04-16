---
title: Roller och behörigheter för Azure Data Factory
description: Beskriver de roller och behörigheter som krävs för att skapa datafabriker och arbeta med underordnade resurser.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 8b7791c5c04e986b30959d2fcae17142fdd8b7ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418328"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Roller och behörigheter för Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


I den här artikeln beskrivs de roller som krävs för att skapa och hantera Azure Data Factory-resurser och de behörigheter som beviljas av dessa roller.

## <a name="roles-and-requirements"></a>Roller och krav

Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna *deltagare* eller *ägare*, eller vara *administratör* för Azure-prenumerationen. Om du vill visa vilka behörigheter du har i prenumerationen öppnar du Azure-portalen, väljer användarnamnet i det övre högra hörnet och väljer sedan **Behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. 

För att skapa och hantera underordnade resurser för Data Factory – inklusive datauppsättningar, länkade tjänster, pipelines, utlösare och integreringskörningar – gäller följande krav:
- För att kunna skapa och hantera underordnade resurser i Azure-portalen måste du tillhöra rollen **Data Factory-deltagare** på resursgruppsnivå eller högre.
- För att skapa och hantera underordnade resurser med PowerShell eller SDK räcker det att du har rollen som **deltagare** på resursnivå eller högre.

För exempel på instruktioner om hur du lägger till en användare till en roll läser du artikeln [Lägg till roller](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="set-up-permissions"></a>Ställ in behörigheter

När du har skapat en datafabrik kanske du vill låta andra användare arbeta med datafabriken. Om du vill ge den här åtkomsten till andra användare måste du lägga till dem i den inbyggda rollen **Data Factory Contributor** i resursgruppen som innehåller datafabriken.

### <a name="scope-of-the-data-factory-contributor-role"></a>Omfattningen av rollen datafabriksdeltagare

Med medlemskap i rollen **Data Factory Contributor** kan användarna göra följande:
- Skapa, redigera och ta bort datafabriker och underordnade resurser, inklusive datauppsättningar, länkade tjänster, pipelines, utlösare och integrationskörningar.
- Distribuera Resource Manager-mallar. Resurshanterarens distribution är den distributionsmetod som används av Data Factory i Azure-portalen.
- Hantera appstatistikvarningar för en datafabrik.
- Skapa supportbiljetter.

Mer information om den här rollen finns i [rollen Data Factory Contributor](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Distribution av Resource Manager-mall

Med rollen **Data Factory Contributor,** på resursgruppsnivå eller högre, kan användare distribuera Resource Manager-mallar. Därför kan medlemmar i rollen använda Resource Manager-mallar för att distribuera både datafabriker och deras underordnade resurser, inklusive datauppsättningar, länkade tjänster, pipelines, utlösare och integrationskörningar. Medlemskap i den här rollen låter dock inte användaren skapa andra resurser.

Behörigheter för Azure Repos och GitHub är oberoende av behörigheter för datafabriker. Därför kan en användare med behörigheter för repoer som bara är medlem i rollen Läsare redigera underordnade datafabriker och utföra ändringar i osiningen, men inte publicera dessa ändringar.

> [!IMPORTANT]
> Resurshanterarens malldistribution med rollen **Data Factory Contributor** höjer inte dina behörigheter. Om du till exempel distribuerar en mall som skapar en virtuell Azure-dator och du inte har behörighet att skapa virtuella datorer, misslyckas distributionen med ett auktoriseringsfel.

### <a name="custom-scenarios-and-custom-roles"></a>Anpassade scenarier och anpassade roller

Ibland kan du behöva bevilja olika åtkomstnivåer för olika datafabriksanvändare. Ett exempel:
- Du kan behöva en grupp där användarna bara har behörighet för en viss datafabrik.
- Du kan också behöva en grupp där användarna bara kan övervaka en datafabrik (eller fabriker) men inte kan ändra den.

Du kan uppnå dessa anpassade scenarier genom att skapa anpassade roller och tilldela användare till dessa roller. Mer information om anpassade roller finns [i Anpassade roller i Azure](..//role-based-access-control/custom-roles.md).

Här är några exempel som visar vad du kan uppnå med anpassade roller:

- Låt en användare skapa, redigera eller ta bort datafabrik i en resursgrupp från Azure-portalen.

  Tilldela den inbyggda **datafabriksdeltagarerollen** på resursgruppsnivå för användaren. Om du vill tillåta åtkomst till en datafabrik i en prenumeration tilldelar du rollen på prenumerationsnivå.

- Låt en användare visa (läsa) och övervaka en datafabrik, men inte redigera eller ändra den.

  Tilldela den inbyggda **läsarrollen** på datafabriksresursen för användaren.

- Låt en användare redigera en enda datafabrik i Azure-portalen.

  Det här scenariot kräver två rolltilldelningar.

  1. Tilldela den inbyggda **deltagarrollen** på datafabriksnivå.
  2. Skapa en anpassad roll med behörigheten **Microsoft.Resources/deployments/**. Tilldela den här anpassade rollen till användaren på resursgruppsnivå.

- Låt en användare endast kunna testa anslutningen i en länkad tjänst

    Skapa en anpassad rollroll med behörigheter för följande åtgärder: **Microsoft.DataFactory/factories/getFeatureValue/read** och **Microsoft.DataFactory/factories/getDataPlaneAccess/read**. Tilldela den här anpassade rollen på datafabriksresursen för användaren.

- Låt en användare uppdatera en datafabrik från PowerShell eller SDK, men inte i Azure-portalen.

  Tilldela den inbyggda **deltagarrollen** på datafabriksresursen för användaren. Med den här rollen kan användaren se resurserna i Azure-portalen, men användaren kan inte komma åt knapparna **Publicera** och **publicera alla.**

## <a name="next-steps"></a>Nästa steg

- Läs mer om roller i Azure – [Förstå rolldefinitioner](../role-based-access-control/role-definitions.md)

- Läs mer om rollen **datafabriksdeltagare** – [rollen Data Factory Contributor](../role-based-access-control/built-in-roles.md#data-factory-contributor).
