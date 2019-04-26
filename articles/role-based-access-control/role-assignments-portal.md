---
title: Hantera åtkomst till Azure-resurser med RBAC och Azure portal | Microsoft Docs
description: Lär dig mer om att hantera åtkomst till Azure-resurser för användare, grupper, tjänstens huvudnamn och hanterade identiteter med hjälp av rollbaserad åtkomstkontroll (RBAC) och Azure-portalen. Detta innefattar hur du listar åtkomst, ger åtkomst och tar bort åtkomst.
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
ms.openlocfilehash: bb23cbc275e01eab5361504c547c020b0a29f4c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533008"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Hantera åtkomst till Azure-resurser med RBAC och Azure portal

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är metoden som du använder när du hanterar åtkomst till Azure-resurser. Den här artikeln beskriver hur du hanterar åtkomst med hjälp av Azure-portalen. Om du behöver hantera åtkomst till Azure Active Directory finns i [visa och tilldela administratörsroller i Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill lägga till och ta bort rolltilldelningar, måste du ha:

- `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Översikt över åtkomstkontroll (IAM)

**Åtkomstkontroll (IAM)** är bladet som används för att hantera åtkomst till Azure-resurser. Det kallas även för identitets- och åtkomsthantering och visas på flera platser i Azure-portalen. Nedan visas ett exempel på åtkomstkontroll (IAM)-bladet för en prenumeration.

![Åtkomstkontroll (IAM)-bladet för en prenumeration](./media/role-assignments-portal/access-control-numbers.png)

I följande tabell beskrivs vad några av elementen som används för:

| # | Element | Det du använder den för |
| --- | --- | --- |
| 1 | Resurs där åtkomstkontroll (IAM) öppnas | Identifiera omfattning (prenumeration i det här exemplet) |
| 2 | **Lägg till** knappen | Lägg till rolltilldelningar |
| 3 | **Kontrollera åtkomst** fliken | Visa rolltilldelningar för en enskild användare |
| 4 | **Rolltilldelningar** fliken | Visa rolltilldelningar i det aktuella omfånget |
| 5 | **Roller** fliken | Visa alla roller och behörigheter |

Om du vill att de mest effektiva med åtkomstkontroll (IAM)-bladet, underlättar det om du kan svara på följande tre frågor när du försöker hantera åtkomst:

1. **Vilka som behöver åtkomst?**

    Som refererar till en användare, grupp, tjänstens huvudnamn eller hanterad identitet. Detta kallas också en *säkerhetsobjekt*.

1. **Vilka behörigheter behöver de?**

    Behörigheter grupperas tillsammans i roller. Du kan välja från en lista över flera inbyggda roller.

1. **Där behöver de åtkomst?**

    Där refererar till uppsättningen resurser som åtkomsten som gäller för. Där kan vara en hanteringsgrupp, prenumeration, resursgrupp eller en enskild resurs, till exempel ett storage-konto. Detta kallas den *omfång*.

## <a name="open-access-control-iam"></a>Öppna åtkomstkontroll (IAM)

Det första du måste bestämma är var du vill öppna åtkomstkontroll (IAM)-bladet. Det beror på vilka resurser som du vill hantera åtkomst för. Vill du hantera åtkomsten för allt innehåll i en hanteringsgrupp, allt i en prenumeration, allt i en resursgrupp eller en enskild resurs?

1. I Azure-portalen klickar du på **alla tjänster** och välj sedan omfånget. Du kan till exempel välja **hanteringsgrupper**, **prenumerationer**, **resursgrupper**, eller en resurs.

1. Klicka på den specifika resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

    Nedan visas ett exempel på åtkomstkontroll (IAM)-bladet för en prenumeration. Om du gör några ändringar åtkomstkontroll här, skulle de gäller för hela prenumerationen.

    ![Åtkomstkontroll (IAM)-bladet för en prenumeration](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Visa roller och behörigheter

En rolldefinition är en uppsättning behörigheter som du använder för rolltilldelningar. Azure har över 70 [inbyggda roller för Azure-resurser](built-in-roles.md). Följ dessa steg om du vill visa de tillgängliga roller och behörigheter.

1. Öppna **åtkomstkontroll (IAM)** i alla omfånget.

1. Klicka på den **roller** fliken för att se en lista över alla inbyggda och anpassade roller.

   Du kan se hur många användare och grupper som är kopplade till varje roll i det aktuella omfånget.

   ![Lista över roller](./media/role-assignments-portal/roles-list.png)

1. Klicka på en enskild roll om du vill se vem som har tilldelats den här rollen och även visa behörigheter för rollen.

   ![Rolltilldelningar](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Visa rolltilldelningar

När du hanterar åtkomst du vill veta vem som har åtkomst, vad är deras behörigheter och i vilken omfattning. Lista-åtkomst för en användare, grupp, tjänstens huvudnamn eller hanterad identitet du visa sin rolltilldelningar.

### <a name="view-role-assignments-for-a-single-user"></a>Visa rolltilldelningar för en enskild användare

Följ dessa steg om du vill visa åtkomst för en enskild användare, grupp, tjänstens huvudnamn eller hanterad identitet i ett visst omfång.

1. Öppna **åtkomstkontroll (IAM)** i ett omfång, till exempel hanteringsgruppen, prenumeration, resursgrupp eller resurs, där du vill visa åtkomst.

1. Klicka på fliken **Kontrollera åtkomst**.

    ![Åtkomstkontroll – fliken Kontrollera åtkomst](./media/role-assignments-portal/access-control-check-access.png)

1. I listan **Hitta** väljer du den typ av säkerhetsprincip som du vill kontrollera åtkomst för.

1. I sökrutan anger du en sträng för att söka i katalogen efter visningsnamn, e-postadresser eller objektidentifierare.

    ![Välj lista för Kontrollera åtkomst](./media/role-assignments-portal/check-access-select.png)

1. Klicka på säkerhetsprincipen för att öppna fönsterrutan **tilldelningar**.

    ![fönsterrutan tilldelningar](./media/role-assignments-portal/check-access-assignments.png)

    I den här fönsterrutan kan du se de roller som tilldelats till den valda säkerhetsprincipen och omfånget. Om det finns några nekande tilldelningar i det här omfånget eller som ärvts till det här omfånget visas de.

### <a name="view-all-role-assignments-at-a-scope"></a>Visa alla rolltilldelningar för ett omfång

1. Öppna **åtkomstkontroll (IAM)** i ett omfång, till exempel hanteringsgruppen, prenumeration, resursgrupp eller resurs, där du vill visa åtkomst.

1. Klicka på den **rolltilldelningar** fliken för att visa alla rolltilldelningar i det här omfånget.

   ![Åtkomstkontroll - rollen tilldelningar fliken](./media/role-assignments-portal/access-control-role-assignments.png)

   Du kan se vem som har åtkomst i den här omfattningen på fliken rollen tilldelningar. Observera att vissa roller är begränsade till **den här resursen** medan andra är **(Ärvda)** från ett annat omfång. Åtkomst tilldelas till den här resursen eller ärvs från en tilldelning till den överordnade omfattningen.

## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

I RBAC, om du vill bevilja åtkomst måste tilldela du en roll till en användare, grupp, tjänstens huvudnamn eller hanterad identitet. Följ dessa steg om du vill bevilja åtkomst med olika omfång.

### <a name="assign-a-role-at-a-scope"></a>Tilldela en roll i ett omfång

1. Öppna **åtkomstkontroll (IAM)** i ett omfång, till exempel hanteringsgruppen, prenumeration, resursgrupp eller resurs, där du vill bevilja åtkomst.

1. Klicka på den **rolltilldelningar** fliken för att visa alla rolltilldelningar i det här omfånget.

1. Klicka på **Lägg till** > **Lägg till rolltilldelning** för att öppna fönsterrutan Lägg till rolltilldelning.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till](./media/role-assignments-portal/add-menu.png)

   ![Fönsterrutan Lägg till rolltilldelning](./media/role-assignments-portal/add-role-assignment.png)

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. I den **Välj** väljer du en användare, grupp, tjänstens huvudnamn eller hanterad identitet. Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.

1. Klicka på **spara** du tilldela rollen.

   Efter en liten stund tilldelas säkerhetsobjektet rollen i det valda omfånget.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Tilldela en användare som administratör för en prenumeration

Om du vill göra en administratör för en Azure-prenumeration för en användare, tilldela dem den [ägare](built-in-roles.md#owner) rollen prenumerationsområde. Ägarrollen ger användaren fullständig åtkomst till alla resurser i prenumerationen, inklusive rätten att delegera åtkomst till andra. De här stegen är desamma som alla andra rolltilldelning.

1. I Azure-portalen klickar du på **Alla tjänster** och sedan **Prenumerationer**.

1. Klicka på den prenumeration där du vill bevilja åtkomst.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på den **rolltilldelningar** fliken för att visa alla rolltilldelningar för den här prenumerationen.

1. Klicka på **Lägg till** > **Lägg till rolltilldelning** för att öppna fönsterrutan Lägg till rolltilldelning.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till](./media/role-assignments-portal/add-menu.png)

   ![Fönsterrutan Lägg till rolltilldelning](./media/role-assignments-portal/add-role-assignment.png)

1. I den **rollen** listrutan, väljer den **ägare** roll.

1. I den **Välj** väljer du en användare. Om du inte ser användare i listan kan du skriva i den **Välj** om du vill söka i katalogen för visningsnamn och e-postadresser.

1. Klicka på **spara** du tilldela rollen.

   Efter en liten stund tilldelas användaren ägarrollen prenumerationsområde.

## <a name="remove-role-assignments"></a>Ta bort rolltilldelningar

I RBAC kan du ta bort en rolltilldelning för att ta bort åtkomst. Följ dessa steg om du vill ta bort åtkomst.

1. Öppna **åtkomstkontroll (IAM)** i ett omfång, till exempel hanteringsgruppen, prenumeration, resursgrupp eller resurs, där du vill ta bort åtkomst.

1. Klicka på den **rolltilldelningar** fliken för att visa alla rolltilldelningar för den här prenumerationen.

1. Lägg till en bock intill säkerhetsobjektet med rolltilldelningen som du vil ta bort i listan med rolltilldelningar.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klicka på **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment.png)

1. I meddelandet om att ta bort rolltilldelningen klickar du på **Ja**.

    Ärvda tilldelningar kan inte tas bort. Om du behöver ta bort en ärvd tilldelning så måste du göra det i samma omfång som där rolltilldelningen skapades. I den **omfång** kolumn, bredvid **(ärvt)** det finns en länk som leder till området där den här rollen har tilldelats. Gå till omfånget som visas där om du vill ta bort rolltilldelningen.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Bevilja åtkomst för en användare till Azure-resurser med hjälp av RBAC och Azure-portalen](quickstart-assign-role-user-portal.md)
* [Självstudie: Ge en användare åtkomst till Azure-resurser med RBAC och Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Felsöka RBAC för Azure-resurser](troubleshooting.md)
* [Ordna resurser med hanteringsgrupper i Azure](../governance/management-groups/index.md)
