---
title: Använd Azure AD-autentisering att få åtkomst till Azure Media Services-API med övriga | Microsoft Docs
description: Lär dig hur du kommer åt Azure Media Services API med Azure Active Directory-autentisering med hjälp av REST.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/26/2017
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: ed78d6c6d4c695b841dbfbf917cd1681adc44ee7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790463"
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Använd Azure AD-autentisering för åtkomst till Azure Media Services-API med övriga

När du använder Azure AD-autentisering med Azure Media Services kan autentisera du på något av två sätt:

- **Användarautentisering** autentiserar en person som använder appen för att interagera med Azure Media Services-resurser. Interaktiva program bör först uppmana användaren att ange autentiseringsuppgifter. Ett exempel är en management konsolapp som används av behöriga användare att övervaka kodning jobb eller direktsänd strömning. 
- **Autentiseringen av tjänsten huvudnamn** autentiserar en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som körs daemon tjänster, mellannivå tjänster eller schemalagt jobb, t.ex webbprogram, funktionen appar, logikappar, API: er eller mikrotjänster.

    Den här kursen visar hur du använder Azure AD **tjänstens huvudnamn** autentisering åtkomst AMS API med övriga. 

    > [!NOTE]
    > **Tjänstens huvudnamn** är rekommenderad praxis för de flesta program som ansluter till Azure Media Services. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Hämta autentiseringsinformation om från Azure-portalen
> * Hämta åtkomsttoken med hjälp av Postman
> * Testa den **tillgångar** API: et med åtkomst-token


