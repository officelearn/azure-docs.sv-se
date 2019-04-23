---
title: Skapa en Video Indexer-konto i Azure portal
titlesuffix: Azure Media Services
description: Den här artikeln visar hur du skapar en Video Indexer-konto i Azure-portalen.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 01/12/2019
ms.author: juliako
ms.openlocfilehash: affa6f9a808543401b7d57812c7d2bef4324a83c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796553"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Skapa en Video Indexer-konto som är ansluten till Azure

När du skapar ett Video Indexer-konto kan du välja ett kostnadsfritt utvärderingskonto (där du får ett visst antal kostnadsfria indexeringsminuter) eller ett betalalternativ (där du inte begränsas av kvoten). Med den kostnadsfria utvärderingen ger Video Indexer upp till 600 minuter kostnadsfri indexering för webbplatsanvändare och upp till 2 400 minuter kostnadsfri indexering för API-användare. Med alternativet betald skapar du en Video Indexer-konto som är ansluten till din Azure-prenumeration och ett Azure Media Services-konto. Du betalar för minuter som indexeras samt kostnader relaterade till mediekontot. 

Den här artikeln visar hur du skapar en Video Indexer-konto som är länkad till en Azure-prenumeration och ett Azure Media Services-konto. Avsnittet innehåller steg för att ansluta till Azure med hjälp av flödet automatisk (standard). Den visar även hur du ansluter till Azure manuellt (Avancerat).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration.

    Om du inte har en Azure-prenumeration ännu kan registrera dig för [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/).

* En Azure Active Directory (AD)-domän.

    Om du inte har en Azure AD-domän kan du skapa den här domänen med Azure-prenumerationen. Mer information finns i [hantera anpassade domännamn i Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md)

