---
title: "Tjänst-till-tjänst-autentisering: .NET SDK med Data Lake Store med Azure Active Directory | Microsoft Docs"
description: "Lär dig att uppnå service to service autentisering med Data Lake Store med hjälp av Azure Active Directory med .NET SDK"
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
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: c336cda6f3af4e2a4647371458b2db3e97917105
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-net-sdk"></a>Tjänst-till-tjänst-autentisering med Data Lake Store med hjälp av .NET SDK
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Använda REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

I den här artikeln får du lära dig hur du använder .NET SDK för att göra service to service autentisering med Azure Data Lake Store. Slutanvändarens autentisering med Data Lake Store med hjälp av .NET SDK, se [slutanvändarens autentisering med Data Lake Store med hjälp av .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).


## <a name="prerequisites"></a>Krav
* **Visual Studio 2013, 2015 eller 2017**. Anvisningarna nedan använder Visual Studio 2017.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett program för Azure Active Directory ”Web”**. Du måste ha slutfört stegen i [tjänst-till-tjänst-autentisering med Data Lake Store med Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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

## <a name="service-to-service-authentication-with-client-secret"></a>Tjänst-till-tjänst-autentisering med klienthemlighet
Lägg till följande kodutdrag i .NET-klientprogrammet. Vill du ersätta platshållarvärdena med värdena som hämtas från en Azure AD-webbprogram (visas som en förutsättning).  Den här fragment låter dig autentisera ditt program **icke-interaktivt** med Data Lake Store med hjälp av klienten hemlig/nyckel för Azure AD-webbprogram. 

    private static void Main(string[] args)
    {    
        // Service principal / appplication authentication with client secret / key
        // Use the client ID of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
        var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
        var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
    }

Sammandraget som föregår använder en hjälpfunktion `GetCreds_SPI_SecretKey`. Koden för den här helper-funktionen är tillgänglig [här på Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Tjänst-till-tjänst-autentisering med certifikat

Lägg till följande kodutdrag i .NET-klientprogrammet. Vill du ersätta platshållarvärdena med värdena som hämtas från en Azure AD-webbprogram (visas som en förutsättning). Den här fragment låter dig autentisera ditt program **icke-interaktivt** med Data Lake Store med hjälp av certifikatet för en Azure AD-webbprogram. Instruktioner om hur du skapar ett Azure AD-program finns i [skapa tjänstens huvudnamn med certifikat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    
    private static void Main(string[] args)
    {
        // Service principal / application authentication with certificate
        // Use the client ID and certificate of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
        var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
        var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
    }

Sammandraget som föregår använder en hjälpfunktion `GetCreds_SPI_Cert`. Koden för den här helper-funktionen är tillgänglig [här på Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder autentisering för tjänst-till-tjänst för att autentisera med Azure Data Lake Store med hjälp av .NET SDK. Du kan nu se följande artiklar som talar om hur du använder .NET SDK för att arbeta med Azure Data Lake Store.

* [Kontohanteringsåtgärder på Azure Data Lake Store med hjälp av .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Dataåtgärder på Data Lake Store med hjälp av .NET SDK](data-lake-store-data-operations-net-sdk.md)


