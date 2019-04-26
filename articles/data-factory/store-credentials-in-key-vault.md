---
title: Store autentiseringsuppgifter i Azure Key Vault | Microsoft Docs
description: Lär dig hur du lagrar autentiseringsuppgifter för datalager som används i ett Azure key vault som Azure Data Factory kan automatiskt hämta vid körning.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: a7d440509e2b823400cde83c1ac2ec054c37eb74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311919"
---
# <a name="store-credential-in-azure-key-vault"></a>Store-autentiseringsuppgifter i Azure Key Vault

Du kan lagra autentiseringsuppgifter för datalager och beräkningar i ett [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory hämtar autentiseringsuppgifterna när du kör en aktivitet som använder data datalager/Beräkningstjänster.

För närvarande stöder alla aktivitetstyper utom anpassad aktivitet den här funktionen. Kopplingskonfiguration mer specifikt finns i avsnittet ”länkade tjänstegenskaper” [varje avsnitt om anslutningsprogram](copy-activity-overview.md#supported-data-stores-and-formats) mer information.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här funktionen är beroende av data factory hanterad identitet. Lär dig hur det fungerar från [hanterad identitet för Data factory](data-factory-service-identity.md) och se till att din data factory har en associerad.

## <a name="steps"></a>Steg

Om du vill referera till en autentiseringsuppgift lagras i Azure Key Vault, måste du:

1. **Hämta data factory hanterad identitet** genom att kopiera värdet för ”SERVICE IDENTITETSPROGRAM-ID” genererade tillsammans med din datafabrik. Om du använder ADF redigering Användargränssnittet kan program-ID för hanterad identitet visas i fönstret för Azure Key Vault länkad tjänst skapas. Du kan också hämta den från Azure-portalen, som avser [hämta data factory-hanterad identitet](data-factory-service-identity.md#retrieve-managed-identity).
2. **Bevilja hanterad identitet åtkomst till Azure Key Vault.** I ditt nyckelvalv åtkomst -> Principer -> Lägg till ny -> Sök detta hanteras identitetsprogram-ID att ge **hämta** behörighet i listrutan för hemliga behörigheter. Det gör att den här avsedda factory ska kunna komma åt hemlighet i nyckelvalvet.
3. **Skapa en länkad tjänst som pekar på Azure Key Vault.** Referera till [Azure Key Vault-länkade tjänst](#azure-key-vault-linked-service).
4. **Skapa data länkad lagringstjänst, i vilken referens motsvarande privata nyckel för lagras i valvet.** Referera till [referens-hemlighet som lagras i nyckelvalvet](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault länkad tjänst

Följande egenskaper har stöd för Azure Key Vault-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till: **AzureKeyVault**. | Ja |
| baseUrl | Ange URL: en för Azure Key Vault. | Ja |

**Med redigering av Användargränssnittet:**

Klicka på **anslutningar** -> **länkade tjänster** -> **+ ny** -> Sök efter ”Azure Key Vault”:

![Sök AKV](media/store-credentials-in-key-vault/search-akv.png)

Välj den etablerade Azure Key Vault där dina autentiseringsuppgifter lagras. Du kan göra **Testanslutningen** för att kontrollera att din AKV anslutningen är giltig. 

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

## <a name="reference-secret-stored-in-key-vault"></a>Referens-hemlighet som lagras i nyckelvalvet

Följande egenskaper stöds när du konfigurerar ett fält i den länkade tjänsten refererar till en hemlighet i nyckelvalvet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för fältet måste anges till: **AzureKeyVaultSecret**. | Ja |
| secretName | Namnet på hemlighet i azure key vault. | Ja |
| secretVersion | Versionen av hemlighet i azure key vault.<br/>Om inte anges använder alltid den senaste versionen av hemligheten.<br/>Om anges fastnar den angivna versionen.| Nej |
| Store | Refererar till en Azure Key Vault-länkade tjänst som används för att lagra autentiseringsuppgifterna. | Ja |

**Med redigering av Användargränssnittet:**

Välj **Azure Key Vault** för hemliga fält när du skapar anslutningen till ditt datalager/Beräkningstjänster i data. Välj den etablerade Azure Key Vault länkade tjänsten och ange den **hemligt namn**. Du kan också bifoga en hemlig version. 

>[!TIP]
>Du kan välja att lagra endast hemliga fältet t.ex. lösenord i AKV eller att lagra hela anslutningssträngen i AKV för kopplingar med anslutningssträngen i den länkade tjänsten som SQL Server, Blob storage och så vidare. Du kan hitta båda alternativen i Användargränssnittet.

![Konfigurera AKV-hemlighet](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON-exempel: (se avsnittet ”lösenord”)**

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
