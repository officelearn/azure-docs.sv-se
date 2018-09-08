---
title: Tilldela MSI-åtkomst till en Azure-resurs, med hjälp av Azure portal
description: Stegvisa instruktioner för att tilldela en MSI åtkomst till en resurs till en annan resurs med hjälp av Azure portal.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: c2048583cde397ac3325fd149982b3a3db475566
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157287"
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Tilldela en hanterad tjänstidentitet åtkomst till en resurs med hjälp av Azure-portalen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad tjänstidentitet (MSI), kan du ge MSI-åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Den här artikeln visar hur du ge en Azure virtuell dator eller virtual machine scale Sets MSI åtkomst till ett Azure storage-konto med hjälp av Azure portal.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Använd RBAC för att tilldela MSI-åtkomst till en annan resurs

När du har aktiverat MSI på en Azure-resurs, till exempel en [Azure VM](qs-configure-portal-windows-vm.md) eller [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som du har konfigurerat MSI.

2. Navigera till önskad resurs som du vill ändra åtkomstkontroll. I det här exemplet vi ger en Azure virtuell dator och Azure VM-skalningsuppsättningen åtkomst till ett lagringskonto, så vi navigerar till storage-kontot.

3. För en Azure-dator, väljer du den **åtkomstkontroll (IAM)** i resursen och välj **+ Lägg till**. Ange sedan den **rollen**, **tilldela åtkomst till virtuell dator**, och ange motsvarande **prenumeration** och **resursgrupp** där resursen finns. Du bör se resursen under området för sökvillkor. Markera resursen och välj **spara**. 

   ![Skärmbild för åtkomst-åtkomstkontroll (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   Azure VM-skalningsuppsättning, Välj den **åtkomstkontroll (IAM)** i resursen och välj **+ Lägg till**. Ange sedan den **rollen**, **tilldela åtkomst till**. Sök efter din skalningsuppsättning för virtuell dator under området för sökvillkor. Markera resursen och välj **spara**.
   
   ![Skärmbild för åtkomst-åtkomstkontroll (IAM)](./media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. Du kommer tillbaka till huvudfönstret **åtkomstkontroll (IAM)** sidan där du ser en ny post för den resursen MSI.

    Azure-dator:

   ![Skärmbild för åtkomst-åtkomstkontroll (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Azure VM-skalningsuppsättning:

    ![Skärmbild för åtkomst-åtkomstkontroll (IAM)](./media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>Felsökning

Om MSI för resursen inte visas i listan över tillgängliga identiteter, kontrollerar du att MSI har aktiverats. I vårt fall kan vi gå tillbaka till Azure-datorer och kontrollera följande:

- Titta på den **Configuration** sidan och se till att värdet för **MSI aktiverat** är **Ja**.
- Titta på den **tillägg** sidan och se till att MSI-tillägget har distribuerats (**tillägg** sidan är inte tillgänglig för en Azure VM-skalningsuppsättning).

Om något är fel, kan du behöva distribuera om MSI på resursen igen eller felsöka distributionsfel.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](overview.md).
- För att aktivera MSI på virtuella Azure-datorer, se [konfigurera en Azure VM hanterad tjänstidentitet (MSI) med Azure portal](qs-configure-portal-windows-vm.md).
- För att aktivera MSI på en Azure VM-skalningsuppsättning, se [konfigurera en Azure virtuell dator skala ange hanterad tjänstidentitet (MSI) med Azure portal](qs-configure-portal-windows-vmss.md)


