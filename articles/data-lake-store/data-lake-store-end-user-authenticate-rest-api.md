---
title: 'Autentisering av slutanvändare: REST API med Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du uppnår slutanvändarautentisering med Azure Data Lake Storage Gen1 med Hjälp av Azure Active Directory med REST API
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
ms.openlocfilehash: 0ef65c23ee1bf4f064695779b71c8616427da204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60877830"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Slutanvändarens autentisering med Azure Data Lake Storage Gen1 med REST API
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

I den här artikeln får du lära dig mer om hur du använder REST API för att göra slutanvändarens autentisering med Azure Data Lake Storage Gen1. För autentisering från service till tjänst med Data Lake Storage Gen1 med REST API finns i [Autentisering från tjänst med DataSjölagring Gen1 med REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett "native"-program i Azure Active Directory**. Du måste ha slutfört stegen i [Slutanvändarautentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](https://curl.haxx.se/)**. Den här artikeln använder cURL för att visa hur du gör REST API-anrop mot ett Data Lake Storage Gen1-konto.

## <a name="end-user-authentication"></a>Slutanvändarautentisering
Autentisering av slutanvändare är den rekommenderade metoden om du vill att en användare ska logga in på ditt program med Azure AD. Ditt program kan komma åt Azure-resurser med samma åtkomstnivå som den inloggade användaren. Användaren måste ange sina autentiseringsuppgifter regelbundet för att ditt program ska kunna behålla åtkomsten.

Resultatet av att ha slutanvändarens inloggning är att ditt program får en åtkomsttoken och en uppdateringstoken. Åtkomsttoken kopplas till varje begäran som görs till Data Lake Storage Gen1 eller Data Lake Analytics, och den är giltig i en timme som standard. Uppdateringstoken kan användas för att hämta en ny åtkomsttoken och den är giltig i upp till två veckor som standard, om den används regelbundet. Du kan använda två olika metoder för slutanvändarinloggning.

I det här scenariot uppmanar programmet användaren att logga in och alla åtgärder utförs i kontexten för användaren. Utför följande steg:

1. Omdirigera användaren via ditt program till följande URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > \<REDIRECT-URI> måste kodas för användning i en URL. Så, https://localhostför `https%3A%2F%2Flocalhost`, användning)

    För självstudierna kan du ersätta platshållarvärdena i URL-adressen ovan och klistra in den i webbläsarens adressfält. Du omdirigeras för att autentisera med Azure-autentiseringsuppgifter. När du har loggat in visas svaret i webbläsarens adressfält. Svaret ska ha följande format:

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Avbilda auktoriseringskoden från svaret. För den här självstudien kan du kopiera auktoriseringskoden från webbläsarens adressfält och skicka den i POST-begäran till tokenslutpunkten, som visas i följande utdrag:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>

   > [!NOTE]
   > I det här fallet behöver \<REDIRECT-URI> inte vara kodad.
   > 
   > 

3. Svaret är ett JSON-objekt som innehåller en `"access_token": "<ACCESS_TOKEN>"`åtkomsttoken (till exempel `"refresh_token": "<REFRESH_TOKEN>"`) och en uppdateringstoken (till exempel ). Ditt program använder åtkomsttoken när du använder Azure Data Lake Storage Gen1 och uppdateringstoken för att få en annan åtkomsttoken när en åtkomsttoken upphör att gälla.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. När åtkomsttoken upphör att gälla kan du begära en ny åtkomsttoken med uppdateringstoken, vilket visas i följande utdrag:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Mer information om interaktiv användarautentisering finns i [Flöde beviljat med auktoriseringskod](https://msdn.microsoft.com/library/azure/dn645542.aspx).

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig hur du använder tjänst-till-tjänst-autentisering för att autentisera med Azure Data Lake Storage Gen1 med REST API. Du kan nu titta på följande artiklar som talar om hur du använder REST API för att arbeta med Azure Data Lake Storage Gen1.

* [Kontohanteringsåtgärder på Data Lake Storage Gen1 med REST API](data-lake-store-get-started-rest-api.md)
* [Dataåtgärder på DataSjölagring Gen1 med REST API](data-lake-store-data-operations-rest-api.md)

