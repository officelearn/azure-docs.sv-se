---
title: Skriptexempel för Azure CLI – Skapa och skicka ett jobb | Microsoft Docs
description: Azure CLI-skriptet i det här avsnittet visar hur du skickar ett jobb till en enkel kodningstransformering med hjälp av HTTPs-URL.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 63d036ea4faaf7e24f337fa3956986d165c84854
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244355"
---
# <a name="cli-example-create-and-submit-a-job"></a>CLI-exempel: Skapa och skicka ett jobb

När du skickar in jobb för att bearbeta videor i Media Services v3 måste du informera Media Services om var indatavideo finns. Något av alternativen är att ange en HTTPS-URL som ett jobb som indata (som visas i den här artikeln). 

## <a name="prerequisites"></a>Förutsättningar 

[Skapa ett Media Services-konto](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exempelskript

När du kör `az ams job start` kan du applicera en etikett på jobbets utdata. Etiketten kan senare användas för att identifiera vad den här utdatatillgången är till för. 

- Om du tilldelar ett värde till etiketten anger du ”--output-assets” till ”assetname=label”
- Om du inte tilldelar ett värde till etiketten anger du ”--output-assets” till ”assetname=”.
  Observera att du lägger till ”=” i `output-assets`. 

```azurecli
az ams job start \
  --name testJob001 \
  --transform-name testEncodingTransform \
  --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' \
  --files 'Ignite-short.mp4' \
  --output-assets testOutputAssetName= \
  -a amsaccount \
  -g amsResourceGroup 
```

Du får ett svar som liknar följande:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [Azure CLI-exempel](../cli-samples.md).
