---
title: Använd Azure AD-autentisering för att få åtkomst till Azure Media Services-API med REST | Microsoft Docs
description: Lär dig hur du kommer åt Azure Media Services-API med Azure Active Directory-autentisering med hjälp av REST.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: 6284a1aa0cc3a49291553309b058e4d9f65b24c6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701019"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Använda Azure AD-autentisering för att få åtkomst till Media Services-API med REST

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledningen från v2 till v3](../latest/migrate-from-v2-to-v3.md)

När du använder Azure AD-autentisering med Azure Media Services kan autentisera du på något av två sätt:

- **Användarautentisering** autentiserar en person som använder appen för att interagera med Azure Media Services-resurser. Interaktivt program bör först frågar användaren om autentiseringsuppgifter. Ett exempel är en management-konsolapp som används av behöriga användare att övervaka kodningsjobb eller liveuppspelning. 
- **Autentisering av tjänstens huvudnamn** autentiserar en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemon-tjänster, mellannivå tjänster eller schemalagda jobb, t.ex webbappar, funktionsappar, logic apps, API: er eller mikrotjänster.

    Den här självstudien visar hur du använder Azure AD **tjänstens huvudnamn** autentisering för att få åtkomst till AMS API med REST. 

    > [!NOTE]
    > **Tjänstens huvudnamn** är rekommenderad praxis för de flesta program som ansluter till Azure Media Services. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Hämta autentiseringsinformation om från Azure portal
> * Hämta åtkomsttoken med hjälp av Postman
> * Testa den **tillgångar** API med åtkomsttoken