* En användare och medlemmar i din Azure AD-domän. När du ansluter ditt Video Indexer-konto till Azure ska du använda den här medlemmen.

    Den här användaren ska vara en Azure AD-användare med ett arbets eller skolkonto, inte ett personligt konto, till exempel outlook.com, live.com eller hotmail.com.

    ![alla AAD-användare](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Ytterligare krav för automatisk flow

En användare och medlemmar i din Azure AD-domän. När du ansluter ditt Video Indexer-konto till Azure ska du använda den här medlemmen.

Den här användaren ska vara medlem i Azure-prenumerationen med antingen en **ägare** roll eller båda **deltagare** och **administratör för användaråtkomst** roller. En användare kan läggas till två gånger, med 2-roller. En gång med deltagare och en gång med användaren administratör.

![Åtkomstkontroll](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Ytterligare krav för manuell flow

Registrera resursprovidern EventGrid med Azure portal.

I den [Azure-portalen](https://portal.azure.com/)går du till **prenumerationer**-> [prenumeration] ->**ResourceProviders**. 

Sök efter **Microsoft.Media** och **Microsoft.EventGrid**. Om inte i tillståndet ”Registered” klickar du på **registrera**. Det tar några minuter att registrera.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Anslut till Azure

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.

2. Klicka på den **Skapa nytt konto** knappen:

    ![Ansluta till Azure](./media/create-account/connect-to-azure.png)

3. När listan över prenumerationer visas väljer du den prenumeration som du vill använda.

    ![ansluta Video Indexer till Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Välj en Azure-region från platser som stöds: Västra USA 2, Norra Europa eller Asien.
5. Under **Azure Media Services-konto**, Välj något av följande alternativ:

    * Om du vill skapa ett nytt Media Services-konto, Välj **Skapa ny resursgrupp**. Ange ett namn för resursgruppen.

        Azure skapar det nya kontot i din prenumeration, inklusive ett nytt Azure Storage-konto. Ditt nya Media Services-konto har en inledande standardkonfigurationen med en slutpunkt för direktuppspelning och 10 Mediereserverade S3-enheter.
    * Om du vill använda ett befintligt Media Services-konto, Välj **använda befintlig resurs**. Välj ditt konto från kontolistan över.

        Media Services-kontot måste ha samma region som din Video Indexer-konto. 

        > [!NOTE]
        > För att minimera indexering varaktighet och lågt dataflöde, vi rekommenderar starkt att justera typ och antal [reserverade enheter](../previous/media-services-scale-media-processing-overview.md ) till **10 S3 Mediereserverade enheter** i Media Services-kontot. Se [Använd portalen för att ändra reserverade enheter](../previous/media-services-portal-scale-media-processing.md).

    * För att manuellt konfigurera anslutningen, klickar du på den **växla till manuell konfiguration** länk.

        Detaljerad information finns i den [ansluta till Azure manuellt](#connect-to-azure-manually-advanced-option) (avancerade alternativ) avsnitt.
6. När du är klar väljer **Connect**. Den här åtgärden kan ta några minuter. 

    När du är ansluten till Azure, visas det nya Video Indexer-kontot i kontolistan:

    ![nytt konto](./media/create-account/new-account.png)

7. Bläddra till ditt nya konto

## <a name="connect-to-azure-manually-advanced-option"></a>Ansluta till Azure manuellt (Avancerat alternativ)

Om det gick inte att ansluta till Azure, kan du försöka att felsöka problemet genom att ansluta manuellt.

> [!NOTE]
> Vi rekommenderar starkt att du har följande tre konton i samma region: Video Indexer-konto som du ansluter med Media Services-kontot, samt Azure storage-kontot som är anslutna till samma Media Services-kontot.

### <a name="create-and-configure-a-media-services-account"></a>Skapa och konfigurera ett Media Services-konto

1. Använd den [Azure](https://portal.azure.com/) portalen för att skapa ett Azure Media Services-konto, enligt beskrivningen i [skapa ett konto](../previous/media-services-portal-create-account.md).

    När du skapar ett lagringskonto för Media Services-kontot väljer **StorageV2** för typ av konto och **Geo-redundant (RGS)** för replikering fält.

    ![nytt AMS-konto](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Se till att anteckna namnen för Media Services resursen och konto. Du behöver den för stegen i nästa avsnitt.

2. Justera typ och antal [reserverade enheter](../previous/media-services-scale-media-processing-overview.md ) till **10 S3 Mediereserverade enheter** i Media Services-kontot som du skapade. Se [Använd portalen för att ändra reserverade enheter](../previous/media-services-portal-scale-media-processing.md).
3. Innan du kan spela upp videor i Video Indexer-webbprogram, måste du starta standard **Strömningsslutpunkt** på det nya Media Services-kontot.

    I det nya Media Services-kontot, klickar du på **slutpunkter för direktuppspelning**. Välj den slutpunkt för direktuppspelning och tryck på starten.

    ![nytt AMS-konto](./media/create-account/create-ams-account2.png)

4. Ett AD-program måste skapas för Video Indexer att autentisera med Media Services-API. Följande steg-guide igenom processen för Azure AD-autentisering som beskrivs i [Kom igång med Azure AD-autentisering med hjälp av Azure portal](../previous/media-services-portal-get-started-with-aad.md):

    1. I det nya Media Services-kontot, väljer **API-åtkomst**.
    2. Välj [Service principal autentiseringsmetod](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).
    3. Hämta klient-ID och klienthemlighet, som beskrivs i den [hämta klient-ID och klienthemlighet](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret) avsnittet.

        När du har valt **inställningar**->**nycklar**, lägga till **beskrivning**, tryck på **spara**, nyckelvärdet fylls.

        Om nyckeln upphör att gälla måste kontoägaren Video Indexer-supporten om du vill förnya nyckeln.

        > [!NOTE]
        > Se till att anteckna nyckelvärdet och programmets ID. Du behöver den för stegen i nästa avsnitt.

### <a name="connect-manually"></a>Ansluta manuellt

I den **ansluta till en Azure-prenumeration i Video Indexer** dialogrutan av din [Video Indexer](https://www.videoindexer.ai/) väljer den **växla till manuell konfiguration** länk.

Ange följande information i dialogrutan:

|Inställning|Beskrivning|
|---|---|
|Video Indexer-kontoregion|Namnet på regionen Video Indexer-konto. För bättre prestanda och lägre kostnader rekommenderas att ange namnet på den region där Azure Media Services-resurs och Azure Storage-konto finns. |
|Azure Active Directory (AAD)-klient|Namnet på Azure AD-klient, till exempel ”contoso.onmicrosoft.com”. Klient-informationen kan hämtas från Azure-portalen. Placera markören över namnet på den inloggade användaren i det övre högra hörnet. Hitta namnet till höger om **domän**.|
|Prenumerations-ID:t|Azure-prenumerationen som den här anslutningen ska skapas. Prenumerations-ID kan hämtas från Azure-portalen. Klicka på **alla tjänster** i den vänstra panelen och söka efter ”prenumerationer”. Välj **prenumerationer** och välj önskad ID i listan med dina prenumerationer.|
|Azure Media Services-resursgruppens namn|Namnet på resursgruppen där du skapade Media Services-kontot.|
|Mediatjänstresursnamn|Namnet på Media Services-konto som du skapade i föregående avsnitt.|
|Program-ID:t|Azure AD application ID (med behörigheter för det angivna Media Services-kontot) som du skapade i föregående avsnitt.|
|Programnyckel|Nyckeln för Azure AD-program som du skapade i föregående avsnitt. |

## <a name="considerations"></a>Överväganden

Följande gäller Azure Media Services relaterade:

* Om du ansluter automatiskt, visas en ny resursgrupp, Media Services-konto och ett lagringskonto i Azure-prenumerationen.
* Om du ansluter automatiskt Video Indexer anger mediet **reserverade enheter** till 10 S3-enheter:

    ![Media Services-reserverade enheter](./media/create-account/ams-reserved-units.png)

* Om du ansluter till ett befintligt Media Services-konto, ändras inte Video Indexer befintlig medieuppsättning **reserverade enheter** konfiguration.

   Du kan behöva justera typen och antalet Mediereserverade enheter enligt planerad inläsningen. Tänk på att om din belastningen är hög och du inte har tillräckligt med enheter eller hastighet, videor bearbetning kan leda till timeout-fel.

* Om du ansluter till ett nytt Media Services-konto, Video Indexer automatiskt startar standard **Strömningsslutpunkt** i den:

    ![Slutpunkten för direktuppspelning av Media Services](./media/create-account/ams-streaming-endpoint.png)

    Slutpunkter för direktuppspelning har en betydande starttiden. Därför kan det ta flera minuter från det att du har anslutit ditt konto till Azure, tills dina videor kan strömmas och sett i Video Indexer-webbprogram.

* Om du ansluter till ett befintligt Media Services-konto, ändras inte Video Indexer-slutpunkt för direktuppspelning standardkonfigurationen. Om inga körs **Strömningsslutpunkt**, kommer du inte kunna titta på videor från Media Services-konto eller i Video Indexer.

## <a name="next-steps"></a>Nästa steg

Du kan interagera med ditt utvärderingskonto och/eller med din Video Indexer-användarkonton som är anslutna till azure genom att följa anvisningarna i: [Använd API: er](video-indexer-use-apis.md).

Du bör använda samma Azure AD-användare som du använde när du ansluter till Azure.


