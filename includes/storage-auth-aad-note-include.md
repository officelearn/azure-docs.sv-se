---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 50217829be7aff3bcdbf417f19fff9f1fd7c6901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372682"
---
> [!NOTE]
> - RBAC-rolltilldelningar kan ta upp till två minuter att sprida.
>
> Du måste använda HTTPS för att auktorisera blob och kö åtgärder med en OAuth-token.
>
> - Azure-portalen stöder nu användning av autentiseringsuppgifter för Azure AD att läsa och skriva blob och kö data, som en del av förhandsversionen av versionen. Om du vill få åtkomst till blob och kö data i Azure-portalen, en användare måste tilldelas Azure Resource Manager **läsare** roll, förutom rätt roll för blob-eller kön. Mer information finns i [bevilja åtkomst till Azure-behållare och köer med RBAC i Azure-portalen](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) används för närvarande din lagringskontonyckel för att komma åt data för blob och kö. Om nyckeln inte är tillgänglig används Azure AD-auktorisering för åtkomst till blobar. Azure AD-auktorisering stöds inte för närvarande för köer. 
>
> - Azure Files stöder autentisering med Azure AD via SMB för domänanslutna datorer endast (förhandsversion). Läs om hur du använder Azure AD via SMB för Azure Files i [översikt av Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion)](../articles/storage/files/storage-files-active-directory-overview.md).