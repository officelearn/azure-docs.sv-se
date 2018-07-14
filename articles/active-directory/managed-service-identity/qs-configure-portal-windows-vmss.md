---
title: Konfigurera MSI för en Azure VM-skalningsuppsättning med Azure portal
description: Steg för steg-instruktioner för att konfigurera en hanterad tjänstidentitet (MSI) på Azure VMSS, med hjälp av Azure portal.
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
ms.openlocfilehash: 8779600f2c85a8bb309f7b2a8874608170de8877
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035249"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurera en virtuell dator scale Sets hanterad tjänstidentitet (MSI) med Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och inaktiverar systemtilldelad identitet för en VM-skalningsuppsättning med Azure portal. Tilldela och ta bort användartilldelade identiteter från en skalningsuppsättning för virtuella Azure-datorer stöds inte för närvarande via Azure portal.

> [!NOTE]
> Användartilldelad identitet åtgärder stöds för närvarande inte via Azure-portalen. Kolla igen senare.

## <a name="prerequisites"></a>Förutsättningar


- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Hanterad tjänstidentitet under skapandet av en Azure VM-skalningsuppsättning

Skapa en virtuell dator via Azure portal stöder för närvarande inte hanterad tjänstidentitet åtgärder. I stället finns följande Azure-dator scale set skapas snabbstartsartikeln du först skapa en Azure VM-skalningsuppsättning:

- [Skapa en VM-Skalningsuppsättning i Azure portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

Gå sedan vidare till nästa avsnitt för information om hur du aktiverar MSI på virtuella datorns skalningsuppsättning.

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>Aktivera hanterad tjänstidentitet på en befintlig Azure-VMMS

För att aktivera systemtilldelad identitet på en virtuell dator som ursprungligen etablerades utan den:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.

2. Navigera till önskade virtuella datorns skalningsuppsättning.

3. Aktivera systemtilldelad identitet på den virtuella datorn genom att välja ”Ja” under ”hanterad tjänstidentitet” och klicka sedan på **spara**. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

   [![Skärmbild av konfiguration av sidan](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort hanterad tjänstidentitet från en Azure VM-skalningsuppsättning

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver en MSI:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på virtuella datorns skalningsuppsättning.

2. Navigera till önskade virtuella datorns skalningsuppsättning.

3. Inaktivera systemtilldelad identitet på den virtuella datorn genom att välja ”Nej” under ”hanterad tjänstidentitet” och sedan på Spara. Den här åtgärden kan ta 60 sekunder eller mer att slutföra:

   ![Skärmbild av konfiguration av sidan](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>Relaterat innehåll

- En översikt av hanterad tjänstidentitet finns [översikt](overview.md).

## <a name="next-steps"></a>Nästa steg

- Med Azure portal, ge en Azure VM-skalningsuppsättningen MSI [åtkomst till en annan Azure-resurs](howto-assign-access-portal.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.
