---
title: Azure AD v2 Windows Desktop komma igång - Config | Microsoft Docs
description: Hur ett Windows Desktop .NET (XAML)-program kan få en åtkomsttoken och anropa ett API som skyddas av Azure Active Directory v2-slutpunkten.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef32cef67ae5f32959e026157f5126647b01401d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540567"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Lägg till programmets registreringsinformation i din app
Du måste lägga till program-Id till ditt projekt i det här steget.

1.  Öppna `App.xaml.cs` och Ersätt den rad som innehåller den `ClientId` med:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Vad är nästa

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
