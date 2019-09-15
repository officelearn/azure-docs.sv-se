---
title: Använda Azure Key Vault med hanterade program | Microsoft Docs
description: Visar hur du använder åtkomst hemligheter i Azure Key Vault när du distribuerar hanterade program
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: a87066425845a7f1043576a858a361e601ba9cc8
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003410"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Åtkomst Key Vault hemlighet när du distribuerar Azure Managed Applications

När du behöver skicka ett säkert värde (till exempel ett lösen ord) som en parameter under distributionen kan du hämta värdet från en [Azure Key Vault](../key-vault/key-vault-overview.md). Om du vill komma åt Key Vault när du distribuerar hanterade program måste du bevilja åtkomst till tjänstens huvud namn för enhets **resursen** . Tjänsten Managed Applications använder den här identiteten för att köra åtgärder. För att kunna hämta ett värde från en Key Vault under distributionen måste tjänstens huvud namn kunna komma åt Key Vault.

I den här artikeln beskrivs hur du konfigurerar Key Vault att arbeta med hanterade program.

## <a name="enable-template-deployment"></a>Aktivera mall distribution

1. I portalen väljer du din Key Vault.

1. Välj **Åtkomstprinciper**.   

   ![Välj åtkomst principer](./media/key-vault-access/select-access-policies.png)

1. Välj **Click to show advanced access policies** (Klicka för att visa avancerade åtkomstprinciper).

   ![Visa avancerade åtkomst principer](./media/key-vault-access/advanced.png)

1. Välj **Aktivera åtkomst till Azure Resource Manager för mall distribution**. Välj sedan **Spara**.

   ![Aktivera mall distribution](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Lägg till tjänst som deltagare

1. Välj **åtkomstkontroll (IAM)** .

   ![Välj åtkomst kontroll](./media/key-vault-access/access-control.png)

1. Välj **Lägg till rolltilldelning**.

   ![Välj Lägg till](./media/key-vault-access/add-access-control.png)

1. Välj **deltagare** för rollen. Sök efter installations **resurs leverantören** och välj den från de tillgängliga alternativen.

   ![Sök efter Provider](./media/key-vault-access/search-provider.png)

1. Välj **Spara**.

## <a name="reference-key-vault-secret"></a>Referens Key Vault hemlighet

Om du vill skicka en hemlighet från en Key Vault till en mall i det hanterade programmet måste du använda en [länkad mall](../azure-resource-manager/resource-group-linked-templates.md) och referera till Key Vault i parametrarna för den länkade mallen. Ange resurs-ID för Key Vault och namnet på hemligheten.

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>Nästa steg

Du har konfigurerat dina Key Vault så att de är tillgängliga under distributionen av ett hanterat program.

* Information om hur du skickar ett värde från en Key Vault som en mallparameter finns i [använda Azure Key Vault för att skicka ett säkert parameter värde under distributionen](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Exempel på hanterade program finns i [exempel projekt för Azure-hanterade program](sample-projects.md).
* Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.