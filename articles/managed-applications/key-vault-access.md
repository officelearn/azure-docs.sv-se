---
title: Använda Azure Key Vault med hanterade program | Microsoft Docs
description: Visar hur du använder åtkomsthemligheter i Azure Key Vault vid distribution av hanterade program
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 07/11/2018
ms.author: tomfitz
ms.openlocfilehash: f091ba44a3170dcc4141829f2f4105d6e7993cdf
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035297"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Få åtkomst till Key Vault-hemlighet när du distribuerar Azure Managed Applications

När du vill skicka ett säkert värde (t.ex. ett lösenord) som en parameter under distributionen kan du hämta värdet från en [Azure Key Vault](../key-vault/key-vault-whatis.md). För att få åtkomst till Key Vault vid distribution av hanterade program, måste du ge åtkomst till den **installation Resource Provider** tjänstens huvudnamn. Den här artikeln beskriver hur du konfigurerar Key Vault för att arbeta med hanterade program.

## <a name="enable-template-deployment"></a>Aktivera malldistribution

1. Välj ditt Nyckelvalv i portalen.

1. Välj **Åtkomstprinciper**.   

   ![Välj principer för åtkomst](./media/key-vault-access/select-access-policies.png)

1. Välj **Klicka om du vill visa avancerade åtkomstprinciper**.

   ![Visa avancerade åtkomstprinciper](./media/key-vault-access/advanced.png)

1. Välj **Aktivera åtkomst till Azure Resource Manager för malldistribution**. Välj sedan **Spara**.

   ![Aktivera malldistribution](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Lägg till tjänst som deltagare

1. Välj **åtkomstkontroll (IAM)**.

   ![Välj åtkomstkontroll](./media/key-vault-access/access-control.png)

1. Välj **Lägg till**.

   ![Välj Lägg till](./media/key-vault-access/add-access-control.png)

1. Välj **deltagare** för rollen. Sök efter **installation Resource Provider** och välj bland de tillgängliga alternativen.

   ![Sök efter leverantör](./media/key-vault-access/search-provider.png)

1. Välj **Spara**.

## <a name="reference-key-vault-secret"></a>Referens för Key Vault-hemlighet

Om du vill skicka en hemlighet från Key Vault till en mall i ditt hanterade program, måste du använda en [länkad mall](../azure-resource-manager/resource-group-linked-templates.md) och referera till Key Vault i parametrarna för den länkade mallen. Ange resurs-ID för Key Vault och namnet på hemligheten.

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

Du har konfigurerat Key Vault för att vara tillgänglig under distributionen av ett hanterat program.

* Läs om hur skicka ett värde från ett Nyckelvalv som en mallparameter [använda Azure Key Vault för att skicka säkra parametervärdet under distributionen](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Hanterat program exempel finns i [exempelprojekten för Azure hanterade program](sample-projects.md).
* Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.