---
title: Hantera åtkomst till Azure resoruces med RBAC och Azure portal | Microsoft Docs
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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e3057108ef179af2f4692c061091fbdf59f0af2
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342345"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Hantera åtkomst till Azure-resurser med RBAC och Azure portal

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är ett sätt som du hanterar åtkomst till Azure-resurser. Den här artikeln beskriver hur du hanterar åtkomst för användare, grupper, tjänstens huvudnamn och hanterade identiteter med RBAC och Azure-portalen.

## <a name="open-access-control-iam"></a>Öppna åtkomstkontroll (IAM)

Den **åtkomstkontroll (IAM)** bladet, kallas även för identitets- och åtkomsthantering, visas i portalen. Om du vill visa eller hantera åtkomst i portalen, är det första du gör öppna åtkomstkontroll (IAM)-bladet definitionsområdet där du vill visa eller göra en ändring.

1. I Azure-portalen klickar du på **alla tjänster** och välj sedan omfång eller resurs som du vill visa eller hantera. Du kan till exempel välja **hanteringsgrupper**, **prenumerationer**, **resursgrupper**, eller en resurs.

1. Klicka på den specifika resursen som du vill visa eller hantera.

1. Klicka på **Åtkomstkontroll (IAM)**.

    Nedan visas ett exempel på åtkomstkontroll (IAM)-bladet för en prenumeration.

    ![Åtkomstkontroll (IAM)-bladet för en prenumeration](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Visa roller och behörigheter

En rolldefinition är en uppsättning behörigheter som du använder för rolltilldelningar. Azure har över 70 [inbyggda roller för Azure-resurser](built-in-roles.md). Följ dessa steg om du vill visa de roller och behörigheter som kan utföras på hanterings- och dataplanet.

1. Öppna **åtkomstkontroll (IAM)** i ett omfång, till exempel hanteringsgruppen, prenumeration, resursgrupp eller resurs, där du vill visa roller och behörigheter.

1. Klicka på den **roller** fliken för att se en lista över alla inbyggda och anpassade roller.

   Du kan se hur många användare och grupper som är kopplade till varje roll i den här omfattningen.

   ![Lista över roller](./media/role-assignments-portal/roles-list.png)

1. Klicka på en enskild roll om du vill se vem som har tilldelats den här rollen och även visa behörigheter för rollen.

   ![Rolltilldelningar](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Visa rolltilldelningar

Vid hantering av åtkomst vill du veta vem som har åtkomst, deras behörigheter och på vilken nivå behörigheterna gäller. Lista-åtkomst för en användare, grupp, tjänstens huvudnamn eller hanterad identitet du visa rolltilldelningar.

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

1. Klicka på den **rolltilldelningar** fliken (eller klicka på den **visa** knappen på panelen Visa rollen tilldelningar) att visa alla rolltilldelningar i det här omfånget.

   ![Åtkomstkontroll - rollen tilldelningar fliken](./media/role-assignments-portal/access-control-role-assignments.png)

   Du kan se vem som har åtkomst i den här omfattningen på fliken rollen tilldelningar. Observera att vissa roller är begränsade till **den här resursen** medan andra är **(Ärvda)** från ett annat omfång. Åtkomst tilldelas till den här resursen eller ärvs från en tilldelning till den överordnade omfattningen.

## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

I RBAC, om du vill bevilja åtkomst måste tilldela du en roll till en användare, grupp, tjänstens huvudnamn eller hanterad identitet. Följ dessa steg om du vill bevilja åtkomst med olika omfång.

### <a name="assign-a-role-at-a-scope"></a>Tilldela en roll i ett omfång

1. Öppna **åtkomstkontroll (IAM)** i ett omfång, till exempel hanteringsgruppen, prenumeration, resursgrupp eller resurs, där du vill bevilja åtkomst.

1. Klicka på den **rolltilldelningar** fliken för att visa alla rolltilldelningar i det här omfånget.

1. Klicka på **Lägg till rolltilldelning** att öppna fönstret Lägg till rollen tilldelning.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

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

1. Klicka på **Lägg till rolltilldelning** att öppna fönstret Lägg till rollen tilldelning.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

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

1. Klicka på **ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment.png)

1. Ta bort rollen tilldelningsmeddelandet som visas, klicka på **Ja**.

    Ärvda tilldelningar kan inte tas bort. Om du behöver ta bort en ärvd tilldelning så måste du göra det i samma omfång som där rolltilldelningen skapades. I den **omfång** kolumn, bredvid **(ärvt)** det finns en länk som leder till området där den här rollen har tilldelats. Gå till omfånget som visas där om du vill ta bort rolltilldelningen.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Nästa steg

* [Självstudier: Ge en användaråtkomst till Azure-resurser med RBAC och Azure portal](quickstart-assign-role-user-portal.md)
* [Självstudier: Ge en användaråtkomst till Azure-resurser med RBAC och Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Felsöka RBAC för Azure-resurser](troubleshooting.md)
* [Organisera dina resurser med Azure-hanteringsgrupper](../governance/management-groups/index.md)
