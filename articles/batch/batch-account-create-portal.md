---
title: Skapa ett Azure Batch-konto | Microsoft Docs
description: "Lär dig hur du skapar ett Azure Batch-konto på Azure Portal för att köra storskaliga parallella arbetsbelastningar i molnet"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2016
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 6f05aa3f06c8ec86d58cafd406c2752ddb2eecc1
ms.openlocfilehash: cf5b560028f3f80e3ba46ef96ae7a157dad7847d


---
# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>Skapa ett Azure Batch-konto med hjälp av Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Lär dig hur du skapar ett Azure Batch-konto på [Azure Portal][azure_portal] och var du hittar viktiga kontoegenskaper som åtkomstnycklar och konto-URL:er. Vi diskuterar också Batch-priser och hur du länkar ett Azure Storage-konto till ditt Batch-konto så att du kan använda [programpaket](batch-application-packages.md) och [spara utdata från jobb och aktiviteter](batch-task-output.md).

## <a name="create-a-batch-account"></a>Skapa ett Batch-konto
1. Logga in på [Azure Portal][azure_portal].
2. Klicka på **Ny** > **Beräkna** > **Batch-tjänst**.
   
    ![Batch på Marketplace][marketplace_portal]
3. Bladet **Nytt Batch-konto** visas. Punkt *a* till och med *e* nedan innehåller beskrivningar av varje bladelement.
   
    ![Skapa ett Batch-konto][account_portal]
   
    a. **Kontonamn**: Namnet för ditt Batch-konto. Det namn du väljer måste vara unikt inom Azure-regionen där det nya kontot skapas (se **plats** nedan). Kontonamnet får bara innehålla gemener eller siffror och måste vara mellan 3 och 24 tecken långt.
   
    b. **Prenumeration**: Prenumerationen som Batch-kontot skapas i. Om du bara har en prenumeration väljs den som standard.
   
    c. **Resursgrupp**: Välj en befintlig resursgrupp för ditt nya Batch-konto. Du kan också skapa en ny resursgrupp.
   
    d. **Plats**: Azure-regionen som Batch-kontot skapas i. Endast de regioner som stöds av din prenumeration och resursgrupp visas som alternativ.
   
    e. **Lagringskonto** (valfritt): Ett generellt Azure Storage-konto som du associerar med ditt nya Batch-konto. Mer information finns i [Länkat Azure Storage-konto](#linked-azure-storage-account) nedan.

4. Skapa kontot genom att klicka på **Skapa**.
   
   Portalen meddelar att kontot **distribueras**. När åtgärden är klar visas meddelandet **Distributionen är klar** i *Meddelanden*.

## <a name="view-batch-account-properties"></a>Visa egenskaper för ett Batch-konto
När kontot har skapats kan du öppna bladet för **Batch-kontot** och komma åt kontots inställningar och egenskaper. Du kan komma åt alla kontoinställningar och kontoegenskaper från den vänstra menyn på bladet för Batch-kontot.

![Bladet för Batch-kontot på Azure Portal][account_blade]

* **Batch-kontots URL**: När du utvecklar ett program med [Batch-API:er](batch-technical-overview.md#batch-development-apis), behöver du ett konto-URL till dina Batch-resurser. URL:en för ett Batch-konto har följande format:
  
    `https://<account_name>.<region>.batch.azure.com`

![Batch-kontots URL på portalen][account_url]

* **Snabbtangenter**: För att autentisera åtkomst till ditt Batch-konto från programmet, behöver du en snabbtangent för konto. Om du vill visa eller återskapa Batch-kontots åtkomstnycklar skriver du `keys` i rutan **Sök** på den vänstra menyn i bladet för Batch-kontot och väljer **Nycklar**.
  
    ![Batch-kontonycklar på Azure Portal][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Länkat Azure Storage-konto

Som vi nämnde tidigare kan du om du vill länka ett allmänt Azure Storage-konto till ditt Batch-konto. Funktionen [programpaket](batch-application-packages.md) i Batch använder Azure Blob Storage, precis som biblioteket [Batch File Conventions .NET](batch-task-output.md) gör. Med dessa valfria funktioner kan du distribuera de program som körs av Batch-aktiviteterna och spara de data som de genererar.

Vi rekommenderar att du skapar ett nytt lagringskonto för exklusiv användning av Batch-kontot.

![Skapa ett allmänt lagringskonto][storage_account]

> [!NOTE] 
> Azure Batch stöder för närvarande endast den allmänna lagringskontotypen. Den här kontotypen beskrivs i steg 5, [Skapa ett lagringskonto] (... /storage/storage-create-storage-account.md#create-a-storage-account) i [Om Azure Storage-konton](../storage/storage-create-storage-account.md).
>
>

> [!WARNING]
> Var noga när du återskapar åtkomstnycklarna för ett länkat Storage-konto. Återskapa endast en Storage-kontonyckel och klicka på **Synkronisera nycklar** på bladet för det länkade Storage-kontot. Vänta fem minuter så att nycklarna hinner distribueras till beräkningsnoderna i poolerna och återskapa och synkronisera sedan den andra nyckeln om det behövs. Om du återskapar båda nycklarna samtidigt kan dina beräkningsnoder inte synkronisera någon av nycklarna och de förlorar åtkomst till Storage-kontot.
> 
> 

![Återskapa lagringskontonycklar][4]

## <a name="batch-service-quotas-and-limits"></a>Kvoter och begränsningar för Batch-tjänsten
Tänk på att, precis som din Azure-prenumeration och andra Azure-tjänster, så har Batch-konton vissa [kvoter och begränsningar](batch-quota-limit.md). De aktuella kvoterna för ett Batch-konto visas på portalen i **kontoegenskaperna**.

![Kvoter för Batch-konto på Azure Portal][quotas]

Tänk på dessa kvoter när du utformar och skalar upp dina Batch-arbetsbelastningar. Om din pool till exempel inte når antalet beräkningsnoder som du har angett som mål kan det bero på att du har nått grundkvoten för Batch-kontot.

Kvoten för Batch-konton är per region per prenumeration, så du kan ha fler än ett Batch-konto som standard så länge som de är i olika områden. Du kan köra flera Batch-arbetsbelastningar i samma Batch-konto eller distribuera dina arbetsbelastningar mellan Batch-konton som är i samma prenumeration, men i olika Azure-regioner.

Vidare kan du öka många av dessa kvoter genom att bara skicka en kostnadsfri begäran om produktsupport på Azure Portal. Information om hur du gör den här typen av begäran finns i [Kvoter och begränsningar för Azure Batch-tjänsten](batch-quota-limit.md).

## <a name="other-batch-account-management-options"></a>Andra alternativ för Batch-kontohantering
Förutom att använda Azure Portal kan du också skapa och hantera Batch-konton med följande:

* [PowerShell-cmdlets för Batch](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](../xplat-cli-install.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Nästa steg
* Mer information om begrepp och funktioner relaterade till Batch-tjänsten finns i [funktionsöversikten för Azure Batch](batch-api-basics.md). Artikeln beskriver de viktigaste Batch-resurserna, t.ex. pooler, beräkningsnoder, jobb och aktiviteter, och innehåller en översikt över funktioner i tjänsten som stöder storskaliga arbetsbelastningar.
* Lär dig hur du utvecklar ett enkelt Batch-aktiverat program med hjälp av [Batch .NET-klientbiblioteket](batch-dotnet-get-started.md). [Introduktionsartikeln](batch-dotnet-get-started.md) beskriver steg för steg ett program som använder Batch-tjänsten för att köra en arbetsbelastning på flera beräkningsnoder och förklarar hur du använder Azure Storage för mellanlagring och hämtning av filer i arbetsbelastningar.

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



<!--HONumber=Jan17_HO1-->


