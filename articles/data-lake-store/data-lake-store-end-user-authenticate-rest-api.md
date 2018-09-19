---
title: 'Slutanvändarautentisering: REST API med Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft Docs'
description: Lär dig att uppnå slutanvändarautentisering med Azure Data Lake Storage Gen1 med Azure Active Directory med hjälp av REST API
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: ea550c0959f5de13f013f135926251bf9f8b450f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124447"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Slutanvändarautentisering med Azure Data Lake Storage Gen1 med hjälp av REST API
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

I den här artikeln lär du dig hur du använder REST API för att göra slutanvändarautentisering med Azure Data Lake Storage Gen1. Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med REST API, se [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory ”interna” program**. Du måste ha slutfört stegen i [slutanvändarautentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](http://curl.haxx.se/)**. Den här artikeln använder cURL för att demonstrera hur du gör REST API-anrop mot ett Data Lake Storage Gen1-konto.

## <a name="end-user-authentication"></a>Slutanvändarautentisering
Slutanvändarautentisering är den rekommenderade metoden om du vill att en användare att logga in på ditt program med Azure AD. Programmet kan komma åt Azure-resurser med samma nivå av åtkomst som den inloggade användaren. Användaren måste ange sina autentiseringsuppgifter med jämna mellanrum i ordning för ditt program att upprätthålla åtkomsten.

Resultatet av att ha slutanvändarens inloggningen är att ditt program är baserat på en åtkomsttoken och en uppdateringstoken. Åtkomsttoken hämtar kopplade till varje begärande gjordes till Data Lake Storage Gen1 eller Data Lake Analytics och är giltig i en timme som standard. Uppdateringstoken kan användas till att hämta en ny åtkomsttoken och det är giltigt i upp till två veckor som standard om användas ofta. Du kan använda två olika metoder för slutanvändarinloggning.

I det här scenariot uppmanar programmet användaren att logga in och alla åtgärder utförs i kontexten för användaren. Utför följande steg:

1. Omdirigera användaren via ditt program till följande URL:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<REDIRECT-URI> måste kodas för användning i en URL. Så för https://localhost, använda `https%3A%2F%2Flocalhost`)
   > 
   > 
   
    För självstudierna kan du ersätta platshållarvärdena i URL-adressen ovan och klistra in den i webbläsarens adressfält. Du omdirigeras för att autentisera med Azure-autentiseringsuppgifter. När du har loggat in visas svaret i webbläsarens adressfält. Svaret ska ha följande format:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Avbilda auktoriseringskoden från svaret. För den här självstudien får kopiera du Auktoriseringskoden från adressfältet i webbläsaren och pass den i INLÄGGET begäran till tokenslutpunkten, enligt följande kodavsnitt:
   
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

3. Svaret är ett JSON-objekt som innehåller en åtkomsttoken (till exempel `"access_token": "<ACCESS_TOKEN>"`) och en uppdateringstoken (till exempel `"refresh_token": "<REFRESH_TOKEN>"`). Programmet använder åtkomsttoken vid åtkomst till Azure Data Lake Storage Gen1 och uppdateringstoken för att få en annan åtkomsttoken när en åtkomst-token upphör att gälla.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. När åtkomsttoken upphör att gälla, kan du begära en ny åtkomsttoken med hjälp av uppdateringstoken som visas i följande kodavsnitt:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Mer information om interaktiv användarautentisering finns i [Flöde beviljat med auktoriseringskod](https://msdn.microsoft.com/library/azure/dn645542.aspx).
   
## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder tjänst-till-tjänst-autentisering för att autentisera med Azure Data Lake Storage Gen1 med hjälp av REST API. Du kan nu se ut i följande artiklar som pratar om hur du använder REST API för att arbeta med Azure Data Lake Storage Gen1.

* [Kontohanteringsåtgärder i Data Lake Storage Gen1 med hjälp av REST API](data-lake-store-get-started-rest-api.md)
* [Dataåtgärder på Data Lake Storage Gen1 med hjälp av REST API](data-lake-store-data-operations-rest-api.md)

