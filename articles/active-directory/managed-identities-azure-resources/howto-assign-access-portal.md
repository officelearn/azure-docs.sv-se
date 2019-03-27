---
title: Så här tilldelar du en hanterad identitet åtkomst till en Azure-resurs med hjälp av Azure portal
description: Stegvisa instruktioner för att tilldela en hanterad identitet på en resursåtkomst till en annan resurs med hjälp av Azure portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 958b3d72a3a8df4a3b67f62e7db788d7142ca667
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445474"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Tilldela en hanterad identitet åtkomst till en resurs med hjälp av Azure-portalen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet, kan du ge hanterad identitet-åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Den här artikeln visar hur du ge en Azure virtuell dator eller virtual machine scale Sets hanterad identitet åtkomst till ett Azure storage-konto med hjälp av Azure portal.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använd RBAC för att tilldela en hanterad identitet åtkomst till en annan resurs

När du har aktiverat hanterad identitet på en Azure-resurs, till exempel en [Azure VM](qs-configure-portal-windows-vm.md) eller [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som du har konfigurerat den hanterade identitet.

2. Navigera till önskad resurs som du vill ändra åtkomstkontroll. I det här exemplet ger vi en virtuell Azure-datoråtkomst till ett lagringskonto, så vi navigerar du till lagringskontot.

3. Välj den **åtkomstkontroll (IAM)** i resursen och välj **+ Lägg till rolltilldelning**. Ange sedan den **rollen**, **tilldela åtkomst till**, och ange motsvarande **prenumeration**. Du bör se resursen under området för sökvillkor. Markera resursen och välj **spara**. 

   ![Skärmbild för åtkomst-åtkomstkontroll (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Nästa steg

- [Hanterad identitet för översikt över Azure-resurser](overview.md)
- För att aktivera hanterad identitet på virtuella Azure-datorer, se [konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure portal](qs-configure-portal-windows-vm.md).
- För att aktivera hanterad identitet på en Azure VM-skalningsuppsättning, se [konfigurera hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning med Azure portal](qs-configure-portal-windows-vmss.md).


