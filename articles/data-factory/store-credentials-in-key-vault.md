---
title: Lagra autentiseringsuppgifter i Azure Key Vault | Microsoft Docs
description: Lär dig hur du lagrar autentiseringsuppgifterna för datalager som används i en Azure key vault som Azure Data Factory kan automatiskt hämta vid körning.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: jingwang
ms.openlocfilehash: 4a8c96bf9124feede2e5a28beb791636784dcad7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="store-credential-in-azure-key-vault"></a>Spara autentiseringsuppgifter i Azure Key Vault

Du kan lagra autentiseringsuppgifter för datalager och beräknar i en [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory hämtar autentiseringsuppgifterna vid körning av en aktivitet som använder data store/beräkning.

För närvarande stöder alla aktivitetstyper utom anpassad aktivitet den här funktionen. För kopplingskonfiguration i synnerhet Kontrollera avsnittet ”länkade tjänstegenskaper” i [varje koppling avsnitt](copy-activity-overview.md#supported-data-stores-and-formats) mer information.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [dokumentationen för Data Factory version1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>Förutsättningar

Den här funktionen är beroende av tjänstidentiteten data factory. Lär dig hur det fungerar från [Data factory tjänstidentiteten](data-factory-service-identity.md) och se till att din data factory har en associerad.

>[!TIP]
>I Azure Key Vault, när du skapar en hemlighet **put hela värdet för en hemlig egenskap att ADF länkade tjänsten begär (t.ex. anslutning sträng-lösenord-tjänsten primära nyckel o.s.v.)**. Till exempel för Azure Storage länkade tjänsten placerar `DefaultEndpointsProtocol=http;AccountName=myAccount;AccountKey=myKey;` som AKV hemlighet och sedan referens i fältet ”connectionString” från ADF; för den länkade tjänsten för Dynamics, placera `myPassword` som AKV hemlighet sedan refererar till i fältet ”paassword” från ADF. Referera till varje koppling/beräknings-artikel på stöds egenskapsinformationen.

## <a name="steps"></a>Steg

Om du vill referera en autentiseringsuppgift lagras i Azure Key Vault, måste du:

1. **Hämta data factory-tjänsten** genom att kopiera värdet för ”IDENTITY-ID för programmet” skapas tillsammans med din factory. Om du använder ADF redigering Användargränssnittet för tjänstidentiteten ID visas i fönstret Azure Key Vault länkad tjänst skapas; Du kan också hämta från Azure-portalen avser [hämta data factory-tjänsten](data-factory-service-identity.md#retrieve-service-identity).
2. **Ge service identitet åtkomst till Azure Key Vault.** I nyckelvalvet åtkomst -> Principer -> Lägg till ny -> Sök tjänstprogrammet identitet ID för att ge **hämta** behörighet i listrutan hemliga behörigheterna. Det gör denna avsedda factory åtkomst till hemlighet i nyckelvalvet.
3. **Skapa en länkad tjänst som pekar på Azure Key Vault.** Referera till [Azure Key Vault länkade tjänsten](#azure-key-vault-linked-service).
4. **Skapa data länkade lagringstjänst, i vilken referens motsvarande privata nyckel för lagras i valvet.** Referera till [referens hemlighet i nyckelvalvet](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault länkad tjänst

Följande egenskaper stöds för Azure Key Vault länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **AzureKeyVault**. | Ja |
| BaseUrl | Ange Azure Key Vault-URL. | Ja |

**Använda redigering UI:**

Klicka på **anslutningar** -> **länkade tjänster** -> **+ ny** -> Sök efter ”Azure Key Vault”:

![Sök AKV](media/store-credentials-in-key-vault/search-akv.png)

Välj de etablerade Azure Key Vault där dina autentiseringsuppgifter lagras. Du kan göra **Testanslutningen** för att kontrollera din AKV anslutningen är giltig. 

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

## <a name="reference-secret-stored-in-key-vault"></a>Referens hemlighet i nyckelvalvet

När du konfigurerar ett fält i den länkade tjänsten refererar till en hemlighet i nyckelvalvet stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type i fältet måste anges till: **AzureKeyVaultSecret**. | Ja |
| secretName | Namnet på hemlighet i azure key vault. | Ja |
| secretVersion | Versionen av hemlighet i azure key vault.<br/>Om den inte anges använder alltid den senaste versionen av hemligheten.<br/>Om anges fastnar den angivna versionen.| Nej |
| lagra | Refererar till en Azure Key Vault länkade tjänst som används för att lagra autentiseringsuppgifter. | Ja |

**Använda redigering UI:**

Välj **Azure Key Vault** för hemliga fält vid anslutning till dina data store/beräknings skapas. Välj etablerade Azure Key Vault länkade Service och ange den **hemliga namnet**. Alternativt kan du ange en hemliga versionen. 

![Konfigurera AKV hemligheten](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON-exempel: (se avsnittet ”password”)**

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
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).