---
title: 'Tjänst-till-tjänst-autentisering: Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft Docs'
description: Lär dig att uppnå tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med Azure Active Directory
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
ms.openlocfilehash: a7fdcf396f586a65efa17e489d002f1c8847a193
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197008"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med Azure Active Directory
> [!div class="op_single_selector"]
> * [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Tjänst-till-tjänst-autentisering](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 använder Azure Active Directory för autentisering. Innan du skapar ett program som fungerar med Data Lake Storage Gen1, måste du bestämma hur du autentiserar ditt program med Azure Active Directory (AD Azure). De två viktigaste alternativen är:

* Slutanvändarautentisering 
* Tjänst-till-tjänst-autentisering (den här artikeln) 

Båda alternativen resultera i ditt program som tillhandahålls med en OAuth 2.0-token som åt varje begärande gjordes till Data Lake Storage Gen1.

Den här artikeln berättar om hur du skapar en **Azure AD-webbappen för tjänst-till-tjänst-autentisering**. Anvisningar för programkonfiguration i Azure AD för autentisering för slutanvändare finns i [slutanvändarautentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Steg 1: Skapa ett Active Directory-webbprogram

Skapa och konfigurera ett Azure AD-webbprogram för tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med Azure Active Directory. Anvisningar finns i [skapa ett Azure AD-program](../active-directory/develop/howto-create-service-principal-portal.md).

När du följa anvisningarna i länken ovan, se till att välja **Webbapp / API** för programtyp, enligt följande skärmbild:

![Skapa webbapp](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "skapa webbapp")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Steg 2: Hämta program-ID och autentiseringsnyckel klient-ID
När du loggar in, behöver du ID: T för ditt program. Om programmet körs under sina egna autentiseringsuppgifter, måste du också en autentiseringsnyckel.

* Anvisningar om hur du hämtar den program-ID och autentiseringsnyckel-nyckeln (kallas även för klienthemligheten) för ditt program finns i [Get ID och autentiseringsnyckel programnyckel](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Anvisningar om hur du hämtar klient-ID finns i [hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Steg 3: Tilldela Azure AD-program till Azure Data Lake Storage Gen1 kontofilen eller mappen


1. Logga in på [Azure Portal](https://portal.azure.com). Öppna det Data Lake Storage Gen1-konto som du vill associera med Azure Active Directory-program som du skapade tidigare.
2. I ditt Data Lake Storage Gen1-kontoblad klickar du på **Datautforskaren**.
   
    ![Skapa kataloger i Data Lake Storage Gen1 konto](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "skapa kataloger i Data Lake-konto")
3. I den **Datautforskaren** bladet klickar du på filen eller mappen som du vill ge åtkomst till Azure AD-programmet och klicka sedan på **åtkomst**. Om du vill konfigurera åtkomst till en fil, måste du klicka på **åtkomst** från den **Filförhandsgranskning** bladet.
   
    ![Ange ACL: er i Data Lake filsystemet](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Ange ACL: er i Data Lake-filsystemet")
4. Den **åtkomst** bladet visas standardåtkomst och anpassad åtkomst som redan har tilldelats till roten. Klicka på den **Lägg till** ikon för att lägga till anpassad nivå ACL: er.
   
    ![Lista över standardentiteter och anpassade åtkomst](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "lista standardentiteter och anpassade åtkomst")
5. Klicka på den **Lägg till** ikonen för att öppna den **Lägg till anpassad åtkomst** bladet. I det här bladet, klickar du på **Välj användare eller grupp**, och sedan i **Välj användare eller grupp** bladet utseende för Azure Active Directory-program som du skapade tidigare. Om du har många grupper att söka från, använder du textrutan längst upp för att filtrera efter gruppnamnet. Klicka på den grupp som du vill lägga till och klicka sedan på **Välj**.
   
    ![Lägga till en grupp](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "lägga till en grupp")
6. Klicka på **Select-behörigheter**, Välj behörigheterna och om du vill tilldela behörigheterna som en standard-ACL kan komma åt ACL, eller båda. Klicka på **OK**.
   
    ![Tilldela behörigheter till gruppen](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "tilldela behörigheter till gruppen")
   
    Läs mer om behörigheter i Data Lake Storage Gen1 och standard/åtkomst-ACL: er, [åtkomstkontroll i Data Lake Storage Gen1](data-lake-store-access-control.md).
7. I den **Lägg till anpassad åtkomst** bladet klickar du på **OK**. Nyligen tillagda gruppen med associerade behörigheter visas i den **åtkomst** bladet.
   
    ![Tilldela behörigheter till gruppen](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "tilldela behörigheter till gruppen")

> [!NOTE]
> Om du planerar att begränsa din Azure Active Directory-program till en viss mapp, kommer du också behöva ge samma Azure Active directory programmet **kör** behörighet till roten för att aktivera skapandet av filåtkomst via .NET SDK.

> [!NOTE]
> Om du vill använda SDK: erna för att skapa ett Data Lake Storage Gen1-konto måste du tilldela Azure AD-webbappen som en roll till resursgruppen där du skapar Gen1 för Data Lake Storage-konto.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Steg 4: Hämta OAuth 2.0-tokenslutpunkten (endast för Java-baserade program)

1. Logga in på den [Azure-portalen](https://portal.azure.com) och klicka på Active Directory i den vänstra rutan.

2. I den vänstra rutan klickar du på **appregistreringar**.

3. Klicka på högst upp på bladet registreringar **slutpunkter**.

    ![Slutpunkt för OAuth-token](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "slutpunkt för OAuth-token")

4. Från listan över slutpunkter, kopierar du tokenslutpunkten OAuth 2.0.

    ![Slutpunkt för OAuth-token](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "slutpunkt för OAuth-token")   

## <a name="next-steps"></a>Nästa steg
I den här artikeln får du skapade en Azure AD-webbappen och samlat in den information du behöver i ditt klientprogram att du skapar med hjälp av .NET SDK, Java, Python, REST API, osv. Du kan nu fortsätta i följande artiklar som pratar om hur du använder det Azure AD-programmet att först autentisera med Data Lake Storage Gen1 och utföra andra åtgärder i arkivet.

* [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med hjälp av Java](data-lake-store-service-to-service-authenticate-java.md)
* [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med hjälp av Python](data-lake-store-service-to-service-authenticate-python.md)
* [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med REST API](data-lake-store-service-to-service-authenticate-rest-api.md)


