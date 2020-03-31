---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806598"
---
## <a name="install-client-library-packages"></a>Installera klientbibliotekspaket

> [!NOTE]
> De exempel som visas här använder Azure Storage-klientbiblioteket version 12. Klientbiblioteket version 12 är en del av Azure SDK. Mer information om Azure SDK finns i Azure SDK-databasen på [GitHub](https://github.com/Azure/azure-sdk).

Om du vill installera Blob-lagringspaketet kör du följande kommando från NuGet package manager-konsolen:

```powershell
Install-Package Azure.Storage.Blobs
```

Exemplen som visas här använder också den senaste versionen av [Azure Identity-klientbiblioteket för .NET för](https://www.nuget.org/packages/Azure.Identity/) att autentisera med Azure AD-autentiseringsuppgifter. Om du vill installera paketet kör du följande kommando från NuGet package manager-konsolen:

```powershell
Install-Package Azure.Identity
```
