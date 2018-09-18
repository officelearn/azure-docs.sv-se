---
title: Hantera inställningar för lagringskonto i Azure portal – Azure Storage | Microsoft Docs
description: Lär dig mer om att hantera inställningarna för lagringskontot i Azure-portalen, inklusive hur du konfigurerar inställningar för åtkomstkontroll, återskapar åtkomstnycklar, ändring av åtkomstnivå eller ändra typ av replikering som används av kontot. Lär dig också att ta bort ett lagringskonto i portal.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 5237be9fd75edc8abf5c5fa043574e8c2deb79e9
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45740714"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Hantera inställningar för lagringskonto i Azure portal

Det finns många olika inställningar för ditt lagringskonto i den [Azure-portalen](https://portal.azure.com). Den här artikeln beskriver några av de här inställningarna och hur de används.

## <a name="access-control"></a>Åtkomstkontroll

Azure Storage stöder autentisering med Azure Active Directory för Blob storage och Queue storage (förhandsversion) via rollbaserad åtkomstkontroll (RBAC). Mer information om autentisering med Azure AD finns i [autentisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory (förhandsversion)](storage-auth-aad.md).

Den **åtkomstkontroll** inställningar i Azure-portalen erbjuder ett enkelt sätt att tilldela RBAC-roller till användare, grupper, tjänstens huvudnamn och hanterade identiteter. Mer information om att tilldela RBAC-roller finns i [hantera åtkomsträttigheter till blob och kö data med RBAC (förhandsversion)](storage-auth-aad-rbac.md).

> [!NOTE]
> Autentisera användare eller program med hjälp av autentiseringsuppgifter för Azure AD tillhandahåller överlägsen säkerhet och användarvänlighet över annan typ av auktorisering. Du kan fortsätta använda delad nyckel auktorisering med program, kringgår med hjälp av Azure AD behovet av att spara din åtkomstnyckel med din kod. Du kan också fortsätta att använda signaturer för delad åtkomst (SAS) för att ge detaljerad åtkomst till resurser i ditt storage-konto, men Azure AD erbjuder liknande funktioner utan att behöva hantera SAS-token eller oroa dig om du återkallar en komprometterad SAS. 

## <a name="access-keys"></a>Åtkomstnycklar

När du skapar ett lagringskonto genererar Azure två 512-bitars lagringskontots åtkomstnycklar. De här nycklarna kan användas för att bevilja åtkomst till ditt storage-konto via delad nyckel. Du kan rotera och återskapa nycklar utan avbrott för dina program och Microsoft rekommenderar att du gör det. regelbundet.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

### <a name="view-and-copy-access-keys"></a>Visa och kopiera åtkomstnycklar

Visa autentiseringsuppgifterna för lagringskontot:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Välj **Åtkomstnycklar** i avsnittet **Inställningar** i lagringskontoöversikten. Åtkomstnycklarna för kontot visas, samt den fullständiga anslutningssträngen för varje nyckel.
4. Sök efter värdet för **Key** (Nyckel) **key1** och klicka på knappen **Kopiera** för att kopiera kontonyckeln.
5. Alternativt kan kopiera du hela anslutningssträngen. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att klicka på **Kopiera**.

    ![Skärmbild som visar hur du visar åtkomstnycklarna i Azure portal](media/storage-manage-account/portal-connection-string.png)

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
