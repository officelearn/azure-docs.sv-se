---
title: Till gångs konverterings REST API
description: Beskriver hur du konverterar en till gång via REST API
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 889a70005f1cbabaad525147b4661ea04886138a
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445616"
---
# <a name="use-the-model-conversion-rest-api"></a>Använda REST API:et för modellkonvertering

[Modell konverterings](model-conversion.md) tjänsten styrs via en [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer). I den här artikeln beskrivs API-information för konverterings tjänsten.

## <a name="regions"></a>Regioner

Se [listan över tillgängliga regioner](../../reference/regions.md) för bas-URL: er för att skicka begär anden till.

## <a name="common-headers"></a>Vanliga rubriker

### <a name="common-request-headers"></a>Vanliga begärandehuvuden

Dessa huvuden måste anges för alla begär Anden:

- **Authorization** -huvudet måste ha värdet "Bearer [ *token* ]", där [ *token* ] är en [tjänst åtkomst-token](../tokens.md).

### <a name="common-response-headers"></a>Vanliga svars rubriker

Alla svar innehåller följande rubriker:

- **MS-ka** -huvudet innehåller en unik sträng som kan användas för att spåra anropet i tjänsten.

## <a name="endpoints"></a>Slutpunkter

Konverterings tjänsten tillhandahåller tre REST API-slutpunkter för att:

- Starta modell konverteringen med ett lagrings konto som är kopplat till ditt Azure Remote rendering-konto. 
- Starta modell konverteringen med hjälp av under angivna *signaturer för delad åtkomst (SAS)*.
- fråga om konverterings status

### <a name="start-conversion-using-a-linked-storage-account"></a>Starta konverteringen med ett länkat lagrings konto
Ditt Azure Remote rendering-konto måste ha åtkomst till det tillhandahållna lagrings kontot genom att följa stegen för att [Länka lagrings konton](../create-an-account.md#link-storage-accounts).

| Slutpunkt | Metod |
|-----------|:-----------|
| /v1/Accounts/ **accountID** /conversions/Create | POST |

Returnerar ID: t för den pågående konverteringen, figursatt i ett JSON-dokument. Fält namnet är "conversionId".

#### <a name="request-body"></a>Begärandetext

> [!NOTE]
> Allt under hämtas `input.folderPath` för att utföra konverteringen på Azure. Om `input.folderPath` inte anges hämtas hela innehållet i behållaren. Alla blobbar och mappar som hämtats måste ha [giltiga Windows-filnamn](/windows/win32/fileio/naming-a-file#naming-conventions).

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Starta konverteringen med angivna signaturer för delad åtkomst
Om ARR-kontot inte är länkat till ditt lagrings konto kan du använda det här REST-gränssnittet för att ge åtkomst med *signaturer för delad åtkomst (SAS)*.

| Slutpunkt | Metod |
|-----------|:-----------|
| /v1/Accounts/ **accountID** /conversions/createWithSharedAccessSignature | POST |

Returnerar ID: t för den pågående konverteringen, figursatt i ett JSON-dokument. Fält namnet är `conversionId` .

#### <a name="request-body"></a>Begärandetext

Begär ande texten är samma som i Create REST-anropet ovan, men indata och utdata innehåller *SAS-token (signatur för delad åtkomst)*. Dessa tokens ger åtkomst till lagrings kontot för att läsa indata och skriva konverterings resultatet.

> [!NOTE]
> Dessa SAS URI-token är frågesträngarna och inte hela URI: n. 

> [!NOTE]
> Allt under hämtas `input.folderPath` för att utföra konverteringen på Azure. Om `input.folderPath` inte anges hämtas hela innehållet i behållaren. Alla blobbar och mappar som hämtats måste ha [giltiga Windows-filnamn](/windows/win32/fileio/naming-a-file#naming-conventions).

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>Status för avsöknings konvertering
Statusen för en pågående konvertering startade med ett av REST-anropen ovan kan frågas med hjälp av följande gränssnitt:


| Slutpunkt | Metod |
|-----------|:-----------|
| /v1/Accounts/ **accountID** /conversions/ **conversionId** | GET |

Returnerar ett JSON-dokument med ett "status"-fält som kan ha följande värden:

- Create
- Kör
- Resultatet
- Haverera

Om statusen är "haveri" visas ett ytterligare "fel"-fält med ett "meddelande"-underfält med fel information. Ytterligare loggar överförs till din utmatnings behållare.

## <a name="list-conversions"></a>List konverteringar

Om du vill hämta en lista över alla konverteringar för ett konto använder du gränssnittet:

| Slutpunkt | Metod |
|-----------|:-----------|
| /v1/Accounts/ **accountID** /conversions? Skiptoken = **skiptoken** | GET |

| Parameter | Krävs |
|-----------|:-----------|
| accountID | Ja |
| skiptoken | Nej |

Returnerar ett JSON-dokument som innehåller en matris med konverteringar och deras information. Den här frågan returnerar högst 50 konverteringar åt gången. Om det finns fler konverteringar som ska hämtas innehåller svaret en **nextLink** -egenskap som innehåller skipToken som kan frågas för att hämta nästa uppsättning resultat.

## <a name="next-steps"></a>Nästa steg

- [Använda Azure-Blob Storage för modellkonvertering](blob-storage.md)
- [Modell konvertering](model-conversion.md)