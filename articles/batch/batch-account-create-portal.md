---
title: "Skapa ett Batch-konto på Azure Portal | Microsoft Docs"
description: "Lär dig hur du skapar ett Azure Batch-konto på Azure Portal för att köra storskaliga parallella arbetsbelastningar i molnet"
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b7629e496f2d73798b94acdc611014a8b3afead7
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Skapa ett Batch-konto med Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
>
>

Lär dig hur du skapar ett Azure Batch-konto på [Azure Portal][azure_portal] och välj de kontoegenskaper som passar ditt beräkningsscenario. Lär dig var du hittar viktiga kontoegenskaper som snabbtangenter och konto-URL:er.

Bakgrundsinformation om Batch-konton och Batch-scenarier finns i [funktionsöversikten](batch-api-basics.md).



## <a name="create-a-batch-account"></a>Skapa ett Batch-konto



1. Logga in på [Azure Portal][azure_portal].
2. Klicka på **Ny** och sök på Marketplace efter **Batch-tjänst**.

    ![Batch på Marketplace][marketplace_portal]
3. Välj **Batch-tjänst**, klicka på **Skapa** och ange inställningar för **Nytt Batch-konto**. Se följande information.

    ![Skapa ett Batch-konto][account_portal]

    a. **Kontonamn**: Det namn du väljer måste vara unikt i den Azure-region där kontot skapas (se **Plats** nedan). Kontonamnet får bara innehålla gemener eller siffror och måste vara mellan 3 och 24 tecken långt.

    b. **Prenumeration**: Prenumerationen som Batch-kontot skapas i. Om du bara har en prenumeration väljs den som standard.

    c. **Poolallokeringsläge**: Om den här inställningen visas godkänner du standardinställningen för **Batch-tjänst**.

    c. **Resursgrupp**: Välj en befintlig resursgrupp för ditt nya Batch-konto. Du kan också skapa en ny resursgrupp.

    d. **Plats**: Azure-regionen som Batch-kontot skapas i. Endast de regioner som stöds av din prenumeration och resursgrupp visas som alternativ.

    e. **Lagringskonto** (valfritt): Ett generellt Azure Storage-konto som du associerar med ditt Batch-konto. Detta rekommenderas för de flesta Batch-konton. Mer information finns i [Länkat Azure Storage-konto](#linked-azure-storage-account) senare i den här artikeln.

4. Skapa kontot genom att klicka på **Skapa**.



## <a name="view-batch-account-properties"></a>Visa egenskaper för ett Batch-konto
När kontot har skapats kan du klicka på kontot för att komma åt dess inställningar och egenskaper. Du kan komma åt alla kontoinställningar och kontoegenskaper från den vänstra menyn.

![Bladet för Batch-kontot på Azure Portal][account_blade]

* **Batch-kontots URL**: När du utvecklar ett program med [Batch-API:er](batch-apis-tools.md#azure-accounts-for-batch-development) behöver du en konto-URL till Batch-resurserna. URL:en för ett Batch-konto har följande format:

    `https://<account_name>.<region>.batch.azure.com`

![Batch-kontots URL på portalen][account_url]

* **Åtkomstnycklar**: Du kan använda en kontoåtkomstnyckel för att autentisera åtkomst till Batch-kontot från programmet. (Batch har även stöd för Azure Active Directory-autentisering.)

    Om du vill visa eller återskapa åtkomstnycklarna väljer du **Nycklar**.

    ![Batch-kontonycklar på Azure Portal][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Länkat Azure Storage-konto

Du kan länka ett allmänt Azure Storage-konto till Batch-kontot, vilket är till hjälp i många scenarier. Funktionen [programpaket](batch-application-packages.md) i Batch använder Azure Blob Storage, precis som biblioteket [Batch File Conventions .NET](batch-task-output.md) gör. Med dessa valfria funktioner kan du distribuera de program som körs av Batch-aktiviteterna och spara de data som de genererar.

Vi rekommenderar att du skapar ett nytt lagringskonto för exklusiv användning av Batch-kontot. Azure Batch stöder för närvarande endast den allmänna lagringskontotypen. Den här kontotypen beskrivs i steg 5, [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account), i [Om Azure-lagringskonton](../storage/common/storage-create-storage-account.md).

![Skapa ett allmänt lagringskonto][storage_account]

> [!NOTE]
> Var noga när du återskapar åtkomstnycklarna för ett länkat Storage-konto. Återskapa endast en Storage-kontonyckel och klicka på **Synkronisera nycklar** på bladet för det länkade Storage-kontot. Vänta fem minuter så att nycklarna hinner distribueras till beräkningsnoderna i poolerna och återskapa och synkronisera sedan den andra nyckeln om det behövs. Om du återskapar båda nycklarna samtidigt kan dina beräkningsnoder inte synkronisera någon av nycklarna och de förlorar åtkomst till Storage-kontot.
>
>

![Återskapa lagringskontonycklar][4]

## <a name="batch-service-quotas-and-limits"></a>Kvoter och begränsningar för Batch-tjänsten
Precis som med Azure-prenumerationen och andra Azure-tjänster, så har Batch-konton vissa [kvoter och begränsningar](batch-quota-limit.md). Aktuella kvoter för Batch-kontot visas i **Kvoter**.

![Kvoter för Batch-konto på Azure Portal][quotas]



Vidare kan du öka många av dessa kvoter genom att skicka en kostnadsfri begäran om produktsupport på Azure Portal. Information om hur du gör den här typen av begäran finns i [Kvoter och begränsningar för Azure Batch-tjänsten](batch-quota-limit.md).

## <a name="other-batch-account-management-options"></a>Andra alternativ för Batch-kontohantering
Förutom att använda Azure Portal kan du skapa och hantera Batch-konton med följande:

* [PowerShell-cmdletar för Batch](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Nästa steg
* Mer information om begrepp och funktioner relaterade till Batch-tjänsten finns i [funktionsöversikten för Batch](batch-api-basics.md). Artikeln beskriver de viktigaste Batch-resurserna, t.ex. pooler, beräkningsnoder, jobb och aktiviteter, och innehåller en översikt över funktioner i tjänsten för storskaliga arbetsbelastningar.
* Lär dig hur du utvecklar ett enkelt Batch-aktiverat program med hjälp av [Batch .NET-klientbiblioteket](batch-dotnet-get-started.md) eller [Python](batch-python-tutorial.md). Introduktionsartikeln beskriver steg för steg ett program som använder Batch-tjänsten för att köra en arbetsbelastning på flera beräkningsnoder och förklarar hur du använder Azure Storage för mellanlagring och hämtning av filer i arbetsbelastningar.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Återskapa lagringskontonycklar"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[account_portal_byos]: ./media/batch-account-create-portal/batch_acct_portal_byos.png
