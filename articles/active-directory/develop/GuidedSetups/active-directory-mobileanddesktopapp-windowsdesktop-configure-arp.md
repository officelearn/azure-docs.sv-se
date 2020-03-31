---
title: Komma igång med Azure AD v2 Windows Desktop - Config | Microsoft-dokument
description: Så här kan ett XAML-program (Windows Desktop .NET) hämta en åtkomsttoken och anropa ett API som skyddas av Azure Active Directory v2-slutpunkten.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 4c757b52e7378bffe4a4954022b131b772a337cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897680"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Lägga till programmets registreringsinformation i appen
I det här steget måste du lägga till program-ID:et i projektet.

1.  Öppna `App.xaml.cs` och byt `ClientId` ut raden som innehåller raden med:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Vad är nästa

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
