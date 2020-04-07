---
title: Faser i en skissdistribution
description: Lär dig vilka säkerhets- och artefaktrelaterade steg som Azure Blueprints-tjänsterna går igenom när du skapar en skisstilldelning.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: 61d19c84cd659b9df3a272c5c2743944e51df06e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677314"
---
# <a name="stages-of-a-blueprint-deployment"></a>Faser i en skissdistribution

När en skiss distribueras vidtas en serie åtgärder av Azure Blueprints-tjänsten för att distribuera de resurser som definierats i skissen. Den här artikeln innehåller information om vad varje steg innebär.

Skissdistribution utlöses genom att tilldela en skiss till en prenumeration eller [uppdatera en befintlig tilldelning](../how-to/update-existing-assignments.md). Under distributionen tar Azure Blueprints följande steg på hög nivå:

> [!div class="checklist"]
> - Azure Blueprints beviljade ägarrättigheter
> - Skisstilldelningsobjektet skapas
> - Valfritt - Azure Blueprints skapar **systemtilldelade** hanterade identitet
> - Den hanterade identiteten distribuerar skissartefakter
> - Azure Blueprints-tjänsten och **systemtilldelade** hanterade identitetsrättigheter återkallas

## <a name="azure-blueprints-granted-owner-rights"></a>Azure Blueprints beviljade ägarrättigheter

Azure Blueprints-tjänstens huvudnamn beviljas ägarrättigheter till den tilldelade prenumerationen eller prenumerationerna när en [systemtilldelad hanterad identitetshanterad](../../../active-directory/managed-identities-azure-resources/overview.md) identitet används. Den beviljade rollen gör att Azure Blueprints kan skapa och senare återkalla den **systemtilldelade** hanterade identiteten. Om du använder en **användartilldelad hanterad** identitet får inte Azure Blueprints-tjänstens huvudnamn och behöver inte ägarrättigheter för prenumerationen.

Rättigheterna beviljas automatiskt om tilldelningen görs via portalen. Men om tilldelningen görs via REST API, bevilja rättigheterna måste göras med ett separat API-anrop. Azure Blueprints AppId `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`är , men tjänstens huvudnamn varierar beroende på klient. Använd [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) och REST-slutpunktstjänstPrincipprinciper för att hämta [tjänstens](/graph/api/resources/serviceprincipal) huvudnamn. Ge sedan Azure Blueprints _rollen Ägare_ via [Portalen,](../../../role-based-access-control/role-assignments-portal.md) [Azure CLI](../../../role-based-access-control/role-assignments-cli.md), Azure [PowerShell,](../../../role-based-access-control/role-assignments-powershell.md) [REST API](../../../role-based-access-control/role-assignments-rest.md)eller en Resource [Manager-mall](../../../role-based-access-control/role-assignments-template.md).

Azure Blueprints-tjänsten distribuerar inte resurserna direkt.

## <a name="the-blueprint-assignment-object-is-created"></a>Skisstilldelningsobjektet skapas

En användare, grupp eller tjänsthuvudnamn tilldelar en skiss till en prenumeration. Tilldelningsobjektet finns på prenumerationsnivå där skissen tilldelades. Resurser som skapas av distributionen görs inte i samband med den distribuerande entiteten.

När du skapar skisstilldelningen väljs typen av [hanterad identitet.](../../../active-directory/managed-identities-azure-resources/overview.md) Standard är en **systemtilldelad hanterad** identitet. En **användartilldelad hanterad** identitet kan väljas. När du använder en **användartilldelad hanterad** identitet måste den definieras och beviljas behörigheter innan skisstilldelningen skapas. Både de inbyggda rollerna [ägare](../../../role-based-access-control/built-in-roles.md#owner) och `blueprintAssignment/write` [skissoperator](../../../role-based-access-control/built-in-roles.md#blueprint-operator) har nödvändig behörighet för att skapa en tilldelning som använder en **användartilldelad hanterad** identitet.

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Valfritt - Azure Blueprints skapar systemtilldelade hanterade identitet

När [systemtilldelade hanterade identitet väljs](../../../active-directory/managed-identities-azure-resources/overview.md) under tilldelning skapar Azure Blueprints identiteten och ger den hanterade identiteten [ägarrollen.](../../../role-based-access-control/built-in-roles.md#owner) Om en [befintlig tilldelning uppgraderas](../how-to/update-existing-assignments.md)använder Azure Blueprints den tidigare skapade hanterade identiteten.

Den hanterade identiteten som är relaterad till skisstilldelningen används för att distribuera eller distribuera om de resurser som definierats i skissen. Den här designen undviker att tilldelningar oavsiktligt stör varandra.
Den här designen stöder också [resurslåsningsfunktionen](./resource-locking.md) genom att styra säkerheten för varje distribuerad resurs från skissen.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Den hanterade identiteten distribuerar skissartefakter

Den hanterade identiteten utlöser sedan Resource Manager-distributionerna för artefakterna i skissen i den definierade [sekvenseringsordningen](./sequencing-order.md). Ordern kan justeras för att säkerställa artefakter beroende på andra artefakter distribueras i rätt ordning.

Ett åtkomstfel av en distribution är ofta resultatet av åtkomstnivån som beviljas den hanterade identiteten. Azure Blueprints-tjänsten hanterar säkerhetslivscykeln för den **systemtilldelade** hanterade identiteten. Användaren är dock ansvarig för att hantera rättigheter och livscykel för en **användartilldelad hanterad** identitet.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Skisstjänst och systemtilldelade hanterade identitetsrättigheter återkallas

När distributionerna har slutförts återkallar Azure Blueprints rättigheterna för den **systemtilldelade** hanterade identiteten från prenumerationen. Azure Blueprints-tjänsten återkallar sedan sina rättigheter från prenumerationen. Borttagning av rättigheter förhindrar att Azure Blueprints blir permanent ägare i en prenumeration.

## <a name="next-steps"></a>Nästa steg

- Förstå hur du använder [statiska och dynamiska parametrar](parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem under tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).
