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
ms.openlocfilehash: af17bf716ce22bc7c02d40def36248facb6fbcc4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurera en VMSS hanteras Service identitet (MSI) med Azure-portalen

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du aktiverar och inaktiverar tilldelats identitet för en VMSS med Azure-portalen. Tilldela och ta bort användare som tilldelats identiteter från en Azure-VMSS stöds inte för närvarande via Azure Portal.

> [!NOTE]
> För närvarande stöds inte användaren som har tilldelats identitet åtgärder via Azure Portal. Sök igen efter uppdateringar.

## <a name="prerequisites"></a>Förutsättningar


- Om du är bekant med hanterade tjänstidentiteten kan ta en titt på [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Hanterade tjänstidentiteten under skapandet av en skaluppsättning för virtuell Azure-dator

Skapa en virtuell dator via Azure portal stöder för närvarande inte hanteras tjänstidentiteten åtgärder. I stället finns i följande virtuella Azure-datorn scale set skapa Quickstart artikel först skapa en skaluppsättning för virtuell dator i Azure:

- [Skapa en Virtual Machine Scale Set i Azure-portalen](../../virtual-machine-scale-sets/quick-create-portal.md)  

Gå sedan vidare till nästa avsnitt för mer information om hur du aktiverar MSI på virtuella datorns skaluppsättning.

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>Aktivera hanterade tjänstidentiteten på en befintlig Azure-VMMS

Tilldelad identiteten på en virtuell dator som ursprungligen var etablerad utan den så att systemet:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration som innehåller virtuella datorns skaluppsättning.

2. Navigera till önskade virtuella datorns skaluppsättning.

3. Aktivera som tilldelats Systemidentiteten på den virtuella datorn genom att välja ”Ja” under ”hanterade tjänstidentiteten” och klicka sedan på **spara**. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

   [![Konfiguration av sidan skärmbild](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort hanterade tjänstidentiteten från en skaluppsättning för virtuell Azure-dator

Om du har en skaluppsättning för virtuell dator som inte längre behöver en MSI:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration som innehåller virtuella datorns skaluppsättning. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på virtuella datorns skaluppsättning.

2. Navigera till önskade virtuella datorns skaluppsättning.

3. Inaktivera tilldelats identiteten på den virtuella datorn genom att välja ”Nej” under ”hanterade tjänstidentiteten” och sedan på Spara. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

   ![Konfiguration av sidan skärmbild](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>Relaterat innehåll

- En översikt över hanterade tjänstidentiteten finns [översikt](overview.md).

## <a name="next-steps"></a>Nästa steg

- Med Azure-portalen, ge en virtuell dator i Azure skaluppsättning MSI [åtkomst till en annan Azure-resurs](howto-assign-access-portal.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.
