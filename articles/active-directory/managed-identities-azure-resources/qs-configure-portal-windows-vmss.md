---
title: Så här konfigurerar du hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning
description: Steg för steg-instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning med Azure portal.
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
ms.openlocfilehash: 57f0ec91bd5c72b593d9b28f7d47f691181a6a0f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446342"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Konfigurera hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning med Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln med hjälp av PowerShell, du lära dig hur du utför följande hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning:

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande Azure rollbaserad åtkomstkontroll tilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:

    > [!NOTE]
    > Inga ytterligare Azure AD directory rolltilldelningar krävs.

    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) aktivera och ta bort systemtilldelade hanterad identitet från en skalningsuppsättning för virtuell dator.

## <a name="system-assigned-managed-identity"></a>Systemtilldelade hanterad identitet

I det här avsnittet får lära du dig att aktivera och inaktivera det systemtilldelade hanterad identitet med hjälp av Azure portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterad identitet när du skapar en VM-skalningsuppsättning

För närvarande stöder inte Azure-portalen att aktivera systemtilldelade hanterad identitet när du skapar en VM-skalningsuppsättning. I stället referera till följande uppsättning skapa en virtuell dator skala snabbstartsartikel för att först skapa en skalningsuppsättning för virtuell dator och gå sedan vidare till nästa avsnitt för information om hur du aktiverar systemtilldelade hanterad identitet på en VM-skalningsuppsättning:

- [Skapa en VM-Skalningsuppsättning i Azure portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterad identitet på en befintlig VM-skalningsuppsättning

För att aktivera hanterad systemtilldelad identitet på en skalningsuppsättning för virtuella datorer som ursprungligen etablerades utan den:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.

2. Navigera till önskade virtuella datorns skalningsuppsättning.

3. Under **systemtilldelad**, **Status**väljer **på** och klicka sedan på **spara**:

   ![Skärmbild av konfiguration av sidan](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Ta bort systemtilldelade hanterad identitet från en VM-skalningsuppsättning

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver en automatiskt genererad hanterad identitet:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på virtuella datorns skalningsuppsättning.

2. Navigera till önskade virtuella datorns skalningsuppsättning.

3. Under **systemtilldelad**, **Status**väljer **av** och klicka sedan på **spara**:

   ![Skärmbild av konfiguration av sidan](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får lära du att lägga till och ta bort en hanterad Användartilldelad identitet från en VM-skalningsuppsättning med Azure portal.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en hanterad Användartilldelad identitet när du skapar en VM-skalningsuppsättning

För närvarande stöder inte Azure portal tilldelar du en hanterad Användartilldelad identitet när du skapar en VM-skalningsuppsättning. I stället referera till följande uppsättning skapa en virtuell dator skala snabbstartsartikel för att först skapa en skalningsuppsättning för virtuell dator och gå sedan vidare till nästa avsnitt för information om hur du tilldelar en hanterad Användartilldelad identitet till den:

- [Skapa en VM-Skalningsuppsättning i Azure portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Tilldela en hanterad Användartilldelad identitet till en befintlig VM-skalningsuppsättning

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.
2. Navigera till önskade virtuella datorns skalningsuppsättning och klicka på **identitet**, **Användartilldelad** och sedan  **\+Lägg till**.

   ![Lägg till Användartilldelad identitet i VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Klicka på den Användartilldelad identitet som du vill lägga till virtuella datorns skalningsuppsättning och sedan på **Lägg till**.
   
   ![Lägg till Användartilldelad identitet i VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Ta bort en hanterad Användartilldelad identitet från en VM-skalningsuppsättning

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn.
2. Navigera till önskade virtuella datorns skalningsuppsättning och klicka på **identitet**, **Användartilldelad**, namnet på den Användartilldelad hanterad identitet som du vill ta bort och klicka sedan på **ta bort** (klicka på **Ja** i fönstret bekräftelse).

   ![Ta bort Användartilldelad identitet från en VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Nästa steg

- Med Azure portal, ge en Azure VM-skalningsuppsättningen hanterad identitet [åtkomst till en annan Azure-resurs](howto-assign-access-portal.md).


