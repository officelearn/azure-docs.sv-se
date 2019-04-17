---
title: Rollbaserad åtkomstkontroll för Media Services-konton – Azure | Microsoft Docs
description: Den här artikeln beskriver rollbaserad åtkomstkontroll (RBAC) för Azure Media Services-konton.
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
ms.openlocfilehash: 82c438ea246ba18fa7dac2281d68a3ea7ba41db8
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617724"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Rollbaserad åtkomstkontroll (RBAC) för Media Services-konton

Azure Media Services anger för närvarande inte någon specifik anpassade roller till tjänsten. För att få fullständig åtkomst till Media Services-kontot kan kunder kan använda de inbyggda rollerna för **ägare** eller **deltagare**. Den största skillnaden mellan dessa roller är: den **ägare** kan styra vem som har åtkomst till en resurs och **deltagare** kan inte. Inbyggt **läsare** rollen kan också användas, men användaren eller programmet har bara läsbehörighet till API: er för Media Services. 

## <a name="design-principles"></a>Designprinciper

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3-API: er inte returnerar hemligheter eller autentiseringsuppgifter på **hämta** eller **lista** åtgärder. Nycklarna är alltid null, tomma eller oberoende av svaret. Användaren måste anropa en separat åtgärd-metod för att hämta hemligheter eller autentiseringsuppgifter. Den **läsare** rollen kan inte anropa åtgärder som Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Med separata åtgärder kan du ange mer detaljerade RBAC-säkerhetsbehörighet i en anpassad roll om du vill.

Om du vill visa åtgärder Media Services stöder, gör du:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

Den [inbyggda rolldefinitioner](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) artikeln får du veta exakt vad rollen ger. 

Se följande artiklar för mer information:

- [Administratörsroller för klassiska prenumerationer, Azure RBAC-roller och administratörsroller för Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Vad är RBAC för Azure-resurser?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Använd RBAC för att hantera åtkomst](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)

## <a name="next-steps"></a>Nästa steg

- [Utveckla med Media Services v3-API: er](media-services-apis-overview.md)
- [Hämta innehåll viktiga princip med hjälp av Media Services .NET](get-content-key-policy-dotnet-howto.md)
