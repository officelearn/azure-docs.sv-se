---
title: Rollbaserad åtkomstkontroll för Media Services-konton – Azure | Microsoft-dokument
description: I den här artikeln beskrivs rollbaserad åtkomstkontroll (RBAC) för Azure Media Services-konton.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66236920"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Rollbaserad åtkomstkontroll (RBAC) för Media Services-konton

Azure Media Services definierar för närvarande inga anpassade roller som är specifika för tjänsten. Om du vill få fullständig åtkomst till Media Services-kontot kan kunderna använda de inbyggda rollerna **för Ägare** eller **Deltagare**. Den största skillnaden mellan dessa roller är: **Ägaren** kan styra vem som har åtkomst till en resurs och **deltagaren** kan inte. Rollen inbyggd **läsare** kan också användas, men användaren eller programmet har bara läsbehörighet till API:erna för Medietjänster. 

## <a name="design-principles"></a>Designprinciper

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3 API:er returnerar inte hemligheter eller autentiseringsuppgifter för **get-** **eller liståtgärder.** Nycklarna är alltid null, tomma eller oberoende av svaret. Användaren måste anropa en separat åtgärdsmetod för att få hemligheter eller autentiseringsuppgifter. **Rollen Läsare** kan inte anropa åtgärder som Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Med separata åtgärder kan du ange mer detaljerade RBAC-säkerhetsbehörigheter i en anpassad roll om så önskas.

Så här listar du de åtgärder som Media Services stöder:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

I artikeln [med inbyggda rolldefinitioner](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) får du veta exakt vad rollen ger. 

Mer information finns i följande artiklar:

- [Administratörsroller för klassiska prenumerationer, Azure RBAC-roller och administratörsroller för Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Vad är RBAC för Azure-resurser?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Använda RBAC för att hantera åtkomst](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Resursprovideråtgärder för Media Services](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Nästa steg

- [Utveckla med Media Services v3 API:er](media-services-apis-overview.md)
- [Hämta princip för innehållsnyckel med Media Services .NET](get-content-key-policy-dotnet-howto.md)
