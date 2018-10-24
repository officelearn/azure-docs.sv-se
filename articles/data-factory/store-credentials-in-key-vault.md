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
ms.devlang: na
ms.topic: conceptual
ms.date: 10/22/2017
ms.author: jingwang
ms.openlocfilehash: 3428fb5034435d9f3444347329171d803136177c
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944676"
---
# <a name="store-credential-in-azure-key-vault"></a>Store-autentiseringsuppgifter i Azure Key Vault

Du kan lagra autentiseringsuppgifter för datalager och beräkningar i ett [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory hämtar autentiseringsuppgifterna när du kör en aktivitet som använder data datalager/Beräkningstjänster.

För närvarande stöder alla aktivitetstyper utom anpassad aktivitet den här funktionen. Kopplingskonfiguration mer specifikt finns i avsnittet ”länkade tjänstegenskaper” [varje avsnitt om anslutningsprogram](copy-activity-overview.md#supported-data-stores-and-formats) mer information.

## <a name="prerequisites"></a>Förutsättningar

Den här funktionen är beroende av tjänstidentiteten för data factory. Lär dig hur det fungerar från [tjänstidentitet för datafabrik](data-factory-service-identity.md) och se till att din data factory har en associerad.

>[!TIP]
>I Azure Key Vault, när du skapar en hemlighet **put hela värdet för en hemlig egenskap att ADF länkad tjänst begär (t.ex. anslutning sträng/lösenord/tjänstens huvudnamn nyckel/osv)**. Till exempel för Azure Storage länkade tjänsten, placera `DefaultEndpointsProtocol=http;AccountName=myAccount;AccountKey=myKey;` som AKV-hemlighet och sedan referens i fältet ”connectionString” från ADF; för den länkade tjänsten av Dynamics, placera `myPassword` som AKV-hemlighet sedan referera i ”password” fält från ADF. Finns i varje anslutning/compute-artikel på stöds egenskapsinformationen.

## <a name="steps"></a>Steg

Om du vill referera till en autentiseringsuppgift lagras i Azure Key Vault, måste du:

1. **Hämta tjänstidentitet för datafabrik** genom att kopiera värdet för ”SERVICE IDENTITETSPROGRAM-ID” genererade tillsammans med din datafabrik. Om du använder ADF redigering av Användargränssnittet för tjänstidentiteten ID kan visas i fönstret för Azure Key Vault länkad tjänst skapas. Du kan också hämta den från Azure-portalen, som avser [hämta tjänstidentitet för datafabrik](data-factory-service-identity.md#retrieve-service-identity).
2. **Ge tjänstens identitet åtkomst till Azure Key Vault.** I ditt nyckelvalv åtkomst -> Principer -> Lägg till ny -> Sök efter det här tjänstprogrammet för identitets-ID: T för att bevilja **hämta** behörighet i listrutan för hemliga behörigheter. Det gör att den här avsedda factory ska kunna komma åt hemlighet i nyckelvalvet.
3. **Skapa en länkad tjänst som pekar på Azure Key Vault.** Referera till [Azure Key Vault-länkade tjänst](#azure-key-vault-linked-service).
4. **Skapa data länkad lagringstjänst, i vilken referens motsvarande privata nyckel för lagras i valvet.** Referera till [referens-hemlighet som lagras i nyckelvalvet](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault länkad tjänst

Följande egenskaper har stöd för Azure Key Vault-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **AzureKeyVault**. | Ja |
| BaseUrl | Ange URL: en för Azure Key Vault. | Ja |

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
