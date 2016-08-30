<properties
    pageTitle="Skapa ett Azure Batch-konto | Microsoft Azure"
    description="Lär dig hur du skapar ett Azure Batch-konto på Azure-portalen för att köra storskaliga parallella arbetsbelastningar i molnet"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="marsma"/>

# Skapa och hantera ett Azure Batch-konto på Azure-portalen

> [AZURE.SELECTOR]
- [Azure-portalen](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

[Azure-portalen][azure_portal] ger tillgång till de verktyg som du behöver för att skapa och hantera ett Azure Batch-konto, som du kan använda för att köra storskaliga parallella arbetsbelastningar. Den här artikeln beskriver hur du skapar ett Batch-konto på portalen och förklarar viktiga inställningar och egenskaper för ett Batch-konto. Till exempel behöver de program och tjänster som du utvecklar med Batch URL:en för ditt konto och en åtkomstnyckel för att kommunicera med Batch-tjänstens API:er, som båda finns på Azure-portalen.

>[AZURE.NOTE] Azure-portalen stöder för närvarande en delmängd av funktionerna i Batch-tjänsten och du kan till exempel skapa konton, hantera inställningar och egenskaper för Batch-konton och skapa och övervaka pooler och jobb. Den fullständiga funktionsuppsättningen för Batch är tillgänglig för utvecklare via Batch-API:erna.

## Skapa ett Batch-konto

1. Logga in på [Azure-portalen][azure_portal].

2. Klicka på **Nytt** > **Virtuella datorer** > **Batch-tjänst**.

    ![Batch på Marketplace][marketplace_portal]

3. Bladet **Nytt Batch-konto** visas. Punkt *a* till och med *e* nedan innehåller beskrivningar av varje bladelement.

    ![Skapa ett Batch-konto][account_portal]

    a. **Kontonamn**: Ett unikt namn för ditt Batch-konto. Det här namnet måste vara unikt i Azure-regionen som kontot skapas i (se *Plats* nedan). Det får bara innehålla gemener och siffror och måste vara mellan 3 och 24 tecken långt.

    b. **Prenumeration**: En prenumeration som Batch-kontot skapas i. Om du bara har en prenumeration väljs den som standard.

    c. **Resursgrupp**: En befintlig resursgrupp för ditt nya Batch-konto. Du kan också skapa en ny resursgrupp.

    d. **Plats**: En Azure-region som Batch-kontot skapas i. Endast de regioner som stöds av din prenumeration och resursgrupp visas som alternativ.

    e. **Lagringskonto** (valfritt): Ett **generellt** lagringskonto som du associerar (länkar) till ditt nya Batch-konto. Funktionen [programpaket](batch-application-packages.md) i Batch använder det länkade lagringskontot för lagring och hämtning av programpaket. Mer information om den här funktionen finns i [Programdistribution med Azure Batch-programpaket](batch-application-packages.md).

     > [AZURE.IMPORTANT] Det finns några saker som du bör tänka på när du återskapar nycklar i ett länkat Storage-konto. Mer information finns i [Saker att tänka på när du använder Batch-konton](#considerations-for-batch-accounts) nedan.

4. Skapa kontot genom att klicka på **Skapa**.

  Portalen meddelar att kontot **distribueras**. När åtgärden är klar visas meddelandet **Distributionen är klar** i *Meddelanden*.

## Visa egenskaper för ett Batch-konto

På bladet Batch-konto visas flera egenskaper för kontot. Bladet ger också åtkomst till ytterligare inställningar som åtkomstnycklar, kvoter, användare och associationen med ett lagringskonto.

* **URL för Batch-konto**: Den här URL:en ger åtkomst till Batch-kontot när du använder API:er som [Batch REST][api_rest]-API:et eller [Batch .NET][api_net]-klientbiblioteket, och har följande format:

    `https://<account_name>.<region>.batch.azure.com`

* **Åtkomstnycklar**: Om du vill visa och hantera Batch-kontots åtkomstnycklar klickar du på nyckelikonen för att öppna bladet **Hantera nycklar** eller på **Alla inställningar** > **Nycklar**. En åtkomstnyckel krävs vid kommunikation med Batch-tjänstens API:er, t.ex. med [Batch REST][api_rest]- eller [Batch .NET][api_net]-klientbiblioteket.

    ![Batch-kontonycklar][account_keys]

* **Alla inställningar**: Om du vill hantera alla inställningar för Batch-kontot eller om du vill visa dess egenskaper klickar du på **Alla inställningar** för att öppna bladet **Inställningar**. Det här bladet ger åtkomst till alla inställningar och egenskaper för kontot och du kan bland annat visa kontots kvoter, välja ett Azure Storage-konto för att länka till Batch-kontot och hantera användare.

    ![Blad för inställningar och egenskaper för Batch-konton][5]

## Saker att tänka på när du använder Batch-konton

* Du kan också skapa och hantera Batch-konton med [PowerShell-cmdlets för Batch](batch-powershell-cmdlets-get-started.md) och [Batch Management .NET](batch-management-dotnet.md)-biblioteken.

* Du debiteras inte för själva Batch-kontot. Du debiteras för de Azure-beräkningsresurser som dina Batch-lösningar använder och för de resurser som används av andra tjänster när dina arbetsbelastningar körs. Exempelvis kan du debiteras för beräkningsnoder i dina pooler, och om du använder funktionen för [programpaket](batch-application-packages.md) debiteras du för de Azure Storage-resurser som används för att lagra dina programpaketversioner. Mer information finns i avsnittet med [priser för Batch][batch_pricing].

* Du kan köra flera Batch-arbetsbelastningar i samma Batch-konto eller distribuera dina egna arbetsbelastningar mellan Batch-gruppkonton i olika Azure-regioner.

* Om du kör flera storskaliga Batch-arbetsbelastningar måste du vara medveten om vissa [kvoter och gränser för Batch-tjänsten](batch-quota-limit.md) som gäller för din Azure-prenumeration och varje Batch-konto. Aktuella kvoter för ett Batch-konto visas på portalen i kontoegenskaperna.

* Om du associerar (länkar) ett lagringskonto med ditt Batch-konto bör du vara försiktig när du återskapar åtkomstnycklarna för lagringskontot. Skapa bara en lagringskontonyckel. Klicka på **Synkronisera nycklar** på bladet för länkade lagringskonton, vänta fem minuter så att nycklarna hinner spridas till beräkningsnoderna i dina pooler och återskapa och synkronisera sedan den andra nyckeln om det behövs. Om du återskapar båda nycklarna samtidigt kan dina beräkningsnoder inte synkronisera någon av nycklarna och de förlorar åtkomst till lagringskontot.

  ![Återskapa lagringskontonycklar][4]

> [AZURE.IMPORTANT] Batch stöder för närvarande *endast* den **allmänna** lagringskontotypen, vilket förklaras i steg 5, [Skapa ett lagringskonto](../storage/storage-create-storage-account.md#create-a-storage-account), i [Om Azure-lagringskonton](../storage/storage-create-storage-account.md). När du länkar ett Azure Storage-konto till ditt Batch-konto är det viktigt att du länkar ** ett **allmänt** lagringskonto.

## Nästa steg

* Mer information om begrepp och funktioner relaterade till Batch-tjänsten finns i [funktionsöversikten för Azure Batch](batch-api-basics.md). Artikeln beskriver de viktigaste Batch-resurserna, t.ex. pooler, beräkningsnoder, jobb och aktiviteter, och innehåller en översikt över funktioner i tjänsten som stöder storskaliga arbetsbelastningar.

* Lär dig hur du utvecklar ett enkelt Batch-aktiverat program med hjälp av [Batch .NET-klientbiblioteket](batch-dotnet-get-started.md). [Introduktionsartikeln](batch-dotnet-get-started.md) beskriver steg för steg ett program som använder Batch-tjänsten för att köra en arbetsbelastning på flera beräkningsnoder och förklarar hur du använder Azure Storage för mellanlagring och hämtning av filer i arbetsbelastningar.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Återskapa lagringskontonycklar"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "Blad för inställningar och egenskaper för Batch-konton"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG



<!--HONumber=jun16_HO2-->


