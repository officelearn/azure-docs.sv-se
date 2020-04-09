---
title: REST-API för tillgångskonvertering
description: Beskriver hur du konverterar en tillgång via REST API
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 38116efc9e87eca8e2514a0a84045a69b8d42326
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887052"
---
# <a name="use-the-model-conversion-rest-api"></a>Använda REST API:et för modellkonvertering

[Modellkonverteringstjänsten](model-conversion.md) styrs via ett [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer). I den här artikeln beskrivs API-informationen för konverteringstjänsten.

## <a name="regions"></a>Regioner

Se [listan över tillgängliga regioner](../../reference/regions.md) som bas-URL:erna ska skicka begärandena till.

## <a name="common-headers"></a>Vanliga rubriker

### <a name="common-request-headers"></a>Vanliga begäranden

Dessa rubriker måste anges för alla begäranden:

- **Auktoriseringshuvudet** måste ha värdet "Bärare [*TOKEN*]", där [*TOKEN*] är en [tjänståtkomsttoken](../tokens.md).

### <a name="common-response-headers"></a>Vanliga svarsrubriker

Alla svar innehåller följande rubriker:

- **MS-CV-huvudet** innehåller en unik sträng som kan användas för att spåra anropet i tjänsten.

## <a name="endpoints"></a>Slutpunkter

Konverteringstjänsten tillhandahåller tre REST API-slutpunkter till:

- starta modellkonvertering med ett lagringskonto som är kopplat till ditt Azure Remote Rendering-konto. 
- starta modellkonvertering med hjälp av angivna *SIGNATURER för delad åtkomst (SAS)*.
- fråga om konverteringsstatus

### <a name="start-conversion-using-a-linked-storage-account"></a>Starta konverteringen med ett länkat lagringskonto
Ditt Azure Remote Rendering-konto måste ha åtkomst till det angivna lagringskontot genom att följa stegen för hur du [länkar lagringskonton](../create-an-account.md#link-storage-accounts).

| Slutpunkt | Metod |
|-----------|:-----------|
| /v1/accounts/**accountID**/konverteringar/skapa | POST |

Returnerar ID:et för den pågående konverteringen, insvept i ett JSON-dokument. Fältnamnet är "conversionId".

#### <a name="request-body"></a>Begärandetext


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
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Starta konverteringen med hjälp av angivna signaturer för delad åtkomst
Om ditt ARR-konto inte är länkat till ditt lagringskonto kan du med det här REST-gränssnittet ge åtkomst med hjälp av *SAS (Shared Access Signatures).*

| Slutpunkt | Metod |
|-----------|:-----------|
| /v1/accounts/ accountID /conversions/createWithSharedAccessSignature /v1/accounts/ accountID/conversions/createWithSharedAccessSignature /v1/accounts/**accountID**/conversions/createWithSharedAccessSignature /v | POST |

Returnerar ID:et för den pågående konverteringen, insvept i ett JSON-dokument. Fältnamnet är "conversionId".

#### <a name="request-body"></a>Begärandetext

Brödtexten för begäran är samma som i det skapa REST-anropet ovan, men indata och utdata innehåller *SAS-token (Shared Access Signatures).* Dessa token ger åtkomst till lagringskontot för att läsa indata och skriva konverteringsresultatet.

> [!NOTE]
> Dessa SAS URI-token är frågesträngarna och inte den fullständiga URI.These SAS URI tokens are the query strings and not the full URI. 


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

### <a name="poll-conversion-status"></a>Status för avsökningskonvertering
Status för en pågående konvertering som startades med ett av REST-anropen ovan kan efterfrågas med hjälp av följande gränssnitt:


| Slutpunkt | Metod |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/conversionId**conversionId** | HÄMTA |

Returnerar ett JSON-dokument med ett "status"-fält som kan ha följande värden:

- "Löpning"
- "Framgång"
- "Misslyckande"

Om statusen är "Fel" kommer det att finnas ett ytterligare "fel" fält med ett "meddelande" delfält som innehåller felinformation. Ytterligare loggar kommer att överföras till din utdatabehållare.

## <a name="next-steps"></a>Nästa steg

- [Använda Azure-Blob Storage för modellkonvertering](blob-storage.md)
- [Modellkonvertering](model-conversion.md)
