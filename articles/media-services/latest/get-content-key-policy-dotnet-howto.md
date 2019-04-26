---
title: Hämta en signeringsnyckel från den befintliga principen med hjälp av Media Services v3 .NET SDK - Azure | Microsoft Docs
description: Det här avsnittet visar hur du hämtar en signeringsnyckel från den befintliga principen med hjälp av Media Services v3 .NET SDK.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 58b6f49f4bbbd93fefb9b616f92baf7ef30f7deb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322642"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Hämta en signeringsnyckel från den befintliga principen

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3-API: er inte returnerar hemligheter eller autentiseringsuppgifter på **hämta** eller **lista** åtgärder. Se en detaljerad förklaring: Mer information finns i [RBAC och Media Services-konton](rbac-overview.md)

I exemplet i den här artikeln visar hur du använder .NET för att få en signeringsnyckel från den befintliga principen. 
 
## <a name="download"></a>Ladda ned 

Klona en GitHub-lagringsplats som innehåller det fullständiga .NET-exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
ContentKeyPolicy med hemligheter exempel finns i den [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) mapp.

## <a name="get-contentkeypolicy-with-secrets"></a>Hämta ContentKeyPolicy med hemligheter 

Gå till nyckeln genom att använda **GetPolicyPropertiesWithSecretsAsync**, vilket visas i exemplet nedan.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Nästa steg

[Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md) 
