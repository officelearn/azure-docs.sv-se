---
title: 'Autentisering av slutanvändare: Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du uppnår slutanvändarautentisering med Azure Data Lake Storage Gen1 med Azure Active Directory
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
ms.openlocfilehash: 4c2b774c304e46f9fc68f3beaf64218e614ecad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66234063"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Slutanvändarens autentisering med Azure Data Lake Storage Gen1 med Azure Active Directory
> [!div class="op_single_selector"]
> * [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Tjänst-till-tjänst-autentisering](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Storage Gen1 använder Azure Active Directory för autentisering. Innan du redigerar ett program som fungerar med Data Lake Storage Gen1 eller Azure Data Lake Analytics måste du bestämma hur du autentiserar ditt program med Azure Active Directory (Azure AD). De två viktigaste alternativen är:

* Autentisering av slutanvändare (den här artikeln)
* Autentisering från service till tjänst (välj det här alternativet i listrutan ovan)

Båda dessa alternativ resulterar i att ditt program får en OAuth 2.0-token, som kopplas till varje begäran som görs till Data Lake Storage Gen1 eller Azure Data Lake Analytics.

Den här artikeln beskriver hur du skapar ett **inbyggt Azure AD-program för slutanvändarens autentisering**. Instruktioner om Azure AD-programkonfiguration för autentisering mellan tjänst och tjänst finns i [Autentisering från Tjänst till tjänst med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Krav
* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* Ditt prenumerations-ID. Du kan hämta den från Azure-portalen. Den är till exempel tillgänglig från kontobladet DataSjölagring Gen1.
  
    ![Hämta prenumerations-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Ditt Azure AD-domännamn. Du kan hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. Från skärmbilden nedan är domännamnet **contoso.onmicrosoft.com**och GUID inom parentes är klient-ID. 
  
    ![Skaffa AAD-domän](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Ditt Azure-klient-ID. Instruktioner om hur du hämtar klient-ID finns i [Hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="end-user-authentication"></a>Slutanvändarautentisering
Den här autentiseringsmekanismen är den rekommenderade metoden om du vill att en slutanvändare ska logga in på ditt program via Azure AD. Ditt program kan sedan komma åt Azure-resurser med samma åtkomstnivå som slutanvändaren som loggade in. Slutanvändaren måste ange sina autentiseringsuppgifter regelbundet för att ditt program ska kunna behålla åtkomsten.

Resultatet av att slutanvändaren loggar in är att ditt program får en åtkomsttoken och en uppdateringstoken. Åtkomsttoken kopplas till varje begäran som görs till Data Lake Storage Gen1 eller Data Lake Analytics, och den är giltig i en timme som standard. Uppdateringstoken kan användas för att hämta en ny åtkomsttoken och den är giltig i upp till två veckor som standard. Du kan använda två olika metoder för att logga in för slutanvändare.

### <a name="using-the-oauth-20-pop-up"></a>Använda popup-programmet OAuth 2.0
Ditt program kan utlösa ett OAuth 2.0-auktoriseringspop- -där slutanvändaren kan ange sina autentiseringsuppgifter. Det här popup-programmet fungerar även med Azure AD Two-factor Authentication (2FA) om det behövs. 

> [!NOTE]
> Den här metoden stöds ännu inte i Azure AD Authentication Library (ADAL) för Python eller Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Direkt passerar in användaruppgifter
Ditt program kan direkt tillhandahålla användaruppgifter till Azure AD. Den här metoden fungerar bara med användarkonton för organisations-ID. Det är inte kompatibelt med personliga / "live ID" användarkonton, inklusive de konton som slutar i @outlook.com eller @live.com. Dessutom är den här metoden inte kompatibel med användarkonton som kräver Azure AD Two-factor Authentication (2FA).

### <a name="what-do-i-need-for-this-approach"></a>Vad behöver jag för detta tillvägagångssätt?
* Azure AD-domännamn. Det här kravet visas redan i förutsättningen för den här artikeln.
* Azure AD-klient-ID. Det här kravet visas redan i förutsättningen för den här artikeln.
* Azure **AD-inbyggt program**
* Program-ID för det inbyggda Azure AD-programmet
* Omdirigera URI för det inbyggda Azure AD-programmet
* Ange delegerade behörigheter


## <a name="step-1-create-an-active-directory-native-application"></a>Steg 1: Skapa ett Active Directory-inbyggt program

Skapa och konfigurera ett Azure AD-inbyggt program för slutanvändarens autentisering med Data Lake Storage Gen1 med Hjälp av Azure Active Directory. Instruktioner finns i [Skapa ett Azure AD-program](../active-directory/develop/howto-create-service-principal-portal.md).

När du följer instruktionerna i länken kontrollerar du att du väljer **Native** för programtyp, som visas i följande skärmbild:

![Skapa webbapp](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Skapa inbyggd app")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Steg 2: Hämta program-ID och omdirigera URI

Se [Hämta program-ID:et](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in) för att hämta program-ID:et.

Gör följande steg om du vill hämta omdirigerings-URI:n.

1. På Azure-portalen väljer du **Azure Active Directory**, klickar på **Appregistreringar**och sedan hitta och klicka på det inbyggda Azure AD-programmet som du skapade.

2. Klicka på **Omdirigera URI:er**i bladet **Inställningar** för programmet .

    ![Hämta Omdirigera URI](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Kopiera värdet som visas.


## <a name="step-3-set-permissions"></a>Steg 3: Ange behörigheter

1. På Azure-portalen väljer du **Azure Active Directory**, klickar på **Appregistreringar**och sedan hitta och klicka på det inbyggda Azure AD-programmet som du skapade.

2. Klicka på **Obligatoriska behörigheter**i bladet **Inställningar** för programmet och klicka sedan på **Lägg till**.

    ![klient-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Klicka på Välj ett **API**i bladet **Lägg till API Access,** klicka på **Azure Data Lake**och klicka sedan på **Välj**.

    ![klient-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  Klicka på **Välj behörigheter**i bladet **Lägg till API Access,** markera kryssrutan för att ge fullständig åtkomst till **DataSjölagring**och klicka sedan på **Markera**.

    ![klient-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Klicka på **Klar**.

5. Upprepa de två sista stegen för att bevilja behörigheter för Api för **Windows Azure Service Management.**
   
## <a name="next-steps"></a>Nästa steg
I den här artikeln skapade du ett inbyggt Azure AD-program och samlade in den information du behöver i klientprogrammen som du skapar med .NET SDK, Java SDK, REST API osv. Du kan nu gå vidare till följande artiklar som talar om hur du använder Azure AD-webbprogrammet för att först autentisera med Data Lake Storage Gen1 och sedan utföra andra åtgärder i arkivet.

* [Slutanvändarautentisering med DataSjölagring Gen1 med Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Slutanvändarautentisering med DataSjölagring Gen1 med .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Slutanvändarens autentisering med Data Lake Storage Gen1 med Python](data-lake-store-end-user-authenticate-python.md)
* [Slutanvändarens autentisering med Data Lake Storage Gen1 med REST API](data-lake-store-end-user-authenticate-rest-api.md)

