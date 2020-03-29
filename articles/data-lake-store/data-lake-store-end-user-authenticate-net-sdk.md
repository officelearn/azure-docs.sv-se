---
title: 'Autentisering för slutanvändare: .NET SDK med Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du uppnår slutanvändarautentisering med Azure Data Lake Storage Gen1 med Azure Active Directory med .NET SDK
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 215b839c21c2590c08ac2f4250086eaf97914ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66243715"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Slutanvändarens autentisering med Azure Data Lake Storage Gen1 med .NET SDK
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

I den här artikeln får du lära dig mer om hur du använder .NET SDK för att göra slutanvändarens autentisering med Azure Data Lake Storage Gen1. För tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med .NET SDK finns i [Autentisering från tjänst till tjänst med DataSjölagring gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Krav
* **Visual Studio 2013 eller högre**. Instruktionerna nedan använder Visual Studio 2019.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett "native"-program i Azure Active Directory**. Du måste ha slutfört stegen i [Slutanvändarautentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Skapa ett .NET-program
1. Välj Arkiv-menyn, **File** **Ny**och sedan **Project**.
2. Välj **Console App (.NET Framework)** och välj sedan **Nästa**.
3. I **Projektnamn** `CreateADLApplication`anger du och väljer sedan **Skapa**.

4. Lägg till NuGet-paketen i projektet.

   1. Högerklicka på projektnamnet i Solution Explorer och klicka på **Hantera NuGet-paket**.
   2. Kontrollera att **paketkällan** är inställd **på nuget.org** på fliken **NuGet Package Manager** och att kryssrutan Inkludera **förhandsversion** är markerad.
   3. Sök efter och installera följande NuGet-paket:

      * `Microsoft.Azure.Management.DataLake.Store` – I den här självstudiekursen används v2.1.3-förhandsversionen.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` –I den här självstudiekursen används v2.2.12.

        ![Lägga till en NuGet-källa](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Skapa ett nytt Azure Data Lake-konto")
   4. Stäng **NuGet Package Manager**.

5. Öppna **Program.cs**
6. Ersätt användningssatserna med följande rader:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Slutanvändarautentisering
Lägg till det här kodavsnittet i .NET-klientprogrammet. Ersätt platshållarvärdena med de värden som hämtas från ett azure AD-internt program (som anges som en förutsättning). Med det här kodavsnittet kan du autentisera ditt program **interaktivt** med Data Lake Storage Gen1, vilket innebär att du uppmanas att ange dina Azure-autentiseringsuppgifter.

För enkel användning använder följande kodavsnitt standardvärden för klient-ID och omdirigerar URI som är giltiga för alla Azure-prenumerationer. I följande kodavsnitt behöver du bara ange värdet för ditt klient-ID. Du kan hämta klient-ID med hjälp av instruktionerna på [Hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    
- Ersätt funktionen Main() med följande kod:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Ett par saker att veta om föregående utdrag:

* Det föregående kodavsnittet använder en `GetTokenCache` `GetCreds_User_Popup`hjälpfunktioner och . Koden för dessa hjälpfunktioner finns [här på GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* För att hjälpa dig att slutföra självstudien snabbare använder kodavsnittet ett inbyggt programklient-ID som är tillgängligt som standard för alla Azure-prenumerationer. Så kan du **använder detta utdrag i befintligt skick i ditt program**.
* Men om du vill använda en egen Azure AD-domän och ett eget programklient-ID måste du skapa ett eget Azure AD-program och sedan använda Azure AD-domänens ID, klient-ID och omdirigerings-URI för det program som du skapade. Instruktioner finns [i Skapa ett Active Directory-program för slutanvändarens autentisering med DataSjölagringsgenm1.](data-lake-store-end-user-authenticate-using-active-directory.md)

  
## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig hur du använder slutanvändarens autentisering för att autentisera med Azure Data Lake Storage Gen1 med .NET SDK. Du kan nu titta på följande artiklar som beskriver hur du använder .NET SDK för att arbeta med Azure Data Lake Storage Gen1.

* [Kontohanteringsåtgärder på DataSjölagring Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Dataåtgärder på Datasjölagring Gen1 med .NET SDK](data-lake-store-data-operations-net-sdk.md)

