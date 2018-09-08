---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/06/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3a3ee0cda3643ac73581f868e47905841d9f1563
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095599"
---
> [!IMPORTANT]
> - Förhandsversionen av Azure AD-autentisering för blobbar och köer är endast avsedd för icke-produktion användning. Produktion servicenivåavtal (SLA) är inte tillgängliga. Om Azure AD-autentisering inte stöds ännu för ditt scenario, fortsätta att använda auktorisering för delad nyckel eller SAS-token i dina program.
>
> - RBAC-rolltilldelningar kan ta upp till fem minuter att sprida i förhandsversionen.
>
> - Du måste använda HTTPS för att autentisera med Azure AD när du anropar åtgärder för blob och kö.
>
> - I förhandsversionen kan använder Azure-portalen inte autentiseringsuppgifter för Azure AD att läsa och skriva blob-och kö. Portalen fortsätter i stället förlita dig på åtkomstnyckeln för kontot. Om du vill visa eller uppdatera data för blob eller en kö i portalen, användaren måste tilldelas en RBAC-roll som omfattar [Microsoft.Storage/storageAccounts/listkeys/action](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role), som ger behörighet att anropa [Lagringskonton – Lista över nycklar](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/listkeys). Rollerna deltagare och läsare för blobbar och köer omfattar inte för närvarande den **listnycklar** åtgärden som en del av förhandsversionen av versionen och så ger inte åtkomst till data via Azure portal. Använd PowerShell eller Azure CLI för att utforska identitetsbaserad åtkomst till blob-och kön i förhandsutgåvan.
