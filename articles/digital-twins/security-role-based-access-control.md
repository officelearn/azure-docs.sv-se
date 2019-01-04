---
title: Förstå Azure Digital Twins rollbaserad åtkomstkontroll | Microsoft Docs
description: Läs om autentisering i digitala Twins med rollbaserad åtkomstkontroll.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: lyrana
ms.openlocfilehash: c1e48f6cb7ca4f26b8cafc31605bc9441ed047d3
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807609"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Rollbaserad åtkomstkontroll i Azure Digital Twins

Azure Digital Twins kan exakt åtkomstkontroll till specifika data, resurser och åtgärder i grafen spatial. Detta sker via detaljerade roll- och behörighetshantering som kallas rollbaserad åtkomstkontroll (RBAC). RBAC består av _roller_ och _rolltilldelningar_. Roller identifiera behörighetsnivån. Rolltilldelningar koppla en roll till en användare eller enhet.

Med RBAC kan kan behörighet beviljas till:

- En användare.
- En enhet.
- Ett huvudnamn för tjänsten.
- En användardefinierad funktion.
- Alla användare som tillhör en domän.
- En klient.

Graden av åtkomst kan också vara välavvägda.

RBAC är unikt eftersom behörigheter ärvs av spatial diagrammet.

## <a name="what-can-i-do-with-rbac"></a>Vad kan jag göra med RBAC?

Utvecklare kan använda RBAC för att:

- Bevilja en användare möjlighet att hantera enheter för en hel byggnad eller endast för ett specifikt rum eller våning.
- Bevilja en global administratörsåtkomst till alla spatial graph-noder för en hela diagrammet eller endast för en del av diagrammet.
- Ger en support specialist läsåtkomst till diagrammet, förutom åtkomstnycklar.
- Ge alla medlemmar i en domän läsbehörighet till alla objekt i diagrammet.

## <a name="rbac-best-practices"></a>Metodtips för RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Roller

### <a name="role-definitions"></a>Rolldefinitioner

En rolldefinition är en uppsättning behörigheter och andra attribut som utgör en roll. En rolldefinition visar en lista över tillåtna åtgärder, bland annat *skapa*, *läsa*, *uppdatering*, och *ta bort* att något objekt med som rollen kan utföra. Den anger också som objektbehörigheter för typer som gäller för.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Fråga systemet/roller API för att hämta fullständig definitionerna för rollerna som tidigare.
> Lär dig mer genom att läsa [skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md#all).

### <a name="object-identifier-types"></a>Identifierare för objekttyper

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Lär dig att ge behörighet till tjänstens huvudnamn genom att läsa [skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md#grant).

I följande dokumentation referensartiklar beskriver:

- Så här [fråga eller objekt-ID för en användare](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Så här [hämta objekt-ID för tjänstens huvudnamn](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1).
- Så här [hämta objekt-ID för en Azure AD-klient](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Rolltilldelningar

En rolltilldelning i Azure Digital Twins associerar ett objekt, till exempel en användare eller en Azure AD-klient med en roll eller ett blanksteg. Behörigheter för alla objekt som hör till det området. Området innehåller hela spatial diagrammet under den.

Exempelvis kan en användare får en rolltilldelning med rollen `DeviceInstaller` för rotnoden i ett spatial diagram, som representerar en byggnad. Användaren kan läsa och uppdatera enheter för den aktuella noden och alla andra underordnade utrymmen i byggnaden.

Skapa en rolltilldelning för att tilldela behörigheter till en mottagare. Om du vill återkalla behörighet att ta bort rolltilldelningen.

>[!IMPORTANT]
> Läs mer om rolltilldelningar genom att läsa [skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om att skapa och hantera rolltilldelningar i Azure Digital Twins [skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md).
