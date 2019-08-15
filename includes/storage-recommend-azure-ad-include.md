---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 825a9f5668cc80f1306d74623db2ea54614ba4a3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985391"
---
> [!TIP]
> Azure Storage stöder auktorisering av förfrågningar till blob-och Queue Storage med hjälp av Azure Active Directory (Azure AD). Auktorisering av användare eller program med hjälp av en OAuth 2,0-token som returnerades av Azure AD ger överlägsen säkerhet och lätt att använda för autentisering med delad nyckel. Med Azure AD behöver du inte lagra konto åtkomst nyckeln med din kod och riskerar att innebära potentiella säkerhets risker.
>
> Dessutom stöder Azure Storage användar delegeringens signatur för delad åtkomst (SAS) för Blob Storage. Användarens Delegerings-sa är signerade med autentiseringsuppgifter för Azure AD. När program designen kräver delade åtkomst-signaturer för åtkomst till Blob Storage, använder du autentiseringsuppgifter för Azure AD för att skapa en användar Delegerings-SAS för överlägsen säkerhet.
>
> Microsoft rekommenderar att du använder Azure AD med dina Azure Storage-program när det är möjligt. Mer information finns i [bevilja åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
