---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5d0e919159569e7512b6e7dc0458a4a3e397943b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57251878"
---
> [!NOTE]
> - Förhandsversionen av Azure AD-autentisering för blobbar och köer är endast avsedd för icke-produktion användning. Produktion servicenivåavtal (SLA) är inte tillgängliga. Om Azure AD-autentisering inte stöds ännu för ditt scenario, fortsätta att använda auktorisering för delad nyckel eller SAS-token i dina program.
>
> - RBAC-rolltilldelningar kan ta upp till fem minuter att sprida i förhandsversionen.
>
> - Du måste använda HTTPS för att auktorisera blob och kö åtgärder med en OAuth-token.
>
> - Azure-portalen stöder nu användning av autentiseringsuppgifter för Azure AD att läsa och skriva blob och kö data, som en del av förhandsversionen av versionen. En användare måste tilldelas rollen Azure Resource Manager-läsare RBAC förutom rollen lämplig förhandsversionen för blob- eller kön för för att komma åt blob och kö data i Azure-portalen. Mer information finns i [bevilja åtkomst till Azure-behållare och köer med RBAC i Azure portal (förhandsversion)](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) används för närvarande din lagringskontonyckel för att komma åt data för blob och kö. OAuth-åtkomst har stöd för blobar.
>
> - Azure Files stöder autentisering med Azure AD via SMB för domänanslutna datorer endast (förhandsversion). Läs om hur du använder Azure AD via SMB för Azure Files i [översikt av Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion)](../articles/storage/files/storage-files-active-directory-overview.md).