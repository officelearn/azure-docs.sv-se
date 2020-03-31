---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76118093"
---
## <a name="about-the-user-delegation-sas"></a>Om SAS-användardelegeringen

En SAS-token för åtkomst till en behållare eller blob kan skyddas med hjälp av azure AD-autentiseringsuppgifter eller en kontonyckel. En SAS som skyddas med Azure AD-autentiseringsuppgifter kallas en användardelegering SAS, eftersom OAuth 2.0-token som används för att signera SAS begärs för användarens räkning.

Microsoft rekommenderar att du använder Azure AD-autentiseringsuppgifter när det är möjligt som en säkerhetspraxis, i stället för att använda kontonyckeln, som lättare kan komprometteras. När programdesignen kräver signaturer för delad åtkomst använder du Azure AD-autentiseringsuppgifter för att skapa en SAS för användardelegering för överlägsen säkerhet. Mer information om SAS för användardelegering finns i [Skapa en SAS för användardelegation](/rest/api/storageservices/create-user-delegation-sas).

> [!CAUTION]
> Alla klienter som har en giltig SAS kan komma åt data i ditt lagringskonto enligt det SAS.Any client that hav a valid SAS can access data in your storage account as permitted by that SAS. Det är viktigt att skydda en SAS från skadlig eller oavsiktlig användning. Använd diskretion när du distribuerar en SAS och har en plan för att återkalla en komprometterade SAS.

Mer information om signaturer för delad åtkomst finns i [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SAS (Shared Access Signatures).](../articles/storage/common/storage-sas-overview.md)
