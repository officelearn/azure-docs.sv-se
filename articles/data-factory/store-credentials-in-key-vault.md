---
title: Lagra autentiseringsuppgifter i Azure Key Vault | Microsoft Docs
description: "Lär dig hur du lagrar autentiseringsuppgifterna för datalager som används i en Azure key vault som Azure Data Factory kan automatiskt hämta vid körning."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 193d7c77f01384106b3e0d932d02ba6cdff9e750
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Spara autentiseringsuppgifter i Azure Key Vault

Du kan lagra autentiseringsuppgifter för datalager i en [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory hämtar autentiseringsuppgifterna vid körning av en aktivitet som använder datalagret. För närvarande endast [Dynamics connector](connector-dynamics-crm-office-365.md) stöder den här funktionen.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [documenttion för Data Factory version1](v1/data-factory-introduction.md).

## <a name="steps"></a>Steg

När du skapar en datafabrik, kan du skapa en tjänstidentitet tillsammans med generering. Tjänstidentiteten är ett hanterat program som är registrerad på Azure aktivitet Directory och representerar den här specifika data factory.

- När du skapar datafabriken via **Azure-portalen eller PowerShell**, tjänstidentiteten kommer alltid att skapas automatiskt eftersom förhandsversion.
- När du skapar datafabriken via **SDK**, tjänstidentiteten skapas bara om du anger ”identitet = ny FactoryIdentity()” i fabriken objektet för att skapa. Se exempel från [Snabbstart för .NET - skapa datafabriken](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- När du skapar datafabriken via **REST API**, tjänstidentiteten skapas bara om du anger ”identitetsavsnittet” i begärandetexten. Se exempel från [REST-Snabbstart - skapa datafabriken](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Om du vill referera en autentiseringsuppgift lagras i Azure Key Vault, måste du:

1. Kopiera ”identitet TJÄNSTPROGRAMMETS ID” skapas tillsammans med din data factory. Referera till [hämta tjänstidentiteten](#retrieve-service-identity).
2. Ge service identitet åtkomst till Azure Key Vault. I nyckelvalvet -> Access control -> Lägg till -> söka efter den här identiteten-ID för programmet för att lägga till läsaren behörighet. Det gör denna avsedda factory åtkomst till hemlighet i nyckelvalvet.
3. Skapa en länkad tjänst som pekar på Azure Key Vault. Referera till [Azure Key Vault länkade tjänsten](#azure-key-vault-linked-service).
4. Skapa data länkade lagringstjänst, i vilken referens motsvarande privata nyckel för lagras i valvet. Referera till [refererar till autentiseringsuppgifter i nyckelvalvet](#reference-credential-stored-in-key-vault).

## <a name="retrieve-service-identity"></a>Hämta tjänstidentitet

Du kan hämta tjänstidentiteten från Azure-portalen eller programmässigt. Följande avsnitt visar några exempel.

>[!TIP]
> Om du inte ser tjänstidentiteten, [generera tjänstidentiteten](#generate-service-identity) genom att uppdatera din factory.

### <a name="using-azure-portal"></a>Använda Azure Portal

Du kan hitta identitetsinformation tjänsten från Azure portal -> din data factory -> Inställningar -> Egenskaper:

- ID FÖR IDENTITET
- SERVICE IDENTITY-KLIENT
- **IDENTITY-ID för programmet** > Kopiera det här värdet om du vill ge åtkomst i Key Vault

![Hämta tjänstidentitet](media/store-credentials-in-key-vault/retrieve-service-identity-portal.png)

### <a name="using-powershell"></a>Använda PowerShell

Tjänstidentiteten ägar-ID och klient-ID returneras när du får en specifik datafabrik på följande sätt:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Kopiera ägar-ID och sedan köra under Azure Active Directory-kommando med ägar-ID som parameter för att hämta den **ApplicationId**, som du använder för att ge åtkomst i Nyckelvalvet:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault länkad tjänst

Följande egenskaper stöds för Azure Key Vault länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **AzureKeyVault**. | Ja |
| BaseUrl | Ange Azure Key Vault-URL. | Ja |

**Exempel:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>Referens-autentiseringsuppgifter som lagras i nyckelvalvet

När du konfigurerar ett fält i den länkade tjänsten refererar till en hemlighet i nyckelvalvet stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type i fältet måste anges till: **AzureKeyVaultSecret**. | Ja |
| secretName | Namnet på hemlighet i azure key vault. | Ja |
| secretVersion | Versionen av hemlighet i azure key vault.<br/>Om den inte anges använder alltid den senaste versionen av hemligheten.<br/>Om anges fastnar den angivna versionen.| Nej |
| lagra | Refererar till en Azure Key Vault länkade tjänst som används för att lagra autentiseringsuppgifter. | Ja |

**Exempel: (se avsnittet ”password”)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="generate-service-identity"></a>Generera tjänstidentitet

Om du hittar din data factory inte har en tjänstidentitet associerade följande [hämta tjänstidentiteten](#retrieve-service-identity) anvisning, kan du generera en genom att uppdatera data factory med identity initieraren programmässigt.

> [!NOTE]
> - **Tjänstidentiteten går inte att ändra**. Uppdatera en datafabrik som redan har en tjänstidentitet inte har någon effekt, tjänstidentiteten sparas oförändrat.
> - **Kan inte tas bort tjänstidentiteten**. Om du uppdaterar en datafabrik som redan har en tjänstidentitet utan att ange ”ID”-parametern i objektet fabriken eller utan att ange ”identitetsavsnittet” i REST-begärandetexten får du ett fel.

Följande avsnitt visar några exempel på Generera tjänstidentiteten för din factory om det inte finns.

### <a name="using-powershell"></a>Använda PowerShell

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

### <a name="using-rest-api"></a>Använda REST-API:et

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

### <a name="using-sdk"></a>Med hjälp av SDK

Anropa funktionen data factory create_or_update med ID = ny FactoryIdentity(). Exempelkod med hjälp av .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).