> [!IMPORTANT]
> Media Services stöder för närvarande Azure Access Control services autentisering-modell. Access Control-autentisering kommer dock föråldrad den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- [Skapa ett Azure Media Services-konto med hjälp av Azure portal](media-services-portal-create-account.md).
- Granska de [åtkomst till Azure Media Services API med AAD autentiseringsöversikt](media-services-use-aad-auth-to-access-ams-api.md) artikel.
- Installera den [Postman](https://www.getpostman.com/) REST-klient för att köra av REST-API som visas i den här artikeln. 

    I den här kursen är vi er **Postman** men ett REST-verktyg skulle vara lämplig. Andra alternativ är: **Visual Studio Code** med plugin-programmet för REST eller **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Hämta autentiseringsinformation om från Azure-portalen

### <a name="overview"></a>Översikt

Du behöver samla in följande datapunkter för att komma åt Media Services API.

|Inställning|Exempel|Beskrivning|
|---|-------|-----|
|Azure Active Directory-klientorganisationsdomän|Microsoft.onmicrosoft.com|Azure AD som en säker säkerhetstokentjänst (STS)-slutpunkt skapas med hjälp av följande format: https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token. Azure AD utfärdar en JWT för att komma åt resurser (en åtkomst-token).|
|REST API-slutpunkt|https://amshelloworld.restv2.westus.media.azure.net/api/|Detta är den slutpunkt mot vilken alla Media Services REST API-anrop i ditt program görs.|
|Klient-ID (program-ID)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD-program (klient)-ID. Klient-ID krävs för att få åtkomst-token. |
|Klienthemlighet|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD application-nycklar (klienthemlighet). Klienthemligheten krävs för att få åtkomst-token.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Hämta AAD auth information från Azure-portalen

Följ dessa steg om du vill hämta information om:

1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Gå till din AMS-instans.
3. Välj **API-åtkomst**.
4. Klicka på **Anslut till Azure Media Services-API med tjänstens huvudnamn**.

    ![API-åtkomst](./media/connect-with-rest/connect-with-rest01.png)

5. Välj en befintlig **Azure AD-program** eller skapa en ny (se nedan).

    > [!NOTE]
    > För Azure Media REST-begäran ska lyckas den anropande användaren måste ha en **deltagare** eller **ägare** roll för Media Services-kontot som försöker komma åt. Om du får ett undantag som säger ”fjärrservern returnerade ett fel: (401) obehörig” Se [åtkomstkontroll](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Följ dessa steg om du behöver skapa en ny AD-app:
    
    1. Tryck på **skapa nya**.
    2. Ange ett namn.
    3. Tryck på **Skapa nytt** igen.
    4. Tryck på **spara**.

    ![API-åtkomst](./media/connect-with-rest/new-app.png)

    Den nya appen visas på sidan.

6. Hämta den **klient-ID** (program-ID).
    
    1. Välj ett program.
    2. Hämta den **klient-ID** från fönstret till höger. 

    ![API-åtkomst](./media/connect-with-rest/existing-client-id.png).

7.  Hämta programmets **nyckeln** (klienthemlighet). 

    1. Klicka på den **hantera program** knappen (Observera att informationen i klient-ID är under **program-ID**). 
    2. Tryck på **nycklar**.
    
        ![API-åtkomst](./media/connect-with-rest/manage-app.png)
    3. Generera app nyckeln (klienthemlighet) genom att fylla i **beskrivning** och **EXPIRES** och trycka på **spara**.
    
        En gång i **spara** knappen är nedtryckt, visas värdet för nyckeln. Kopiera värdet för nyckeln innan de lämnar bladet.

    ![API-åtkomst](./media/connect-with-rest/connect-with-rest03.png)

Du kan lägga till värden för parametrarna för AD-anslutningen till din web.config eller app.config-fil för senare användning i din kod.

> [!IMPORTANT]
> Den **klientnyckel** är ett viktigt hemlighet och vara korrekt skyddade i ett nyckelvalv eller krypterade i produktion.

## <a name="get-the-access-token-using-postman"></a>Hämta åtkomsttoken med hjälp av Postman

Det här avsnittet visar hur du använder **Postman** att köra en REST-API som returnerar en JWT ägar-Token (åtkomst-token). För att anropa Media Services REST API: er, måste du lägga till rubriken ”tillstånd” anropen och lägga till värdet för ”ägar *your_access_token*” till varje anrop (som visas i nästa avsnitt i den här kursen). 

1. Öppna **Postman**.
2. Välj **POST**.
3. Ange den URL som innehåller ditt klientnamn med hjälp av följande format: klientnamnet ska avslutas med **. onmicrosoft.com** och URL: en måste sluta med **oauth2-token**: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Välj den **huvuden** fliken.
5. Ange den **huvuden** information med hjälp av datarutnätet ”nyckel/värde”. 

    ![Datarutnätet](./media/connect-with-rest/headers-data-grid.png)

    Alternativt klickar du på **Massredigera** länka till höger i fönstret Postman och klistra in följande kod.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Tryck på den **brödtext** fliken.
7. Ange brödtext information med hjälp av datarutnätet ”nyckel/värde” (Ersätt klient-ID och Hemlig värden). 

    ![Datarutnätet](./media/connect-with-rest/data-grid.png)

    Alternativt klickar du på **Massredigera** till höger i fönstret Postman och klistra in följande organ (Ersätt klient-ID och Hemlig värden):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your AAD Application}
        client_secret:{Your client secret that you got from your AAD Application's Keys}
        resource:https://rest.media.azure.net

8. Tryck på **Skicka**.

    ![Hämta token](./media/connect-with-rest/connect-with-rest04.png)

Returnerade svaret innehåller den **åtkomsttoken** att du behöver använda för åtkomst till alla AMS APIs.

## <a name="test-the-assets-api-using-the-access-token"></a>Testa den **tillgångar** API: et med åtkomst-token

Det här avsnittet visas hur du kommer åt den **tillgångar** API med hjälp av **Postman**.

1. Öppna **Postman**.
2. Välj **GET**.
3. Klistra in REST API-slutpunkt (t.ex. https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Välj den **auktorisering** fliken. 
5. Välj **Ägartoken**.
6. Klistra in den token som skapades i föregående avsnitt.

    ![Hämta token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman UX kan vara olika mellan Mac- och PC. Om Mac-version inte har alternativet ”ägar-Token” i den **autentisering** avsnittet listrutan som du bör lägga till den **auktorisering** huvud manuellt på Mac-klienten.

   ![Auth-huvud](./media/connect-with-rest/auth-header.png)

7. Välj **huvuden**.
5. Klicka på **Massredigera** länka Postman fönstret till höger.
6. Klistra in följande huvuden:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Tryck på **Skicka**.

Returnerade svaret innehåller de resurser som finns i ditt konto.

## <a name="next-steps"></a>Nästa steg

* Prova den här exempelkoden i [Azure AD-autentisering för Azure Media Services-åtkomst: båda via REST API](https://github.com/willzhan/WAMSRESTSoln)
* [Överföra filer med .NET](media-services-dotnet-upload-files.md)
