---
title: Skapa en Video Indexer-konto som är ansluten till Azure | Microsoft Docs
description: Den här artikeln visar hur du skapar en Video Indexer-konto som är ansluten till Azure.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 09/09/2018
ms.author: juliako
ms.openlocfilehash: 229624f4070c931b6ad892533aad269af49c738b
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45544202"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Skapa en Video Indexer-konto som är ansluten till Azure

När du skapar en Video Indexer-konto, kan du välja ett kostnadsfritt konto (där du får ett visst antal kostnadsfria indexering minuter) eller ett betalt alternativ (där du inte begränsas av kvoten). Video Indexer ger upp till 600 minuter för kostnadsfria indexering för webbplatsen användare och upp till 2 400 minuters kostnadsfria indexering för API-användare med kostnadsfri utvärderingsversion. Med betalt alternativ skapar du en Video Indexer-konto som är ansluten till din Azure-prenumeration och ett Azure Media Services-konto. Du betalar för minuter som indexeras som Media-konto som är relaterade kostnader. 

Den här artikeln visar hur du skapar en Video Indexer-konto som är länkad till en Azure-prenumeration och ett Azure Media Services-konto. 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. 

    Om du inte har en Azure-prenumeration ännu kan registrera dig för [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/).

* En Azure Active Directory (AD)-domän. 

    Om du inte har en Azure AD-domän kan du skapa den här domänen med Azure-prenumerationen. Mer information finns i [hantera anpassade domännamn i Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md)

* En användare och medlemmar i din Azure AD-domän. När du ansluter ditt Video Indexer-konto till Azure ska du använda den här medlemmen.

    Den här användaren ska uppfylla dessa kriterier:

    * Vara en Azure AD-användare med ett arbets eller skolkonto, inte ett personligt konto, till exempel outlook.com, live.com eller hotmail.com.
        
        ![alla AAD-användare](./media/create-account/all-aad-users.png)

    *  Vara medlem i Azure-prenumerationen med en ägarrollen eller både deltagare och administratör för användaråtkomst roller. En användare kan läggas till två gånger, med 2-roller. En gång med deltagare och en gång med användaren administratör.

        ![Åtkomstkontroll](./media/create-account/access-control-iam.png)

