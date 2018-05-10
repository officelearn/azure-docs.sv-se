---
title: Förutsättningar för att få åtkomst till Azure Active Directory reporting API | Microsoft Docs
description: Lär dig mer om förutsättningar för att kunna komma åt Azure AD reporting API
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ab05907f1f23c3856b41a941c1b95992ed5a79a4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Förutsättningar för att få åtkomst till Azure Active Directory reporting API

Den [Azure Active Directory (AD Azure) reporting API: er](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) ger dig programmatisk åtkomst till data via en uppsättning REST-baserad API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg.

Reporting API: N används [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) att bevilja åtkomst till webb-API: er.

För att förbereda din åtkomst till reporting API, måste du:

1. Tilldela roller
2. Registrera ett program
3. Bevilja behörigheter
4. Samla in konfigurationsinställningar



## <a name="assign-roles"></a>Tilldela roller

Om du vill få åtkomst till rapporteringsdata via API: et, måste du ha något av följande roller:

- Säkerhetsläsare

- Säkerhetsadministratör

- Global administratör




## <a name="register-an-application"></a>Registrera ett program

Du måste registrera en app, även om du försöker komma åt reporting API: et med ett skript. Detta ger dig en **program-ID**, vilket krävs för ett anrop för auktorisering och det möjliggör koden för att ta emot token.

Om du vill konfigurera din katalog för att komma åt Azure AD reporting API som du måste logga in på Azure-portalen med Azure-administratörskontot som även är medlem i den **Global administratör** directory roll i Azure AD-klienten.

> [!IMPORTANT]
> Program som körs under autentiseringsuppgifterna med privilegier som ”admin” så här kan vara mycket kraftfulla, så kontrollera att det för att skydda programmets ID-hemlighet autentiseringsuppgifter.
> 


**Registrera ett Azure Active Directory-program:**

1. I den [Azure-portalen](https://portal.azure.com), klicka på det vänstra navigeringsfönstret **Azure Active Directory**.
   
    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. På den **Azure Active Directory** klickar du på **App registreringar**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. På den **App registreringar** i verktygsfältet högst upp på sidan klickar du på **nya appregistrering**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. På den **skapa** utför följande steg:

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. I den **namn** textruta typen `Reporting API application`.

    b. Som **programtyp**väljer **webbapp / API**.

    c. I den **inloggnings-URL** textruta typen `https://localhost`.

    d. Klicka på **Skapa**. 


## <a name="grant-permissions"></a>Bevilja behörigheter 

Beroende på API som du vill komma åt, måste du bevilja följande behörigheter för din app:  

| API | Behörighet |
| --- | --- |
| Windows Azure Active Directory | Läsa katalogdata |
| Microsoft Graph | Läsa alla granska loggdata |


![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


Följande avsnitt innehåller stegen för både API: er. Om du inte vill komma åt någon av de API: er kan du hoppa över relaterade steg.
 

**Ge ditt program behörighet att använda API: erna:**

1. På den **App registreringar** , i listan över appar, klickar du på **Reporting API-program**.

2. På den **Reporting API-program** i verktygsfältet högst upp på sidan klickar du på **inställningar**. 

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. På den **inställningar** klickar du på **nödvändiga behörigheter**. 

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. På den **nödvändiga behörigheter** sidan den **API** klickar du på **Windows Azure Active Directory**. 

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. På den **Aktivera åtkomst** väljer **läsa katalogdata** och avmarkera **logga in och Läs användarprofil**. 

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Klicka på i verktygsfältet högst upp **spara**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. På den **nödvändiga behörigheter** i verktygsfältet högst upp på sidan klickar du på **Lägg till**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. På den **lägga till API-åtkomst** klickar du på **väljer en API**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. På den **väljer en API** klickar du på **Microsoft Graph**, och klicka sedan på **Välj**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. På den **Aktivera åtkomst** väljer **läsa alla granska loggdata**, och klicka sedan på **Välj**.  

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. På den **lägga till API-åtkomst** klickar du på **klar**.  

12. På den **nödvändiga behörigheter** i verktygsfältet högst upp på sidan. Klicka på **bevilja med**, och klicka sedan på **Ja**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Samla in konfigurationsinställningar 

Det här avsnittet visar hur du får följande inställningar från din katalog:

- Domännamn
- Klient-ID
- Klienthemlighet

Du måste dessa värden när du konfigurerar anrop reporting-API: et. 

### <a name="get-your-domain-name"></a>Hämta ditt domännamn

**Hämta ditt domännamn:**

1. I den [Azure-portalen](https://portal.azure.com), klicka på det vänstra navigeringsfönstret **Azure Active Directory**.
   
    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. På den **Azure Active Directory** klickar du på **anpassade domännamn**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Kopiera ditt domännamn från listan över domäner.


### <a name="get-your-applications-client-id"></a>Hämta programmets klient-ID

**Hämta programmets klient-ID:**

1. I den [Azure-portalen](https://portal.azure.com), klicka på det vänstra navigeringsfönstret **Azure Active Directory**.
   
    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. På den **App registreringar** , i listan över appar, klickar du på **Reporting API-program**.

3. På den **Reporting API-program** sidan på den **program-ID**, klickar du på **Klicka för att kopiera**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Hämta programmets klienthemlighet
För att få ditt program klienthemlighet, måste du skapa en ny nyckel och spara dess värde vid sparas den nya nyckeln eftersom det inte går att hämta det här värdet senare längre.

**Hämta programmets klienthemlighet:**

1. I den [Azure-portalen](https://portal.azure.com), klicka på det vänstra navigeringsfönstret **Azure Active Directory**.
   
    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. På den **App registreringar** , i listan över appar, klickar du på **Reporting API-program**.


3. På den **Reporting API-program** i verktygsfältet högst upp på sidan klickar du på **inställningar**. 

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. På den **inställningar** sidan den **APIR åtkomst** klickar du på **nycklar**. 

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. På den **nycklar** utför följande steg:

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. I den **beskrivning** textruta typen `Reporting API`.

    b. Som **Expires**väljer **i två år**.

    c. Klicka på **Spara**.

    d. Kopiera värdet för nyckeln.


## <a name="next-steps"></a>Nästa steg

- [Hämta data med Azure Active Directory reporting API med certifikat](active-directory-reporting-api-with-certificates.md).

- [Hämta en första intryck reporting API](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Skapa din egen lösning](active-directory-reporting-api-getting-started-azure-portal.md#customize)

