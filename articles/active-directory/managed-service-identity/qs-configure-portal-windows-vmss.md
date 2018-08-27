---
title: Konfigurera hanterad tjänstidentitet på en Azure VM-skalningsuppsättning med Azure portal
description: Steg för steg-instruktioner för att konfigurera en hanterad tjänstidentitet på Azure VMSS, med hjälp av Azure portal.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: cbe2e3d9f60ced5c707ce5a701a5aac937ccc072
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887997"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>Konfigurera en virtuell dator scale Sets hanterad tjänstidentitet med hjälp av Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och inaktiverar system- och Användartilldelad identitet för en VM-skalningsuppsättning med Azure portal.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att utföra vilka hanteringsåtgärder i den här artikeln, måste ditt konto följande rolltilldelningen:
    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att aktivera och ta bort systemtilldelad hanterad identitet från en skalningsuppsättning för virtuell dator.

## <a name="system-assigned-identity"></a>Systemtilldelad identitet 

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelad identitet på en VM-skalningsuppsättning med Azure portal.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Aktivera systemtilldelade identiteter under skapandet av en VM-skalningsuppsättning

Azure-portalen stöder för närvarande inte aktivera systemtilldelad identitet när du skapar en VM-skalningsuppsättning. I stället referera till följande VM scale set skapas snabbstartsartikeln att först skapa en skalningsuppsättning för virtuell dator och gå sedan vidare till nästa avsnitt för information om hur du aktiverar systemtilldelad identitet på en VM-skalningsuppsättning:

- [Skapa en VM-Skalningsuppsättning i Azure portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-identity-on-an-existing-virtual-machine-scale-set"></a>Aktivera systemtilldelade identiteter på en befintlig VM-skalningsuppsättning

För att aktivera den systemtilldelade identiteten på en skalningsuppsättning för virtuella datorer som ursprungligen etablerades utan den:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.

2. Navigera till önskade virtuella datorns skalningsuppsättning.

3. Under **systemtilldelad**, **Status**väljer **på** och klicka sedan på **spara**:

   [![Skärmbild av konfiguration av sidan](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

### <a name="remove-system-assigned-identity-from-a-virtual-machine-scale-set"></a>Ta bort systemtilldelade identiteter från en VM-skalningsuppsättning

Om du har en VM-skalningsuppsättning som inte längre behöver en systemtilldelad identitet:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på virtuella datorns skalningsuppsättning.

2. Navigera till önskade virtuella datorns skalningsuppsättning.

3. Under **systemtilldelad**, **Status**väljer **av** och klicka sedan på **spara**:

   ![Skärmbild av konfiguration av sidan](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-identity"></a>Användartilldelad identitet

Du lär dig hur du lägger till och ta bort Användartilldelad identitet från en VM-skalningsuppsättning med Azure portal i det här avsnittet.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en Användartilldelad identitet när du skapar en VM-skalningsuppsättning

För närvarande stöder inte Azure-portalen tilldela Användartilldelad identitet när du skapar en VM-skalningsuppsättning. I stället referera till följande VM scale set skapas snabbstartsartikeln att först skapa en skalningsuppsättning för virtuell dator och gå sedan vidare till nästa avsnitt för information om hur du tilldelar en Användartilldelad identitet till den:

- [Skapa en VM-Skalningsuppsättning i Azure portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-identity-to-an-existing-virtual-machine-scale-set"></a>Tilldela en Användartilldelad identitet till en befintlig VM-skalningsuppsättning

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.
2. Navigera till önskade virtuella datorns skalningsuppsättning och klicka på **identitet**, **Användartilldelad** och sedan  **\+Lägg till**.

   ![Lägg till Användartilldelad identitet i VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Klicka på den Användartilldelad identitet som du vill lägga till virtuella datorns skalningsuppsättning och sedan på **Lägg till**.
   
   ![Lägg till Användartilldelad identitet i VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Ta bort en Användartilldelad identitet från en VM-skalningsuppsättning

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn.
2. Navigera till önskade virtuella datorns skalningsuppsättning och klicka på **identitet**, **Användartilldelad**, namnet på Användartilldelad identitet som du vill ta bort och klicka sedan på **ta bort** () Klicka på **Ja** i fönstret bekräftelse).

   ![Ta bort Användartilldelad identitet från en VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="related-content"></a>Relaterat innehåll

- En översikt av hanterad tjänstidentitet finns [översikt](overview.md).

## <a name="next-steps"></a>Nästa steg

- Med Azure portal, ge en Azure VM-skalningsuppsättningen hanterad tjänstidentitet [åtkomst till en annan Azure-resurs](howto-assign-access-portal.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.
