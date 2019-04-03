---
title: 'Autentisering för slutanvändare: Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft Docs'
description: Lär dig att uppnå slutanvändarautentisering med Azure Data Lake Storage Gen1 med Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c0fe63e395ee08cb65e9bbbadc4ce1f03032ce95
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880091"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Slutanvändarautentisering med Azure Data Lake Storage Gen1 med Azure Active Directory
> [!div class="op_single_selector"]
> * [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Tjänst-till-tjänst-autentisering](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Storage Gen1 använder Azure Active Directory för autentisering. Innan du skapar ett program som fungerar med Data Lake Storage Gen1 eller Azure Data Lake Analytics, måste du bestämma hur du autentiserar ditt program med Azure Active Directory (AD Azure). De två viktigaste alternativen är:

* Slutanvändarautentisering (den här artikeln)
* Tjänst-till-tjänst-autentisering (Välj detta alternativ från listan ovan)

Båda alternativen resultera i ditt program som tillhandahålls med en OAuth 2.0-token som åt varje begärande gjordes till Data Lake Storage Gen1 eller Azure Data Lake Analytics.

Den här artikeln berättar om hur du skapar en **Azure AD-program för slutanvändarautentisering**. Anvisningar för Azure AD-programkonfiguration för tjänst-till-tjänst-autentisering finns i [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* Ditt prenumerations-ID. Du kan hämta den från Azure-portalen. Till exempel finns den på Data Lake Storage Gen1 kontobladet.
  
    ![Hämta prenumerations-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Din Azure AD-domännamn. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. Från skärmbilden nedan domännamnet är **contoso.onmicrosoft.com**, och inom parentes även är klient-ID. 
  
    ![Hämta AAD-domän](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Ditt Azure-klient-ID. Anvisningar om hur du hämtar klient-ID finns i [hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).

## <a name="end-user-authentication"></a>Slutanvändarautentisering
Den här autentiseringsmekanismen är den rekommenderade metoden om du vill att en användare att logga in på ditt program via Azure AD. Programmet kan sedan komma åt Azure-resurser med samma nivå på dataåtkomst som användaren som har loggat in. Användaren måste ange sina autentiseringsuppgifter med jämna mellanrum i ordning för ditt program att upprätthålla åtkomsten.

Resultatet av att ha slutanvändarens inloggningen är att ditt program är baserat på en åtkomsttoken och en uppdateringstoken. Åtkomsttoken hämtar kopplade till varje begärande gjordes till Data Lake Storage Gen1 eller Data Lake Analytics och är giltig i en timme som standard. Uppdateringstoken kan användas till att hämta en ny åtkomsttoken och den är giltig i upp till två veckor som standard. Du kan använda två olika metoder för slutanvändarinloggning.

### <a name="using-the-oauth-20-pop-up"></a>Med hjälp av OAuth 2.0 popup-fönstret
Ditt program kan utlösa en OAuth 2.0-auktorisering popup-menyn där användaren kan ange sina autentiseringsuppgifter. Det här popup-fönster fungerar även med Azure AD, tvåfaktorautentisering (2FA)-processen om det behövs. 

> [!NOTE]
> Den här metoden stöds inte ännu i Azure AD Authentication Library (ADAL) för Python och Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Skicka direkt i användarens autentiseringsuppgifter
Ditt program kan direkt att ange autentiseringsuppgifter för användare till Azure AD. Den här metoden fungerar bara med organisationens ID user accounts Det är inte kompatibelt med personal / ”live ID” användarkonton, inklusive de konton som slutar på @outlook.com eller @live.com. Dessutom är är den här metoden inte kompatibel med användarkonton som kräver Azure AD, tvåfaktorautentisering (2FA).

### <a name="what-do-i-need-for-this-approach"></a>Vad behöver jag för den här metoden?
* Azure AD-domännamn. Det här kravet finns redan i de nödvändiga komponenterna i den här artikeln.
* Azure AD-klient-ID. Det här kravet finns redan i de nödvändiga komponenterna i den här artikeln.
* Azure AD **internt program**
* Program-ID för det Azure AD-programmet
* Omdirigerings-URI för det Azure AD-programmet
* Ange delegerade behörigheter


## <a name="step-1-create-an-active-directory-native-application"></a>Steg 1: Skapa ett internt Active Directory-program

Skapa och konfigurera en Azure AD-program för slutanvändarautentisering med Data Lake Storage Gen1 med Azure Active Directory. Anvisningar finns i [skapa ett Azure AD-program](../active-directory/develop/howto-create-service-principal-portal.md).

Kontrollera att du väljer när du följer anvisningarna i länken **interna** för programtyp, enligt följande skärmbild:

![Skapa webbapp](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "skapar en inbyggd app")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Steg 2: Hämta program-ID och omdirigerings-URI

Se [hämta program-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key) att hämta program-ID.

Utför följande steg för att hämta omdirigeringen-URI.

1. Azure-portalen väljer du **Azure Active Directory**, klickar du på **appregistreringar**, och leta upp och klicka på den Azure AD-program som du skapade.

2. Från den **inställningar** bladet för programmet, klickar du på **omdirigerings-URI: er**.

    ![Hämta omdirigerings-URI](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Kopiera värdet som visas.


## <a name="step-3-set-permissions"></a>Steg 3: Ställa in behörigheter

1. Azure-portalen väljer du **Azure Active Directory**, klickar du på **appregistreringar**, och leta upp och klicka på den Azure AD-program som du skapade.

2. Från den **inställningar** bladet för programmet, klickar du på **nödvändiga behörigheter**, och klicka sedan på **Lägg till**.

    ![klient-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. I den **Lägg till API-åtkomst** bladet klickar du på **Välj en API**, klickar du på **Azure Data Lake**, och klicka sedan på **Välj**.

    ![klient-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  I den **Lägg till API-åtkomst** bladet klickar du på **Välj behörigheter**, markera kryssrutan för att ge **fullständig åtkomst till Data Lake Store**, och klicka sedan på **Välj**.

    ![klient-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Klicka på **Klar**.

5. Upprepa de två sista stegen för att bevilja behörigheter för **Windows Azure Service Management API** samt.
   
## <a name="next-steps"></a>Nästa steg
I den här artikeln får du skapade en Azure AD-program och samlat in den information du behöver i ditt klientprogram att du skapar med hjälp av .NET SDK, Java SDK, REST API, osv. Du kan nu fortsätta i följande artiklar som pratar om hur du använder Azure AD-webbappen att först autentisera med Data Lake Storage Gen1 och utföra andra åtgärder i arkivet.

* [Klient-slutanvändaren-autentisering med Data Lake Storage Gen1 med hjälp av Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Slutanvändarautentisering med Data Lake Storage Gen1 med .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Slutanvändarautentisering med Data Lake Storage Gen1 med hjälp av Python](data-lake-store-end-user-authenticate-python.md)
* [Slutanvändarautentisering med Data Lake Storage Gen1 med hjälp av REST API](data-lake-store-end-user-authenticate-rest-api.md)

