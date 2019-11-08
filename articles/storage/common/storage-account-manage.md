---
title: Hantera inställningar för lagrings konton i Azure Portal-Azure Storage | Microsoft Docs
description: Lär dig hur du hanterar inställningar för lagrings konton i Azure Portal, inklusive hur du konfigurerar inställningar för åtkomst kontroll, återskapar konto åtkomst nycklar, ändrar åtkomst nivå eller ändrar den typ av replikering som används av kontot. Lär dig också hur du tar bort ett lagrings konto i portalen.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 3c01cc870b20c8256b215eb700548e6cd69ad0d5
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749015"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Hantera inställningar för lagrings kontot i Azure Portal

Det finns flera olika inställningar för ditt lagrings konto i [Azure Portal](https://portal.azure.com). I den här artikeln beskrivs några av de här inställningarna och hur de används.

## <a name="access-control"></a>Åtkomstkontroll

Azure Storage stöder auktorisering med Azure Active Directory för Blob Storage och Queue Storage via rollbaserad åtkomst kontroll (RBAC). Mer information om auktorisering med Azure AD finns i [bevilja åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md).

Inställningarna för **åtkomst kontroll** i Azure Portal ger ett enkelt sätt att tilldela RBAC-roller till användare, grupper, tjänstens huvud namn och hanterade identiteter. Mer information om hur du tilldelar RBAC-roller finns i [Hantera åtkomst rättigheter till blob-och Queue-data med RBAC](storage-auth-aad-rbac.md).

## <a name="tags"></a>Taggar

Azure Storage stöder Azure Resource Manager taggar för att organisera dina Azure-resurser med en anpassad taxonomi. Du kan använda taggar för dina lagrings konton så att du kan gruppera dem i prenumerationen på ett logiskt sätt.

För lagrings konton är ett taggnamn begränsat till 128 tecken och ett taggvärde är begränsat till 256 tecken.

Mer information finns i [använda taggar för att ordna dina Azure-resurser](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Åtkomstnycklar

När du skapar ett lagrings konto genererar Azure åtkomst nycklar för 2 512-bitars lagrings konto. Dessa nycklar kan användas för att ge åtkomst till ditt lagrings konto via en delad nyckel. Du kan rotera och återskapa nycklarna utan avbrott i dina program, och Microsoft rekommenderar att du gör det regelbundet.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Visa konto nycklar och anslutnings sträng

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Återskapa åtkomstnycklar

Microsoft rekommenderar att du återskapar dina åtkomst nycklar regelbundet så att du kan skydda ditt lagrings konto. Två åtkomst nycklar tilldelas så att du kan rotera dina nycklar. När du roterar dina nycklar ser du till att ditt program behåller åtkomsten till Azure Storage under hela processen. 

> [!WARNING]
> Återskapande av åtkomst nycklar kan påverka alla program eller Azure-tjänster som är beroende av lagrings konto nyckeln. Alla klienter som använder konto nyckeln för att komma åt lagrings kontot måste uppdateras för att använda den nya nyckeln, inklusive Media Services, moln, skriv bords-och mobil program och grafiska användar gränssnitts program för Azure Storage, till exempel [Azure Storage Explorer ](https://azure.microsoft.com/features/storage-explorer/).

Följ den här processen för att rotera dina lagrings konto nycklar:

1. Uppdatera anslutnings strängarna i program koden för att använda den sekundära nyckeln.
2. Återskapa lagringskontots primära åtkomstnyckel. Klicka på **Återskapa KEY1**på bladet **åtkomst nycklar** i Azure Portal och klicka sedan på **Ja** för att bekräfta att du vill generera en ny nyckel.
3. Uppdatera anslutningssträngarna i koden så att de refererar till den nya primärnyckeln.
4. Återskapa den sekundära åtkomstnyckeln på samma sätt.

## <a name="account-configuration"></a>Konto konfiguration

När du har skapat ett lagrings konto kan du ändra dess konfiguration. Du kan till exempel ändra hur dina data replikeras eller ändra kontots åtkomst nivå från frekvent till låg frekvent. I [Azure Portal](https://portal.azure.com)navigerar du till ditt lagrings konto och letar upp och klickar på **konfiguration** under **Inställningar** för att visa och/eller ändra konto konfigurationen.

Att ändra lagrings kontots konfiguration kan leda till kostnader som har lagts till. Mer information finns på sidan med [Azure Storage priser](https://azure.microsoft.com/pricing/details/storage/) .

## <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto

Om du vill ta bort ett lagringskonto som du inte längre använder navigerar du till lagringskontot på [Azure Portal](https://portal.azure.com) och klickar på **Ta bort**. Om du tar bort ett lagringskonto tas hela kontot bort, inklusive alla data på kontot.

> [!WARNING]
> Det går inte att återställa ett borttaget lagringskonto eller att hämta innehåll som det innehöll före borttagningen. Var noga med att säkerhetskopiera allt som du vill spara innan du tar bort kontot. Detta gäller även alla resurser i kontot. När du tar bort en blobb, tabell, kö eller fil tas den bort permanent.
> 

Om du försöker ta bort ett lagringskonto som är kopplat till en virtuell Azure-dator kan du få ett felmeddelande om att lagringskontot fortfarande används. Information om hur du felsöker det här felet finns i [Felsöka fel när du tar bort lagringskonton](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Storage-kontot](storage-account-overview.md)
- [Skapa ett lagringskonto](storage-quickstart-create-account.md)
