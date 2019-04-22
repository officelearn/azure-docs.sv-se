---
title: '.NET SDK: Kontohanteringsåtgärder på Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Använda Azure Data Lake Storage Gen1 .NET SDK för att utföra kontohanteringsåtgärder i Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8ab051d49e7ed67e642ef656dfb382ed07763ed2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879717"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Kontohanteringsåtgärder på Azure Data Lake Storage Gen1 med .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

I den här artikeln får du lära dig hur du utför kontohanteringsåtgärder på Azure Data Lake Storage Gen1 med .NET SDK. Kontohanteringsåtgärder omfattar att skapa ett Data Lake Storage Gen1-konto, ange kontona i en Azure-prenumeration, ta bort konton osv.

Anvisningar för hur du utför datahanteringsåtgärder på Data Lake Storage Gen1 med .NET SDK finns i [filsystemsåtgärder på Data Lake Storage Gen1 med .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
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
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

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
                    _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

I de återstående avsnitten i artikeln kan du se hur du använder tillgängliga .NET-metoder för att utföra åtgärder som autentisering, ladda upp filer, osv.

## <a name="authentication"></a>Authentication

* Information om slutanvändarautentisering för programmet, se [slutanvändarautentisering med Data Lake Storage Gen1 med .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Tjänst-till-tjänst-autentisering för programmet, se [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Skapa klientobjekt
Följande kodavsnitt skapar klientobjektet konto för Data Lake Storage Gen1 som används för att utfärda kontohanteringsbegäran till tjänsten, som att skapa konto, ta bort konto, osv.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto
Följande kodfragment skapar ett Data Lake Storage Gen1-konto i Azure-prenumeration du angav när du skapade klientobjektet för Data Lake Storage Gen1 konto.

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Lista över alla Data Lake Storage Gen1 konton inom en prenumeration
Lägg till följande metod i klassdefinitionen. Följande kodavsnitt visar en lista över alla Data Lake Storage Gen1 konton inom en Azure-prenumeration.

    // List all Data Lake Storage Gen1 accounts within the subscription
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

## <a name="delete-a-data-lake-storage-gen1-account"></a>Ta bort ett Data Lake Storage Gen1-konto
Följande kodavsnitt tar bort Data Lake Storage Gen1-kontot som du skapade tidigare.

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Se också
* [Filsystemsåtgärder på Data Lake Storage Gen1 med .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK-referens](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Nästa steg
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
