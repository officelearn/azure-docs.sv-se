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
ms.openlocfilehash: 2d641287bcf095f13719667a91b7f61295309b5d
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430365"
---
> [!NOTE]
> - Förhandsversionen av Azure AD-autentisering för blobbar och köer är endast avsedd för icke-produktion användning. Produktion servicenivåavtal (SLA) är inte tillgängliga. Om Azure AD-autentisering inte stöds ännu för ditt scenario, fortsätta att använda auktorisering för delad nyckel eller SAS-token i dina program.
>
> - RBAC-rolltilldelningar kan ta upp till fem minuter att sprida i förhandsversionen.
>
> - Du måste använda HTTPS för att autentisera med Azure AD när du anropar åtgärder för blob och kö.
>
> - Azure-portalen stöder nu användning av autentiseringsuppgifter för Azure AD att läsa och skriva blob-data som en del av förhandsversionen.
> 
> - Azure-portalen stöder för närvarande inte använder Azure AD-autentiseringsuppgifter för att läsa och skriva data i kön. Kön data nås via dina lagringskontonycklar.
>
> - [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) används för närvarande din lagringskontonyckel för att komma åt data för blob och kö.
>
> - Azure Files stöder autentisering med Azure AD via SMB för domänanslutna datorer endast (förhandsversion). Läs om hur du använder Azure AD via SMB för Azure Files i [översikt av Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion)](../articles/storage/files/storage-files-active-directory-overview.md).



