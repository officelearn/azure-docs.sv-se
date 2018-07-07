---
title: Hur du konfigurerar MSI på en Azure-dator med Azure portal
description: Steg för steg-instruktioner för att konfigurera en hanterad tjänstidentitet (MSI) på en Azure-dator med Azure portal.
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
ms.openlocfilehash: 9d9c18d576f3975d4c8272efff7161366f3f53fe
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901365"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och inaktiverar systemtilldelad identitet för en Azure-dator med Azure portal. Tilldela och ta bort användartilldelade identiteter från virtuella Azure-datorer stöds inte för närvarande via Azure-portalen.

> [!NOTE]
> Användartilldelad identitet åtgärder stöds för närvarande inte via Azure-portalen. Kolla igen senare. 

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Hanterad tjänstidentitet under skapandet av en Azure-dator

Skapa en virtuell dator via Azure portal stöder för närvarande inte hanterad tjänstidentitet åtgärder. I stället finns något av följande virtuella datorer skapas Snabbstart artiklar för att först skapa en virtuell dator:

- [Skapa en Windows-dator med Azure-portalen](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Skapa en Linux-dator med Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Gå sedan vidare till nästa avsnitt för information om hur du aktiverar hanterad tjänstidentitet på den virtuella datorn.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Aktivera hanterad tjänstidentitet på en befintlig Azure VM

För att aktivera systemtilldelad identitet på en virtuell dator som ursprungligen etablerades utan den:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”virtuell Datordeltagare”.

2. Navigera till den önskade virtuella datorn och välj ”Configuration”-sidan.

3. Aktivera systemtilldelad identitet på den virtuella datorn genom att välja ”Ja” under ”hanterad tjänstidentitet” och klicka sedan på **spara**. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

    > [!NOTE]
    > Att lägga till en Användartilldelad identitet till en virtuell dator stöds inte för närvarande via Azure-portalen.

   ![Skärmbild av konfiguration av sidan](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Ta bort hanterade tjänstidentiteter från en Azure virtuell dator

Om du har en virtuell dator som inte längre behöver systemtilldelade identiteter:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”virtuell Datordeltagare”.

2. Navigera till den önskade virtuella datorn och välj ”Configuration”-sidan.

3. Inaktivera systemtilldelad identitet på den virtuella datorn genom att välja ”Nej” under ”hanterad tjänstidentitet” och sedan på Spara. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

    > [!NOTE]
    > Att lägga till en Användartilldelad identitet till en virtuell dator stöds inte för närvarande via Azure-portalen.

   ![Skärmbild av konfiguration av sidan](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Relaterat innehåll

- En översikt av hanterad tjänstidentitet finns [översikt](overview.md).

## <a name="next-steps"></a>Nästa steg

- Med hjälp av Azure-portalen ger en Azure-dator MSI [åtkomst till en annan Azure-resurs](howto-assign-access-portal.md).

