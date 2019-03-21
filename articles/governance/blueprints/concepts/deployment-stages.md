---
title: Faser i en skissdistribution
description: Läs om stegen skissen tjänsterna går igenom under en distribution.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: a3ab5589cd327b73f2e66540da5c49343c4449cd
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2019
ms.locfileid: "57999182"
---
# <a name="stages-of-a-blueprint-deployment"></a>Faser i en skissdistribution

När en skiss distribueras, tas en serie åtgärder av tjänsten Azure skisser att distribuera de resurser som definierats i skissen. Den här artikeln innehåller information om varje steg omfattar.

Skissen distribution utlöses genom att tilldela en skiss till en prenumeration eller [uppdatera en befintlig tilldelning](../how-to/update-existing-assignments.md). Under distributionen tar skisser följande anvisningar:

> [!div class="checklist"]
> - Skisser behörighet för ägare
> - Tilldelning av skissobjektet har skapats
> - Valfritt - skisser skapar **systemtilldelade** hanterad identitet
> - Den hanterade identitet distribuerar skissartefakter
> - Skiss service och **systemtilldelade** hanterad identitet rättigheter har återkallats

## <a name="blueprints-granted-owner-rights"></a>Skisser behörighet för ägare

Azure skisser tjänstens huvudnamn beviljas ägarrättigheter till tilldelade prenumerationen eller prenumerationerna. Beviljade rollen gör skisser att skapa och senare återkallar den [systemtilldelade hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md).

Rättigheter beviljas automatiskt om tilldelningen görs via portalen. Men om tilldelningen görs via REST API, bevilja behörighet måste vara med ett separat API anropa. Azure skissen AppId är `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, men tjänstobjektet varierar beroende på klienten. Använd [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) och REST-slutpunkt [servicePrincipals](/graph/api/resources/serviceprincipal) att hämta tjänstens huvudnamn. Sedan kan ge Azure-skisser den _ägare_ rollen via den [Portal](../../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API: et](../../../role-based-access-control/role-assignments-rest.md), eller en [Resource Manager-mall](../../../role-based-access-control/role-assignments-template.md).

Tjänsten skisser inte direkt att distribuera resurserna.

## <a name="the-blueprint-assignment-object-is-created"></a>Tilldelning av skissobjektet har skapats

En användare, grupp eller tjänstens huvudnamn tilldelar du en skiss till en prenumeration. Tilldelningsprojektet finns på prenumerationsnivå där skissen tilldelades. Resurser som skapades av distributionen göras inte i samband med distribution entiteten.

När du skapar skisstilldelningen, vilken typ av [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md) har valts. Standardvärdet är en **systemtilldelade** hanterad identitet. En **Användartilldelad** hanterad identitet kan väljas. När du använder en **Användartilldelad** hanterad identitet, måste definieras och beviljade behörigheter innan skisstilldelningen har skapats.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Valfritt - skisser skapar systemtilldelade hanterad identitet

När [systemtilldelade hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md) väljs under tilldelning, skisser skapar identitet och ger den hanterade identitet i [ägare](../../../role-based-access-control/built-in-roles.md#owner) roll. Om en [befintliga tilldelningen uppgraderas](../how-to/update-existing-assignments.md), skisser använder det tidigare skapade hanterad identitet.

Den hanterade identitet som rör skisstilldelningen används för att distribuera eller distribuera om de resurser som definierats i skissen. Den här designen undviker tilldelningar oavsiktligen stör av varandra.
Den här metoden stöder också den [resource låsning](./resource-locking.md) funktionen genom att kontrollera säkerheten för varje resurs som är distribuerade från skissen.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Den hanterade identitet distribuerar skissartefakter

Den hanterade identitet och utlöser därefter Resource Manager-distributioner av artefakter i skissen i det definierade [ordningsföljd](./sequencing-order.md). Ordningen kan justeras för att säkerställa artefakter som är beroende av andra artefakter distribueras i rätt ordning.

Ett fel för åtkomst av en distribution är ofta resultatet av åtkomstnivån som beviljats till hanterad identitet. Skisser-tjänsten hanterar livscykeln för säkerhet för den **systemtilldelade** hanterad identitet. Användaren är dock ansvarar för att hantera behörigheter och livscykeln för en **Användartilldelad** hanterad identitet.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Skissen tjänster och systemtilldelade hanterad identitet rättigheter har återkallats

När distributionen har slutförts, skisser återkallar rättigheter den **systemtilldelade** hanterad identitet från prenumerationen. Tjänsten skisser återkallar sedan sina rättigheter från prenumerationen. Rättigheter borttagning förhindrar skisser blir en permanenta ägare för en prenumeration.

## <a name="next-steps"></a>Nästa steg

- Förstå hur du använder [statiska och dynamiska parametrar](parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).