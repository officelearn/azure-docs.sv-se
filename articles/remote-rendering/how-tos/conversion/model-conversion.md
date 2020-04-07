---
title: Modellkonvertering
description: Beskriver processen att konvertera en modell för rendering
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 135c58a665779ffaad8750ffe618bdbe38639b66
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681498"
---
# <a name="convert-models"></a>Konvertera modeller

Med Azure Remote Rendering kan du återge mycket komplexa modeller. För att uppnå maximal prestanda måste data förbearbetas för att vara i ett optimalt format. Beroende på mängden data kan det här steget ta ett tag. Det skulle vara opraktiskt, om denna tid spenderades under modell lastning. Dessutom skulle det vara slösaktigt att upprepa denna process för flera sessioner. Av dessa skäl tillhandahåller ARR-tjänsten en dedikerad *konverteringstjänst*som du kan köra i förväg.
När en modell har konverterats kan den läsas in från ett Azure Storage-konto.

## <a name="supported-source-formats"></a>Källformat som stöds

Konverteringstjänsten stöder följande format:

- **FBX** (version 2011 och senare)
- **GLTF** (version 2.x)
- **GLB** (version 2.x)

Det finns mindre skillnader mellan formaten när det gäller materialegenskapskonvertering, som anges i [kapitelmaterialmappning för modellformat](../../reference/material-mapping.md).

## <a name="the-conversion-process"></a>Konverteringsprocessen

1. [Förbered två Azure Blob Storage-behållare:](blob-storage.md)en för indata, en för utdata
1. Ladda upp modellen till indatabehållaren (eventuellt under en underbana)
1. Utlösa konverteringsprocessen genom [REST API-API:et för modellkonvertering](conversion-rest-api.md)
1. Avsök tjänsten för konverteringsförlopp
1. När du är klar läser du in en modell
    - från ett länkat lagringskonto (se stegen "Länka lagringskonton" på [Skapa ett konto](../create-an-account.md#link-storage-accounts) för att länka ditt lagringskonto)
    - eller genom att tillhandahålla en *SIGNATURE för delad åtkomst (SAS)*.

Alla modelldata (indata och utdata) lagras i azure-bloblagring. Azure Remote Rendering ger dig full kontroll över din kapitalförvaltning.

## <a name="conversion-parameters"></a>Konverteringsparametrar

De olika konverteringsalternativen finns i [det här kapitlet](configure-model-conversion.md).

## <a name="examples"></a>Exempel

- [Snabbstart: Konvertera en modell för rendering](../../quickstarts/convert-model.md) är en steg-för-steg introduktion hur man konverterar en modell.
- [Exempel på PowerShell-skript](../../samples/powershell-example-scripts.md), som demonstrerar användningen av konverteringstjänsten, finns i [ARR-exempeldatabasen](https://github.com/Azure/azure-remote-rendering) i mappen *Skript.*

## <a name="next-steps"></a>Nästa steg

- [Använda Azure Blob Storage för modellkonvertering](blob-storage.md)
- [REST-API för modellkonvertering](conversion-rest-api.md)
- [Konfigurera modellkonverteringen](configure-model-conversion.md)
- [Materialmappning för modellformat](../../reference/material-mapping.md)
