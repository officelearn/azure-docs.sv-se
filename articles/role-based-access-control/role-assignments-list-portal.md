---
title: Lista roll tilldelningar med hjälp av Azure RBAC och Azure Portal
description: Lär dig hur du avgör vilka resurser användare, grupper, tjänstens huvud namn eller hanterade identiteter har åtkomst till med hjälp av rollbaserad åtkomst kontroll i Azure (RBAC) och Azure Portal.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 25e11ae1311df9d0392340b32e0691298f78ee1c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710430"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Lista roll tilldelningar med hjälp av Azure RBAC och Azure Portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] den här artikeln beskriver hur du visar roll tilldelningar med hjälp av Azure Portal.

## <a name="list-role-assignments-for-a-user-or-group"></a>Lista roll tilldelningar för en användare eller grupp

Det enklaste sättet att se de roller som har tilldelats en användare eller grupp i en prenumeration är att använda fönstret **Azure-resurser** .

1. I Azure Portal klickar du på **alla tjänster** och väljer sedan **användare** eller **grupper**.

1. Klicka på den användare eller grupp som du vill visa en lista över roll tilldelningarna för.

1. Klicka på **Azure-resurser**.

    Du ser en lista över roller som har tilldelats den valda användaren eller gruppen vid olika omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs. Den här listan innehåller alla roll tilldelningar som du har behörighet att läsa.

    ![Rolltilldelningar för en användare](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Klicka på listan **prenumerationer** om du vill ändra prenumerationen.

## <a name="list-role-assignments-at-a-scope"></a>Lista roll tilldelningar i ett omfång

1. I Azure Portal klickar du på **alla tjänster** och väljer sedan omfånget. Du kan till exempel välja **hanterings grupper**, **prenumerationer**, **resurs grupper**eller en resurs.

1. Klicka på den aktuella resursen.

1. Klicka på **Åtkomstkontroll (IAM)** .

1. Klicka på fliken **roll tilldelningar** för att visa alla roll tilldelningar i det här omfånget.

   ![Åtkomst kontroll – fliken roll tilldelningar](./media/role-assignments-list-portal/access-control-role-assignments.png)

   På fliken roll tilldelningar kan du se vem som har åtkomst till det här omfånget. Observera att vissa roller är begränsade till **den här resursen** medan andra är **(Ärvda)** från ett annat omfång. Åtkomst tilldelas antingen specifikt till den här resursen eller ärvts från en tilldelning till det överordnade omfånget.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Lista roll tilldelningar för en användare i ett omfång

Om du vill visa en lista över åtkomsten för en användare, grupp, tjänstens huvud namn eller en hanterad identitet anger du roll tilldelningarna. Följ dessa steg om du vill visa roll tilldelningarna för en enskild användare, grupp, tjänstens huvud namn eller hanterad identitet i ett visst omfång.

1. I Azure Portal klickar du på **alla tjänster** och väljer sedan omfånget. Du kan till exempel välja **hanterings grupper**, **prenumerationer**, **resurs grupper**eller en resurs.

1. Klicka på den aktuella resursen.

1. Klicka på **Åtkomstkontroll (IAM)** .

1. Klicka på fliken **Kontrollera åtkomst**.

    ![Åtkomstkontroll – fliken Kontrollera åtkomst](./media/role-assignments-list-portal/access-control-check-access.png)

1. I listan **Hitta** väljer du den typ av säkerhetsprincip som du vill kontrollera åtkomst för.

1. I sökrutan anger du en sträng för att söka i katalogen efter visningsnamn, e-postadresser eller objektidentifierare.

    ![Välj lista för Kontrollera åtkomst](./media/role-assignments-list-portal/check-access-select.png)

1. Klicka på säkerhetsprincipen för att öppna fönsterrutan **tilldelningar**.

    ![fönsterrutan tilldelningar](./media/role-assignments-list-portal/check-access-assignments.png)

    I den här fönsterrutan kan du se de roller som tilldelats till den valda säkerhetsprincipen och omfånget. Om det finns några nekande tilldelningar i det här omfånget eller som ärvts till det här omfånget visas de.

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort roll tilldelningar med hjälp av Azure RBAC och Azure Portal](role-assignments-portal.md)
- [Felsöka RBAC för Azure-resurser](troubleshooting.md)
