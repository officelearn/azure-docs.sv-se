---
title: Hantera åtkomst till Azure-resurser med RBAC och Azure Portal | Microsoft Docs
description: Lär dig hur du hanterar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn och hanterade identiteter med hjälp av rollbaserad åtkomst kontroll (RBAC) och Azure Portal. Detta innefattar hur du listar åtkomst, ger åtkomst och tar bort åtkomst.
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
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e9a53c41535c17de2d56227012160c7f6eb25c6
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337609"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Hantera åtkomst till Azure-resurser med RBAC och Azure Portal

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är metoden som du använder när du hanterar åtkomst till Azure-resurser. Den här artikeln beskriver hur du hanterar åtkomst med hjälp av Azure Portal. Om du behöver hantera åtkomst till Azure Active Directory, se [Visa och tilldela administratörs roller i Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill lägga till och ta bort roll tilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Översikt över åtkomst kontroll (IAM)

**Åtkomst kontroll (IAM)** är det blad som du använder för att hantera åtkomst till Azure-resurser. Det kallas även identitets-och åtkomst hantering och visas på flera platser i Azure Portal. Följande visar ett exempel på bladet åtkomst kontroll (IAM) för en prenumeration.

![Åtkomst kontroll (IAM) bladet för en prenumeration](./media/role-assignments-portal/access-control-numbers.png)

I följande tabell beskrivs hur några av elementen används för:

| # | Element | Det du använder den för |
| --- | --- | --- |
| 1 | Resurs där åtkomst kontroll (IAM) är öppen | Identifiera omfattning (prenumeration i det här exemplet) |
| 2 | Knappen **Lägg till** | Lägg till roll tilldelningar |
| 3 | **Kontrol lera åtkomst** fliken | Visa roll tilldelningarna för en enskild användare |
| 4 | Fliken **roll tilldelningar** | Visa roll tilldelningarna i det aktuella omfånget |
| 5 | Fliken **roller** | Visa alla roller och behörigheter |

För att bli mest effektiv med åtkomst kontroll (IAM)-bladet hjälper det om du kan besvara följande tre frågor när du försöker hantera åtkomst:

1. **Vem behöver åtkomst?**

    Som refererar till en användare, grupp, tjänstens huvud namn eller en hanterad identitet. Detta kallas även för ett *säkerhets objekt*.

1. **Vilka behörigheter behöver de?**

    Behörigheter grupperas tillsammans i roller. Du kan välja från en lista över flera inbyggda roller.

1. **Var behöver de ha åtkomst?**

    Där refererar till den uppsättning resurser som åtkomsten gäller för. Var kan vara en hanterings grupp, en prenumeration, en resurs grupp eller en enskild resurs, till exempel ett lagrings konto. Detta kallas för *omfånget*.

## <a name="open-access-control-iam"></a>Öppna åtkomst kontroll (IAM)

Det första du behöver bestämma är var du ska öppna bladet åtkomst kontroll (IAM). Det beror på vilka resurser som du vill hantera åtkomst för. Vill du hantera åtkomst för allt i en hanterings grupp, allt i en prenumeration, allt i en resurs grupp eller en enskild resurs?

1. I Azure Portal klickar du på **alla tjänster** och väljer sedan omfånget. Du kan till exempel välja **hanterings grupper**, **prenumerationer**, **resurs grupper**eller en resurs.

1. Klicka på den aktuella resursen.

1. Klicka på **åtkomstkontroll (IAM)** .

    Följande visar ett exempel på bladet åtkomst kontroll (IAM) för en prenumeration. Om du gör ändringar i åtkomst kontrollen gäller de för hela prenumerationen.

    ![Åtkomst kontroll (IAM) bladet för en prenumeration](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Visa roller och behörigheter

En rolldefinition är en uppsättning behörigheter som du använder för rolltilldelningar. Azure har över 70 [inbyggda roller för Azure-resurser](built-in-roles.md). Följ dessa steg om du vill visa tillgängliga roller och behörigheter.

1. Öppna **åtkomst kontroll (IAM)** i valfri omfattning.

1. Klicka på fliken **roller** om du vill se en lista över alla inbyggda och anpassade roller.

   Du kan se hur många användare och grupper som har tilldelats varje roll i det aktuella omfånget.

   ![Lista över roller](./media/role-assignments-portal/roles-list.png)

1. Klicka på en enskild roll om du vill se vem som har tilldelats den här rollen och även Visa behörigheterna för rollen.

   ![Roll tilldelningar](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Visa rolltilldelningar

När du hanterar åtkomst vill du veta vem som har åtkomst, vilka behörigheter som finns och i vilken omfattning. Om du vill visa åtkomst för en användare, grupp, tjänstens huvud namn eller hanterad identitet visar du roll tilldelningarna.

### <a name="view-role-assignments-for-a-single-user"></a>Visa roll tilldelningar för en enskild användare

Följ dessa steg om du vill visa åtkomsten för en enskild användare, grupp, tjänstens huvud namn eller hanterad identitet i ett visst omfång.

1. Öppna **åtkomst kontroll (IAM)** i ett omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs, där du vill visa åtkomst.

1. Klicka på fliken **Kontrollera åtkomst**.

    ![Åtkomstkontroll – fliken Kontrollera åtkomst](./media/role-assignments-portal/access-control-check-access.png)

1. I listan **Hitta** väljer du den typ av säkerhetsprincip som du vill kontrollera åtkomst för.

1. I sökrutan anger du en sträng för att söka i katalogen efter visningsnamn, e-postadresser eller objektidentifierare.

    ![Välj lista för Kontrollera åtkomst](./media/role-assignments-portal/check-access-select.png)

1. Klicka på säkerhetsprincipen för att öppna fönsterrutan **tilldelningar**.

    ![fönsterrutan tilldelningar](./media/role-assignments-portal/check-access-assignments.png)

    I den här fönsterrutan kan du se de roller som tilldelats till den valda säkerhetsprincipen och omfånget. Om det finns några nekande tilldelningar i det här omfånget eller som ärvts till det här omfånget visas de.

### <a name="view-all-role-assignments-at-a-scope"></a>Visa alla roll tilldelningar i ett omfång

1. Öppna **åtkomst kontroll (IAM)** i ett omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs, där du vill visa åtkomst.

1. Klicka på fliken **roll tilldelningar** för att visa alla roll tilldelningar i det här omfånget.

   ![Åtkomst kontroll – fliken roll tilldelningar](./media/role-assignments-portal/access-control-role-assignments.png)

   På fliken roll tilldelningar kan du se vem som har åtkomst till det här omfånget. Observera att vissa roller är begränsade till **den här resursen** medan andra är **(Ärvda)** från ett annat omfång. Åtkomst tilldelas antingen specifikt till den här resursen eller ärvts från en tilldelning till det överordnade omfånget.

## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

I RBAC för att bevilja åtkomst tilldelar du en roll till en användare, grupp, tjänstens huvud namn eller hanterad identitet. Följ dessa steg om du vill bevilja åtkomst i olika omfattningar.

### <a name="assign-a-role-at-a-scope"></a>Tilldela en roll i ett omfång

1. Öppna **åtkomst kontroll (IAM)** i ett omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs, där du vill bevilja åtkomst.

1. Klicka på fliken **roll tilldelningar** för att visa alla roll tilldelningar i det här omfånget.

1. Klicka på **Lägg till** > **Lägg till rolltilldelning** för att öppna fönsterrutan Lägg till rolltilldelning.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till](./media/role-assignments-portal/add-menu.png)

   ![Fönsterrutan Lägg till rolltilldelning](./media/role-assignments-portal/add-role-assignment.png)

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. Välj en användare, grupp, tjänstens huvud namn eller hanterad identitet i listan **Välj** . Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.

1. Klicka på **Spara** för att tilldela rollen.

   Efter en liten stund tilldelas säkerhets objekt rollen i det valda omfånget.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Tilldela en användare som administratör för en prenumeration

Om du vill göra en användare till en administratör för en Azure-prenumeration tilldelar du den till [ägar](built-in-roles.md#owner) rollen i prenumerations omfånget. Rollen ägare ger fullständig åtkomst till alla resurser i prenumerationen, inklusive rätten att ge åtkomst till andra. De här stegen är desamma som för andra rolltilldelningar.

1. I Azure-portalen klickar du på **Alla tjänster** och sedan **Prenumerationer**.

1. Klicka på prenumerationen du vill ge åtkomst till.

1. Klicka på **åtkomstkontroll (IAM)** .

1. Klicka på fliken **Rolltilldelningar** så att du ser alla rolltilldelningar för prenumerationen.

1. Klicka på **Lägg till** > **Lägg till rolltilldelning** för att öppna fönsterrutan Lägg till rolltilldelning.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till](./media/role-assignments-portal/add-menu.png)

   ![Fönsterrutan Lägg till rolltilldelning](./media/role-assignments-portal/add-role-assignment.png)

1. I listrutan **Roll** väljer du rollen **Ägare**.

1. Välj en användare i listan **Välj**. Om du inte ser användaren i listan kan du ange visningsnamn och e-postadresser i rutan **Välj** om du vill söka i katalogen.

1. Klicka på **Spara** för att tilldela rollen.

   Efter en stund tilldelas rollen Ägare till användaren i prenumerationsomfånget.

## <a name="remove-role-assignments"></a>Ta bort rolltilldelningar

I RBAC kan du ta bort en rolltilldelning för att ta bort åtkomst. Följ dessa steg om du vill ta bort åtkomst.

1. Öppna **åtkomst kontroll (IAM)** i ett omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs, där du vill ta bort åtkomsten.

1. Klicka på fliken **Rolltilldelningar** så att du ser alla rolltilldelningar för prenumerationen.

1. Lägg till en bock intill säkerhetsobjektet med rolltilldelningen som du vil ta bort i listan med rolltilldelningar.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klicka på **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment.png)

1. I meddelandet om att ta bort rolltilldelningen klickar du på **Ja**.

    Ärvda tilldelningar kan inte tas bort. Om du behöver ta bort en ärvd tilldelning så måste du göra det i samma omfång som där rolltilldelningen skapades. I kolumnen **omfattning** bredvid **(ärvd)** finns en länk som tar dig till den omfattning där rollen tilldelades. Gå till omfånget som visas där om du vill ta bort rolltilldelningen.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Nästa steg

* [Självstudier: Bevilja åtkomst för en användare till Azure-resurser med hjälp av RBAC och Azure-portalen](quickstart-assign-role-user-portal.md)
* [Självstudier: Ge en användare åtkomst till Azure-resurser med RBAC och Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Felsöka RBAC för Azure-resurser](troubleshooting.md)
* [Organisera dina resurser med Azure-hanteringsgrupper](../governance/management-groups/overview.md)
