---
title: Skapa en Azure Media Services-jobbindata från en HTTPS-URL | Microsoft-dokument
description: Det här avsnittet visar hur du skapar en Azure Media Services Job-indata från en HTTPS-URL.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: a3d4568dd237491f28ae2880bdd78dd236870c3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899834"
---
# <a name="create-a-job-input-from-an-https-url"></a>Skapa en jobbinmatning från en HTTPS-URL

När du skickar in jobb för att bearbeta videor i Media Services v3 måste du informera Media Services om var indatavideo finns. Ett alternativ är att ange en HTTPS-URL som jobbindata (vilket visas i det här exemplet). Observera att AMS v3 för närvarande inte stöder segmentvis överföringskodning över HTTPS-URL:er. Ett fullständigt exempel finns i det här [GitHub-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Innan du börjar utveckla, granska [Utveckla med Media Services v3 API:er](media-services-apis-overview.md) (innehåller information om åtkomst till API:er, namngivningskonventioner osv.)

## <a name="net-sample"></a>.NET-exempel

Följande kod visar hur du skapar ett jobb med en HTTPS-URL-inmatning.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Jobbfelkoder

Se [Felkoder](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Nästa steg

[Skapa en jobbinmatning från en lokal fil](job-input-from-local-file-how-to.md).
