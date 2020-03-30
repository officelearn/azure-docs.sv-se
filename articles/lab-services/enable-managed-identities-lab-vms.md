---
title: Aktivera hanterade identiteter på dina virtuella labbdamer i Azure DevTest Labs
description: Den här artikeln visar hur en labbägare kan aktivera användartilldelade hanterade identiteter på dina virtuella labbdatorer.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692672"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Aktivera användartilldelade hanterade identiteter på virtuella labbdatorer i Azure DevTest Labs
Som labbägare kan du aktivera användartilldelade hanterade identiteter på dina virtuella labbdatorer (VMs) i Azure DevTest Labs.

En hanterad identitet kan användas för att autentisera till alla tjänster som stöder Azure Active Directory (AD) autentisering, inklusive Key Vault, utan att skicka några autentiseringsuppgifter i koden. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md).

Med den här funktionen kan labbanvändare dela Azure-resurser som Azure SQL Database i samband med labbet. Autentiseringen till resursen tas om hand av själva identiteten. När du har konfigurerats aktiveras alla befintliga/nyskapade labbdasss med den här identiteten. Labbanvändare kan komma åt resurser när de har loggat in på sina datorer.

> [!NOTE]
> Du kan lägga till flera användartilldelade hanterade identiteter som ska aktiveras på dina virtuella labb-datorer.

## <a name="use-azure-portal"></a>Använda Azure-portalen
Så här lägger du till en användare som tilldelats hanterad identitet för virtuella labb-datorer:

1. [Skapa en användartilldelad hanterad identitet i din prenumeration](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Navigera till sidan **Konfiguration och principer** för ditt labb.
2. Välj **Identitet (förhandsgranskning)** på den vänstra menyn.
3. Välj fliken **Virtuell dator.**
4. Välj **Lägg till** om du vill välja en befintlig identitet i en ikonberad listruta. 

    ![Knappen Lägg till identitet](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Välj en befintlig **användarhanterad identitet** i listan nedsläpp och välj **OK**. 

    ![Lägg till identitet](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Använda API

1.  När du har skapat en identitet noterar du identitetens resurs-ID. Det bör se ut som följande exempel: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Kör en PUT HTTPS-metod för att lägga till en ny **ServiceRunner-resurs** i labbet som visas i följande exempel. 

    Servicerunner-resurs är en proxyresurs för att hantera och styra hanterade identiteter i DevTest Labs. Tjänstlöparens namn kan vara valfritt giltigt namn, men vi rekommenderar att du använder namnet på den hanterade identitetsresursen.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md).