> [!IMPORTANT]
> Media Services stöder för närvarande Azure Access Control services autentisering-modell. Dock gälla autentisering för Access Control upphör att den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- [Skapa ett Azure Media Services-konto med Azure portal](media-services-portal-create-account.md).
- Granska den [åtkomst till Azure Media Services-API med översikt över Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md) artikeln.
- Installera den [Postman](https://www.getpostman.com/) REST-klient för att köra REST API: er i den här artikeln. 

    I den här självstudien använder vi **Postman** men alla REST-verktyget skulle vara lämplig. Andra alternativ är: **Visual Studio Code** med plugin-programmet för REST eller **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Hämta autentiseringsinformation om från Azure portal

### <a name="overview"></a>Översikt

Du behöver samla in följande datapunkter för att komma åt Media Services-API.

|Inställning|Exempel|Beskrivning|
|---|-------|-----|
|Azure Active Directory-klientorganisationsdomän|microsoft.onmicrosoft.com|Azure AD som en säker säkerhetstokentjänst (STS)-slutpunkt skapas med hjälp av följande format: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>. Azure AD utfärdar en JWT för att komma åt resurser (en åtkomsttoken).|
|REST API-slutpunkt|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Detta är den slutpunkt mot vilken alla Media Services REST API-anrop i ditt program har utförts.|
|Klient-ID (program-ID)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD-program (klient)-ID. Klient-ID krävs för att hämta åtkomsttoken. |
|Klienthemlighet|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD programnycklar (klienthemlighet). Klienthemlighet krävs för att hämta åtkomsttoken.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Hämta AAD-autentiseringsinformation från Azure-portalen

Följ dessa steg för att hämta information:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till AMS-instans.
3. Välj **API-åtkomst**.
4. Klicka på **Anslut till Azure Media Services-API med tjänstens huvudnamn**.

    ![API-åtkomst](./media/connect-with-rest/connect-with-rest01.png)

5. Välj en befintlig **Azure AD-program** eller skapa en ny (se nedan).

    > [!NOTE]
    > För Azure Media REST-begäranden ska lyckas, den anropande användaren måste ha en **deltagare** eller **ägare** för Media Services-kontot som försöker komma åt. Om du får ett undantag där det står ”fjärrservern returnerade ett fel: (401) Ej behörig ”, se [åtkomstkontroll](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Följ dessa steg om du vill skapa en ny AD-app:
    
   1. Tryck på **skapa en ny**.
   2. Ange ett namn.
   3. Tryck på **Skapa ny** igen.
   4. Tryck på **Spara**.

      ![API-åtkomst](./media/connect-with-rest/new-app.png)

      Den nya appen som visas på sidan.

6. Hämta den **klient-ID** (program-ID).
    
   1. Välj ett program.
   2. Hämta den **klient-ID** från fönstret till höger. 

      ![API-åtkomst](./media/connect-with-rest/existing-client-id.png)

7. Hämta programmets **nyckel** (klienthemlighet). 

   1. Klicka på den **Hanteringsappen** knappen (Observera att den klient-ID-informationen är under **program-ID**). 
   2. Tryck på **nycklar**.
    
       ![API-åtkomst](./media/connect-with-rest/manage-app.png)
   3. Generera appnyckel (klienthemlighet) genom att fylla i **beskrivning** och **FÖRFALLER** och trycka på **spara**.
    
       När den **spara** knappen trycks, nyckelvärdet visas. Kopiera nyckelvärdet innan de lämnar bladet.

   ![API-åtkomst](./media/connect-with-rest/connect-with-rest03.png)

Du kan lägga till värden för parametrarna för AD-anslutningen i filen web.config eller app.config för senare användning i din kod.

> [!IMPORTANT]
> Den **klientnyckel** är ett viktigt hemlighet och bör vara korrekt skyddade i ett nyckelvalv eller krypterade i produktion.

## <a name="get-the-access-token-using-postman"></a>Hämta åtkomsttoken med hjälp av Postman

Det här avsnittet visar hur du använder **Postman** att köra ett REST-API som returnerar en JWT ägar-Token (åtkomsttoken). För att anropa alla Media Services REST-API kan du behöva lägga till rubriken ”Authorization” anrop och lägga till värdet för ”ägar *your_access_token*” för varje anrop (som visas i nästa avsnitt av den här kursen). 

1. Öppna **Postman**.
2. Välj **POST**.
3. Ange den URL som innehåller ditt klientnamn med hjälp av följande format: klientnamnet ska sluta med **. onmicrosoft.com** och URL: en ska sluta med **oauth2/token**: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Välj den **rubriker** fliken.
5. Ange den **rubriker** information med hjälp av datarutnätet ”nyckel/värde”. 

    ![Datarutnät](./media/connect-with-rest/headers-data-grid.png)

    Alternativt klickar du på **Massredigera** länken till höger i fönstret Postman och klistra in följande kod.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Tryck på den **brödtext** fliken.
7. Ange brödtext informationen med hjälp av datarutnätet ”nyckel/värde” (Ersätt klient-ID och hemliga värden). 

    ![Datarutnät](./media/connect-with-rest/data-grid.png)

    Alternativt klickar du på **Massredigera** till höger i fönstret Postman och klistra in följande brödtext (Ersätt klient-ID och hemliga värden):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. Tryck på **Skicka**.

    ![Hämta åtkomsttoken](./media/connect-with-rest/connect-with-rest04.png)

Returnerade svaret innehåller den **åtkomsttoken** att du behöver använda för att få åtkomst till alla AMS APIs.

## <a name="test-the-assets-api-using-the-access-token"></a>Testa den **tillgångar** API med åtkomsttoken

Det här avsnittet visas hur du kommer åt den **tillgångar** API med hjälp av **Postman**.

1. Öppna **Postman**.
2. Välj **GET**.
3. Klistra in REST API-slutpunkt (t.ex. https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Välj den **auktorisering** fliken. 
5. Välj **Ägartoken**.
6. Klistra in den token som skapades i föregående avsnitt.

    ![Hämta åtkomsttoken](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman-UX kan vara olika mellan en Mac och PC. Om Mac-version inte har alternativet ”ägar-Token” i den **autentisering** avsnittet listrutan som du bör lägga till den **auktorisering** rubrik manuellt på Mac-klienten.

   ![Auth-huvud](./media/connect-with-rest/auth-header.png)

7. Välj **rubriker**.
5. Klicka på **Massredigera** länken till höger i Postman-fönstret.
6. Klistra in följande huvuden:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Tryck på **Skicka**.

Returnerade svaret innehåller de resurser som finns i ditt konto.

## <a name="next-steps"></a>Nästa steg

* Prova den här exempelkoden i [Azure AD-autentisering för Azure Media Services-åtkomst: Både via REST-API](https://github.com/willzhan/WAMSRESTSoln)
* [Ladda upp filer med .NET](media-services-dotnet-upload-files.md)
