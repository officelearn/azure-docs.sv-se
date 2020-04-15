---
title: Använda Azure-portalen för att komma åt blob- eller ködata
titleSuffix: Azure Storage
description: När du kommer åt blob- eller ködata med Azure-portalen gör portalen förfrågningar till Azure Storage under omslagen. Dessa begäranden till Azure Storage kan autentiseras och auktoriseras med antingen ditt Azure AD-konto eller åtkomstnyckeln för lagringskontot.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/14/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e556e21238db5de7dddce13ea912dae30723fe8c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383655"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Använda Azure-portalen för att komma åt blob- eller ködata

När du kommer åt blob- eller ködata med [Azure-portalen](https://portal.azure.com)gör portalen begäranden till Azure Storage under omslagen. En begäran till Azure Storage kan auktoriseras med antingen ditt Azure AD-konto eller åtkomstnyckeln för lagringskontot. Portalen visar vilken metod du använder och gör att du kan växla mellan de två om du har rätt behörighet.  

Du kan också ange hur du auktoriserar en enskild blob-överföringsåtgärd i Azure-portalen. Som standard använder portalen den metod som du redan använder för att auktorisera en blob-uppladdningsåtgärd, men du har möjlighet att ändra den här inställningen när du laddar upp en blob.

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Behörigheter som behövs för att komma åt blob- eller ködata

Beroende på hur du vill auktorisera åtkomst till blob- eller ködata i Azure-portalen behöver du specifika behörigheter. I de flesta fall tillhandahålls dessa behörigheter via rollbaserad åtkomstkontroll (RBAC). Mer information om RBAC finns i [Vad är rollbaserad åtkomstkontroll (RBAC)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Använda kontoåtkomstnyckeln

Om du vill komma åt blob- och ködata med kontoåtkomstnyckeln måste du ha tilldelat en RBAC-roll som innehåller RBAC-åtgärden **Microsoft.Storage/storageAccounts/listkeys/action**. Den här RBAC-rollen kan vara en inbyggd eller en anpassad roll. Inbyggda roller som stöder **Microsoft.Storage/storageAccounts/listkeys/action** inkluderar:

- Ägarrollen [för](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- [Deltagarrollen](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- [Rollen Deltagare i lagringskontot](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

NÃ¤r du fÃ¶rsÃ¶k att komma åt blob- eller ködata i Azure-portalen kontrollerar portalen först om du har tilldelats en roll med **Microsoft.Storage/storageAccounts/listkeys/action**. Om du har tilldelats en roll med den här åtgärden använder portalen kontonyckeln för åtkomst till blob- och ködata. Om du inte har tilldelats en roll med den här åtgärden försöker portalen komma åt data med ditt Azure AD-konto.

> [!NOTE]
> De klassiska prenumerationsadministratörsrollerna Tjänstadministratör och Medadministratör innehåller motsvarigheten till azure Resource [Manager-ägarrollen.](../../role-based-access-control/built-in-roles.md#owner) **Ägarrollen** innehåller alla åtgärder, inklusive **Microsoft.Storage/storageAccounts/listkeys/action**, så att en användare med en av dessa administrativa roller också kan komma åt blob- och ködata med kontonyckeln. Mer information finns i [Administratörsroller för klassiska prenumerationer, Azure RBAC-roller och administratörsroller för Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Använda ditt Azure AD-konto

Om du vill komma åt blob- eller ködata från Azure-portalen med ditt Azure AD-konto måste båda följande satser vara sanna för dig:

- Du har tilldelats azure Resource Manager [Reader-rollen,](../../role-based-access-control/built-in-roles.md#reader) på ett minimum, begränsad till lagringskontots nivå eller högre. **Rollen Läsare** ger de mest begränsade behörigheterna, men en annan Azure Resource Manager-roll som ger åtkomst till lagringskontohanteringsresurser är också godtagbar.
- Du har tilldelats antingen en inbyggd eller anpassad roll som ger åtkomst till blob- eller ködata.

**Rolltilldelningen för läsare** eller en annan Azure Resource Manager-rolltilldelning är nödvändig så att användaren kan visa och navigera resurser för hantering av lagringskonto i Azure-portalen. RBAC-roller som ger åtkomst till blob- eller ködata ger inte åtkomst till lagringskontohanteringsresurser. För att komma åt blob- eller ködata i portalen behöver användaren behörighet för att navigera i lagringskontoresurser. Mer information om det här kravet finns i [Tilldela rollen Läsare för portalåtkomst](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

De inbyggda rollerna som stöder åtkomst till blob- eller ködata inkluderar:

- [Lagring blob dataägare:](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)För POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2.
- [Storage Blob Data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Läs/skriv/ta bort behörigheter för blobbar.
- [Lagring blob dataläsare:](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)Skrivskyddade behörigheter för blobbar.
- [Storage Queue Data Contributor](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Läs/skriv/ta bort behörigheter för köer.
- [Dataläsare för lagringskö:](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader)Skrivskyddade behörigheter för köer.

Anpassade roller kan stödja olika kombinationer av samma behörigheter som tillhandahålls av de inbyggda rollerna. Mer information om hur du skapar anpassade RBAC-roller finns i [Anpassade roller för Azure-resurser](../../role-based-access-control/custom-roles.md) och [Förstå rolldefinitioner för Azure-resurser](../../role-based-access-control/role-definitions.md).

Det går inte att lista köer med en klassisk prenumerationsadministratörsroll. Om du vill visa en lista över köer måste en användare ha tilldelat dem rollen Azure Resource Manager **Reader,** rollen **Dataläsare för lagringskö** eller rollen **Data contributor för lagringskö.**

> [!IMPORTANT]
> Förhandsversionen av Storage Explorer i Azure-portalen stöder inte att använda Azure AD-autentiseringsuppgifter för att visa och ändra blob- eller ködata. Storage Explorer i Azure-portalen använder alltid kontonycklarna för att komma åt data. Om du vill använda Storage Explorer i Azure-portalen måste du tilldelas en roll som innehåller **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Navigera till blobbar eller köer i portalen

Om du vill visa blob- eller ködata i portalen navigerar du till **översikten** för ditt lagringskonto och klickar på länkarna för **Blobbar** eller **Köer**. Alternativt kan du navigera till avsnitten **Blob-tjänst** **och Kötjänst** på menyn. 

![Navigera till blob- eller ködata i Azure-portalen](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Fastställa den aktuella autentiseringsmetoden

När du navigerar till en behållare eller en kö anger Azure-portalen om du för närvarande använder kontoåtkomstnyckeln eller ditt Azure AD-konto för att autentisera.

Exemplen i det här avsnittet visar åtkomst till en behållare och dess blobbar, men portalen visar samma meddelande när du använder en kö och dess meddelanden, eller lista köer.

### <a name="authenticate-with-the-account-access-key"></a>Autentisera med kontoåtkomstnyckeln

Om du autentiserar med hjälp av kontoåtkomstnyckeln visas **Åtkomstnyckel** som anges som autentiseringsmetod i portalen:

![Åtkomst till behållardata med kontonyckeln](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Om du vill växla till att använda Azure AD-konto klickar du på länken som är markerad i avbildningen. Om du har rätt behörighet via RBAC-rollerna som har tilldelats dig kan du fortsätta. Om du saknar rätt behörighet visas dock ett felmeddelande som följande:

![Fel som visas om Azure AD-konto inte stöder åtkomst](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Observera att inga blobbar visas i listan om ditt Azure AD-konto saknar behörigheter för att visa dem. Klicka på **länken Växla för att komma åt nyckellänken** för att använda åtkomstnyckeln för autentisering igen.

### <a name="authenticate-with-your-azure-ad-account"></a>Autentisera med ditt Azure AD-konto

Om du autentiserar med ditt Azure **AD-konto visas Azure AD-användarkontot** som anges som autentiseringsmetod i portalen:

![Åtkomst till behållardata med Azure AD-konto](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Om du vill växla till att använda åtkomstnyckeln för kontot klickar du på länken som är markerad i bilden. Om du har tillgång till kontonyckeln kan du fortsätta. Om du saknar åtkomst till kontonyckeln visas dock ett felmeddelande som följande:

![Fel visas om du inte har tillgång till kontonyckel](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Observera att inga blobbar visas i listan om du inte har tillgång till kontonycklarna. Klicka på länken **Växla till Azure AD-användarkonto** om du vill använda ditt Azure AD-konto för autentisering igen.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Ange hur du auktoriserar en blob-uppladdningsåtgärd

När du laddar upp en blob från Azure-portalen kan du ange om du vill autentisera och godkänna åtgärden med kontoåtkomstnyckeln eller med dina Azure AD-autentiseringsuppgifter. Som standard använder portalen den aktuella autentiseringsmetoden, som visas i [Bestäm den aktuella autentiseringsmetoden](#determine-the-current-authentication-method).

Så här anger du hur du auktoriserar en blob-uppladdning:

1. I Azure-portalen navigerar du till behållaren där du vill ladda upp en blob.
1. Välj knappen **Ladda upp**.
1. Expandera avsnittet **Avancerat** om du vill visa de avancerade egenskaperna för blobben.
1. I fältet **Autentiseringstyp** anger du om du vill auktorisera överföringen med hjälp av ditt Azure AD-konto eller med kontoåtkomstnyckeln, som visas i följande bild:

    :::image type="content" source="media/storage-access-blobs-queues-portal/auth-blob-upload.png" alt-text="Skärmbild som visar hur du ändrar auktoriseringsmetod vid blob-uppladdning":::

## <a name="next-steps"></a>Nästa steg

- [Autentisera åtkomst till Azure-blobbar och köer med Azure Active Directory](storage-auth-aad.md)
- [Bevilja åtkomst till Azure-behållare och köer med RBAC i Azure-portalen](storage-auth-aad-rbac-portal.md)
- [Bevilja åtkomst till blob- och ködata i Azure med RBAC med hjälp av Azure CLI](storage-auth-aad-rbac-cli.md)
- [Bevilja åtkomst till blob- och ködata i Azure med RBAC med hjälp av PowerShell](storage-auth-aad-rbac-powershell.md)
