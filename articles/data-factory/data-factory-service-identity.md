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
ms.openlocfilehash: 45699680ad2003c034bce588857f8b102a0b6d26
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121802"
---
# <a name="managed-identity-for-data-factory"></a>Hanterad identitet för Data Factory

Den här artikeln hjälper dig att förstå vad som är hanterad identitet för Data Factory (tidigare kallat Hanterad tjänstidentitet/MSI) och hur det fungerar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

När du skapar en data fabrik kan du skapa en hanterad identitet tillsammans med fabriks skapande. Den hanterade identiteten är ett hanterat program som är registrerat för Azure Active Directory och representerar den specifika data fabriken.

Hanterad identitet för Data Factory fördelar följande funktioner:

- [Lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md), i vilket fall Data Factory-hanterad identitet används för Azure Key Vault autentisering.
- Anslutningar inklusive [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md)och [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Webb aktivitet](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Generera hanterad identitet

Hanterad identitet för Data Factory genereras på följande sätt:

- När du skapar Data Factory via **Azure Portal eller PowerShell**skapas alltid hanterad identitet automatiskt.
- När du skapar Data Factory via **SDK**skapas hanterad identitet endast om du anger "identitet = New FactoryIdentity ()" i objektet Factory för att skapa. Se exempel i [.net snabb start – skapa data fabrik](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- När du skapar Data Factory via **REST API**skapas hanterad identitet endast om du anger avsnittet "identitet" i begär ande texten. Se exempel i [rest-snabb start – skapa data fabrik](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Om du hittar din data fabrik inte har en hanterad identitet som är kopplad till följande [Hämta hanterad identitets](#retrieve-managed-identity) instruktion kan du uttryckligen generera en genom att uppdatera data fabriken med identitets initieraren program mässigt:

- [Generera hanterad identitet med PowerShell](#generate-managed-identity-using-powershell)
- [Generera hanterad identitet med hjälp av REST API](#generate-managed-identity-using-rest-api)
- [Skapa hanterad identitet med hjälp av en Azure Resource Manager mall](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Generera hanterad identitet med hjälp av SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Hanterad identitet kan inte ändras. Att uppdatera en data fabrik som redan har en hanterad identitet har ingen påverkan, den hanterade identiteten sparas oförändrad.
>- Om du uppdaterar en data fabrik som redan har en hanterad identitet utan att ange "ID"-parameter i Factory-objektet eller utan att ange "Identity"-avsnittet i REST-begäran, får du ett fel meddelande.
>- När du tar bort en data fabrik tas den tillhör ande hanterade identiteten bort tillsammans.

### <a name="generate-managed-identity-using-powershell"></a>Generera hanterad identitet med PowerShell

Anropa **set-AzDataFactoryV2** kommandot igen, visas "Identity"-fält som nyligen genereras:

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

### <a name="generate-managed-identity-using-rest-api"></a>Generera hanterad identitet med hjälp av REST API

Anropa nedanstående API med avsnittet "Identity" i begär ande texten:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Brödtext i begäran**: Lägg till "identitet": {"typ": "SystemAssigned"}.

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

**Svar**: hanterad identitet skapas automatiskt och avsnittet "Identity" fylls i.

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Skapa hanterad identitet med hjälp av en Azure Resource Manager mall

**Mall**: Lägg till "identitet": {"typ": "SystemAssigned"}.

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

### <a name="generate-managed-identity-using-sdk"></a>Generera hanterad identitet med hjälp av SDK

Anropa Data Factory-create_or_update funktionen med identitet = New FactoryIdentity (). Exempel kod med .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Hämta hanterad identitet

Du kan hämta den hanterade identiteten från Azure Portal eller program mässigt. I följande avsnitt visas några exempel.

>[!TIP]
> Om du inte ser den hanterade identiteten kan du [Generera en hanterad identitet](#generate-managed-identity) genom att uppdatera fabriken.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Hämta hanterad identitet med hjälp av Azure Portal

Du kan hitta informationen om hanterad identitet från Azure Portal-> Data Factory-> Egenskaper.

- Objekt-ID för hanterad identitet
- Hanterad identitet klient
- ID för hanterad identitets program

Den hanterade identitets informationen visas också när du skapar en länkad tjänst som stöder autentisering med hanterad identitet, till exempel Azure Blob, Azure Data Lake Storage, Azure Key Vault osv.

När du beviljar behörighet använder du objekt-ID eller data fabriks namn (som hanterad identitet) för att hitta den här identiteten.

### <a name="retrieve-managed-identity-using-powershell"></a>Hämta hanterad identitet med PowerShell

Det hanterade identitetens huvud-ID och klient-ID returneras när du hämtar en data fabrik enligt följande. Använd **PrincipalId** för att bevilja åtkomst:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Du kan hämta program-ID genom att kopiera ovanstående huvud-ID, och sedan köra det Azure Active Directory kommandot med huvud-ID som parameter.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Nästa steg
Se följande avsnitt som introducerar när och hur du använder Data Factory-hanterad identitet:

- [Lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopiera data från/till Azure Data Lake Store med hanterade identiteter för Azure-resurser autentisering](connector-azure-data-lake-store.md)

Mer bakgrunds information om hanterade identiteter för Azure-resurser finns i [Översikt över Managed identiteter för Azure](/azure/active-directory/managed-identities-azure-resources/overview) -resurser, vilken Data Factory-hanterad identitet baseras på. 
