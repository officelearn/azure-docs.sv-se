---
title: Hämta en signerings nyckel från en princip med hjälp av Azure Media Services v3 .NET
description: Det här avsnittet visar hur du hämtar en signerings nyckel från den befintliga principen med hjälp av Media Services v3 .NET SDK.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80065968"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Hämta en signeringsnyckel från den befintliga principen

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3-API: er returnerar inte hemligheter eller autentiseringsuppgifter för **Get** -eller **list** åtgärder. Se den detaljerade förklaringen här: Mer information finns i [RBAC-och Media Services-konton](rbac-overview.md)

Exemplet i den här artikeln visar hur du använder .NET för att hämta en signerings nyckel från den befintliga principen. 
 
## <a name="download"></a>Ladda ned 

Klona en GitHub-lagringsplats som innehåller det fullständiga .NET-exemplet på din dator med hjälp av följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Exemplet på ContentKeyPolicy med hemligheter finns i mappen [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .

## <a name="get-contentkeypolicy-with-secrets"></a>Hämta ContentKeyPolicy med hemligheter 

Använd **GetPolicyPropertiesWithSecretsAsync**, som du ser i exemplet nedan, för att komma till nyckeln.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Nästa steg

[Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md) 
