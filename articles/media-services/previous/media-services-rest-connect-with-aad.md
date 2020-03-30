---
title: Använd Azure AD-autentisering för att komma åt Azure Media Services API med REST | Microsoft-dokument
description: Lär dig hur du kommer åt Azure Media Services API med Azure Active Directory-autentisering med REST.
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
ms.author: juliako
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: a693eb374365670da3fe8c4b2bb8ce664a024217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295448"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Använda Azure AD-autentisering för att ansluta till API:et för Media Services med REST

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

När du använder Azure AD-autentisering med Azure Media Services kan du autentisera på ett av två sätt:

- **Användarautentisering** autentiserar en person som använder appen för att interagera med Azure Media Services-resurser. Det interaktiva programmet bör först uppmana användaren om autentiseringsuppgifter. Ett exempel är en hanteringskonsolapp som används av behöriga användare för att övervaka kodningsjobb eller livestreaming. 
- **Tjänstens huvudautentisering** autentiserar en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemontjänster, mellannivåtjänster eller schemalagda jobb, till exempel webbappar, funktionsappar, logikappar, API:er eller mikrotjänster.

    Den här självstudien visar hur du använder Azure **AD-tjänstens huvudautentisering** för att komma åt AMS API med REST. 

    > [!NOTE]
    > **Tjänstens huvudnamn** är den rekommenderade bästa metoden för de flesta program som ansluter till Azure Media Services. 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Hämta autentiseringsinformation från Azure-portalen
> * Hämta åtkomsttoken med Postman
> * Testa **API:et för tillgångar** med hjälp av åtkomsttoken


