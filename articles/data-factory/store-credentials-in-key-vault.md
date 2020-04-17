---
title: Spara autentiseringsuppgifter i Azure Key Vault
description: Lär dig hur du lagrar autentiseringsuppgifter för datalager som används i ett Azure-nyckelvalv som Azure Data Factory automatiskt kan hämta under körning.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 22ab4433d84db926733fd0b18035875e63322dda
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451694"
---
# <a name="store-credential-in-azure-key-vault"></a>Lagra autentiseringsuppgifter i Azure Key Vault

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Du kan lagra autentiseringsuppgifter för datalager och beräkningar i ett [Azure Key Vault](../key-vault/general/overview.md). Azure Data Factory hämtar autentiseringsuppgifterna när du kör en aktivitet som använder datalagret/beräkningen.

För närvarande stöder alla aktivitetstyper utom anpassad aktivitet den här funktionen. Mer information finns i avsnittet "länkade tjänstegenskaper" i [varje kopplingsavsnitt.](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="prerequisites"></a>Krav

Den här funktionen är beroende av datafabrikens hanterade identitet. Lär dig hur det fungerar från [Managed identity for Data factory](data-factory-service-identity.md) och se till att din datafabrik har en associerad.

## <a name="steps"></a>Steg

Om du vill referera till en autentiseringsuppgifter som lagras i Azure Key Vault måste du:

1. **Hämta datafabrikens hanterade identitet** genom att kopiera värdet "Managed Identity Object ID" som genereras tillsammans med din fabrik. Om du använder ADF-redigeringsgränssnitt visas det hanterade identitetsobjekt-ID:t i fönstret Azure Key Vault-länkade tjänstskapande. Du kan också hämta den från Azure-portalen, referera till [Hämta data fabrikshanterad identitet](data-factory-service-identity.md#retrieve-managed-identity).
2. **Bevilja den hanterade identitetsåtkomsten till ditt Azure Key Vault.** Sök i den här hanterade identiteten > > i listrutan **Hemliga** behörigheter i listrutan Hemliga behörigheter i listrutan Hemliga behörigheter i rullgardinsmenyn Hemliga behörigheter. Det gör att denna utsedda fabrik att komma åt hemlighet i nyckelvalvet.
3. **Skapa en länkad tjänst som pekar på ditt Azure Key Vault.** Se [azure key vault-länkad tjänst](#azure-key-vault-linked-service).
4. **Skapa datalagerlänkad tjänst, inuti vilken referens motsvarande hemlighet lagras i nyckelvalvet.** Se [referenshemlighet som lagras i nyckelvalvet](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault-länkad tjänst

Följande egenskaper stöds för Azure Key Vault-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till: **AzureKeyVault**. | Ja |
| baseUrl () | Ange URL:en för Azure Key Vault. | Ja |

**Använda användargränssnittet för redigering:**

Välj **Anslutningar** -> **Länkade tjänster** -> **Ny**. I Ny länkad tjänst söker du efter och väljer "Azure Key Vault":

![Sök i Azure Key Vault](media/store-credentials-in-key-vault/search-akv.png)

Välj det etablerade Azure Key Vault där dina autentiseringsuppgifter lagras. Du kan göra **Test Connection** för att se till att din AKV-anslutning är giltig. 

![Konfigurera Azure Key Vault](media/store-credentials-in-key-vault/configure-akv.png)

**JSON exempel:**

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

## <a name="reference-secret-stored-in-key-vault"></a>Referera till en hemlighet som lagras i nyckelvalvet

Följande egenskaper stöds när du konfigurerar ett fält i länkad tjänst som refererar till en nyckelvalvshemlighet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen för fältet måste anges till: **AzureKeyVaultSecret**. | Ja |
| secretName (hemligtnamn) | Namnet på hemligheten i Azure Key Vault. | Ja |
| secretVersion (hemligVersion) | Den hemliga versionen i Azure Key Vault.<br/>Om det inte anges används alltid den senaste versionen av hemligheten.<br/>Om det anges, då det fastnar på den givna versionen.| Inga |
| butik | Refererar till en Azure Key Vault-länkad tjänst som du använder för att lagra autentiseringsuppgifterna. | Ja |

**Använda användargränssnittet för redigering:**

Välj **Azure Key Vault** för hemliga fält när du skapar anslutningen till ditt datalager/beräkning. Välj den etablerade Azure Key Vault-länkade tjänsten och ange det **hemliga namnet**. Du kan också ange en hemlig version. 

>[!TIP]
>För kopplingar som använder anslutningssträng i länkad tjänst som SQL Server, Blob storage, etc., kan du välja att antingen bara lagra det hemliga fältet, t.ex. Du hittar båda alternativen i användargränssnittet.

![Konfigurera Secret för Azure Key Vault](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON exempel: (se avsnittet "lösenord" )**

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
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