* Registrera resursprovidern EventGrid med Azure portal.

    I den [Azure-portalen](https://portal.azure.com/)går du till **prenumerationer** > [prenumeration] > **ResourceProviders** > **Microsoft.EventGrid**. Om inte i tillståndet ”Registered” klickar du på **registrera**. Det tar några minuter att registrera. 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Anslut till Azure

1. Bläddra till den [Video Indexer](https://www.videoindexer.ai/) och logga in.

2. Klicka på den **Anslut till Azure** knappen:

    ![Ansluta till Azure](./media/create-account/connect-to-azure.png)

3. När listan över prenumerationer visas väljer du den prenumeration som du vill använda. 

    ![ansluta Video Indexer till Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Välj en Azure-region från platser som stöds: västra USA 2, Norra Europa eller Asien.
5. Under **Azure Media Services-konto**, Välj något av följande alternativ:

    * Om du vill skapa ett nytt Media Services-konto, Välj **Skapa ny resursgrupp**. Ange ett namn för resursgruppen.

        Azure skapar det nya kontot i din prenumeration, inklusive ett nytt Azure Storage-konto. Ditt nya Media Services-konto har en inledande standardkonfigurationen med en slutpunkt för direktuppspelning och 10 Mediereserverade S3-enheter.
    * Om du vill använda ett befintligt Media Services-konto, Välj **använda befintlig resurs**. Välj ditt konto från kontolistan över.

        Media Services-kontot måste ha samma region som din Video Indexer-konto. För att minimera indexering varaktighet och lågt dataflöde, justera typen och antalet Mediereserverade enheter för att **10 S3 Mediereserverade enheter** i Media Services-kontot.
    * För att manuellt konfigurera anslutningen, klickar du på den **växla till manuell konfiguration**. 
    
        Du kanske vill manuellt konfigurera anslutningen, om du av någon anledning det automatiska alternativet som inte går att slutföra eller om din installation och konfiguration är annorlunda än i vanliga fall, eller om du vill ha full insyn och kontroll över inställningarna. 
        
        I den **ansluta Video Indexer till en Azure-prenumeration**, ange följande information.

        |Inställning|Beskrivning|
        |---|---|
        |Region för video Indexer|Namnet på regionen Video Indexer-konto. För bättre prestanda och lägre kostnader rekommenderas att ange namnet på den region där Azure Media Services-resurs och Azure Storage-konto finns. |
        |Azure Active Directory (AAD)-klient|Namnet på Azure AD-klient, till exempel ”contoso.onmicrosoft.com”. Klient-informationen kan hämtas från Azure-portalen. Placera markören över namnet på den inloggade användaren i övre högra hörnet.|
        |Prenumerations-ID:t|Azure-prenumerationen som den här anslutningen ska skapas. Prenumerations-ID kan hämtas från Azure-portalen. Klicka på **alla tjänster** i den vänstra panelen och söka efter ”prenumerationer”. Välj, **prenumerationer** och välj önskad ID i listan med dina prenumerationer.|
        |Azure Media Services resursgruppens namn|Namnet på resursgruppen där till Media Services-kontot finns.|
        |Media service-resursnamn|Namnet på Azure Media Services-resursen.|
        |Program-ID:t|Det program-ID för Azure AD med behörigheter för det angivna Media Services-kontot. Mer information finns i [Använd tjänstobjektautentisering](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).|
        |Programnyckel|Mer information finns i [Använd tjänstobjektautentisering](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).|

6. När du är klar väljer **Connect**. Den här åtgärden kan ta några minuter. 

    När du är ansluten till Azure, visas det nya Video Indexer-kontot i kontolistan:

    ![nytt konto](./media/create-account/new-account.png)

7. Bläddra till ditt nya konto: 

    ![Video Indexer-konto](./media/create-account/vi-account.png)

## <a name="considerations"></a>Överväganden

Följande gäller Azure Media Services relaterade:

* Om du är ansluten till ett nytt Media Services-konto, visas en ny resursgrupp, Media Services-konto och ett lagringskonto i Azure-prenumerationen.
* Om du är ansluten till ett nytt Media Services-konto, Video Indexer kommer ange media **reserverade enheter** till 10 S3-enheter:

    ![Media Services-reserverade enheter](./media/create-account/ams-reserved-units.png)

* Om du har anslutit till ett befintligt konto för Media Services Video Indexer inte ändra befintliga media **reserverade enheter** konfiguration.

    Du kan behöva justera typ och antal media **reserverade enheter**, enligt planerad inläsningen. Tänk på att om din belastningen är hög och du inte har tillräckligt med enheter eller hastighet, videor bearbetning kan leda till timeout-fel.

* Om du är ansluten till ett nytt Media Services-konto, Video Indexer automatiskt startar standard **Strömningsslutpunkt** i den:

    ![Slutpunkten för direktuppspelning av Media Services](./media/create-account/ams-streaming-endpoint.png)

* Om du har anslutit till ett befintligt Media Services-konto, ändras inte Video Indexer-slutpunkt för direktuppspelning standardkonfigurationen. Om inga körs **Strömningsslutpunkt**, kommer du inte kunna titta på videor från Media Services-konto eller i Video Indexer.

## <a name="next-steps"></a>Nästa steg

Du kan interagera med ditt utvärderingskonto och/eller med din Video Indexer-användarkonton som är anslutna till azure genom att följa anvisningarna i: [Använd API: er](video-indexer-use-apis.md).

Du bör använda samma Azure AD-användare som du använde när du ansluter till Azure.


