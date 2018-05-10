---
title: Hur du konfigurerar MSI på en virtuell Azure-dator med hjälp av Azure portal
description: Steg för steg-instruktioner för hur du konfigurerar en hanterad tjänst identitet (MSI) på en Azure VM som använder Azure portal.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 62b8504f5c10f338539d263bb231cf96eb405ba6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurera en virtuell dator hanteras Service identitet (MSI) med Azure-portalen

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du aktiverar och inaktiverar tilldelats identitet för en Azure-dator med hjälp av Azure portal. Tilldela och ta bort användare som tilldelats identiteter från Azure virtuella datorer stöds inte för närvarande via Azure Portal.

> [!NOTE]
> För närvarande stöds inte användaren som har tilldelats identitet åtgärder via Azure Portal. Sök igen efter uppdateringar. 

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade tjänstidentiteten kan ta en titt på [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Hanterade tjänstidentiteten under skapandet av en Azure VM

Skapa en virtuell dator via Azure portal stöder för närvarande inte hanteras tjänstidentiteten åtgärder. I stället finns på någon av följande VM skapa Quickstart artiklar först skapa en virtuell dator:

- [Skapa en Windows-dator med Azure-portalen](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Skapa en virtuell Linux-dator med Azure-portalen](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Gå sedan vidare till nästa avsnitt för mer information om hur du aktiverar hanterade tjänstidentiteten på den virtuella datorn.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Aktivera hanterade tjänstidentiteten på en befintlig virtuell Azure-dator

Tilldelad identiteten på en virtuell dator som ursprungligen var etablerad utan den så att systemet:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”.

2. Navigera till den önskade virtuella datorn och välj sidan ”Configuration”.

3. Aktivera som tilldelats Systemidentiteten på den virtuella datorn genom att välja ”Ja” under ”hanterade tjänstidentiteten” och klicka sedan på **spara**. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

    > [!NOTE]
    > Lägga till en identitet för användaren som har tilldelats till en virtuell dator stöds inte för närvarande via Azure Portal.

   ![Konfiguration av sidan skärmbild](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Ta bort hanterade tjänstidentiteten från en virtuell dator i Azure

Om du har en virtuell dator som inte längre behöver systemidentitet tilldelade:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”.

2. Navigera till den önskade virtuella datorn och välj sidan ”Configuration”.

3. Inaktivera tilldelats identiteten på den virtuella datorn genom att välja ”Nej” under ”hanterade tjänstidentiteten” och sedan på Spara. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

    > [!NOTE]
    > Lägga till en identitet för användaren som har tilldelats till en virtuell dator stöds inte för närvarande via Azure Portal.

   ![Konfiguration av sidan skärmbild](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Relaterat innehåll

- En översikt över hanterade tjänstidentiteten finns [översikt](overview.md).

## <a name="next-steps"></a>Nästa steg

- Använder Azure-portalen ger en Azure VM MSI [åtkomst till en annan Azure-resurs](howto-assign-access-portal.md).

