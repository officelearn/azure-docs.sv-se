---
title: Spara autentiseringsuppgifter i Azure Key Vault
description: Lär dig hur du lagrar autentiseringsuppgifter för data lager som används i ett Azure Key Vault som Azure Data Factory kan hämta automatiskt vid körning.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: 1418205843fefc76db4e73832736b308d0cc79a3
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122618"
---
# <a name="store-credential-in-azure-key-vault"></a>Lagra autentiseringsuppgifter i Azure Key Vault

Du kan lagra autentiseringsuppgifter för data lager och beräkningar i en [Azure Key Vault](../key-vault/key-vault-overview.md). Azure Data Factory hämtar autentiseringsuppgifterna när en aktivitet som använder data lagret/data bearbetningen körs.

För närvarande har alla aktivitets typer förutom anpassad aktivitet stöd för den här funktionen. För kopplings konfiguration är det specifikt att se avsnittet "egenskaper för länkad tjänst" i [varje anslutnings avsnitt](copy-activity-overview.md#supported-data-stores-and-formats) för mer information.

## <a name="prerequisites"></a>Krav

Den här funktionen använder den hanterade identiteten för Data Factory. Lär dig hur det fungerar från [hanterad identitet för Data Factory](data-factory-service-identity.md) och se till att data fabriken har en associerad.

## <a name="steps"></a>Steg

Om du vill referera till en autentiseringsuppgift som lagras i Azure Key Vault måste du:

1. **Hämta Data Factory-hanterad identitet** genom att kopiera värdet "hanterat identitets objekt-ID" som genererats tillsammans med din fabrik. Om du använder användar gränssnittet för ADF-redigering visas det hanterade identitet objekt-ID: t i fönstret Azure Key Vault länkad tjänst skapas. Du kan också hämta den från Azure Portal, se [Hämta Data Factory-hanterad identitet](data-factory-service-identity.md#retrieve-managed-identity).
2. **Ge åtkomst till den hanterade identiteten till din Azure Key Vault.** I ditt nyckel valv – > åtkomst principer – > Lägg till ny-> söker du igenom den här hanterade identiteten för att ge behörighet för att **få** behörighet i list rutan hemliga Det gör att den här utsedda fabriken kan komma åt hemlighet i Key Vault.
3. **Skapa en länkad tjänst som pekar på Azure Key Vault.** Referera till [Azure Key Vault länkade tjänsten](#azure-key-vault-linked-service).
4. **Skapa länkad tjänst för data lager som refererar till motsvarande hemlighet som lagras i Key Vault.** Referera till [referens hemlighet som lagras i Key Vault](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault länkad tjänst

Följande egenskaper stöds för Azure Key Vault länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **AzureKeyVault**. | Ja |
| BaseUrl | Ange Azure Key Vault-URL. | Ja |

**Använda redigerings gränssnittet:**

Klicka på **anslutningar** -> **länkade tjänster** ->  **+ New** -> Sök efter "Azure Key Vault":

![Sök AKV](media/store-credentials-in-key-vault/search-akv.png)

Välj den etablerade Azure Key Vault där dina autentiseringsuppgifter lagras. Du kan **testa anslutningen** för att kontrol lera att din AKV-anslutning är giltig. 

![Konfigurera AKV](media/store-credentials-in-key-vault/configure-akv.png)

**JSON-exempel:**

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

## <a name="reference-secret-stored-in-key-vault"></a>Referens hemlighet lagrad i Key Vault

Följande egenskaper stöds när du konfigurerar ett fält i en länkad tjänst som refererar till en Key Vault-hemlighet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för fältet måste anges till: **AzureKeyVaultSecret**. | Ja |
| SecretName | Namnet på hemligheten i Azure Key Vault. | Ja |
| secretVersion | Den hemliga versionen i Azure Key Vault.<br/>Om detta inte anges används alltid den senaste versionen av hemligheten.<br/>Om detta anges, kommer det att göras till den angivna versionen.| Inga |
| store | Refererar till en Azure Key Vault länkad tjänst som du använder för att lagra autentiseringsuppgifterna. | Ja |

**Använda redigerings gränssnittet:**

Välj **Azure Key Vault** för hemliga fält när du skapar anslutningen till data lagret/data bearbetningen. Välj den tillhandahållna Azure Key Vault länkade tjänsten och ange det **hemliga namnet**. Du kan också ange en hemlig version. 

>[!TIP]
>För anslutningar som använder anslutnings sträng i den länkade tjänsten, t. ex. SQL Server, Blob Storage osv., kan du välja att endast lagra fältet hemligt, t. ex. lösen ord i AKV eller för att lagra hela anslutnings strängen i AKV. Du kan hitta båda alternativen i användar gränssnittet.

![Konfigurera AKV-hemlighet](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON-exempel: (se avsnittet "lösen ord")**

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
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
