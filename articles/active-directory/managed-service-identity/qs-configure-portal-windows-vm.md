---
title: Hur du konfigurerar hanterad tjänstidentitet på en Azure-dator med Azure portal
description: Steg för steg-instruktioner för att konfigurera en hanterad tjänstidentitet på en Azure-dator med Azure portal.
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
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 7ac4dd41c8a1adb422539e0832715d59ec385694
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745567"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Konfigurera en virtuell dator hanterad tjänstidentitet med hjälp av Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och inaktiverar system- och Användartilldelad identitet för en Azure virtuell dator (VM), med hjälp av Azure portal. 

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att utföra vilka hanteringsåtgärder i den här artikeln, måste ditt konto följande rolltilldelningen:
    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) aktivera och ta bort systemtilldelade identiteter från en Azure-dator.

## <a name="system-assigned-identity"></a>Systemtilldelad identitet

I det här avsnittet ska du lära dig hur du aktiverar och inaktiverar systemtilldelad identitet för virtuell dator med Azure portal.

### <a name="enable-system-assigned-identity-during-creation-of-a-vm"></a>Aktivera systemtilldelad identitet under skapandet av en virtuell dator

Azure-portalen stöder för närvarande inte aktivera systemtilldelade identiteter under genereringen av en virtuell dator. I stället se något av följande virtuella datorer skapas Snabbstart artiklar för att först skapa en virtuell dator och gå sedan vidare till nästa avsnitt för information om hur du aktiverar systemtilldelade identiteter på den virtuella datorn:

- [Skapa en Windows-dator med Azure-portalen](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Skapa en Linux-dator med Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

### <a name="enable-system-assigned-identity-on-an-existing-vm"></a>Aktivera systemtilldelad identitet på en befintlig virtuell dator

För att aktivera systemtilldelad identitet på en virtuell dator som ursprungligen etablerades utan den:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn.

2. Navigera till den önskade virtuella datorn och välj ”Configuration”-sidan.

3. Aktivera systemtilldelad identitet på den virtuella datorn genom att välja ”Ja” under ”hanterad tjänstidentitet” och klicka sedan på **spara**. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

   ![Skärmbild av konfiguration av sidan](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-identity-from-a-vm"></a>Ta bort systemtilldelad identitet från en virtuell dator

Om du har en virtuell dator som inte längre behöver systemtilldelade identiteter:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn. 

2. Navigera till den önskade virtuella datorn och välj ”Configuration”-sidan.

3. Inaktivera systemtilldelad identitet på den virtuella datorn genom att välja ”Nej” under ”hanterad tjänstidentitet” och sedan på Spara. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

   ![Skärmbild av konfiguration av sidan](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-identity"></a>Användartilldelad identitet

 I det här avsnittet får lära du att lägga till och ta bort Användartilldelad identitet från en virtuell dator med Azure portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Tilldela Användartilldelad identitet när du skapar en virtuell dator

För närvarande stöder inte Azure-portalen tilldela Användartilldelad identitet när du skapar en virtuell dator. I stället se något av följande virtuella datorer skapas Snabbstart artiklar för att först skapa en virtuell dator och gå sedan vidare till nästa avsnitt för information om hur du tilldelar en Användartilldelad identitet till den virtuella datorn:

- [Skapa en Windows-dator med Azure-portalen](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Skapa en Linux-dator med Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-identity-to-an-existing-vm"></a>Tilldela Användartilldelad identitet till en befintlig virtuell dator

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn.
2. Navigera till den önskade virtuella datorn och klicka på **identitet**, **Användartilldelad** och sedan  **\+Lägg till**.

   ![Lägg till Användartilldelad identitet till en virtuell dator](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klicka på den Användartilldelad identitet som du vill lägga till den virtuella datorn och klicka sedan på **Lägg till**.

    ![Lägg till Användartilldelad identitet till en virtuell dator](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-vm"></a>Ta bort Användartilldelad identitet från en virtuell dator

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn.
2. Navigera till den önskade virtuella datorn och klicka på **identitet**, **Användartilldelad**, namnet på Användartilldelad identitet som du vill ta bort och klicka sedan på **ta bort** (klicka på **Ja** i fönstret bekräftelse).

   ![Ta bort Användartilldelad identitet från en virtuell dator](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="related-content"></a>Relaterat innehåll

- En översikt av hanterad tjänstidentitet finns [översikt](overview.md).

## <a name="next-steps"></a>Nästa steg

- Med hjälp av Azure-portalen ger en Azure-dator hanterad tjänstidentitet [åtkomst till en annan Azure-resurs](howto-assign-access-portal.md).

