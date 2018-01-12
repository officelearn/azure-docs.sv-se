---
title: "Slutanvändarens autentisering: .NET SDK med Data Lake Store med Azure Active Directory | Microsoft Docs"
description: "Lär dig att uppnå slutanvändarens autentisering med Data Lake Store med Azure Active Directory med .NET SDK"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: ad9a66e02318ad4a84d65062114c916721047888
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-net-sdk"></a>Slutanvändarens autentisering med Data Lake Store med hjälp av .NET SDK
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

I den här artikeln får du lära dig hur du använder .NET SDK för att göra slutanvändarens autentisering med Azure Data Lake Store. Tjänst-till-tjänst-autentisering med Data Lake Store med hjälp av .NET SDK, se [tjänst-till-tjänst-autentisering med Data Lake Store med hjälp av .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Förutsättningar
* **Visual Studio 2013, 2015 eller 2017**. Anvisningarna nedan använder Visual Studio 2017.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory ”interna” program**. Du måste ha slutfört stegen i [slutanvändarens autentisering med Data Lake Store med Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Skapa ett .NET-program
1. Öppna Visual Studio och skapa ett konsolprogram.
2. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**.
3. Från **Nytt projekt** anger eller väljer du följande värden:

   | Egenskap | Värde |
   | --- | --- |
   | Kategori |Mallar/Visual C#/Windows |
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

6. Öppna **Program.cs**, ta bort den befintliga koden och lägg sedan till följande instruktioner för att lägga till referenser till namnområden.

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
        

## <a name="end-user-authentication"></a>Slutanvändarautentisering
Lägg till följande kodutdrag i .NET-klientprogrammet. Ersätta platshållarvärdena med värden som hämtats från en intern Azure AD-program (visas en förutsättning). Den här fragment låter dig autentisera ditt program **interaktivt** med Data Lake Store, vilket innebär att du uppmanas att ange dina autentiseringsuppgifter för Azure.

Lätt att använda använder följande kodavsnitt standardvärden för klient-ID och omdirigerings-URI som är giltiga för alla Azure-prenumerationer. I följande fragment behöver du bara ange värdet för klient-ID. Du kan hämta klient-ID med hjälp av anvisningarna i [hämta klient-ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    
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

Några saker du behöver veta om föregående fragment:

* Sammandraget som föregår använder en hjälpfunktioner `GetTokenCache` och `GetCreds_User_Popup`. Koden för dessa hjälpfunktioner finns [här på Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* För att slutföra kursen snabbare ska använder kodavsnittet programspecifika klient-ID, som är tillgängligt som standard för alla Azure-prenumerationer. Så kan du **använder detta utdrag i befintligt skick i ditt program**.
* Men om du vill använda en egen Azure AD-domän och ett eget programklient-ID måste du skapa ett eget Azure AD-program och sedan använda Azure AD-domänens ID, klient-ID och omdirigerings-URI för det program som du skapade. Se [Create an Active Directory Application for end-user authentication with Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md) (Skapa ett Active Directory-program för slutanvändarautentisering med Data Lake Store) för anvisningar.

  
## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder slutanvändarens autentisering för att autentisera med Azure Data Lake Store med hjälp av .NET SDK. Du kan nu se följande artiklar som talar om hur du använder .NET SDK för att arbeta med Azure Data Lake Store.

* [Kontohanteringsåtgärder på Azure Data Lake Store med hjälp av .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Dataåtgärder på Data Lake Store med hjälp av .NET SDK](data-lake-store-data-operations-net-sdk.md)

