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
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 882f4650c0a3d558ee06c96658b779f9f0c76f76
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54322507"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Hämta en signeringsnyckel från den befintliga principen

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3 API:er returnerar inte hemlighet eller autentiseringsuppgifter för en åtgärd av typen **hämta** eller **lista**. Nycklarna är alltid null, tomma eller oberoende av svaret. Du måste anropa en separat åtgärdsmetod för att få hemlighet och autentiseringsuppgifter. Med separata åtgärder kan du ställa in olika RBAC-säkerhetsbehörigheter om vissa API:er hämtar/visar hemligheter medan andra API:er inte gör det. Information om hur du hanterar åtkomst med RBAC finns i dokumentationen om att [hantera åtkomst med RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Exempel på det här kan vara att 

* inte returnera ContentKey-värden i Hämta för StreamingLocator, 
* inte returnera begränsningsnycklar för get för ContentKeyPolicy, 
* inte returnera frågesträngsdelen av webbadressen (för att ta bort signaturen) för jobbets HTTP-indatawebbadresser.

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
