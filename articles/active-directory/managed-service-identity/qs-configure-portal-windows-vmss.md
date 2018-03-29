---
title: Konfigurera MSI på en virtuell dator i Azure skaluppsättningen med Azure-portalen
description: Steg för steg-instruktioner för hur du konfigurerar en hanterad tjänst identitet (MSI) på Azure VMSS, med hjälp av Azure portal.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: d9b493203a78aebdfadef15cf53d9cc023bb66f8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="configure-an-azure-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurera en Azure Virtual Machine Scale ange hanteras Service identitet (MSI) med Azure-portalen

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

I den här artikeln du lär dig hur du aktiverar och ta bort MSI för en skaluppsättning för virtuell dator i Azure med Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-azure-virtual-machine-scale-set"></a>Aktivera MSI under skapandet av skaluppsättning för virtuell Azure-dator

Från och med när detta skrivs stöds aktivera MSI under skapandet av en virtuell dator skala i Azure portal inte. I stället finns i följande virtuella Azure-datorn scale set skapa Quickstart artikel först skapa en skaluppsättning för virtuell dator i Azure:

- [Skapa en Virtual Machine Scale Set i Azure-portalen](../../virtual-machine-scale-sets/quick-create-portal.md)  

Gå sedan vidare till nästa avsnitt för mer information om hur du aktiverar MSI på virtuella datorns skaluppsättning.

## <a name="enable-msi-on-an-existing-azure-vmms"></a>Aktivera MSI på en befintlig Azure-VMMS

Om du har en skaluppsättning för virtuell dator som ursprungligen har etablerats utan en MSI:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration som innehåller virtuella datorns skaluppsättning.

2. Navigera till önskade virtuella datorns skaluppsättning.

3. Klicka på den **Configuration** sidan, aktivera MSI på virtuella datorns skaluppsättning genom att välja **Ja** under ”hanterade tjänstidentiteten” Klicka **spara**. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

   ![Konfiguration av sidan skärmbild](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Ta bort MSI från en skaluppsättning för virtuell Azure-dator

Om du har en skaluppsättning för virtuell dator som inte längre behöver en MSI:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration som innehåller virtuella datorns skaluppsättning. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på virtuella datorns skaluppsättning.

2. Navigera till önskade virtuella datorns skaluppsättning.

3. Klicka på den **Configuration** sidan tar du bort MSI från virtuella datorns skaluppsättning genom att välja **Nej** under **hanterade tjänstidentiteten**, klicka på **spara** . Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

   ![Konfiguration av sidan skärmbild](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="next-steps"></a>Nästa steg

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](overview.md).
- Med Azure-portalen, ge en virtuell dator i Azure skaluppsättning MSI [åtkomst till en annan Azure-resurs](howto-assign-access-portal.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.
