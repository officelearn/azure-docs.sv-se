---
title: 'Slutanvändarautentisering: .NET SDK med Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft Docs'
description: Lär dig att uppnå slutanvändarautentisering med Azure Data Lake Storage Gen1 med Azure Active Directory med .NET SDK
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
ms.openlocfilehash: 78a290d8136f8804e853d36a9bc95571625ed89c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880299"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Slutanvändarautentisering med Azure Data Lake Storage Gen1 med .NET SDK
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

I den här artikeln lär du dig hur du använder .NET SDK för att göra slutanvändarautentisering med Azure Data Lake Storage Gen1. Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med hjälp av .NET SDK, se [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
* **Visual Studio 2013, 2015 eller 2017**. Anvisningarna nedan använder Visual Studio 2017.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory ”interna” program**. Du måste ha slutfört stegen i [slutanvändarautentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Skapa ett .NET-program
1. Öppna Visual Studio och skapa ett konsolprogram.
2. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**.
3. Från **Nytt projekt** anger eller väljer du följande värden:

   | Egenskap  | Värde |
   | --- | --- |
   | Category |Mallar/Visual C#/Windows |
   | Mall |Konsolprogram |
   | Namn |CreateADLApplication |

4. Klicka på **OK** för att skapa projektet.

5. Lägg till NuGet-paketen i projektet.

   1. Högerklicka på projektnamnet i Solution Explorer och klicka på **Hantera NuGet-paket**.
   2. På fliken **NuGet Package Manager** ser du till att **Paketkälla** har angetts som **nuget.org** och att kryssrutan **Inkludera förhandsversion** är markerad.
   3. Sök efter och installera följande NuGet-paket:

      * `Microsoft.Azure.Management.DataLake.Store` – I den här självstudiekursen används v2.1.3-förhandsversionen.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` –I den här självstudiekursen används v2.2.12.

        ![Lägg till en NuGet-källa](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Skapa ett nytt Azure Data Lake-konto")
   4. Stäng **NuGet Package Manager**.

6. Öppna **Program.cs**
7. Ersätt den med hjälp av-instruktioner med följande rader:

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
Lägg till det här kodfragmentet i dina .NET-klientprogram. Ersätt platshållarvärdena med värden som hämtats från en Azure AD-program (som listas som krav). Det här kodfragmentet kan du autentisera ditt program **interaktivt** med Data Lake Storage Gen1, vilket innebär att du uppmanas att ange dina autentiseringsuppgifter för Azure.

I följande kodfragment används standardvärden för klient-ID och omdirigerings-URI som är giltiga för alla Azure-prenumerationer för enkelhets skull. I följande kodfragment behöver du bara ange värdet för din klient-ID. Du kan hämta klient-ID med hjälp av anvisningarna på [hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
    
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

Några saker du behöver veta om föregående fragment:

* I föregående kodfragment används en hjälpfunktioner `GetTokenCache` och `GetCreds_User_Popup`. Koden för dessa hjälpfunktioner finns [här på GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Som hjälper dig att slutföra kursen snabbare, används i kodfragment ett internt program klient-ID som är tillgängligt som standard för alla Azure-prenumerationer. Så kan du **använder detta utdrag i befintligt skick i ditt program**.
* Men om du vill använda en egen Azure AD-domän och ett eget programklient-ID måste du skapa ett eget Azure AD-program och sedan använda Azure AD-domänens ID, klient-ID och omdirigerings-URI för det program som du skapade. Se [skapa ett Active Directory-program för slutanvändarautentisering med Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) anvisningar.

  
## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder autentisering för slutanvändare för att autentisera med Azure Data Lake Storage Gen1 med .NET SDK. Du kan nu se ut i följande artiklar som pratar om hur du använder .NET SDK för att arbeta med Azure Data Lake Storage Gen1.

* [Kontohanteringsåtgärder i Data Lake Storage Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Åtgärder på Data Lake Storage Gen1 med .NET SDK](data-lake-store-data-operations-net-sdk.md)

