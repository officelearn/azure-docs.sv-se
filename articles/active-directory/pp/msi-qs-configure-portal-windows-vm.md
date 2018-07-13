---
title: Hur du konfigurerar MSI på en Azure-dator med Azure portal
description: Steg för steg-instruktioner för att konfigurera en hanterad tjänstidentitet (MSI) på en Azure-dator med Azure portal.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 37710015904c8112e5d2de504ed5b42895ffb809
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610323"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med Azure portal

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och ta bort MSI för en Azure-dator med Azure portal.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Aktivera MSI under skapandet av en Azure-dator

När detta skrivs stöds när du aktiverar MSI under skapandet av en virtuell dator i Azure-portalen inte. I stället finns något av följande virtuella datorer skapas Snabbstart artiklar för att först skapa en virtuell dator:

- [Skapa en Windows-dator med Azure-portalen](~/articles/virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Skapa en Linux-dator med Azure portal](~/articles/virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Gå sedan vidare till nästa avsnitt för information om hur du aktiverar MSI på den virtuella datorn.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Aktivera MSI på en befintlig Azure VM

Om du har en virtuell dator som ursprungligen etablerades utan en MSI:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”virtuell Datordeltagare”.

2. Navigera till den önskade virtuella datorn.

2. Klicka på sidan ”Configuration”, aktivera MSI på den virtuella datorn genom att välja ”Ja” under ”hanterad tjänstidentitet” och sedan på **spara**. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

   ![Skärmbild av konfiguration av sidan](~/articles/active-directory/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Ta bort MSI från en Azure virtuell dator

Om du har en virtuell dator som inte längre behöver en MSI:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”virtuell Datordeltagare”.

2. Navigera till den önskade virtuella datorn.

3. Klicka på sidan ”Configuration”, ta bort MSI från den virtuella datorn genom att välja ”Nej” under ”hanterad tjänstidentitet” och sedan på **spara**. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

   ![Skärmbild av konfiguration av sidan](~/articles/active-directory/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).

## <a name="next-steps"></a>Nästa steg

- Med hjälp av Azure-portalen ger en Azure-dator MSI [åtkomst till en annan Azure-resurs](msi-howto-assign-access-portal.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.
