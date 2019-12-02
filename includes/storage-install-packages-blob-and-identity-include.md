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
ms.openlocfilehash: 0adf1280fa50e9ee594f3025dff70786f5ba2199
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666178"
---
## <a name="install-client-library-packages"></a>Installera klient biblioteks paket

> [!NOTE]
> Exemplen som visas här använder Azure Storage klient bibliotek version 12. Version 12-klient biblioteket är en del av Azure SDK. Mer information om Azure SDK finns i Azure SDK-lagringsplatsen på [GitHub](https://github.com/Azure/azure-sdk).

Installera Blob Storage-paketet genom att köra följande kommando från NuGet Package Manager-konsolen:

```powershell
Install-Package Azure.Storage.Blobs
```

Exemplen som visas här använder också den senaste versionen av [klient biblioteket för Azure Identity för .net](https://www.nuget.org/packages/Azure.Identity/) för att autentisera med Azure AD-autentiseringsuppgifter. Installera paketet genom att köra följande kommando från NuGet Package Manager-konsolen:

```powershell
Install-Package Azure.Identity
```

Mer information om hur du autentiserar med klient biblioteket för Azure Identity från Azure Storage finns i avsnittet **autentisera med Azure Identity Library** i [ge åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](/azure/storage/common/storage-auth-aad-msi?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).