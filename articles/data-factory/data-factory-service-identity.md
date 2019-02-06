---
title: Azure Data Factory-tjänstidentitet | Microsoft Docs
description: Läs mer om tjänstidentitet för datafabrik i Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: jingwang
ms.openlocfilehash: b0cb72b8a9c1710b2b22d987c2818a490a2ea553
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753367"
---
# <a name="azure-data-factory-service-identity"></a>Azure Data Factory-tjänstidentitet

Den här artikeln hjälper dig att förstå vad är tjänstidentiteten för data factory och hur det fungerar.

## <a name="overview"></a>Översikt

När du skapar en datafabrik, kan du skapa en tjänstidentitet tillsammans med generering av. Tjänstidentiteten är ett hanterat program som är registrerad på Azure Activity Directory och representerar den här specifika data factory.

Tjänstidentitet för datafabrik fördelar följande funktioner:

- [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md), i vilket fall tjänstidentitet för datafabrik används för Azure Key Vault-autentisering.
- Anslutningsappar, som [Azure Blob storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md), och [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Webb-aktivitet](control-flow-web-activity.md).

## <a name="generate-service-identity"></a>Generera tjänstidentitet

Tjänstidentitet för datafabrik skapas på följande sätt:

- När du skapar data factory via **Azure portal eller PowerShell**, tjänstidentitet skapas alltid automatiskt.
- När du skapar data factory via **SDK**, tjänstidentitet skapas endast om du anger ”Identity = ny FactoryIdentity()” i objektet för att skapa fabriken. Se exemplet i [.NET-Snabbstart – skapa data factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- När du skapar data factory via **REST API**, tjänstidentitet skapas endast om du anger ”identitet”-avsnittet i begärandetexten. Se exemplet i [REST-Snabbstart – skapa data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Om du hitta din datafabrik inte har en tjänstidentitet associerade följa [hämta tjänstidentitet](#retrieve-service-identity) instruktioner, du kan uttryckligen Generera en genom att uppdatera data factory programmässigt med identity initieraren:

- [Generera tjänstidentitet med hjälp av PowerShell](#generate-service-identity-using-powershell)
- [Generera tjänstidentitet med hjälp av REST API](#generate-service-identity-using-rest-api)
- [Generera tjänstidentitet med en Azure Resource Manager-mall](#generate-service-identity-using-azure-resource-manager-template)
- [Generera tjänstidentitet med hjälp av SDK](#generate-service-identity-using-sdk)

>[!NOTE]
>- Tjänstidentitet kan inte ändras. Uppdaterar en data factory som redan har en tjänstidentitet inte har någon effekt, tjänstidentiteten förblir oförändrade.
>- Om du uppdaterar en data factory som redan har en tjänstidentitet utan att ange ”identitet”-parametern i objektet factory eller utan att ange ”identitet”-avsnittet i REST-begärandetexten får du ett fel.
>- När du tar bort en data factory tas den associera tjänstidentiteten bort tillsammans.

### <a name="generate-service-identity-using-powershell"></a>Generera tjänstidentitet med hjälp av PowerShell

Anropa **Set-AzureRmDataFactoryV2** kommandot en gång, kan du se ”identitet” fält som nyligen genereras:

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-service-identity-using-rest-api"></a>Generera tjänstidentitet med hjälp av REST API

Anropa nedan API med ”identitet”-avsnittet i begärandetexten:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**Brödtext i begäran**: Lägg till ”identitet”: {”type”: ”SystemAssigned”}.

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

**Svaret**: tjänstidentitet skapas automatiskt och ”identitet” avsnittet har fyllts i enlighet med detta.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
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

### <a name="generate-service-identity-using-an-azure-resource-manager-template"></a>Generera tjänstidentitet med en Azure Resource Manager-mall

**Mallen**: Lägg till ”identitet”: {”type”: ”SystemAssigned”}.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="generate-service-identity-using-sdk"></a>Generera tjänstidentitet med hjälp av SDK

Anropa data factory create_or_update funktion med ID = ny FactoryIdentity(). Exempelkod med hjälp av .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Hämta tjänstidentitet

Du kan hämta tjänstidentiteten från Azure-portalen eller via programmering. I följande avsnitt visas några exempel.

>[!TIP]
> Om du inte ser tjänstidentiteten [generera tjänstidentitet](#generate-service-identity) genom att uppdatera din datafabrik.

### <a name="retrieve-service-identity-using-azure-portal"></a>Hämta tjänstidentitet med hjälp av Azure portal

Du hittar ID-information för tjänsten från Azure portal -> din data factory -> Inställningar -> Egenskaper:

- TJÄNSTIDENTITETS-ID
- TJÄNSTIDENTITETSKLIENT
- **IDENTITETS-ID för programmet** > Kopiera detta värde

![Hämta tjänstidentitet](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>Hämta tjänstidentitet med hjälp av PowerShell

Tjänstidentiteten ägar-ID och klient-ID returneras när du får en specifik datafabrik på följande sätt:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Kopiera ägar-ID och sedan köra Azure Active Directory-kommandot nedan med ägar-ID som parameter för att hämta den **ApplicationId**, som du använder för att bevilja åtkomst:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Nästa steg
Finns i följande avsnitt som introducerar när och hur du använder tjänstidentitet för datafabrik:

- [Store-autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopieringsdata från/till Azure Data Lake Store med hjälp av hanterade identiteter för autentisering av Azure-resurser](connector-azure-data-lake-store.md)

Se [hanterade identiteter för översikt över Azure-resurser](/azure/active-directory/managed-identities-azure-resources/overview) Mer bakgrundsinformation om hanterade identiteter för Azure-resurser som tjänstidentitet för datafabrik baseras på. 
