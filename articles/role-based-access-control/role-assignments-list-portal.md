---
title: Lista rolltilldelningar med Azure RBAC och Azure-portalen
description: Lär dig hur du tar reda på vilka resurser användare, grupper, tjänsthuvudnamn eller hanterade identiteter har åtkomst till med hjälp av Azure-rollbaserad åtkomstkontroll (RBAC) och Azure-portalen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 03a3d3c7d572d7ec5b8d3ac3d527d0d59e649bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062247"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Lista rolltilldelningar med Azure RBAC och Azure-portalen

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]I den här artikeln beskrivs hur du listar rolltilldelningar med Hjälp av Azure-portalen.

> [!NOTE]
> Om din organisation har lagt ut hanteringsfunktioner på entreprenad till en tjänsteleverantör som använder [Azure-delegerad resurshantering](../lighthouse/concepts/azure-delegated-resource-management.md)visas inte rolltilldelningar som auktoriserats av den tjänsteleverantören här.

## <a name="list-role-assignments-for-a-user-or-group"></a>Lista rolltilldelningar för en användare eller grupp

Det enklaste sättet att se de roller som tilldelats en användare eller grupp i en prenumeration är att använda **Azure-resursfönstret.**

1. Klicka på Alla **tjänster** i Azure-portalen och välj sedan **Användare** eller **Grupper**.

1. Klicka på den användare eller grupp som du vill visa rolltilldelningarna för.

1. Klicka på **Azure-resurser**.

    Du ser en lista över roller som tilldelats den valda användaren eller gruppen i olika scope, till exempel hanteringsgrupp, prenumeration, resursgrupp eller resurs. Den här listan innehåller alla rolltilldelningar som du har behörighet att läsa.

    ![Rolltilldelningar för en användare](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Om du vill ändra prenumerationen klickar du på listan **Prenumerationer.**

## <a name="list-owners-of-a-subscription"></a>Lista ägare av en prenumeration

Användare som har tilldelats [rollen Ägare](built-in-roles.md#owner) för en prenumeration kan hantera allt i prenumerationen. Följ dessa steg för att lista ägarna till en prenumeration.

1. I Azure-portalen klickar du på **Alla tjänster** och sedan **Prenumerationer**.

1. Klicka på den prenumeration som du vill visa ägare till.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Rolltilldelningar** så att du ser alla rolltilldelningar för prenumerationen.

1. Bläddra till avsnittet **Ägare** om du vill visa alla användare som har tilldelats ägarrollen för den här prenumerationen.

   ![Kontroll av prenumerationsåtkomst - fliken Rolltilldelningar](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Lista rolltilldelningar i ett scope

1. Klicka på Alla **tjänster** i Azure-portalen och välj sedan scopet. Du kan till exempel välja **Hanteringsgrupper,** **Prenumerationer,** **Resursgrupper**eller en resurs.

1. Klicka på den specifika resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Rolltilldelningar** om du vill visa alla rolltilldelningar i det här scopet.

   ![Åtkomstkontroll - fliken Rolltilldelningar](./media/role-assignments-list-portal/access-control-role-assignments.png)

   På fliken Rolltilldelningar kan du se vem som har åtkomst i det här omfånget. Observera att vissa roller är begränsade till **den här resursen** medan andra är **(Ärvda)** från ett annat omfång. Åtkomst tilldelas antingen specifikt till den här resursen eller ärvs från en tilldelning till det överordnade scopet.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Lista rolltilldelningar för en användare i ett scope

Om du vill visa åtkomst för en användare, grupp, tjänsthuvudnamn eller hanterad identitet listar du deras rolltilldelningar. Följ dessa steg för att lista rolltilldelningarna för en enskild användare, grupp, tjänsthuvudnamn eller hanterad identitet i ett visst scope.

1. Klicka på Alla **tjänster** i Azure-portalen och välj sedan scopet. Du kan till exempel välja **Hanteringsgrupper,** **Prenumerationer,** **Resursgrupper**eller en resurs.

1. Klicka på den specifika resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Kontrollera åtkomst**.

    ![Åtkomstkontroll – fliken Kontrollera åtkomst](./media/role-assignments-list-portal/access-control-check-access.png)

1. I listan **Hitta** väljer du den typ av säkerhetsprincip som du vill kontrollera åtkomst för.

1. I sökrutan anger du en sträng för att söka i katalogen efter visningsnamn, e-postadresser eller objektidentifierare.

    ![Välj lista för Kontrollera åtkomst](./media/role-assignments-list-portal/check-access-select.png)

1. Klicka på säkerhetsprincipen för att öppna fönsterrutan **tilldelningar**.

    ![fönsterrutan tilldelningar](./media/role-assignments-list-portal/check-access-assignments.png)

    I den här fönsterrutan kan du se de roller som tilldelats till den valda säkerhetsprincipen och omfånget. Om det finns några nekande tilldelningar i det här omfånget eller som ärvts till det här omfånget visas de.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Lista rolltilldelningar för en systemtilldelad hanterad identitet

1. Öppna en systemtilldelad hanterad identitet i Azure-portalen.

1. Klicka på **Identitet**på den vänstra menyn.

    ![Systemtilldelad hanterad identitet](./media/role-assignments-list-portal/identity-system-assigned.png)

1. Under **Rolltilldelningar**klickar du på **Visa Azure RBAC-roller som tilldelats den här hanterade identiteten**.

    Du ser en lista över roller som tilldelats den valda systemtilldelade hanterade identiteten vid olika scope, till exempel hanteringsgrupp, prenumeration, resursgrupp eller resurs. Den här listan innehåller alla rolltilldelningar som du har behörighet att läsa.

    ![Rolltilldelningar för en systemtilldelad hanterad identitet](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Lista rolltilldelningar för en användartilldelad hanterad identitet

1. Öppna en användartilldelad hanterad identitet i Azure-portalen.

1. Klicka på **Azure-resurser**.

    Du ser en lista över roller som tilldelats den valda användartilldelade hanterade identiteten i olika scope, till exempel hanteringsgrupp, prenumeration, resursgrupp eller resurs. Den här listan innehåller alla rolltilldelningar som du har behörighet att läsa.

    ![Rolltilldelningar för en systemtilldelad hanterad identitet](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Om du vill ändra prenumerationen klickar du på listan **Prenumerationer.**

## <a name="list-number-of-role-assignments"></a>Lista antal rolltilldelningar

Du kan ha upp till **2 000** rolltilldelningar i varje prenumeration. På sidan Rolltilldelningar för att hjälpa dig att hålla reda på den här gränsen innehåller fliken **Rolltilldelningar** ett diagram med antalet rolltilldelningar för den aktuella prenumerationen.

![Åtkomstkontroll - Antal rolltilldelningar diagram](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Om du närmar dig det maximala antalet och försöker lägga till fler rolltilldelningar visas en varning i fönstret **Lägg till rolltilldelning.** Hur du kan minska antalet rolltilldelningar finns i [Felsöka Azure RBAC](troubleshooting.md#azure-role-assignments-limit).

![Åtkomstkontroll - Lägga till varning för rolltilldelning](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort rolltilldelningar med Azure RBAC och Azure-portalen](role-assignments-portal.md)
- [Felsöka RBAC för Azure-resurser](troubleshooting.md)
