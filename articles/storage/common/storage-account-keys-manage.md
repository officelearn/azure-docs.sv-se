---
title: Hantera kontots åtkomstnycklar
titleSuffix: Azure Storage
description: Läs om hur du visar, hanterar och roterar åtkomstnycklarna för lagringskontot.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.openlocfilehash: 50c0980800bbc9b2951bf9107114c1a4d9265558
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454670"
---
# <a name="manage-storage-account-access-keys"></a>Hantera åtkomstnycklar för lagringskonto

När du skapar ett lagringskonto genererar Azure två 512-bitars åtkomstnycklar för lagringskonto. Dessa nycklar kan användas för att auktorisera åtkomst till data i ditt lagringskonto via auktorisering av delade nycklar.

Microsoft rekommenderar att du använder Azure Key Vault för att hantera dina åtkomstnycklar och att du regelbundet roterar och återskapar dina nycklar. Med Hjälp av Azure Key Vault blir det enkelt att rotera dina nycklar utan avbrott i dina program. Du kan också rotera tangenterna manuellt.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Visa åtkomstnycklar och anslutningssträng

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Använda Azure Key Vault för att hantera dina åtkomstnycklar

Microsoft rekommenderar att du använder Azure Key Vault för att hantera och rotera dina åtkomstnycklar. Ditt program kan komma åt dina nycklar på ett säkert sätt i Key Vault, så att du kan undvika att lagra dem med din programkod. Mer information om hur du använder Key Vault för nyckelhantering finns i följande artiklar:

- [Hantera lagringskontonycklar med Azure Key Vault och PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Hantera lagringskontonycklar med Azure Key Vault och Azure CLI](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Rotera åtkomsttangenter manuellt

Microsoft rekommenderar att du roterar dina åtkomstnycklar regelbundet för att skydda ditt lagringskonto. Om möjligt kan du använda Azure Key Vault för att hantera dina åtkomstnycklar. Om du inte använder Key Vault måste du rotera tangenterna manuellt.

Två åtkomstnycklar tilldelas så att du kan rotera tangenterna. Med två nycklar säkerställer att ditt program behåller åtkomsten till Azure Storage under hela processen.

> [!WARNING]
> Återskapa dina åtkomstnycklar kan påverka alla program eller Azure-tjänster som är beroende av lagringskontonyckeln. Alla klienter som använder kontonyckeln för att komma åt lagringskontot måste uppdateras för att kunna använda den nya nyckeln, inklusive medietjänster, moln-, skrivbords- och mobilprogram och grafiska användargränssnittsprogram för Azure Storage, till exempel [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

Följ den här processen för att rotera dina lagringskontonycklar:

1. Uppdatera anslutningssträngarna i programkoden om du vill använda den sekundära nyckeln.
2. Återskapa lagringskontots primära åtkomstnyckel. Klicka på **Återskapa nyckel1**på bladet **Åtkomstnycklar** i Azure-portalen och klicka sedan på **Ja** för att bekräfta att du vill generera en ny nyckel.
3. Uppdatera anslutningssträngarna i koden så att de refererar till den nya primärnyckeln.
4. Återskapa den sekundära åtkomstnyckeln på samma sätt.

> [!NOTE]
> Microsoft rekommenderar att du bara använder en av nycklarna i alla dina program samtidigt. Om du använder Nyckel 1 på vissa ställen och Nyckel 2 i andra, du vilja inte kunde rotera din nyckel utan något applicering förlora tillträde.

Om du vill rotera ett kontos åtkomstnycklar måste användaren antingen vara tjänstadministratör eller tilldelas en RBAC-roll som innehåller **Microsoft.Storage/storageAccounts/regeneratekey/action**. Några inbyggda RBAC-roller som innehåller den här åtgärden är rollerna **Ägare,** **Deltagare**och **Nyckeloperatörsroll för lagringskonto.** Mer information om rollen Tjänstadministratör finns i [Klassiska prenumerationsadministratörsroller, Azure RBAC-roller och Azure AD-roller](../../role-based-access-control/rbac-and-directory-admin-roles.md). Detaljerad information om inbyggda RBAC-roller för Azure Storage finns i **avsnittet Lagring** [i Azures inbyggda roller för Azure RBAC](../../role-based-access-control/built-in-roles.md#storage).

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure-lagringskonto](storage-account-overview.md)
- [skapar ett lagringskonto](storage-account-create.md)