> [!IMPORTANT]
> För närvarande stöder Media Services autentiseringsmodellen för Azure Access Control-tjänster. Åtkomstkontrollautentisering kommer dock att vara föråldrad 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- [Skapa ett Azure Media Services-konto med Azure-portalen](media-services-portal-create-account.md).
- Granska [api:et för åtkomst till Azure Media Services med azure AD-autentiseringsöversiktsartikeln.](media-services-use-aad-auth-to-access-ams-api.md)
- Installera [REST-klienten för postman](https://www.getpostman.com/) för att köra REST-API:erna som visas i den här artikeln. 

    I den här guiden använder vi **Postman** men alla REST verktyg skulle vara lämpliga. Andra alternativ är: **Visual Studio Code** med REST plugin eller **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Hämta autentiseringsinformation från Azure-portalen

### <a name="overview"></a>Översikt

För att komma åt Api för Media Services måste du samla in följande datapunkter.

|Inställning|Exempel|Beskrivning|
|---|-------|-----|
|Azure Active Directory-klientdomän|microsoft.onmicrosoft.com|Azure AD som en STS-slutpunkt (Secure Token Service) skapas med följande format: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>. Azure AD utfärdar en JWT för att komma åt resurser (en åtkomsttoken).|
|Slutpunkt för REST-API:t|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Det här är slutpunkten som alla REST API-anrop för Media Services i programmet görs mot.|
|Klient-ID (program-ID)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD-program (klient) ID. Klient-ID krävs för att hämta åtkomsttoken. |
|Klienthemlighet|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD-programnycklar (klienthemlighet). Klienthemligheten krävs för att hämta åtkomsttoken.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Hämta AAD-auth-information från Azure-portalen

Så här hämtar du informationen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till DIN AMS-instans.
3. Välj **API-åtkomst**.
4. Klicka på **Anslut till Azure Media Services API med tjänstens huvudnamn**.

    ![API-åtkomst](./media/connect-with-rest/connect-with-rest01.png)

5. Välj ett befintligt **Azure AD-program** eller skapa ett nytt (visas nedan).

    > [!NOTE]
    > För att Azure Media REST-begäran ska lyckas måste den anropande användaren ha en **deltagar-** eller **ägarroll** för det Media Services-konto som den försöker komma åt. Om du får ett undantag med det står "Fjärrservern returnerade ett fel: (401) Obehörigt", se [Åtkomstkontroll](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Om du behöver skapa en ny AD-app gör du så här:
    
   1. Tryck på **Skapa nytt**.
   2. Ange ett namn.
   3. Tryck på **Skapa ny** igen.
   4. Tryck på **Spara**.

      ![API-åtkomst](./media/connect-with-rest/new-app.png)

      Den nya appen visas på sidan.

6. Hämta **klient-ID** (program-ID).
    
   1. Välj programmet.
   2. Hämta **klient-ID:t** från fönstret till höger. 

      ![API-åtkomst](./media/connect-with-rest/existing-client-id.png)

7. Hämta programmets **nyckel** (klienthemlighet). 

   1. Klicka på knappen **Hantera program** (observera att klient-ID-informationen finns under **Program-ID**). 
   2. Tryck på **tangenter**.
    
       ![API-åtkomst](./media/connect-with-rest/manage-app.png)
   3. Generera apptangenten (klienthemligheten) genom att fylla i **BESKRIVNING** och **UPPHÖR ATT** GÄLLA och trycka på **Spara**.
    
       När **knappen Spara** har tryckts in visas tangentvärdet. Kopiera nyckelvärdet innan du lämnar bladet.

   ![API-åtkomst](./media/connect-with-rest/connect-with-rest03.png)

Du kan lägga till värden för AD-anslutningsparametrar i filen web.config eller app.config som senare används i koden.

> [!IMPORTANT]
> **Klientnyckeln** är en viktig hemlighet och bör säkras korrekt i ett nyckelvalv eller krypteras i produktionen.

## <a name="get-the-access-token-using-postman"></a>Hämta åtkomsttoken med Postman

Det här avsnittet visar hur du använder **Postman** för att köra ett REST API som returnerar en JWT-innehavartoken (åtkomsttoken). Om du vill anropa ett REST-API för Media Services måste du lägga till "Auktoriseringshuvudet" i anropshuvudet och lägga till värdet "Bärare *your_access_token"* till varje anrop (som visas i nästa avsnitt i den här självstudien). 

1. Öppna **Brevbäraren**.
2. Välj **POST**.
3. Ange webbadressen som innehåller klientnamnet med följande format: klientnamnet ska sluta med **.onmicrosoft.com** och URL:en ska sluta med **oauth2/token:** 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Välj fliken **Rubriker.**
5. Ange **information om rubriker** med datarutnätet "Nyckel/värde". 

    ![Data rutnät](./media/connect-with-rest/headers-data-grid.png)

    Du kan också klicka på länken **Massredigering** till höger om Postman-fönstret och klistra in följande kod.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Tryck på fliken **Brödtext.**
7. Ange brödtextinformationen med datarutnätet "Nyckel/värde" (ersätt klient-ID och hemliga värden). 

    ![Data rutnät](./media/connect-with-rest/data-grid.png)

    Du kan också klicka på **Massredigering** till höger om Postman-fönstret och klistra in följande brödtext (ersätt klient-ID och hemliga värden):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. Tryck på **Skicka**.

    ![hämta token](./media/connect-with-rest/connect-with-rest04.png)

Det returnerade svaret innehåller **den åtkomsttoken** som du behöver använda för att komma åt alla AMS-API:er.

## <a name="test-the-assets-api-using-the-access-token"></a>Testa **API:et för tillgångar** med hjälp av åtkomsttoken

I det här avsnittet visas hur du kommer åt **API:et för tillgångar** med **Postman**.

1. Öppna **Brevbäraren**.
2. Välj **GET**.
3. Klistra in REST API-slutpunkten (t.ex.https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Välj fliken **Auktorisering.** 
5. Välj **innehavartoken**.
6. Klistra in den token som skapades i föregående avsnitt.

    ![hämta token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman UX kan skilja sig mellan en Mac och PC. Om Mac-versionen inte har alternativet "Innehavartoken" i listrutan **Autentisering** bör du lägga till **auktoriseringshuvudet** manuellt på Mac-klienten.

   ![Auth-huvud](./media/connect-with-rest/auth-header.png)

7. Välj **Rubriker**.
5. Klicka på länken **Massredigering** till höger i postman-fönstret.
6. Klistra in följande rubriker:

        x-ms-version:2.19
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Tryck på **Skicka**.

Det returnerade svaret innehåller de tillgångar som finns i ditt konto.

## <a name="next-steps"></a>Nästa steg

* Prova den här exempelkoden i [Azure AD-autentisering för Azure Media Services Access: Både via REST API](https://github.com/willzhan/WAMSRESTSoln)
* [Ladda upp filer med .NET](media-services-dotnet-upload-files.md)
