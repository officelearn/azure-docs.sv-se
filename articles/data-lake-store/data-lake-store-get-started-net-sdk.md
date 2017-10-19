---
title: ".NET SDK: Kontohanteringsåtgärder i Azure Data Lake Store | Microsoft Docs"
description: "Använd Azure Data Lake Store .NET SDK för att utföra kontohanteringsåtgärder i Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 861f6b54130f9954c5e565346afd9a8f8e034b3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="account-management-operations-on-azure-data-lake-store-using-net-sdk"></a>Kontohanteringsåtgärder i Azure Data Lake Store med .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

I den här artikeln får du lära dig att utföra kontohanteringsåtgärder i Data Lake Store med .NET SDK. Kontohanteringsåtgärder omfattar att skapa ett Data Lake Store-konto, ange kontona i en Azure-prenumeration, ta bort konton osv.

Anvisningar för att utföra datahanteringsåtgärder i Data Lake Store med .NET SDK finns i [Filsystemsåtgärder i Data Lake Store med .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Krav
* **Visual Studio 2013, 2015 eller 2017**. Anvisningarna nedan använder Visual Studio 2017.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

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
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

7. Deklarera variablerna och ange värden för platshållare. Kontrollera också att den lokala sökvägen och filnamnet som du anger finns på datorn.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

I de återstående avsnitten i artikeln kan du se hur du använder tillgängliga .NET-metoder för att utföra åtgärder som autentisering, ladda upp filer, osv.

## <a name="authentication"></a>Autentisering

* Information om slutanvändarautentisering för programmet finns i [Slutanvändarautentisering med Data Lake Store med hjälp av .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Information om tjänst-till-tjänst-autentisering för programmet finns i [Tjänst-till-tjänst-autentisering med Data Lake Store med hjälp av .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-objects"></a>Skapa klientobjekt
Följande kodavsnitt skapar klientobjektet för Data Lake Store-kontot, som används för att utfärda kontohanteringsbegäran till tjänsten, t.ex. att skapa konto, ta bort konto osv.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-store-account"></a>Skapa ett Data Lake Store-konto
Följande kodavsnitt skapar ett Data Lake Store-konto i den Azure-prenumeration du angav när du skapade klientobjektet för Data Lake Store-kontot.

    // Create Data Lake Store account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Lista över alla Data Lake Store-konton inom en prenumeration
Lägg till följande metod i klassdefinitionen. Följande fragment visar en lista över alla Data Lake Store-konton inom en viss Azure-prenumeration.

    // List all Data Lake Store accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-store-account"></a>Ta bort ett Data Lake Store-konto
Följande kodavsnitt tar bort Data Lake Store-kontot du skapade tidigare.

    // Delete Data Lake Store account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Se även
* [Filsystemsåtgärder på Data Lake Store med hjälp av .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Data Lake Store .NET SDK-referens](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Nästa steg
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
