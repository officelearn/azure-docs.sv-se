---
title: Använda Azure-portalen för att få åtkomst till blob eller köa data - Azure Storage
description: När du använder blob eller kön data med hjälp av Azure-portalen, portalen gör begäranden till Azure Storage under försättsbladen. Dessa begäranden till Azure Storage kan autentiseras och auktoriseras med hjälp av antingen Azure AD-konto eller åtkomstnyckel för lagringskontot.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5a4426b665c58dd2c0bf7f5b5b41ebfca158e782
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444728"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Använda Azure portal för att få åtkomst till blob eller kön data

När du får åtkomst till blob eller kön data med hjälp av den [Azure-portalen](https://portal.azure.com), portalen gör begäranden till Azure Storage under försättsbladen. Dessa begäranden till Azure Storage kan autentiseras och auktoriseras med hjälp av antingen Azure AD-konto eller åtkomstnyckel för lagringskontot. Portalen meddelar vilken autentiseringsmetod som du använder och gör att du kan växla mellan två om du har de behörigheter som krävs.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Behörigheter som krävs att komma åt data i blob eller en kö

Beroende på hur du vill autentisera åtkomst till blob eller kön data i Azure-portalen, behöver du särskilda behörigheter. I de flesta fall finns dessa behörigheter via rollbaserad åtkomstkontroll (RBAC). Läs mer om RBAC [vad är rollbaserad åtkomstkontroll (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Åtkomstnyckel

Om du vill få åtkomst till blob-och kön med åtkomstnyckeln för kontot, måste du ha en RBAC-roll tilldelad till dig som innehåller instruktionen RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Den här RBAC-rollen kan vara en inbyggd eller en anpassad roll. Inbyggda roller som har stöd för **Microsoft.Storage/storageAccounts/listkeys/action** omfattar:

- Azure Resource Manager [ägare](../../role-based-access-control/built-in-roles.md#owner) roll
- Azure Resource Manager [deltagare](../../role-based-access-control/built-in-roles.md#contributor) roll
- Den [Lagringskontodeltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor) roll

När du försöker komma åt data blob eller kön i Azure-portalen, kontrollerar portalen först om du har tilldelats en roll med **Microsoft.Storage/storageAccounts/listkeys/action**. Om du har tilldelats en roll med den här åtgärden, använder portalen kontonyckeln för åtkomst till blob-och kö. Om du inte har tilldelats en roll med den här åtgärden, försöker portalen komma åt data med hjälp av Azure AD-konto.

> [!NOTE]
> Administratörsroller klassiska prenumeration tjänstadministratör och delad administratör är detsamma som för Azure Resource Manager [ägare](../../role-based-access-control/built-in-roles.md#owner) roll. Den **ägare** innefattar alla åtgärder, inklusive den **Microsoft.Storage/storageAccounts/listkeys/action**, så att en användare med någon av dessa administrativa roller kan också komma åt blob och kö data med den kontonyckel. Mer information finns i [klassiska prenumeration administratörsroller](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Azure AD-konto

Om du vill komma åt blob eller kön data från Azure portal med din Azure AD-konto, vara båda av följande uttryck sanna för du:

- Du har tilldelats Azure Resource Manager [läsare](../../role-based-access-control/built-in-roles.md#reader) roll, som ett minimum begränsade till nivån av storage-konto eller högre. Den **läsare** rollen ger de mest begränsade behörigheterna, men en annan Azure Resource Manager-roll som ger åtkomst till hantering av lagringsresurser-konto är också tillåtet.
- Du har tilldelats antingen den inbyggda eller anpassade roll som ger åtkomst till BLOB-objekt eller data i kön.

Den **läsare** rolltilldelningen eller en annan Azure Resource Manager-rolltilldelning krävs så att användaren kan visa och navigera management lagringskontoresurserna i Azure-portalen. RBAC-roller som ger åtkomst till blob-eller kön ger inte åtkomst till hantering av lagringsresurser-konto. Om du vill komma åt data blob eller kö i portalen, måste användaren behörighet att navigera lagringskontoresurserna. Mer information om det här kravet finns i [tilldela rollen Läsare för portalåtkomst](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

De inbyggda roller som har stöd för åtkomst till dina data i blob eller kön är:

- [Storage Blob Data ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): För POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2 (förhandsversion).
- [Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Läs/Skriv/ta bort behörigheter för blobar.
- [Storage Blob Data-läsare](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Läsbehörighet för blobar.
- [Lagringsködata-deltagare](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Läs/Skriv/ta bort behörigheter för köer.
- [Lagringsködata-läsare](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Läsbehörighet för köer.
    
Anpassade roller har stöd för olika kombinationer av samma behörigheter som tillhandahålls av de inbyggda rollerna. Mer information om hur du skapar anpassade RBAC-roller finns i [anpassade roller för Azure-resurser](../../role-based-access-control/custom-roles.md) och [förstå rolldefinitioner för Azure-resurser](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Visa en lista över köer med en administratörsroll för klassiska prenumerationer stöds inte. Visa en lista över köer, måste en användare har tilldelats till dem i Azure Resource Manager **läsare** roll, den **Lagringsködata-läsare** roll, eller **Lagringsködata-deltagare** roll.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Gå till blobar eller köer i portalen

Om du vill visa data i blob eller en kö i portalen, navigera till den **översikt** för storage-konto och klicka på länkarna för **Blobar** eller **köer**. Du kan också navigera till den **Blobtjänst** och **kötjänst** avsnitt på menyn. 

![Gå till blob eller kön data i Azure portal](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Fastställa den aktuella autentiseringsmetoden

När du navigerar till en behållare eller en kö, anger den Azure-portalen om du använder åtkomstnyckeln eller Azure AD-konto för autentisering.

Exemplen i det här avsnittet visar åtkomst till en behållare och dess blobbar, men i portalen visas samma meddelande när du har åtkomst till en kö och dess meddelanden, eller visa en lista över köer.

### <a name="account-access-key"></a>Åtkomstnyckel

Om du autentiserar med åtkomstnyckeln för kontot, visas **åtkomstnyckel** anges som autentiseringsmetod i portalen:

![För närvarande kommer åt behållardata med kontonyckeln](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Om du vill växla till Azure AD-konto, klickar du på länken i bilden. Om du har rätt behörigheter via RBAC-roller som har tilldelats dig, kommer du att kunna fortsätta. Om du inte har rätt behörigheter, visas ett felmeddelande som liknar det följande:

![Felet som visas om Azure AD-konto inte har stöd för åtkomst](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Observera att inga blobbar visas i listan om din Azure AD-kontot saknar behörighet att visa dem. Klicka på den **växel att snabbtangent** länk för att använda åtkomstnyckeln för autentiseringen igen.

### <a name="azure-ad-account"></a>Azure AD-konto

Om du autentiserar med hjälp av Azure AD-konto, visas **Azure AD-användarkonto** anges som autentiseringsmetod i portalen:

![För närvarande kommer åt behållardata med Azure AD-konto](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Om du vill växla till med åtkomstnyckeln för kontot, klickar du på länken i bilden. Om du har åtkomst till nyckeln till kommer sedan du att kunna fortsätta. Om du saknar åtkomst till nyckeln till visas ett felmeddelande som liknar det följande:

![Felet som visas om du inte har åtkomst till kontonyckel](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Observera att inga blobbar visas i listan om du inte har åtkomst till nycklar för kontot. Klicka på den **växla till Azure AD-användarkonto** länk för att använda Azure AD-konto för autentisering igen.

## <a name="next-steps"></a>Nästa steg

- [Autentisera åtkomsten till Azure BLOB-objekt och köer med hjälp av Azure Active Directory](storage-auth-aad.md)
- [Bevilja åtkomst till Azure-behållare och köer med RBAC i Azure portal](storage-auth-aad-rbac-portal.md)
- [Bevilja åtkomst till Azure blob och kö data med RBAC med Azure CLI](storage-auth-aad-rbac-cli.md)
- [Bevilja åtkomst till Azure blob och kö data med RBAC med hjälp av PowerShell](storage-auth-aad-rbac-powershell.md)