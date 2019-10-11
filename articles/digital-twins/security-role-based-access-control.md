---
title: Förstå rollbaserad åtkomst kontroll – Azure Digitals flätar | Microsoft Docs
description: Lär dig mer om rollbaserad åtkomst kontroll och hantering av behörigheter i Azure Digitals dubbla.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: e89a8f98d92e92fa8afe93340576b14ff1dd5051
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249204"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Rollbaserad åtkomst kontroll i Azure Digitals dubbla

Azure Digitals-dubbla ger exakt åtkomst kontroll över specifika data, resurser och åtgärder i din spatialdata. Det gör det genom detaljerad roll-och behörighets hantering som kallas [rollbaserad åtkomst kontroll](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC). RBAC består av _roller_ och _roll tilldelningar_. Roller identifierar behörighets nivån. Roll tilldelningar associerar en roll med en användare eller enhet.

Med RBAC kan du bevilja behörighet till:

- En användare.
- En enhet.
- Ett huvud namn för tjänsten.
- En användardefinierad funktion.
- Alla användare som tillhör en domän.
- En klient.

Åtkomst graden kan också finjusteras.

RBAC är unikt i den behörigheten som ärvs nedåt i diagrammets avstånd.

## <a name="what-can-i-do-with-rbac"></a>Vad kan jag göra med RBAC?

En utvecklare kan använda RBAC för att:

- Ge en användare möjlighet att hantera enheter för en hel byggnad eller bara för ett speciellt rum eller våning.
- Bevilja en global administratörs åtkomst till alla spatiala diagram-noder för en hel graf eller bara för en del av diagrammet.
- Ge support specialist Läs behörighet till grafen, förutom åtkomst nycklar.
- Ge varje medlem i en domän Läs behörighet till alla graf-objekt.

## <a name="rbac-best-practices"></a>Metod tips för RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Roller

### <a name="role-definitions"></a>Roll definitioner

En roll definition är en samling behörigheter och andra attribut som utgör en roll. En roll definition visar en lista över tillåtna åtgärder, bland annat *skapa*, *läsa*, *Uppdatera*och *ta bort* som alla objekt med den rollen kan utföra. Den anger också vilka objekt typer behörigheterna gäller för.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Om du vill hämta de fullständiga definitionerna för de tidigare rollerna frågar du system/roles-API.
> Läs mer om att [skapa och hantera roll tilldelningar](./security-create-manage-role-assignments.md#retrieve-all-roles).

### <a name="object-identifier-types"></a>Objekt identifierare typer

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Lär dig hur du beviljar behörigheter till tjänstens huvud namn genom att läsa [skapa och hantera roll tilldelningar](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal).

I följande artiklar i referens dokumentation beskrivs:

- [Fråga eller objekt-ID för en användare](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Så här [hämtar du objekt-ID för ett huvud namn för tjänsten](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Så här [hämtar du objekt-ID för en Azure AD-klient](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Rolltilldelningar

En Azure Digitals sammanflätad roll tilldelning associerar ett objekt, till exempel en användare eller en Azure AD-klient, med en roll och ett utrymme. Behörigheter beviljas till alla objekt som tillhör det utrymmet. Utrymmet omfattar hela det spatiala diagrammet under det.

En användare får till exempel en roll tilldelning med rollen `DeviceInstaller` för rotnoden i en spatial graf, som representerar en byggnad. Användaren kan sedan läsa och uppdatera enheter för den noden och alla andra underordnade utrymmen i byggnaden.

Om du vill bevilja behörighet till en mottagare skapar du en roll tilldelning. Ta bort roll tilldelningen om du vill återkalla behörigheter.

>[!IMPORTANT]
> Lär dig mer om roll tilldelningar genom att läsa [skapa och hantera roll tilldelningar](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om hur du skapar och hanterar Azure Digitals sammanflätade roll tilldelningar läser du [skapa och hantera roll tilldelningar](./security-create-manage-role-assignments.md).

- Läs mer om [RBAC för Azure](https://docs.microsoft.com/azure/role-based-access-control/).
