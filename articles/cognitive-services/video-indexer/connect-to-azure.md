---
title: Skapa en indexerare Video-konto som är ansluten till Azure | Microsoft Docs
description: Den här artikeln visar hur du skapar en Video indexeraren konto som är ansluten till Azure.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: ac9093d41a2e70905ea82c6d11f020696488ff27
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355866"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Skapa en indexerare Video-konto som är ansluten till Azure

När du använder ett kostnadsfritt utvärderingskonto Video indexerare, är du begränsad kvoten och antal videor som du kan indexera. Den här artikeln visar hur du skapar en Video indexeraren-konto som är kopplad till en Azure-prenumeration som behöver du inte dessa begränsningar och använder betalning per användning priser.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. 

    Om du inte har en Azure-prenumeration ännu kan registrera dig för [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/).

* En Azure Active Directory (AD)-domän. 

    Om du inte har en Azure AD-domän, kan du skapa den här domänen med din Azure-prenumeration.

* En användare och medlemmar i din Azure AD-domän. När du ansluter Video indexeraren kontot till Azure ska du använda den här medlemmen.

    Den här användaren ska uppfyller följande villkor:

    * Vara en Azure AD-användare med ett arbets- eller skolkonto, inte ett personligt konto, till exempel outlook.com, live.com eller hotmail.com.
        
        ![alla AAD-användare](./media/create-account/all-aad-users.png)

    *  Vara medlem i din Azure-prenumeration med en ägarrollen eller både deltagare och administratör för användaråtkomst roller.

        ![Åtkomstkontroll](./media/create-account/access-control-iam.png)

## <a name="connect-to-azure"></a>Anslut till Azure

1. Logga in med den aktuella användaren och klicka på den **Anslut till Azure** knappen:

    ![Ansluta till Azure](./media/create-account/connect-to-azure.png)

2. När listan över prenumerationer visas väljer du den prenumeration som du vill använda. 

    ![ansluta Video indexeraren till Azure](./media/create-account/connect-vi-to-azure-subscription.png)

3. Välj en Azure-region från platser som stöds: västra USA 2, Norra Europa eller Östasien.
4. Under **Azure Media Services-konto**, väljer du något av följande alternativ:

    * Om du vill skapa ett nytt Media Services-konto, Välj **Skapa ny resursgrupp**. Ange ett namn för resursgruppen.

        Azure skapar ett nytt konto i din prenumeration, inklusive ett nytt Azure Storage-konto. Nytt Media Services-kontot har en inledande standardkonfigurationen med en strömmande slutpunkt och 10 S3 reserverade enheter.
    * Om du vill använda ett befintligt konto för Media Services **använda befintliga resursen**. Välj kontot från kontolistan över.

        Media Services-kontot måste ha samma region som din Video indexeraren-kontot. För att minimera indexering varaktighet och låg genomströmning, justera typen och antalet reserverade enheter till **10 S3 reserverade enheter** i Media Services-kontot.
    * Manuellt konfigurera anslutningen genom att klicka på den **växla till manuell konfiguration** länka och ange nödvändig information:

    ![ansluta Video indexeraren till Azure](./media/create-account/connect-vi-to-azure-subscription-2.png)

5. När du är klar väljer **Anslut**. Den här åtgärden kan ta några minuter. 

    När du är ansluten till Azure kan visas ditt nya konto Video indexeraren i kontolistan:

    ![nytt konto](./media/create-account/new-account.png)

6. Bläddra till ditt nya konto: 

    ![Video indexeraren konto](./media/create-account/vi-account.png)

## <a name="considerations"></a>Överväganden

Azure Media Services relaterade följande gäller:

* Om du är ansluten till ett nytt Media Services-konto, visas en ny resursgrupp, Media Services-konto och ett lagringskonto i din Azure-prenumeration.
* Om du är ansluten till ett nytt Media Services-konto, Video-indexeraren kommer ange media **reserverade enheter** till 10 S3 enheter:

    ![Media Services-reserverade enheter](./media/create-account/ams-reserved-units.png)

* Om du är ansluten till ett befintligt Media Services-konto, Video indexeraren inte ändra befintliga media **reserverade enheter** konfiguration.

    Du kan behöva justera typ och antal media **reserverade enheter**, enligt dina planerade belastningen. Tänk på att om din belastningen är hög och du inte har tillräckligt med enheter eller hastighet, videor bearbetning kan medföra timeout-fel.

* Om du är ansluten till ett nytt Media Services-konto, Video indexeraren startar automatiskt en **Strömningsslutpunkt** i den:

    ![Media Services strömmande slutpunkt](./media/create-account/ams-streaming-endpoint.png)

* Om du är ansluten till ett befintligt Media Services-konto, ändras inte Video indexeraren strömmande slutpunkter konfigurationen. Om inga körs **Strömningsslutpunkt**, kommer du inte kunna titta på videor från Media Services-konto eller Video indexeraren.

## <a name="use-video-indexer-apis-v2"></a>Använda Video indexeraren v2 API: er

Du kan interagera med ditt konto och/eller med din Video indexeraren användarkonton som är anslutna till azure genom att följa anvisningarna i: [Använd API: er](video-indexer-use-apis.md).

Du bör använda samma Azure AD-användare som du använde när du ansluter till Azure.

## <a name="next-steps"></a>Nästa steg

[Granska information om resultatet JSON](video-indexer-output-json-v2.md).

