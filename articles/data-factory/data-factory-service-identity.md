---
title: Hanterad identitet för Data Factory
description: Lär dig mer om hanterad identitet för Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: d47450f3252074d3bae8df97766bf8858fca5972
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416583"
---
# <a name="managed-identity-for-data-factory"></a>Hanterad identitet för Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln hjälper dig att förstå vad som hanteras identitet för Data Factory (tidigare känd som Managed Service Identity/MSI) och hur den fungerar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

När du skapar en datafabrik kan en hanterad identitet skapas tillsammans med fabriksskapande. Den hanterade identiteten är ett hanterat program som är registrerat i Azure Active Directory och representerar den här specifika datafabriken.

Hanterade identitet för Data Factory gynnar följande funktioner:

- [Lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md), i vilket fall data fabrikshanterad identitet används för Azure Key Vault-autentisering.
- Kopplingar inklusive [Azure Blob storage](connector-azure-blob-storage.md), Azure Data Lake Storage [Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md)Azure SQL [Database](connector-azure-sql-database.md)och Azure SQL [Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Webbaktivitet](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Generera hanterad identitet

Hanterad identitet för Data Factory genereras på följande sätt:

- När du skapar datafabrik via **Azure Portal eller PowerShell**skapas alltid hanterad identitet automatiskt.
- När du skapar datafabrik via **SDK**skapas den hanterade identiteten endast om du anger "Identity = new FactoryIdentity()" i fabriksobjektet för att skapas. Se exempel i [.NET quickstart - skapa datafabrik](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- När du skapar datafabrik via **REST API**skapas hanterad identitet endast om du anger "identitet" i förfråsättningstexten. Se exempel i [REST-snabbstart - skapa datafabrik](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Om du hittar din datafabrik inte har en hanterad identitet associerad efter [hämta hanterad](#retrieve-managed-identity) identitetsinstruktion, kan du uttryckligen generera en genom att uppdatera datafabriken med identitetsinitierare programmässigt:

- [Generera hanterad identitet med PowerShell](#generate-managed-identity-using-powershell)
- [Generera hanterad identitet med REST API](#generate-managed-identity-using-rest-api)
- [Generera hanterad identitet med hjälp av en Azure Resource Manager-mall](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Generera hanterad identitet med SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Det går inte att ändra den hanterade identiteten. Att uppdatera en datafabrik som redan har en hanterad identitet påverkar inte, den hanterade identiteten hålls oförändrad.
>- Om du uppdaterar en datafabrik som redan har en hanterad identitet utan att ange "identitetsparameter" i fabriksobjektet eller utan att ange avsnittet "identitet" i rest-begäran får du ett felmeddelande.
>- När du tar bort en datafabrik tas den associerade hanterade identiteten bort tillsammans.

### <a name="generate-managed-identity-using-powershell"></a>Generera hanterad identitet med PowerShell

Kommandot Ring **Set-AzDataFactoryV2** igen och ser sedan "Identitet" fält som nyligen genereras:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Generera hanterad identitet med REST API

Anrop nedan API med "identitet" avsnitt i begäran kroppen:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Begärandetext**: lägg till "identitet": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Svar:** hanterad identitet skapas automatiskt och avsnittet "identitet" fylls i i enlighet med detta.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Generera hanterad identitet med hjälp av en Azure Resource Manager-mall

**Mall**: lägg till "identitet": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Generera hanterad identitet med SDK

Anropa funktionen datafabrik create_or_update med Identity=new FactoryIdentity(). Exempelkod med .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Hämta hanterad identitet

Du kan hämta den hanterade identiteten från Azure-portalen eller programmässigt. Följande avsnitt visar några exempel.

>[!TIP]
> Om du inte ser den hanterade identiteten [genererar du den hanterade identiteten](#generate-managed-identity) genom att uppdatera fabriken.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Hämta hanterad identitet med Azure-portal

Du hittar den hanterade identitetsinformationen från Azure-portalen -> din datafabrik -> Egenskaper.

- Id för hanterat identitetsobjekt
- Hanterad identitetsklient
- ID för hanterad identitetsprogram

Den hanterade identitetsinformationen visas också när du skapar länkad tjänst som stöder autentisering av hanterad identitet, till exempel Azure Blob, Azure Data Lake Storage, Azure Key Vault osv.

När du beviljar behörighet använder du objekt-ID eller datafabriksnamn (som hanterat identitetsnamn) för att hitta den här identiteten.

### <a name="retrieve-managed-identity-using-powershell"></a>Hämta hanterad identitet med PowerShell

Det hanterade identitetshuvudet och klient-ID:n returneras när du får en specifik datafabrik enligt följande. Använd **PrincipalId** för att bevilja åtkomst:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Du kan hämta program-ID:t genom att kopiera ovanstående huvud-ID och sedan köra under Azure Active Directory-kommando med huvud-ID som parameter.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Nästa steg
Se följande avsnitt som introducerar när och hur du använder datafabrikens hanterade identitet:

- [Lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopiera data från/till Azure Data Lake Store med hanterade identiteter för Azure-resursautentisering](connector-azure-data-lake-store.md)

Se [Översikt över hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/overview) för mer bakgrund om hanterade identiteter för Azure-resurser, som datafabrikens hanterade identitet baseras på. 
