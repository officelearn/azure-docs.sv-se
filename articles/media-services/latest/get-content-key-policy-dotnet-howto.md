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
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 49cc2b8c151053377f8f1da0792f10a06695b332
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471179"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Hämta en signeringsnyckel från den befintliga principen

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3-API: er inte returnerar hemligheter eller autentiseringsuppgifter på **hämta** eller **lista** åtgärder. Nycklarna är alltid null, tomma eller oberoende av svaret. Användaren måste anropa en separat åtgärd-metod för att hämta hemligheter eller autentiseringsuppgifter. Den **läsare** rollen kan inte anropa åtgärder så att det går inte att anropa åtgärder som Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Med separata åtgärder kan du ange mer detaljerade RBAC-säkerhetsbehörighet i en anpassad roll om du vill.

Mer information finns i [RBAC och Media Services-konton](rbac-overview.md)

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

[Designen av ett system med multi-DRM innehållsskydd med åtkomstkontroll](design-multi-drm-system-with-access-control.md) 
