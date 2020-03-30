---
title: Konfigurera hanterade identiteter på Azure VMSS - Azure AD
description: Steg för steg instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en virtuell dator skala uppsättning med hjälp av Azure-portalen.
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
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32d8944b55794412eb322697469933030e2fbb56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184044"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Konfigurera hanterade identiteter för Azure-resurser på en skalningsuppsättning för virtuella datorer med Azure-portalen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln, med PowerShell, lär du dig hur du utför följande hanterade identiteter för Azure-resurser åtgärder på en virtuell datorskalauppsättning:

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att utföra hanteringsåtgärderna i den här artikeln behöver ditt konto följande Azure-rollbaserade åtkomstkontrolltilldelningar:

    > [!NOTE]
    > Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

    - [Deltagare i virtuella](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) datorer för att aktivera och ta bort den systemtilldelade hanterade identiteten från en skaluppsättning för virtuella datorer.

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar den systemtilldelade hanterade identiteten med Hjälp av Azure-portalen.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterade identiteter när en skaluppsättning för virtuella datorer skapas

Azure-portalen stöder för närvarande inte att aktivera systemtilldelade hanterade identiteter under skapandet av en skalningsuppsättning för virtuella datorer. I stället hänvisas till följande virtual machine scale set creation Quickstart-artikel för att först skapa en skaluppsättning för virtuella datorer och fortsätt sedan till nästa avsnitt för information om hur du aktiverar systemtilldelade hanterade identitet på en skalningsuppsättning för virtuella datorer:

- [Skapa en skalningsuppsättning för virtuella datorer i Azure-portalen](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterade identitet på en befintlig skaluppsättning för virtuella datorer

Så här aktiverar du den systemtilldelade hanterade identiteten på en skaluppsättning för virtuella datorer som ursprungligen etablerades utan den:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är associerat med Azure-prenumerationen som innehåller skalningsuppsättningen för den virtuella datorn.

2. Navigera till önskad skalningsuppsättning för virtuella datorer.

3. Under **Tilldelad system**väljer du **På** **och**klickar sedan på **Spara:**

   ![Skärmbild av konfigurationssidan](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Ta bort systemtilldelade hanterade identitet från en skaluppsättning för virtuella datorer

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver en systemtilldelad hanterad identitet:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är associerat med Azure-prenumerationen som innehåller skalningsuppsättningen för den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet för skalningsuppsättningen för den virtuella datorn.

2. Navigera till önskad skalningsuppsättning för virtuella datorer.

3. Under **System tilldelad**, **Status**väljer du **Av** och klickar sedan på **Spara:**

   ![Skärmbild av konfigurationssidan](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort en användartilldelad hanterad identitet från en skalningsuppsättning för virtuella datorer med Hjälp av Azure-portalen.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en användartilldelad hanterad identitet när en skaluppsättning för virtuella datorer skapas

Azure-portalen stöder för närvarande inte tilldelning av en användartilldelad hanterad identitet när en skalningsuppsättning för virtuella datorer skapas. I stället hänvisa till följande virtual machine scale set creation Quickstart-artikel för att först skapa en skalningsuppsättning för virtuella datorer och fortsätt sedan till nästa avsnitt för information om hur du tilldelar en användartilldelade hanterad identitet till den:

- [Skapa en skalningsuppsättning för virtuella datorer i Azure-portalen](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Tilldela en användartilldelad hanterad identitet till en befintlig skaluppsättning för virtuella datorer

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är associerat med Azure-prenumerationen som innehåller skalningsuppsättningen för den virtuella datorn.
2. Navigera till önskad skalauppsättning för virtuella datorer och klicka på **Identitet**, **Användare tilldelad** och lägg sedan ** \+till**.

   ![Lägga till användartilldelade identitet i VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Klicka på den användartilldelade identitet som du vill lägga till i skaluppsättningen för den virtuella datorn och klicka sedan på **Lägg till**.
   
   ![Lägga till användartilldelade identitet i VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Ta bort en användartilldelad hanterad identitet från en skaluppsättning för virtuella datorer

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen som innehåller den virtuella datorn.
2. Navigera till önskad skaluppsättning för virtuella datorer och klicka på **Identitet**, **Användare tilldelad,** namnet på den användartilldelade hanterade identitet som du vill ta bort och klicka sedan på **Ta bort** (klicka på **Ja** i bekräftelsefönstret).

   ![Ta bort användartilldelade identitet från en VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Nästa steg

- Med Hjälp av Azure-portalen kan du ge en Azure-skala för virtuell dator skala uppsättning hanterad [identitetsåtkomst till en annan Azure-resurs](howto-assign-access-portal.md).


