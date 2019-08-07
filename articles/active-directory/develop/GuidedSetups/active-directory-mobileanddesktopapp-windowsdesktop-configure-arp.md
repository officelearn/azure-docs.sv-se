---
title: Azure AD v2 Windows Desktop Komma igång-config | Microsoft Docs
description: Hur ett Windows Desktop .NET-program (XAML) kan hämta en åtkomsttoken och anropa ett API som skyddas av Azure Active Directory v2-slutpunkten.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a058024f8d6bdf7399e222c134f9f24c4ddffee8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835555"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Lägg till programmets registrerings information i din app
I det här steget måste du lägga till program-ID: t i projektet.

1.  Öppna `App.xaml.cs` och ersätt raden `ClientId` som innehåller med:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Vad är nästa

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
