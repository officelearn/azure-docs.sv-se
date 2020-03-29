---
title: Hämta en signeringsnyckel från en princip med Azure Media Services v3 .NET
description: Det här avsnittet visar hur du hämtar en signeringsnyckel från den befintliga principen med Media Services v3 .NET SDK.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2dea262fadb61adc9e219b76f9ac048c11e650d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065968"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Hämta en signeringsnyckel från den befintliga principen

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3 API:er returnerar inte hemligheter eller autentiseringsuppgifter för **get-** **eller liståtgärder.** Se den detaljerade förklaringen här: Mer information finns i [RBAC- och Media Services-konton](rbac-overview.md)

Exemplet i den här artikeln visar hur du använder .NET för att hämta en signeringsnyckel från den befintliga principen. 
 
## <a name="download"></a>Ladda ned 

Klona en GitHub-databas som innehåller hela .NET-exemplet till datorn med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Exemplet ContentKeyPolicy med hemligheter finns i mappen [EncryptWithDRM.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)

## <a name="get-contentkeypolicy-with-secrets"></a>Hämta ContentKeyPolicy med hemligheter 

För att komma till nyckeln, använd **GetPolicyPropertiesWithSecretsAsync**, som visas i exemplet nedan.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Nästa steg

[Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md) 
