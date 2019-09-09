---
title: Använd Azure Portal för att komma åt BLOB-eller Queue data-Azure Storage
description: När du har åtkomst till BLOB-eller Queue-data med hjälp av Azure Portal, gör portalen förfrågningar till Azure Storage under försättsblad. Dessa förfrågningar till Azure Storage kan autentiseras och auktoriseras med hjälp av antingen ditt Azure AD-konto eller lagrings kontots åtkomst nyckel.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8d19724883e6c2b3630de6100b1b4ca9fd9250a1
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813364"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Använd Azure Portal för att få åtkomst till BLOB-eller Queue-data

När du har åtkomst till BLOB-eller Queue-data med hjälp av [Azure Portal](https://portal.azure.com), gör portalen förfrågningar till Azure Storage under försättsblad. En begäran till Azure Storage kan godkännas med hjälp av antingen ditt Azure AD-konto eller lagrings kontots åtkomst nyckel. Portalen visar vilken metod du använder och gör att du kan växla mellan de två om du har rätt behörighet.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Behörigheter som krävs för att få åtkomst till BLOB-eller Queue data

Beroende på hur du vill ge åtkomst till BLOB-eller Queue-data i Azure Portal måste du ha vissa behörigheter. I de flesta fall tillhandahålls dessa behörigheter via rollbaserad åtkomst kontroll (RBAC). Mer information om RBAC finns i [Vad är rollbaserad åtkomst kontroll (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Konto åtkomst nyckel

Om du vill komma åt blob-och Queue-data med åtkomst nyckeln för kontot måste du ha en RBAC-roll som är tilldelad till dig som innehåller RBAC-åtgärden **Microsoft. Storage/storageAccounts/listnycklar/Action**. Den här RBAC-rollen kan vara en inbyggd eller anpassad roll. Inbyggda roller som stöder **Microsoft. Storage/storageAccounts/listnycklar/Action** är:

- Rollen Azure Resource Manager [ägare](../../role-based-access-control/built-in-roles.md#owner)
- Rollen Azure Resource Manager [Contributor](../../role-based-access-control/built-in-roles.md#contributor)
- Rollen [lagrings konto deltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

När du försöker komma åt BLOB-eller Queue-data i Azure Portal, kontrollerar portalen först om du har tilldelats en roll med **Microsoft. Storage/storageAccounts/listnycklar/Action**. Om du har tilldelats en roll med den här åtgärden använder portalen konto nyckeln för att komma åt blob-och Queue-data. Om du inte har tilldelats en roll med den här åtgärden försöker portalen komma åt data med ditt Azure AD-konto.

> [!NOTE]
> Administratör och medadministratör för rollen administratör i den klassiska prenumerationen är motsvarigheten till Azure Resource Manager- [ägarens](../../role-based-access-control/built-in-roles.md#owner) roll. **Ägar** rollen innehåller alla åtgärder, inklusive **Microsoft. Storage/storageAccounts/listnycklar/Action**, så en användare med någon av dessa administrativa roller kan också komma åt blob-och Queue-data med konto nyckeln. Mer information finns i [klassisk prenumerations administratörs roller](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Azure AD-konto

För att få åtkomst till BLOB-eller Queue-data från Azure Portal med ditt Azure AD-konto måste båda följande satser vara sanna för dig:

- Du har tilldelats rollen som Azure Resource Managers [läsare](../../role-based-access-control/built-in-roles.md#reader) , minst begränsat till lagrings kontots nivå eller högre. Rollen **läsare** ger mest begränsade behörigheter, men en annan Azure Resource Manager roll som beviljar åtkomst till lagrings kontots hanterings resurser är också acceptabel.
- Du har tilldelats antingen en inbyggd eller anpassad roll som ger åtkomst till BLOB-eller Queue-data.

Roll **tilldelnings rollen eller** en annan Azure Resource Manager roll tilldelning krävs så att användaren kan visa och navigera i lagrings kontots hanterings resurser i Azure Portal. RBAC-rollerna som beviljar åtkomst till BLOB-eller Queue data ger inte åtkomst till lagrings kontots hanterings resurser. För att få åtkomst till BLOB-eller Queue-data i portalen behöver användaren behörighet att navigera i lagrings konto resurser. Mer information om det här kravet finns i [tilldela rollen läsare för åtkomst till portalen](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

De inbyggda rollerna som stöder åtkomst till BLOB-eller Queue-data är:

- [Ägare till Storage BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): För POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2.
- [Data deltagare i Storage BLOB](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Läs-/skriv-/borttagnings behörigheter för blobbar.
- [Storage BLOB data Reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Skrivskyddade behörigheter för blobbar.
- [Deltagare i Storage Queue-data](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Läs-/skriv-/borttagnings behörigheter för köer.
- [Data läsare för lagrings köer](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Skrivskyddade behörigheter för köer.
    
Anpassade roller har stöd för olika kombinationer av samma behörigheter som tillhandahålls av de inbyggda rollerna. Mer information om hur du skapar anpassade RBAC-roller finns i [anpassade roller för Azure-resurser](../../role-based-access-control/custom-roles.md) och [förstå roll definitioner för Azure-resurser](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Det finns inte stöd för att Visa köer med en klassisk prenumerations administratörs roll. Om du vill visa en lista över köer måste användaren ha tilldelats rollen som Azure Resource Managers **läsare** , rollen **data läsare för lagrings kön** eller rollen **data deltagare i lagrings kön** .

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Navigera till blobbar eller köer i portalen

Om du vill visa BLOB-eller Queue-data i portalen navigerar du till **översikten** för ditt lagrings konto och klickar på länkarna för **blobbar** eller **köer**. Du kan också navigera till **BLOB service** och **kötjänst** avsnitt på menyn. 

![Navigera till BLOB-eller Queue-data i Azure Portal](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Fastställ den aktuella autentiseringsmetoden

När du navigerar till en behållare eller kö anger Azure Portal om du för närvarande använder kontots åtkomst nyckel eller ditt Azure AD-konto för att autentisera.

I exemplen i det här avsnittet visas hur du använder en behållare och dess blobbar, men portalen visar samma meddelande när du öppnar en kö och dess meddelanden, eller listar köer.

### <a name="account-access-key"></a>Konto åtkomst nyckel

Om du autentiserar med åtkomst nyckeln för kontot visas **åtkomst nyckeln** som anges som autentiseringsmetod i portalen:

![För närvarande åtkomst till behållar data med konto nyckeln](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Om du vill växla till att använda Azure AD-konto klickar du på länken som är markerad i bilden. Om du har rätt behörigheter via de RBAC-roller som har tilldelats dig kan du fortsätta. Men om du saknar rätt behörighet visas ett fel meddelande som liknar följande:

![Det gick inte att visa om Azure AD-kontot inte stöder åtkomst](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Observera att inga blobbar visas i listan om ditt Azure AD-konto saknar behörighet att visa dem. Klicka på länken **Växla till åtkomst för att komma åt nyckeln** för att använda åtkomst nyckeln för autentisering igen.

### <a name="azure-ad-account"></a>Azure AD-konto

Om du autentiserar med hjälp av ditt Azure AD-konto visas **Azure AD-användarkontot** som autentiseringsmetod i portalen:

![För närvarande åtkomst till behållar data med Azure AD-konto](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Om du vill växla till att använda kontots åtkomst nyckel klickar du på länken som är markerad i bilden. Om du har åtkomst till konto nyckeln kan du fortsätta. Men om du saknar åtkomst till konto nyckeln visas ett fel meddelande som liknar följande:

![Fel som visas om du inte har åtkomst till konto nyckeln](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Observera att inga blobbar visas i listan om du inte har åtkomst till konto nycklarna. Klicka på länken **Växla till Azure AD-användarkonto** för att använda ditt Azure AD-konto för autentisering igen.

## <a name="next-steps"></a>Nästa steg

- [Autentisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md)
- [Bevilja åtkomst till Azure-behållare och köer med RBAC i Azure Portal](storage-auth-aad-rbac-portal.md)
- [Bevilja åtkomst till blob- och ködata i Azure med RBAC med hjälp av Azure CLI](storage-auth-aad-rbac-cli.md)
- [Bevilja åtkomst till blob- och ködata i Azure med RBAC med hjälp av PowerShell](storage-auth-aad-rbac-powershell.md)
