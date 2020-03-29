---
title: 'Autentisering från tjänst: Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du uppnår autentisering mellan tjänst och tjänst med Azure Data Lake Storage Gen1 med Azure Active Directory
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
ms.openlocfilehash: 3fbf2f2540e8f1ca84aad2759b9a1fc790e4065d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241363"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med Azure Active Directory
> [!div class="op_single_selector"]
> * [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Tjänst-till-tjänst-autentisering](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 använder Azure Active Directory för autentisering. Innan du redigerar ett program som fungerar med Data Lake Storage Gen1 måste du bestämma hur du autentiserar ditt program med Azure Active Directory (Azure AD). De två viktigaste alternativen är:

* Slutanvändarautentisering 
* Autentisering från tjänst (den här artikeln) 

Båda dessa alternativ resulterar i att ditt program får en OAuth 2.0-token, som kopplas till varje begäran som görs till Data Lake Storage Gen1.

Den här artikeln beskriver hur du skapar ett **Azure AD-webbprogram för autentisering mellan tjänst**och tjänst . Instruktioner om Azure AD-programkonfiguration för slutanvändarens autentisering finns i [Slutanvändarens autentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Krav
* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Steg 1: Skapa ett Active Directory-webbprogram

Skapa och konfigurera ett Azure AD-webbprogram för tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med Azure Active Directory. Instruktioner finns i [Skapa ett Azure AD-program](../active-directory/develop/howto-create-service-principal-portal.md).

När du följer instruktionerna på föregående länk, se till att du väljer **Web App / API** för programtyp, som visas i följande skärmdump:

![Skapa webbapp](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Skapa webbapp")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Steg 2: Hämta program-ID, autentiseringsnyckel och klient-ID
När du loggar in programmässigt behöver du ID:n för ditt program. Om programmet körs under egna autentiseringsuppgifter behöver du också en autentiseringsnyckel.

* Instruktioner om hur du hämtar program-ID och autentiseringsnyckel (kallas även klienthemligheten) för ditt program finns i [Hämta program-ID och autentiseringsnyckel](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

* Instruktioner om hur du hämtar klient-ID finns i [Hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Steg 3: Tilldela Azure AD-programmet till azure data lake storage gen1-kontofilen eller mappen


1. Logga in på [Azure Portal](https://portal.azure.com). Öppna det DataSjölagringsgenm1-konto som du vill associera med Azure Active Directory-programmet som du skapade tidigare.
2. Klicka på **Data Explorer**i kontobladet Datasjölagringsgenm1.
   
    ![Skapa kataloger i Data Lake Storage Gen1-konto](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Skapa kataloger i Data Lake-konto")
3. I bladet **Data Explorer** klickar du på den fil eller mapp som du vill ge åtkomst till Azure AD-programmet för och klickar sedan på **Access**. Om du vill konfigurera åtkomst till en fil måste du klicka på **Access** från bladet **Förhandsgranskning av fil.**
   
    ![Ange ACL:er i datasjöfilsystemet](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Ange ACL:er i datasjöfilsystemet")
4. **Access-bladet** visar den standardåtkomst och anpassade åtkomst som redan har tilldelats roten. Klicka på ikonen **Lägg** till om du vill lägga till ACL:er på anpassad nivå.
   
    ![Lista standard och anpassad åtkomst](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Lista standard och anpassad åtkomst")
5. Klicka på ikonen **Lägg** till för att öppna bladet **Lägg till anpassad åtkomst.** I det här bladet klickar du på **Välj användare eller grupp**och letar sedan efter Azure Active Directory-programmet som du skapade tidigare i Välj användare eller **gruppblad.** Om du har många grupper att söka från använder du textrutan högst upp för att filtrera på gruppnamnet. Klicka på den grupp som du vill lägga till och klicka sedan på **Markera**.
   
    ![Lägga till en grupp](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Lägga till en grupp")
6. Klicka på **Välj behörigheter,** välj behörigheter och om du vill tilldela behörigheterna som standardförseelseförordnad, åtkomst till åtkomst till åtkomstkontrollistan eller båda. Klicka på **OK**.
   
    ![Tilldela behörigheter till grupp](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Tilldela behörigheter till grupp")
   
    Mer information om behörigheter i Gen1 för lagring av datasjö och ACL:er för standard/åtkomst finns [i Åtkomstkontroll i DataSjölagringgengen1](data-lake-store-access-control.md).
7. Klicka på **OK**i bladet **Lägg till anpassad åtkomst** . Den nyligen tillagda gruppen, med tillhörande behörigheter, visas i **Access-bladet.**
   
    ![Tilldela behörigheter till grupp](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Tilldela behörigheter till grupp")

> [!NOTE]
> Om du planerar att begränsa ditt Azure Active Directory-program till en viss mapp måste du också ge samma Azure Active Directory-program **Kör** behörighet till roten för att aktivera filgenereringsåtkomst via .NET SDK.

> [!NOTE]
> Om du vill använda SDK:erna för att skapa ett DataSjölagringsgenm1-konto måste du tilldela Azure AD-webbprogrammet som en roll till resursgruppen där du skapar datasjölagringsgenm1-kontot.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Steg 4: Hämta OAuth 2.0-tokenslutpunkten (endast för Java-baserade program)

1. Logga in på [Azure-portalen](https://portal.azure.com) och klicka på Active Directory från den vänstra rutan.

2. Klicka på **Appregistreringar**i den vänstra rutan .

3. Klicka på **Slutpunkter**högst upp i bladet För appregistreringar .

    ![Slutpunkt för OAuth-token](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Slutpunkt för OAuth-token")

4. Kopiera slutpunkten OAuth 2.0 token från listan över slutpunkter.

    ![Slutpunkt för OAuth-token](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Slutpunkt för OAuth-token")   

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat ett Azure AD-webbprogram och samlat in den information du behöver i klientprogrammen som du skapar med .NET SDK, Java, Python, REST API osv. Du kan nu gå vidare till följande artiklar som talar om hur du använder azure AD-programmet för att först autentisera med Data Lake Storage Gen1 och sedan utföra andra åtgärder i arkivet.

* [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med Java](data-lake-store-service-to-service-authenticate-java.md)
* [Tjänst-till-tjänst-autentisering med DataSjölagring Gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med Python](data-lake-store-service-to-service-authenticate-python.md)
* [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med REST API](data-lake-store-service-to-service-authenticate-rest-api.md)


