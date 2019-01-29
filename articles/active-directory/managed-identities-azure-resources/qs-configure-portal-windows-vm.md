---
title: Så här konfigurerar du hanterade identiteter för Azure-resurser på en Azure virtuell dator med Azure portal
description: Steg för steg-instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en Azure virtuell dator med Azure portal.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/10/2018
ms.author: priyamo
ms.openlocfilehash: e7b20c092a0655c1453daaa67d730a7b49a6330a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181613"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure-portalen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och inaktiverar system- och användartilldelade hanterade identiteter för en Azure virtuell dator (VM), med hjälp av Azure portal. 

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="system-assigned-managed-identity"></a>Systemtilldelade hanterad identitet

I det här avsnittet ska du lära dig hur du aktiverar och inaktiverar systemtilldelade hanterad identitet för virtuell dator med Azure portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Aktivera systemtilldelade hanterad identitet under skapandet av en virtuell dator

Om du vill aktivera systemtilldelade hanterad identitet på en virtuell dator när skapandet, ditt konto måste den [virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) rolltilldelning.  Inga ytterligare Azure AD directory rolltilldelningar krävs.

- Under den **Management** fliken i den **identitet** avsnittet, växla **hanterad tjänstidentitet** till **på**.  

![Aktivera systemtilldelade identiteter under skapandet av VM](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Se följande Snabbstarter för att skapa en virtuell dator: 

- [Skapa en Windows-dator med Azure-portalen](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Skapa en Linux-dator med Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Aktivera systemtilldelade hanterad identitet på en befintlig virtuell dator

Om du vill aktivera systemtilldelade hanterad identitet på en virtuell dator som ursprungligen etablerades utan att det behöver ditt konto måste den [virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) rolltilldelning.  Inga ytterligare Azure AD directory rolltilldelningar krävs.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn.

2. Navigera till den önskade virtuella datorn och välj **identitet**.

3. Under **systemtilldelad**, **Status**väljer **på** och klicka sedan på **spara**:

   ![Skärmbild av konfiguration av sidan](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Ta bort systemtilldelade hanterad identitet från en virtuell dator

Om du vill ta bort systemtilldelade hanterad identitet från en virtuell dator, ditt konto måste den [virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) rolltilldelning.  Inga ytterligare Azure AD directory rolltilldelningar krävs.

Om du har en virtuell dator som inte längre behöver systemtilldelade hanterad identitet:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn. 

2. Navigera till den önskade virtuella datorn och välj **identitet**.

3. Under **systemtilldelad**, **Status**väljer **av** och klicka sedan på **spara**:

   ![Skärmbild av konfiguration av sidan](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Användartilldelade hanterad identitet

 Lär dig att lägga till och ta bort en hanterad Användartilldelad identitet från en virtuell dator med Azure-portalen i det här avsnittet.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Tilldela en Användartilldelad identitet när du skapar en virtuell dator

Om du vill tilldela en Användartilldelad identitet till en virtuell dator, ditt konto måste den [virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) rolltilldelningar. Inga ytterligare Azure AD directory rolltilldelningar krävs.

För närvarande stöder inte Azure portal tilldelar du en hanterad Användartilldelad identitet när du skapar en virtuell dator. I stället se något av följande virtuella datorer skapas Snabbstart artiklar för att först skapa en virtuell dator och gå sedan vidare till nästa avsnitt för information om hur du tilldelar en hanterad Användartilldelad identitet till den virtuella datorn:

- [Skapa en Windows-dator med Azure-portalen](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Skapa en Linux-dator med Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Tilldela en hanterad Användartilldelad identitet till en befintlig virtuell dator

Om du vill tilldela en Användartilldelad identitet till en virtuell dator, ditt konto måste den [virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) rolltilldelningar. Inga ytterligare Azure AD directory rolltilldelningar krävs.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn.
2. Navigera till den önskade virtuella datorn och klicka på **identitet**, **Användartilldelad** och sedan  **\+Lägg till**.

   ![Lägg till användartilldelade hanterad identitet till en virtuell dator](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klicka på den Användartilldelad identitet som du vill lägga till den virtuella datorn och klicka sedan på **Lägg till**.

    ![Lägg till användartilldelade hanterad identitet till en virtuell dator](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Ta bort en hanterad Användartilldelad identitet från en virtuell dator

Om du vill ta bort en Användartilldelad identitet från en virtuell dator, ditt konto måste den [virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) rolltilldelning. Inga ytterligare Azure AD directory rolltilldelningar krävs.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn.
2. Navigera till den önskade virtuella datorn och klicka på **identitet**, **Användartilldelad**, namnet på den Användartilldelad hanterad identitet som du vill ta bort och klicka sedan på **ta bort** (klicka på  **Ja** i fönstret bekräftelse).

   ![Ta bort Användartilldelad hanterad identitet från en virtuell dator](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Nästa steg

- Med Azure portal, ge en Azure-dator hanterad identitet [åtkomst till en annan Azure-resurs](howto-assign-access-portal.md).

