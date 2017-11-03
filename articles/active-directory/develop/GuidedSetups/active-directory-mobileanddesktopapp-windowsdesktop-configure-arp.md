---
title: "Azure AD v2 Windows Desktop komma igång - Config | Microsoft Docs"
description: "Hur ett program för Windows Desktop .NET (XAML) hämta en åtkomst-token och anropa ett API som skyddas av Azure Active Directory v2 slutpunkt."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 5e83171846517496e221f0a84565cdf7b77514df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Lägg till programmets registreringsinformation i appen
Du måste lägga till det program-Id till ditt projekt i det här steget.

1.  Öppna `App.xaml.cs` och Ersätt den rad som innehåller den `ClientId` med:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Vad är nästa

[Testa och validera](active-directory-mobileanddesktopapp-windowsdesktop-test.md)
