---
title: 'Slutanvändarens autentisering: Data Lake Store med Azure Active Directory | Microsoft Docs'
description: Lär dig att uppnå slutanvändarens autentisering med Data Lake Store med Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 39ee32ec8b1d005aac12cb88262d7e2f37898721
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Slutanvändarens autentisering med Data Lake Store med Azure Active Directory
> [!div class="op_single_selector"]
> * [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Tjänst-till-tjänst-autentisering](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store använder Azure Active Directory för autentisering. Innan du skapar ett program som fungerar med Azure Data Lake Store eller Azure Data Lake Analytics kan måste du bestämma hur du autentiserar ditt program med Azure Active Directory (AD Azure). De största tillgängliga alternativ är:

* Slutanvändarens autentisering (den här artikeln)
* Tjänst-till-tjänst-autentisering (Välj det här alternativet från listrutan ovan)

Båda dessa alternativ kan resultera i ditt program som tillhandahålls med en OAuth 2.0-token som hämtar kopplade till varje begäran till Azure Data Lake Store eller Azure Data Lake Analytics.

Den här artikeln handlar om hur du skapar en **interna Azure AD-program för slutanvändare autentisering**. Mer information om programkonfigurationen för Azure AD för autentisering för tjänst-till-tjänst finns [tjänst-till-tjänst-autentisering med Data Lake Store med Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* Ditt prenumerations-ID. Du kan hämta den från Azure-portalen. Till exempel är den tillgänglig från bladet Data Lake Store-konto.
  
    ![Hämta prenumerations-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Din Azure AD-domännamn. Du kan hämta den håller musen i övre högra hörnet i Azure-portalen. Från skärmbilden nedan domännamnet är **contoso.onmicrosoft.com**, och GUID inom hakparenteser är klient-ID. 
  
    ![Hämta AAD-domän](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Azure-klient-ID. Instruktioner om hur du hämtar klient-ID finns [hämta klient-ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="end-user-authentication"></a>Slutanvändarautentisering
Den här autentiseringsmekanismen är den rekommenderade metoden om du vill att en användare kan logga in på ditt program via Azure AD. Programmet kan sedan komma åt Azure-resurser med samma nivå av åtkomst som användaren inloggad. Användaren måste ange sina autentiseringsuppgifter med jämna mellanrum i ordning för programmet att upprätthålla åtkomsten.

Resultat av att låta slutanvändare inloggningen är att tillämpningsprogrammet ges en åtkomst-token och en uppdateringstoken. Åtkomsttoken hämtar ansluten till varje begäran till Data Lake Store eller Data Lake Analytics och är giltig i en timme som standard. Uppdateringstoken som kan användas för att hämta en ny åtkomsttoken och är giltig i upp till två veckor som standard. Du kan använda två olika metoder för slutanvändaren inloggningen.

### <a name="using-the-oauth-20-pop-up"></a>Med hjälp av OAuth 2.0-popup-fönster
Programmet kan utlösa en OAuth 2.0 auktorisering popup, där användaren kan ange sina autentiseringsuppgifter. Det här popup-fönster fungerar även med Azure AD tvåfaktorsautentisering (2FA)-processen om det behövs. 

> [!NOTE]
> Den här metoden stöds inte ännu i Azure AD Authentication Library (ADAL) för Python eller Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Skicka direkt i användarens autentiseringsuppgifter
Programmet kan direkt ange autentiseringsuppgifter för användare till Azure AD. Den här metoden fungerar bara med organisationens ID användarkonton; Det är inte kompatibelt med personal / ”live ID” användarkonton, inklusive de konton som slutar på @outlook.com eller @live.com. Dessutom kan är den här metoden inte kompatibel med användarkonton som kräver Azure AD tvåfaktorsautentisering (2FA).

### <a name="what-do-i-need-for-this-approach"></a>Vad behöver jag för den här metoden?
* Azure AD-domännamn. Det här kravet finns redan i komponenten i den här artikeln.
* Azure AD-klient-ID. Det här kravet finns redan i komponenten i den här artikeln.
* Azure AD **programspecifika**
* Program-ID för det interna Azure AD-programmet
* Omdirigerings-URI för det interna Azure AD-programmet
* Ange delegerade behörigheter


## <a name="step-1-create-an-active-directory-native-application"></a>Steg 1: Skapa en intern Active Directory-program

Skapa och konfigurera en inbyggd Azure AD-program för slutanvändare autentisering med Azure Data Lake Store med Azure Active Directory. Instruktioner finns i [skapa en Azure AD-program](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Kontrollera att du väljer när du följer anvisningarna i länken **interna** för programtyp, som visas i följande skärmbild:

![Skapa webbapp](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "skapa inbyggda appen")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Steg 2: Hämta program-ID och omdirigerings-URI

Se [hämta program-ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) att hämta program-ID.

Utför följande steg för att hämta omdirigerings-URI.

1. Välj den Azure-portalen **Azure Active Directory**, klickar du på **App registreringar**, söka efter och klickar på den interna Azure AD-program som du skapade.

2. Från den **inställningar** bladet för programmet, klickar du på **omdirigerings-URI: er**.

    ![Get omdirigerings-URI](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Kopiera värdet som visas.


## <a name="step-3-set-permissions"></a>Steg 3: Ange behörigheter

1. Välj den Azure-portalen **Azure Active Directory**, klickar du på **App registreringar**, söka efter och klickar på den interna Azure AD-program som du skapade.

2. Från den **inställningar** bladet för programmet, klickar du på **nödvändiga behörigheter**, och klicka sedan på **Lägg till**.

    ![klient-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. I den **lägga till API-åtkomst** bladet, klickar du på **väljer en API**, klickar du på **Azure Data Lake**, och klicka sedan på **Välj**.

    ![klient-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  I den **lägga till API-åtkomst** bladet, klickar du på **Välj behörigheter**, markera kryssrutan för att ge **fullständig åtkomst till Data Lake Store**, och klicka sedan på **Välj**.

    ![klient-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Klicka på **Klar**.

5. Upprepa de två sista stegen för att bevilja behörigheter för **Windows Azure Service Management API** samt.
   
## <a name="next-steps"></a>Nästa steg
I den här artikeln du skapat ett enhetligt Azure AD-program och samlat in den information du behöver i ditt klientprogram att du skapar med hjälp av SDK för .NET, Java SDK, REST-API och så vidare. Du kan nu fortsätta i följande artiklar som talar om hur du använder Azure AD-webbprogram att först autentisera med Data Lake Store och sedan utföra andra åtgärder i store.

* [Klient-slutanvändaren-autentisering med Data Lake Store med hjälp av Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Slutanvändarens autentisering med Data Lake Store med hjälp av .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Slutanvändarens autentisering med Data Lake Store använder Python](data-lake-store-end-user-authenticate-python.md)
* [Slutanvändarens autentisering med Data Lake Store med hjälp av REST API](data-lake-store-end-user-authenticate-rest-api.md)

