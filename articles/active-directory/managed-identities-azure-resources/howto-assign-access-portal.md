---
title: Tilldela en hanterad identitetsåtkomst till en resurs med Hjälp av Azure-portalen - Azure AD
description: Steg-för-steg-instruktioner för tilldelning av en hanterad identitet på en resursåtkomst till en annan resurs med hjälp av Azure-portalen.
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
ms.openlocfilehash: e24c97909870c4d76b07ec837e5f624a509bd1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547289"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Tilldela en hanterad identitetsåtkomst till en resurs med hjälp av Azure-portalen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet kan du ge den hanterade identitetsåtkomsten till en annan resurs, precis som alla säkerhetsobjekt. Den här artikeln visar hur du ger en Azure virtuell dator eller virtuell dator skalningsuppsättningens hanterade identitetsåtkomst till ett Azure-lagringskonto med hjälp av Azure-portalen.

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använda RBAC för att tilldela en hanterad identitetsåtkomst till en annan resurs

När du har aktiverat hanterad identitet på en Azure-resurs, till exempel en [Azure VM](qs-configure-portal-windows-vm.md) eller [Azure VMSS:](qs-configure-portal-windows-vmss.md)

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen under vilken du har konfigurerat den hanterade identiteten.

2. Navigera till önskad resurs som du vill ändra åtkomstkontrollen på. I det här exemplet ger vi en virtuell Azure-dator åtkomst till ett lagringskonto, så vi navigerar till lagringskontot.

3. Välj sidan **Access control (IAM)** för resursen och välj **+ Lägg till rolltilldelning**. Ange sedan **rollen**, **Tilldela åtkomst till**och ange motsvarande **prenumeration**. Under området sökvillkor bör du se resursen. Markera resursen och välj **Spara**. 

   ![Skärmbild av åtkomstkontroll (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identitet för Azure-resurser](overview.md)
- Information om hur du aktiverar hanterad identitet på en virtuell Azure-dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure-portalen](qs-configure-portal-windows-vm.md).
- Information om hur du aktiverar hanterad identitet på en Azure-skalningsuppsättning för virtuella datorer finns i [Konfigurera hanterade identiteter för Azure-resurser på en skaladator för virtuella datorer med Azure-portalen](qs-configure-portal-windows-vmss.md).


