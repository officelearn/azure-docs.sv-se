---
title: 'Utveckla med v3 API: er – Azure | Microsoft Docs'
description: 'Den här artikeln beskrivs regler som gäller för entiteter och API: er när du utvecklar med Media Services v3.'
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492149"
---
# <a name="developing-with-media-services-v3-apis"></a>Utveckla med Media Services v3-API: er

Den här artikeln beskrivs regler som gäller för entiteter och API: er när du utvecklar med Media Services v3.

## <a name="naming-conventions"></a>Namngivningskonventioner

Azure Media Services v3-resursnamn (till exempel tillgångar, jobb, transformeringar) är föremål Azure Resource Manager-namnbegränsningar. I enlighet med Azure Resource Manager är resursnamnen alltid unika. Därför kan du använda alla strängar som unika identifierare (till exempel GUID) för ditt resursnamn. 

Media Services resursnamn får inte innehålla: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', apostrof eller några andra kontrolltecken. Alla andra tecken tillåts. Maxlängden för ett resursnamn är 260 tecken. 

Mer information om namngivning av Azure Resource Manager finns i: [Namngivningskrav](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) och [Namngivningskonventioner](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles-and-rbac"></a>designprinciper för v3-API och RBAC

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3-API: er inte returnerar hemligheter eller autentiseringsuppgifter på **hämta** eller **lista** åtgärder. Nycklarna är alltid null, tomma eller oberoende av svaret. Användaren måste anropa en separat åtgärd-metod för att hämta hemligheter eller autentiseringsuppgifter. Den **läsare** rollen kan inte anropa åtgärder så att det går inte att anropa åtgärder som Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Med separata åtgärder kan du ange mer detaljerade RBAC-säkerhetsbehörighet i en anpassad roll om du vill.

Mer information finns i:

- [Inbyggda rolldefinitioner](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [Använd RBAC för att hantera åtkomst](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Rollbaserad åtkomstkontroll för Media Services-konton](rbac-overview.md)
- [Hämta innehåll viktiga princip – .NET](get-content-key-policy-dotnet-howto.md).

## <a name="long-running-operations"></a>Långvariga åtgärder

Åtgärderna som markerats med `x-ms-long-running-operation` i Azure Media Services [swagger filer](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) är långa åtgärder. 

Mer information om hur du spårar asynkrona åtgärder i Azure finns [asynkrona åtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services har följande långvariga åtgärder:

* Skapa LiveEvent
* Uppdatera LiveEvent
* Delete LiveEvent
* Starta LiveEvent
* Stoppa LiveEvent
* Återställ LiveEvent
* Skapa LiveOutput
* Delete LiveOutput
* Skapa StreamingEndpoint
* Uppdatera StreamingEndpoint
* Ta bort StreamingEndpoint
* Starta StreamingEndpoint
* Stoppa StreamingEndpoint
* Skala StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrering, skrivordning, växling av Media Services-entiteter

Se [filtrering, sortering, växling av Azure Media Services-entiteter](entities-overview.md)

## <a name="next-steps"></a>Nästa steg

[Börja utveckla med Media Services v3 API med hjälp av SDK:er/verktyg](developers-guide.md)
