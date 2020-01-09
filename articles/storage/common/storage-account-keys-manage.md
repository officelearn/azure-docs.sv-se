---
title: Hantera kontots åtkomstnycklar
titleSuffix: Azure Storage
description: Lär dig hur du visar, hanterar och roterar åtkomst nycklar för lagrings kontot.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.openlocfilehash: 68d0ef14a7b5967d6a4535da85d32128c3a12a19
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486734"
---
# <a name="manage-storage-account-access-keys"></a>Hantera åtkomst nycklar för lagrings konton

När du skapar ett lagrings konto genererar Azure åtkomst nycklar för 2 512-bitars lagrings konto. Dessa nycklar kan användas för att ge åtkomst till data i ditt lagrings konto via autentisering med delad nyckel.

Microsoft rekommenderar att du använder Azure Key Vault för att hantera dina åtkomst nycklar och att du regelbundet roterar och återskapar dina nycklar. Med hjälp av Azure Key Vault är det enkelt att rotera dina nycklar utan avbrott i dina program. Du kan också rotera dina nycklar manuellt.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Visa åtkomst nycklar och anslutnings sträng

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Använd Azure Key Vault för att hantera dina åtkomst nycklar

Microsoft rekommenderar att du använder Azure Key Vault för att hantera och rotera dina åtkomst nycklar. Ditt program kan komma åt dina nycklar i Key Vault på ett säkert sätt, så att du kan undvika att lagra dem med program koden. Mer information om hur du använder Key Vault för nyckel hantering finns i följande artiklar:

- [Hantera lagrings konto nycklar med Azure Key Vault och PowerShell](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [Hantera lagrings konto nycklar med Azure Key Vault och Azure CLI](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Rotera åtkomst nycklar manuellt

Microsoft rekommenderar att du roterar dina åtkomst nycklar regelbundet för att skydda ditt lagrings konto. Använd om möjligt Azure Key Vault för att hantera dina åtkomst nycklar. Om du inte använder Key Vault måste du rotera dina nycklar manuellt.

Två åtkomst nycklar tilldelas så att du kan rotera dina nycklar. Att ha två nycklar säkerställer att ditt program behåller åtkomsten till Azure Storage under hela processen.

> [!WARNING]
> Återskapande av åtkomst nycklar kan påverka alla program eller Azure-tjänster som är beroende av lagrings konto nyckeln. Alla klienter som använder konto nyckeln för att komma åt lagrings kontot måste uppdateras för att använda den nya nyckeln, inklusive Media Services, moln, skriv bords-och mobil program och grafiska användar gränssnitts program för Azure Storage, till exempel [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

Följ den här processen för att rotera dina lagrings konto nycklar:

1. Uppdatera anslutnings strängarna i program koden för att använda den sekundära nyckeln.
2. Återskapa lagringskontots primära åtkomstnyckel. Klicka på **Återskapa KEY1**på bladet **åtkomst nycklar** i Azure Portal och klicka sedan på **Ja** för att bekräfta att du vill generera en ny nyckel.
3. Uppdatera anslutningssträngarna i koden så att de refererar till den nya primärnyckeln.
4. Återskapa den sekundära åtkomstnyckeln på samma sätt.

> [!NOTE]
> Microsoft rekommenderar att du bara använder en av nycklarna i alla dina program på samma tidpunkt. Om du använder nyckel 1 på vissa platser och nyckel 2 i andra kommer du inte att kunna rotera dina nycklar utan att några program förlorar åtkomst.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Storage-kontot](storage-account-overview.md)
- [skapar ett lagringskonto](storage-quickstart-create-account.md)
