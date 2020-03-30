---
title: Konfigurera hanterade identiteter med Azure-portalen - Azure AD
description: Steg för steg instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en Azure VM med hjälp av Azure-portalen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75971dbd35a6b29306bc87988423ab662a343705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266668"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure-portalen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du aktiverar och inaktiverar system- och användartilldelade hanterade identiteter för en virtuell Azure-dator (VM) med hjälp av Azure-portalen. 

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar den systemtilldelade hanterade identiteten för virtuell dator med hjälp av Azure-portalen.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Aktivera systemtilldelad hanterad identitet när en virtuell dator skapas

För att aktivera systemtilldelade hanterade identitet på en virtuell dator när det skapas behöver ditt konto rolltilldelningen för deltagare i [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

- Växla **Hanterad tjänstidentitet** till **På**under fliken **Hantering** i avsnittet **Identitet.**  

![Aktivera systemtilldelad identitet när den virtuella datorn skapas](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Se följande snabbstarter för att skapa en virtuell dator: 

- [Skapa en virtuell Windows-dator med Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Skapa en virtuell Linux-dator med Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Aktivera systemtilldelad hanterad identitet på en befintlig virtuell dator

För att aktivera systemtilldelade hanterade identitet på en virtuell dator som ursprungligen etablerades utan den, behöver ditt konto rolltilldelningen för deltagare i [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen som innehåller den virtuella datorn.

2. Navigera till önskad virtuell dator och välj **Identitet**.

3. Under **Tilldelad system**väljer du **På** **och**klickar sedan på **Spara:**

   ![Skärmbild av konfigurationssidan](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Ta bort systemtilldelade hanterade identitet från en virtuell dator

Om du vill ta bort den systemtilldelade hanterade identiteten från en virtuell dator behöver ditt konto rolltilldelningen för deltagare för [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

Om du har en virtuell dator som inte längre behöver systemtilldelade hanterade identitet:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen som innehåller den virtuella datorn. 

2. Navigera till önskad virtuell dator och välj **Identitet**.

3. Under **System tilldelad**, **Status**väljer du **Av** och klickar sedan på **Spara:**

   ![Skärmbild av konfigurationssidan](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

 I det här avsnittet får du lära dig hur du lägger till och tar bort en användartilldelad hanterad identitet från en virtuell dator med Azure-portalen.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Tilldela en användartilldelad identitet när en virtuell dator skapas

Om du vill tilldela en användartilldelning till en virtuell dator behöver ditt konto [rolltilldelningarna för deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitetsoperatör.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

Azure-portalen stöder för närvarande inte tilldelning av en användartilldelad hanterad identitet när en virtuell dator skapas. I stället hänvisas till en av följande snabbstartsartiklar för att skapa en virtuell dator för att först skapa en virtuell dator och gå sedan vidare till nästa avsnitt för information om hur du tilldelar en användartilldelade hanterad identitet till den virtuella datorn:

- [Skapa en virtuell Windows-dator med Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Skapa en virtuell Linux-dator med Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Tilldela en användartilldelad hanterad identitet till en befintlig virtuell dator

Om du vill tilldela en användartilldelning till en virtuell dator behöver ditt konto [rolltilldelningarna för deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitetsoperatör.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen som innehåller den virtuella datorn.
2. Navigera till önskad virtuell dator och klicka på **Identitet**, **Användare tilldelad** och lägg sedan ** \+till**.

   ![Lägga till användartilldelade hanterade identitet till vm](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klicka på den användartilldelade identitet som du vill lägga till i den virtuella datorn och klicka sedan på **Lägg till**.

    ![Lägga till användartilldelade hanterade identitet till vm](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Ta bort en användartilldelad hanterad identitet från en virtuell dator

Om du vill ta bort en användartilldelningsidentitet från en virtuell dator behöver ditt konto rolltilldelningen för deltagare för [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen som innehåller den virtuella datorn.
2. Navigera till önskad virtuell dator och klicka på **Identitet**, **Användare tilldelad**, namnet på den användartilldelade hanterade identitet som du vill ta bort och klicka sedan på **Ta bort** (klicka på **Ja** i bekräftelsefönstret).

   ![Ta bort användartilldelade hanterade identitet från en virtuell dator](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Nästa steg

- Med Azure-portalen ger du en Azure VM-hanterad [identitetsåtkomst till en annan Azure-resurs](howto-assign-access-portal.md).

