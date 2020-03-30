---
title: Förstå rollbaserad åtkomstkontroll – Azure Digital Twins | Microsoft-dokument
description: Lär dig mer om rollbaserad åtkomstkontroll och hantering av behörigheter i Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: feda4b3a7f21b581fb4f08aec013f87c0fabb7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044921"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Rollbaserad åtkomstkontroll i Azure Digital Twins

Azure Digital Twins möjliggör exakt åtkomstkontroll över specifika data, resurser och åtgärder i ditt rumsliga diagram. Det gör det genom detaljerad roll och behörighetshantering som kallas [rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC). RBAC består av _roller_ och _rolltilldelningar_. Roller identifierar behörighetsnivån. Rolltilldelningar associerar en roll med en användare eller enhet.

Med hjälp av RBAC kan behörighet beviljas till:

- En användare.
- En enhet.
- En tjänst huvudman.
- En användardefinierad funktion.
- Alla användare som tillhör en domän.
- En hyresgäst.

Graden av tillgång kan också finjusteras.

RBAC är unikt eftersom behörigheter ärvs ned i det rumsliga diagrammet.

## <a name="what-can-i-do-with-rbac"></a>Vad kan jag göra med RBAC?

En utvecklare kan använda RBAC för att:

- Ge en användare möjlighet att hantera enheter för en hel byggnad, eller bara för ett visst rum eller golv.
- Ge en global administratör åtkomst till alla rumsliga grafnoder för ett helt diagram, eller bara för ett avsnitt i diagrammet.
- Bevilja en supportspecialist läsåtkomst till diagrammet, med undantag för åtkomstnycklar.
- Ge varje medlem i en domän läsbehörighet till alla diagramobjekt.

## <a name="rbac-best-practices"></a>Bästa praxis för RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Roller

### <a name="role-definitions"></a>Rolldefinitioner

En rolldefinition är en samling behörigheter och andra attribut som utgör en roll. En rolldefinition listar de tillåtna åtgärderna, som inkluderar *SKAPA,* *LÄS*, *UPPDATERA*och *TA BORT* som alla objekt med den rollen kan utföra. Den anger också för vilka objekttyper behörigheterna gäller för.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Om du vill hämta de fullständiga definitionerna för de tidigare rollerna frågar du system-/roll-API:et.
> Läs mer genom att läsa [Skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md#retrieve-all-roles).

### <a name="object-identifier-types"></a>Objektidentifierare typer

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Lär dig hur du beviljar behörigheter till tjänstens huvudnamn genom att läsa [Skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal).

Följande artiklar i referensdokumentationen beskriver följande artiklar:

- Fråga [eller objekt-ID för en användare](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Så [här skaffar du objekt-ID:et för ett huvudnamn för tjänsten](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Hämta [objekt-ID:et för en Azure AD-klientorganisation](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Rolltilldelningar

En Azure Digital Twins-rolltilldelning associerar ett objekt, till exempel en användare eller en Azure AD-klient, med en roll och ett utrymme. Behörigheter beviljas alla objekt som tillhör det utrymmet. Utrymmet innehåller hela den rumsliga grafen under den.

En användare får till exempel en rolltilldelning med rollen `DeviceInstaller` för rotnoden i ett rumsligt diagram som representerar en byggnad. Användaren kan sedan läsa och uppdatera enheter för den noden och alla andra underordnade utrymmen i byggnaden.

Skapa en rolltilldelning om du vill bevilja behörigheter till en mottagare. Om du vill återkalla behörigheter tar du bort rolltilldelningen.

>[!IMPORTANT]
> Läs mer om rolltilldelningar genom att läsa [Skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar och hanterar Azure Digital Twins-rolltilldelningar finns i [Skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md).

- Läs mer om [RBAC för Azure](https://docs.microsoft.com/azure/role-based-access-control/).
