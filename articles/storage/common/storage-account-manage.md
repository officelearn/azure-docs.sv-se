---
title: Hantera inställningar för lagringskonto i Azure portal – Azure Storage | Microsoft Docs
description: Lär dig mer om att hantera inställningarna för lagringskontot i Azure-portalen, inklusive hur du konfigurerar inställningar för åtkomstkontroll, återskapar åtkomstnycklar, ändring av åtkomstnivå eller ändra typ av replikering som används av kontot. Lär dig också att ta bort ett lagringskonto i portal.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 66bdc4bd1e17347419a6eccd7c9532db17b33001
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303481"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Hantera inställningar för lagringskonto i Azure portal

Det finns många olika inställningar för ditt lagringskonto i den [Azure-portalen](https://portal.azure.com). Den här artikeln beskriver några av de här inställningarna och hur de används.

## <a name="access-control"></a>Åtkomstkontroll

Azure Storage stöder auktorisering med Azure Active Directory för Blob storage och Queue storage via rollbaserad åtkomstkontroll (RBAC). Mer information om auktorisering med Azure AD finns i [auktorisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md).

Den **åtkomstkontroll** inställningar i Azure-portalen erbjuder ett enkelt sätt att tilldela RBAC-roller till användare, grupper, tjänstens huvudnamn och hanterade identiteter. Mer information om att tilldela RBAC-roller finns i [hantera åtkomsträttigheter till blob och kö data med RBAC](storage-auth-aad-rbac.md).

## <a name="tags"></a>Tags

Azure Storage stöder Azure Resource Manager-taggar för att ordna dina Azure-resurser med en anpassad taxonomi. Du kan lägga till taggar till storage-konton så att du kan gruppera dem i din prenumeration på ett logiskt sätt.

En taggnamnet är begränsat till 128 tecken för storage-konton, och en Taggvärdet är begränsat till 256 tecken.

Mer information finns i [använda taggar för att organisera Azure-resurser](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Åtkomstnycklar

När du skapar ett lagringskonto genererar Azure två 512-bitars lagringskontots åtkomstnycklar. De här nycklarna kan användas för att bevilja åtkomst till ditt storage-konto via delad nyckel. Du kan rotera och återskapa nycklar utan avbrott för dina program och Microsoft rekommenderar att du gör det. regelbundet.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Visa nycklar och anslutningssträngen

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Återskapa åtkomstnycklar

Microsoft rekommenderar att du återskapar åtkomstnycklarna regelbundet för att skydda ditt lagringskonto. Två åtkomstnycklar tilldelas så att du kan rotera nycklarna. När du roterar nycklar du se till att ditt program har åtkomst till Azure Storage under hela processen. 

> [!WARNING]
> Återskapar åtkomstnycklarna kan påverka program eller Azure-tjänster som är beroende av lagringskontonyckeln. Alla klienter som använder nyckeln till att komma åt lagringskontot måste uppdateras för att använda den nya nyckeln, inklusive medietjänster, molnet, skrivbordet och mobilprogram och grafiska gränssnittet program för Azure Storage, som [Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/).

Följ den här processen för att rotera nycklar för ditt lagringskonto:

1. Uppdatera anslutningssträngarna i programkoden för att använda den sekundära nyckeln.
2. Återskapa lagringskontots primära åtkomstnyckel. På den **åtkomstnycklar** bladet i Azure-portalen klickar du på **återskapa Nyckel1**, och klicka sedan på **Ja** att bekräfta att du vill skapa en ny nyckel.
3. Uppdatera anslutningssträngarna i koden så att de refererar till den nya primärnyckeln.
4. Återskapa den sekundära åtkomstnyckeln på samma sätt.

## <a name="account-configuration"></a>Kontokonfigurationen

När du har skapat ett lagringskonto kan du ändra dess konfiguration. Du kan till exempel ändra hur dina data replikeras eller ändra kontots åtkomstnivå från frekvent till lågfrekvent. I den [Azure-portalen](https://portal.azure.com), navigera till ditt lagringskonto, hitta och klicka på **Configuration** under **inställningar** att visa och/eller ändra kontokonfigurationen.

Ändra kontokonfigurationen lagring kan resultera i kostnaderna. Mer information finns i den [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) sidan.

## <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto

Om du vill ta bort ett lagringskonto som du inte längre använder navigerar du till lagringskontot på [Azure Portal](https://portal.azure.com) och klickar på **Ta bort**. Om du tar bort ett lagringskonto tas hela kontot bort, inklusive alla data på kontot.

> [!WARNING]
> Det går inte att återställa ett borttaget lagringskonto eller att hämta innehåll som det innehöll före borttagningen. Var noga med att säkerhetskopiera allt som du vill spara innan du tar bort kontot. Detta gäller även alla resurser i kontot. När du tar bort en blobb, tabell, kö eller fil tas den bort permanent.
> 

Om du försöker ta bort ett lagringskonto som är kopplat till en virtuell Azure-dator kan du få ett felmeddelande om att lagringskontot fortfarande används. Information om hur du felsöker det här felet finns i [Felsöka fel när du tar bort lagringskonton](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure storage-konto](storage-account-overview.md)
- [Skapa ett lagringskonto](storage-quickstart-create-account.md)
