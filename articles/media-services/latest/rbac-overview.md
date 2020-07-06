---
title: Rollbaserad åtkomst kontroll för Media Services konton – Azure | Microsoft Docs
description: Den här artikeln beskriver rollbaserad åtkomst kontroll (RBAC) för Azure Media Services-konton.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "66236920"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Rollbaserad åtkomst kontroll (RBAC) för Media Services-konton

För närvarande definierar Azure Media Services inte några anpassade roller som är speciella för tjänsten. För att få fullständig åtkomst till Media Services kontot kan kunder använda de inbyggda rollerna för **ägare** eller **deltagare**. Den största skillnaden mellan dessa roller är: **ägaren** kan styra vem som har åtkomst till en resurs och **deltagaren** inte kan. Den inbyggda **läsar** rollen kan också användas, men användaren eller programmet kommer bara att ha Läs behörighet till Media Services API: er. 

## <a name="design-principles"></a>Designprinciper

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3-API: er returnerar inte hemligheter eller autentiseringsuppgifter för **Get** -eller **list** åtgärder. Nycklarna är alltid null, tomma eller oberoende av svaret. Användaren måste anropa en separat åtgärds metod för att få hemligheter eller autentiseringsuppgifter. Rollen **läsare** kan inte anropa åtgärder som till gång. ListContainerSas, StreamingLocator. ListContentKeys, ContentKeyPolicies. GetPolicyPropertiesWithSecrets. Med separata åtgärder kan du ställa in mer detaljerade RBAC-säkerhetsbehörigheter i en anpassad roll om du vill.

Gör så här om du vill visa vilka åtgärder som Media Services stöder:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

Den [inbyggda roll definitions](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) artikeln visar exakt vad rollen beviljar. 

Mer information finns i följande artiklar:

- [Administratörsroller för klassiska prenumerationer, Azure RBAC-roller och administratörsroller för Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Vad är RBAC för Azure-resurser?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Använd RBAC för att hantera åtkomst](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Åtgärder för Media Services Resource Provider](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Nästa steg

- [Utveckla med Media Services v3-API: er](media-services-apis-overview.md)
- [Hämta princip för innehålls nyckel med Media Services .NET](get-content-key-policy-dotnet-howto.md)
