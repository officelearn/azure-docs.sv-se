---
title: Skapa ett videoindexerkonto som är kopplat till Azure
titleSuffix: Azure Media Services
description: Lär dig hur du skapar ett videoindexerkonto som är anslutet till Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: 7ec8004fa0a54265962e79320c02287b2fcf814a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499916"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Skapa ett videoindexerkonto som är kopplat till Azure

När du skapar ett videoindexerkonto kan du välja ett kostnadsfritt utvärderingskonto (där du får ett visst antal kostnadsfria indexeringsminuter) eller ett betalt alternativ (där du inte begränsas av kvoten). Med en gratis testperiod ger Video Indexer upp till 600 minuters gratis indexering till webbplatsanvändare och upp till 2400 minuters gratis indexering till API-användare. Med alternativet Betald skapar du ett videoindexerkonto som är kopplat till din Azure-prenumeration och ett Azure Media Services-konto. Du betalar för indexerade minuter samt mediekontorelaterade avgifter.

Den här artikeln visar hur du skapar ett Video Indexer-konto som är kopplat till en Azure-prenumeration och ett Azure Media Services-konto. Avsnittet innehåller steg för att ansluta till Azure med hjälp av det automatiska (standard)flödet. Den visar också hur du ansluter till Azure manuellt (avancerat).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration.

    Om du inte har en Azure-prenumeration ännu registrerar du dig för [Azure Free Trial](https://azure.microsoft.com/free/).

* En Azure Active Directory-domän (Azure AD).

    Om du inte har en Azure AD-domän skapar du den här domänen med din Azure-prenumeration. Mer information finns i [Hantera anpassade domännamn i Din Azure AD](../../active-directory/users-groups-roles/domains-manage.md)

* En användare i din Azure AD-domän med en **programadministratörsroll.** Du använder den här medlemmen när du ansluter ditt Video Indexer-konto till Azure.

    Den här användaren bör vara en Azure AD-användare med ett arbets- eller skolkonto. Använd inte ett personligt konto, till exempel outlook.com, live.com eller hotmail.com.

    ![alla AAD-användare](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Ytterligare förutsättningar för automatiskt flöde

* En användare och medlem i din Azure AD-domän.

    Du använder den här medlemmen när du ansluter ditt Video Indexer-konto till Azure.

    Den här användaren bör vara medlem i din Azure-prenumeration med antingen en **ägarroll** eller roller för både **deltagare** och **administratör för användaråtkomst.** En användare kan läggas till två gånger, med två roller. En gång med Deltagare och en gång med användarens åtkomstadministratör.

    ![åtkomstkontroll](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Ytterligare förutsättningar för manuellt flöde

* Registrera EventGrid-resursleverantören med Hjälp av Azure-portalen.

    Gå till **Prenumerationer**->[prenumeration]->**ResourceProviders**i [Azure-portalen](https://portal.azure.com/).

    Sök efter **Microsoft.Media** och **Microsoft.EventGrid**. Om inte i tillståndet "Registrerad" klickar du på **Registrera**. Det tar ett par minuter att registrera sig.

    ![EventGrid (På ett sätt som är fallet)](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Anslut till Azure

> [!NOTE]
> Om din Azure-prenumeration använder certifikatbaserad multifaktorautentisering är det viktigt att du utför följande steg på en enhet som har de certifikat som krävs installerade.

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.

2. Välj knappen **Skapa nytt konto:**

    ![Skapa nytt videoindexeringskonto](./media/create-account/connect-to-azure.png)

3. När prenumerationslistan visas väljer du den prenumeration du vill använda.

    ![Anslut videoindexerare till Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Välj en Azure-region från de platser som stöds: Västra USA 2, Norra Europa eller Östasien.
5. Under **Azure Media Services-konto**väljer du något av följande alternativ:

    * Om du vill skapa ett nytt Media Services-konto väljer du **Skapa ny resursgrupp**. Ange ett namn för resursgruppen.

        Azure skapar ditt nya konto i din prenumeration, inklusive ett nytt Azure Storage-konto. Ditt nya Media Services-konto har en inledande standardkonfiguration med en slutpunkt för direktuppspelning och 10 S3-reserverade enheter.
    * Om du vill använda ett befintligt Media Services-konto väljer du **Använd befintlig resurs**. Välj ditt konto i kontolistan.

        Ditt Media Services-konto måste ha samma region som ditt videoindexerkonto.

        > [!NOTE]
        > För att minimera indexeringstiden och det låga dataflödet rekommenderar vi starkt att du justerar typen och antalet [reserverade enheter](../previous/media-services-scale-media-processing-overview.md ) i ditt Media Services-konto till **10 S3-reserverade enheter**. Se [Använda portal för att ändra reserverade enheter](../previous/media-services-portal-scale-media-processing.md).

    * Om du vill konfigurera anslutningen manuellt väljer du länken **Växla till manuell konfiguration.**

        Detaljerad information finns i avsnittet [Anslut till Azure manuellt (avancerat](#connect-to-azure-manually-advanced-option) alternativ) som följer.
6. När du är klar väljer du **Anslut**. Den här åtgärden kan ta upp till några minuter.

    När du är ansluten till Azure visas ditt nya videoindexerkonto i kontolistan:

    ![nytt konto](./media/create-account/new-account.png)

7. Bläddra till ditt nya konto.

## <a name="connect-to-azure-manually-advanced-option"></a>Ansluta till Azure manuellt (avancerat alternativ)

Om anslutningen till Azure misslyckades kan du försöka felsöka problemet genom att ansluta manuellt.

> [!NOTE]
> Vi rekommenderar starkt att du har följande tre konton i samma region: videoindexerkontot som du ansluter till Media Services-kontot samt Azure-lagringskontot som är kopplat till samma Media Services-konto.

### <a name="create-and-configure-a-media-services-account"></a>Skapa och konfigurera ett Media Services-konto

1. Använd [Azure-portalen](https://portal.azure.com/) för att skapa ett Azure Media Services-konto enligt beskrivningen i [Skapa ett konto](../previous/media-services-portal-create-account.md).

    När du skapar ett lagringskonto för ditt Media Services-konto väljer du **StorageV2** för kontoslag och **Geo-redundant (GRS)** för replikeringsfält.

    ![Nytt AMS-konto](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Se till att skriva ned Media Services-resursen och kontonamnen. Du behöver dem för stegen i nästa avsnitt.

2. Justera typ och antal [reserverade enheter](../previous/media-services-scale-media-processing-overview.md ) till **10 S3 reserverade enheter** i det Media Services-konto som du skapade. Se [Använda portal för att ändra reserverade enheter](../previous/media-services-portal-scale-media-processing.md).
3. Innan du kan spela upp dina videor i webbappen Video Indexer måste du starta **standardslutpunkten** för direktuppspelning av det nya Medietjänstkontot.

    I det nya Media Services-kontot väljer du **Slutpunkter för direktuppspelning**. Välj sedan slutpunkten för direktuppspelning och tryck på start.

    ![Nytt AMS-konto](./media/create-account/create-ams-account2.png)

4. För att Video Indexer ska kunna autentisera med Api för Medietjänster måste en AD-app skapas. Följande steg hjälper dig att gå igenom Azure AD-autentiseringsprocessen som beskrivs i [Komma igång med Azure AD-autentisering med hjälp av Azure-portalen:](../previous/media-services-portal-get-started-with-aad.md)

    1. I det nya Media Services-kontot väljer du **API-åtkomst**.
    2. Välj [Autentiseringsmetod för tjänstens huvudnamn](../previous/media-services-portal-get-started-with-aad.md).
    3. Hämta klient-ID och klienthemlighet

        När du har valt->**Inställningstangenter**lägger du till **Beskrivning,** trycker på **Spara**och tangentvärdet fylls i. **Settings**

        Om nyckeln upphör att gälla måste kontoägaren kontakta videoindexerarstöd för att förnya nyckeln.

        > [!NOTE]
        > Se till att skriva ner nyckelvärdet och program-ID. Du behöver det för stegen i nästa avsnitt.

### <a name="connect-manually"></a>Anslut manuellt

I dialogrutan **Anslut videoindexerare till en Azure-prenumeration** på sidan [VideoIndexer](https://www.videoindexer.ai/) väljer du länken **Växla till manuell konfiguration.**

Ange följande information i dialogrutan:

|Inställning|Beskrivning|
|---|---|
|Kontoregion för videoindexerare|Namnet på kontoregionen Video Indexer. För bättre prestanda och lägre kostnader rekommenderar vi starkt att du anger namnet på den region där Azure Media Services-resursen och Azure Storage-kontot finns. |
|Azure AD-klient|Namnet på Azure AD-klienten, till exempel "contoso.onmicrosoft.com". Klientinformationen kan hämtas från Azure-portalen. Placera markören över namnet på den inloggade användaren i det övre högra hörnet. Leta reda på namnet till höger om **Domän**.|
|Prenumerations-ID:t|Azure-prenumerationen som anslutningen ska skapas under. Prenumerations-ID kan hämtas från Azure-portalen. Välj **Alla tjänster** i den vänstra panelen och sök efter "prenumerationer". Välj **Prenumerationer** och välj önskat ID i listan över dina prenumerationer.|
|Namn på Azure Media Services-resursgrupp|Namnet på resursgruppen där du skapade Media Services-kontot.|
|Resursnamn för medietjänst|Namnet på Azure Media Services-kontot som du skapade i föregående avsnitt.|
|Program-ID:t|Azure AD-program-ID (med behörigheter för det angivna Media Services-kontot) som du skapade i föregående avsnitt.|
|Programnyckel|Azure AD-programnyckeln som du skapade i föregående avsnitt. |

## <a name="considerations"></a>Överväganden

Följande azure media services-relaterade överväganden gäller:

* Om du ansluter automatiskt visas ett nytt Resursgrupp, Media Services-konto och ett lagringskonto i din Azure-prenumeration.
* Om du ansluter automatiskt ställer Video Indexer in de **mediereserverade enheterna** till 10 S3-enheter:

    ![Reserverade enheter för Media Services](./media/create-account/ams-reserved-units.png)

* Om du ansluter till ett befintligt Media Services-konto ändrar videoindexeraren inte den befintliga konfigurationen för **mediereserverade enheter.**

   Du kan behöva justera typen och antalet mediereserverade enheter enligt din planerade belastning. Tänk på att om din belastning är hög och du inte har tillräckligt med enheter eller hastighet, kan videobearbetning resultera i tidsbegränsningsfel.

* Om du ansluter till ett nytt Media Services-konto startar Video Indexer automatiskt **standardslutpunkten** för direktuppspelning i det:

    ![Slutpunkt för direktuppspelning av Media Services](./media/create-account/ams-streaming-endpoint.png)

    Slutpunkter för direktuppspelning har en avsevärd starttid. Därför kan det ta flera minuter från det att du anslöt ditt konto till Azure tills dina videor kan strömmas och visas i webbappen Video Indexer.

* Om du ansluter till ett befintligt Media Services-konto ändrar videoindexeraren inte standardkonfigurationen för slutpunkt för direktuppspelning. Om det inte finns någon **strömmande slutpunkt**som körs kan du inte titta på videor från det här Media Services-kontot eller i Video Indexer.

## <a name="next-steps"></a>Nästa steg

Du kan interagera med ditt utvärderingskonto och/eller med dina Video Indexer-konton som är anslutna till Azure genom att följa instruktionerna i: [Använd API:er](video-indexer-use-apis.md).

Du bör använda samma Azure AD-användare som du använde när du ansluter till Azure.