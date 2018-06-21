---
title: Azure Data Factory tjänstidentiteten | Microsoft Docs
description: Mer information om data factory tjänstidentiteten i Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: jingwang
ms.openlocfilehash: f4ce76385897c24bd5259d5a39aa1756769fe2aa
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284488"
---
# <a name="azure-data-factory-service-identity"></a>Azure Data Factory tjänstidentitet

Den här artikeln hjälper dig att förstå vad är data factory-tjänstidentitet och hur det fungerar.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [dokumentationen för Data Factory version1](v1/data-factory-introduction.md).

## <a name="overview"></a>Översikt

När du skapar en datafabrik, kan du skapa en tjänstidentitet tillsammans med generering. Tjänstidentiteten är ett hanterat program som är registrerad på Azure aktivitet Directory och representerar den här specifika data factory.

Data factory tjänstidentiteten fördelar följande två funktioner:

- [Spara autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md), i vilket fall data factory tjänstidentiteten används för Azure Key Vault-autentisering.
- [Kopiera data från/till Azure Data Lake Store](connector-azure-data-lake-store.md), i vilket fall data factory tjänstidentiteten kan användas som en av typerna som stöds Data Lake Store-autentisering.

## <a name="generate-service-identity"></a>Generera tjänstidentitet

Data factory tjänstidentiteten skapas på följande sätt:

- När du skapar datafabriken via **Azure-portalen eller PowerShell**, tjänstidentiteten kommer alltid att skapas automatiskt eftersom ADF V2 förhandsversion.
- När du skapar datafabriken via **SDK**, tjänstidentiteten skapas bara om du anger ”identitet = ny FactoryIdentity()” i fabriken objektet för att skapa. Se exemplet i [Snabbstart för .NET - skapa datafabriken](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- När du skapar datafabriken via **REST API**, tjänstidentiteten skapas bara om du anger ”identitetsavsnittet” i begärandetexten. Se exemplet i [REST-Snabbstart - skapa datafabriken](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Om du hittar din data factory inte har en tjänstidentitet associerade följande [hämta tjänstidentiteten](#retrieve-service-identity) anvisning, kan uttryckligen genereras genom att uppdatera data factory med identity initieraren programmässigt:

- [Generera tjänstidentiteten med hjälp av PowerShell](#generate-service-identity-using-powershell)
- [Generera tjänstidentiteten med hjälp av REST API](#generate-service-identity-using-rest-api)
- [Generera tjänstidentiteten med SDK](#generate-service-identity-using-sdk)

>[!NOTE]
>- Tjänstidentiteten kan inte ändras. Uppdatera en datafabrik som redan har en tjänstidentitet inte har någon effekt, tjänstidentiteten hålls oförändrade.
>- Om du uppdaterar en datafabrik som redan har en tjänstidentitet utan att ange ”ID”-parametern i objektet fabriken eller utan att ange ”identitetsavsnittet” i REST-begärandetexten får du ett fel.
>- När du tar bort en datafabrik tas associerade tjänstidentiteten bort tillsammans.

### <a name="generate-service-identity-using-powershell"></a>Generera tjänstidentiteten med hjälp av PowerShell

Anropa **Set AzureRmDataFactoryV2** kommandot på nytt, och sedan ”identitet” fält som nyligen genereras:

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

### <a name="generate-service-identity-using-rest-api"></a>Generera tjänstidentiteten med hjälp av REST API

Anropa nedan API med ”identitetsavsnittet” i frågans brödtext:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**Begäran**: Lägg till ”identitet”: {”typ”: ”SystemAssigned”}.

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

**Svaret**: tjänstidentiteten skapas automatiskt och fylls ”identitetsavsnittet” därefter.

```json
{
    "name": "ADFV2DemoFactory",
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
    "location": "EastUS"
}
```

### <a name="generate-service-identity-using-sdk"></a>Generera tjänstidentiteten med SDK

Anropa funktionen data factory create_or_update med ID = ny FactoryIdentity(). Exempelkod med hjälp av .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Hämta tjänstidentitet

Du kan hämta tjänstidentiteten från Azure-portalen eller programmässigt. Följande avsnitt visar några exempel.

>[!TIP]
> Om du inte ser tjänstidentiteten, [generera tjänstidentiteten](#generate-service-identity) genom att uppdatera din factory.

### <a name="retrieve-service-identity-using-azure-portal"></a>Hämta service med hjälp av Azure portal

Du kan hitta identitetsinformation tjänsten från Azure portal -> din data factory -> Inställningar -> Egenskaper:

- ID FÖR IDENTITET
- SERVICE IDENTITY-KLIENT
- **IDENTITY-ID för programmet** > Kopiera det här värdet

![Hämta tjänstidentitet](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>Hämta service med hjälp av PowerShell

Tjänstidentiteten ägar-ID och klient-ID returneras när du får en specifik datafabrik på följande sätt:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Kopiera ägar-ID och sedan köra under Azure Active Directory-kommando med ägar-ID som parameter för att hämta den **ApplicationId**, som du använder för att bevilja åtkomst:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Nästa steg
Finns i följande avsnitt som inför när och hur du använder data factory tjänstidentiteten:

- [Spara autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopiera data från/till Azure Data Lake Store med hjälp av hanteringstjänster identitetsverifiering](connector-azure-data-lake-store.md)

Se [MSI översikt](~/articles/active-directory/msi-overview.md) för mer bakgrundsinformation om hanterade tjänstidentiteten som data factory tjänstidentiteten baseras på. 