---
title: "Tilldela en MSI-åtkomst till en Azure-resurs som använder Azure portal"
description: "Stegvisa instruktioner för att tilldela en MSI på en resursåtkomst till en annan resurs med hjälp av Azure portal."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 9120104955aac8ca8a0568e4519c99e1bd786541
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Tilldela en hanterade tjänstidentiteten åtkomst till en resurs med hjälp av Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad tjänst identitet (MSI), kan du ge MSI-åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Den här artikeln visar hur du ge en virtuell Azure-dator eller virtuell dator scale set MSI åtkomst till ett Azure storage-konto med hjälp av Azure portal.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Använda RBAC tilldela MSI-åtkomst till en annan resurs

När du har aktiverat MSI på en Azure-resurs, till exempel en [Azure VM](qs-configure-portal-windows-vm.md) eller [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration som du har konfigurerat MSI.

2. Navigera till en resurs som du vill ändra åtkomstkontroll. I det här exemplet vi ger en virtuell dator i Azure och virtuella Azure-datorn skaluppsättning för åtkomst till ett lagringskonto, så vi gå till lagringskontot.

3. En virtuell Azure-dator, Välj den **åtkomstkontroll (IAM)** i resursen och välj **+ Lägg till**. Ange den **rollen**, **tilldelar åtkomst till virtuella**, och ange motsvarande **prenumeration** och **resursgruppen** där resursen finns. Du bör se resursen under området för sökvillkor. Markera resursen och välj **spara**. 

   ![Skärmbild av Access control (IAM)](../media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   En skaluppsättning för virtuell Azure-dator, Välj den **åtkomstkontroll (IAM)** i resursen och välj **+ Lägg till**. Ange den **rollen**, **bevilja åtkomst till**. Sök efter skaluppsättning för virtuell dator under området för sökvillkor. Markera resursen och välj **spara**.
   
   ![Skärmbild av Access control (IAM)](../media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. Du kommer tillbaka till huvudsakliga **åtkomstkontroll (IAM)** sidan där du ser en ny post för den resursen MSI.

    Virtuella Azure-datorn:

   ![Skärmbild av Access control (IAM)](../media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Skaluppsättning för virtuell Azure-dator:

    ![Skärmbild av Access control (IAM)](../media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>Felsökning

Om MSI-filerna för resursen inte visas i listan över tillgängliga identiteter, kontrollerar du att MSI har aktiverats. I vårt fall kan vi gå tillbaka till den virtuella Azure-datorn och kontrollera följande:

- Titta på den **Configuration** sidan och kontrollera att värdet för **MSI aktiverat** är **Ja**.
- Titta på den **tillägg** sidan och kontrollera att tillägget MSI distribuerats (**tillägg** sidan är inte tillgänglig för en skaluppsättning för virtuell Azure-dator).

Om något är fel kan du behöva distribuera om MSI-filerna på din resurs eller felsöka distributionen misslyckades.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](overview.md).
- För att aktivera MSI på en virtuell Azure-dator, se [konfigurera en Azure VM hanterade tjänsten identitet (MSI) med hjälp av Azure portal](qs-configure-portal-windows-vm.md).
- Om du vill aktivera MSI i en skaluppsättning för virtuell dator i Azure finns [konfigurera ett Azure Virtual Machine skala Ange hanterade tjänsten identitet (MSI) med Azure-portalen](qs-configure-portal-windows-vmss.md)


