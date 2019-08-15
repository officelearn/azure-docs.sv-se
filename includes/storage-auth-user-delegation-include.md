---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 2ea936ba489024648583cf22c5ab73ae06bb1537
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011975"
---
## <a name="about-the-user-delegation-sas-preview"></a>Om användar Delegerings-SAS (för hands version)

En SAS-token för åtkomst till en behållare eller BLOB kan skyddas med hjälp av antingen Azure AD-autentiseringsuppgifter eller en konto nyckel. En SAS som skyddas med Azure AD-autentiseringsuppgifter kallas för en användar Delegerings-SAS, eftersom OAuth 2,0-token som används för att signera SAS begärs för användarens räkning.

Microsoft rekommenderar att du använder Azure AD-autentiseringsuppgifter när det är möjligt som en säkerhets åtgärd, i stället för att använda konto nyckeln, vilket kan vara enklare att avslöja. När program designen kräver signaturer för delad åtkomst använder du autentiseringsuppgifter för Azure AD för att skapa en användar Delegerings-SAS för överlägsen säkerhet. Mer information om sa för användar delegering finns i [skapa en användar](/rest/api/storageservices/create-a-user-delegation-sas)Delegerings-SAS.

> [!NOTE]
> För hands versionen av användar delegering SAS är endast avsedd för användning utan produktion.

> [!CAUTION]
> Alla klienter som har en giltig SAS kan komma åt data i ditt lagrings konto som tillåts av denna SAS. Det är viktigt att skydda en SAS från skadlig eller oavsiktlig användning. Använd vad som helst när du distribuerar en SAS och har en plan för att återkalla en komprometterad SAS.

Mer information om signaturer för delad åtkomst finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../articles/storage/common/storage-sas-overview.md).
