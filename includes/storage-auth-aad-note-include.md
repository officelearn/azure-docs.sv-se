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
ms.openlocfilehash: 0182df40a4e7815560a85e60fe9062ccd8001c18
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978800"
---
> [!NOTE]
> - Förhandsversionen av Azure AD-autentisering för blobbar och köer är endast avsedd för icke-produktion användning. Produktion servicenivåavtal (SLA) är inte tillgängliga. Om Azure AD-autentisering inte stöds ännu för ditt scenario, fortsätta att använda auktorisering för delad nyckel eller SAS-token i dina program.
>
> - RBAC-rolltilldelningar kan ta upp till fem minuter att sprida i förhandsversionen.
>
> - Du måste använda HTTPS för att auktorisera blob och kö åtgärder med en OAuth-token.
>
> - Azure-portalen stöder nu användning av autentiseringsuppgifter för Azure AD att läsa och skriva blob och kö data, som en del av förhandsversionen av versionen.
> 
> - [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) används för närvarande din lagringskontonyckel för att komma åt data för blob och kö. OAuth-åtkomst har stöd för blobar.
>
> - Azure Files stöder autentisering med Azure AD via SMB för domänanslutna datorer endast (förhandsversion). Läs om hur du använder Azure AD via SMB för Azure Files i [översikt av Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion)](../articles/storage/files/storage-files-active-directory-overview.md).