---
title: "Förutsättningar för att få åtkomst till Azure AD reporting API | Microsoft Docs"
description: "Lär dig mer om förutsättningar för att kunna komma åt Azure AD reporting API"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9fbcac4bd6240aed607c3da6c6535d207a782448
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Förutsättningar för att få åtkomst till Azure AD reporting API

Den [Azure AD reporting API: er](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) ger dig programmatisk åtkomst till data via en uppsättning REST-baserad API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg.

Reporting API: N används [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) att bevilja åtkomst till webb-API: er. 

Om du vill få åtkomst till rapporteringsdata via API: et, måste du ha något av följande roller:

- Säkerhet läsare
- Säkerhet Admin
- Global administratör


För att förbereda din åtkomst till reporting API, måste du:

1. Registrera ett program 
2. Bevilja behörigheter 
3. Samla in konfigurationsinställningar 

Frågor, frågor eller kommentarer finns [filen ett supportärende](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="register-an-azure-active-directory-application"></a>Registrera ett Azure Active Directory-program

Du måste registrera en app, även om du ansluter till reporting API: et med ett skript. Detta ger dig en **program-ID**, vilket krävs för ett anrop för auktorisering och det möjliggör koden för att ta emot token.

Om du vill konfigurera din katalog för att komma åt Azure AD reporting API som du måste logga in på Azure-portalen med Azure-administratörskontot som även är medlem i den **Global administratör** directory roll i Azure AD-klienten.

> [!IMPORTANT]
> Program som körs under autentiseringsuppgifterna med privilegier som ”admin” så här kan vara mycket kraftfulla, så kontrollera att det för att skydda programmets ID-hemlighet autentiseringsuppgifter.
> 


**Registrera ett Azure Active Directory-program:**

1. I den [Azure-portalen](https://portal.azure.com), klicka på det vänstra navigeringsfönstret **Azure Active Directory**.
   
    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. På den **Azure Active Directory** bladet, klickar du på **App registreringar**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. På den **App registreringar** bladet i verktygsfältet högst upp, klickar du på **nya appregistrering**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. På den **skapa** bladet utför följande steg:

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. I den **namn** textruta typen `Reporting API application`.

    b. Som **programtyp**väljer **webbapp / API**.

    c. I den **inloggnings-URL** textruta typen `https://localhost`.

    d. Klicka på **Skapa**. 


## <a name="grant-permissions"></a>Bevilja behörigheter 

Syftet med det här steget är att ge ditt program **läsa katalogdata** behörigheter till den **Windows Azure Active Directory** API.

![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/16.png)
 

**Ge ditt program behörighet att använda API:**

1. På den **App registreringar** klickar du på bladet i listan över appar **Reporting API-program**.

2. På den **Reporting API-program** bladet i verktygsfältet högst upp, klickar du på **inställningar**. 

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. På den **inställningar** bladet, klickar du på **nödvändiga behörigheter**. 

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. På den **nödvändiga behörigheter** blad i den **API** klickar du på **Windows Azure Active Directory**. 

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. På den **Aktivera åtkomst** bladet väljer **läsa katalogdata**. 

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Klicka på i verktygsfältet högst upp **spara**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. Klicka på **bevilja med**, och klicka sedan på **Ja**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Samla in konfigurationsinställningar 
Det här avsnittet visar hur du får följande inställningar från din katalog:

* Domännamn
* Klient-ID
* Klienthemlighet

Du måste dessa värden när du konfigurerar anrop reporting-API: et. 

### <a name="get-your-domain-name"></a>Hämta ditt domännamn

**Hämta ditt domännamn:**

1. I den [Azure-portalen](https://portal.azure.com), klicka på det vänstra navigeringsfönstret **Azure Active Directory**.
   
    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. På den **Azure Active Directory** bladet, klickar du på **anpassade domännamn**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Kopiera ditt domännamn från listan över domäner.


### <a name="get-your-applications-client-id"></a>Hämta programmets klient-ID

**Hämta programmets klient-ID:**

1. I den [Azure-portalen](https://portal.azure.com), klicka på det vänstra navigeringsfönstret **Azure Active Directory**.
   
    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. På den **App registreringar** klickar du på bladet i listan över appar **Reporting API-program**.

3. På den **Reporting API-program** bladet på den **program-ID**, klickar du på **Klicka för att kopiera**.

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Hämta programmets klienthemlighet
För att få ditt program klienthemlighet, måste du skapa en ny nyckel och spara dess värde vid sparas den nya nyckeln eftersom det inte går att hämta det här värdet senare längre.

**Hämta programmets klienthemlighet:**

1. I den [Azure-portalen](https://portal.azure.com), klicka på det vänstra navigeringsfönstret **Azure Active Directory**.
   
    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. På den **App registreringar** klickar du på bladet i listan över appar **Reporting API-program**.


3. På den **Reporting API-program** bladet i verktygsfältet högst upp, klickar du på **inställningar**. 

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. På den **inställningar** blad i den **APIR åtkomst** klickar du på **nycklar**. 

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. På den **nycklar** bladet utför följande steg:

    ![Registrera program](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. I den **beskrivning** textruta typen `Reporting API`.

    b. Som **Expires**väljer **i två år**.

    c. Klicka på **Spara**.

    d. Kopiera värdet för nyckeln.


## <a name="next-steps"></a>Nästa steg
* Vill du komma åt data från Azure AD reporting API på ett programmässiga sätt? Checka ut [komma igång med Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md).
* Om du vill veta mer om Azure Active Directory reporting finns i [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).  

