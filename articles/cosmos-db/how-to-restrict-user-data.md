---
title: Begränsa användaråtkomst till dataåtgärder endast med Azure Cosmos DB
description: Lär dig hur du begränsar åtkomsten till dataåtgärder endast med Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980379"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Begränsa användaråtkomst till endast dataåtgärder

I Azure Cosmos DB finns det två sätt att autentisera dina interaktioner med databastjänsten:
- använda din Azure Active Directory-identitet när du interagerar med Azure-portalen,
- använda Azure Cosmos [DB-nycklar](secure-access-to-data.md#master-keys) eller [resurstokens](secure-access-to-data.md#resource-tokens) när du utfärdar anrop från API:er och SDK:er.

Varje autentiseringsmetod ger åtkomst till olika uppsättningar ![åtgärder, med viss överlappning: Uppdelning av åtgärder per autentiseringstyp](./media/how-to-restrict-user-data/operations.png)

I vissa fall kanske du vill begränsa vissa användare av organisationen att utföra dataåtgärder (det vill säga CRUD-begäranden och frågor) bara. Detta är vanligtvis fallet för utvecklare som inte behöver skapa eller ta bort resurser, eller ändra det etablerade dataflödet för de behållare som de arbetar med.

Du kan begränsa åtkomsten genom att använda följande steg:
1. Skapa en anpassad Azure Active Directory-roll för de användare som du vill begränsa åtkomsten. Den anpassade Active Directory-rollen bör ha finkornig åtkomstnivå till åtgärder som använder Azure Cosmos DB:s [detaljerade åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb).
1. Tillåt inte körning av icke-dataåtgärder med nycklar. Du kan uppnå detta genom att begränsa dessa åtgärder till Azure Resource Manager-anrop.

I nästa avsnitt i den här artikeln visas hur du utför dessa steg.

> [!NOTE]
> För att kunna köra kommandona i nästa avsnitt måste du installera Azure PowerShell Module 3.0.0 eller senare, samt [Azure-ägarrollen](../role-based-access-control/built-in-roles.md#owner) för prenumerationen som du försöker ändra.

I PowerShell-skripten i nästa avsnitt ersätter du följande platshållare med värden som är specifika för din miljö:
- `$MySubscriptionId`- Prenumerations-ID som innehåller Azure Cosmos-kontot där du vill begränsa behörigheterna. Till exempel: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName`- Resursgruppen som innehåller Azure Cosmos-kontot. Till exempel: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName`- Namnet på ditt Azure Cosmos-konto. Till exempel: `mycosmosdbsaccount`.
- `$MyUserName`- Inloggningenusername@domain( ) för den användare som du vill begränsa åtkomsten till. Till exempel: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Välj din Azure-prenumeration

Azure PowerShell-kommandon kräver att du loggar in och väljer prenumerationen för att köra kommandona:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Skapa den anpassade Azure Active Directory-rollen

Följande skript skapar en Azure Active Directory-rolltilldelning med "Endast nyckel"-åtkomst för Azure Cosmos-konton. Rollen baseras på [anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md) och [detaljerade åtgärder för Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Dessa roller och åtgärder `Microsoft.DocumentDB` är en del av Azure Active Directory-namnområdet.

1. Skapa först ett JSON-dokument med följande `AzureCosmosKeyOnlyAccess.json` innehåll:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Kör följande kommandon för att skapa rolltilldelningen och tilldela den till användaren:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Tillåt inte körning av icke-dataoperationer

Följande kommandon tar bort möjligheten att använda nycklar för att:
- skapa, ändra eller ta bort resurser
- uppdatera behållarinställningar (inklusive indexeringsprinciper, dataflöde osv.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Cosmos DB:s rollbaserade åtkomstkontroll](role-based-access-control.md)
- Få en översikt över [säker åtkomst till data i Cosmos DB](secure-access-to-data.md)
