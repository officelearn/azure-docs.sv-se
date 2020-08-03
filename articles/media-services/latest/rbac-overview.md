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
ms.openlocfilehash: c75b6e67932cfd26a3374eab3f3efa34ceade577
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504491"
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

Den [inbyggda roll definitions](../../role-based-access-control/built-in-roles.md) artikeln visar exakt vad rollen beviljar. 

Mer information finns i följande artiklar:

- [Klassiska administratörs roller för prenumeration, Azure-roller och Azure AD-administratörskonton](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Använd RBAC för att hantera åtkomst](../../role-based-access-control/role-assignments-rest.md)
- [Åtgärder för Media Services Resource Provider](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Nästa steg

- [Utveckla med Media Services v3-API: er](media-services-apis-overview.md)
- [Hämta princip för innehålls nyckel med Media Services .NET](get-content-key-policy-dotnet-howto.md)
