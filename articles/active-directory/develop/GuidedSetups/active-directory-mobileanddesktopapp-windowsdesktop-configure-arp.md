---
title: Komma igång med Azure AD v2 Windows Desktop - Config
description: Så här kan ett XAML-program (Windows Desktop .NET) hämta en åtkomsttoken och anropa ett API som skyddas av Azure Active Directory v2-slutpunkten.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: d82f9beecb1b558fca094c31f8c6718c990debd1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885777"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Lägga till programmets registreringsinformation i appen
I det här steget måste du lägga till program-ID:et i projektet.

1.  Öppna `App.xaml.cs` och byt `ClientId` ut raden som innehåller raden med:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Vad är nästa

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
