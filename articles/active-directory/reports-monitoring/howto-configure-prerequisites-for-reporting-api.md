---
title: 'Förutsättningar för att få åtkomst till Azure Active Directory reporting API: N | Microsoft Docs'
description: Lär dig mer om kraven för att få åtkomst till Azure AD reporting-API
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8d610dc74b7e2ef10295bc0a3407cf7c3d781b51
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42060860"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Förutsättningar för att få åtkomst till Azure Active Directory reporting API

[Azure Active Directory reporting API: er](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg.

Reporting API: et använder [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) att godkänna åtkomst till webb-API: er.

För att förbereda din åtkomst till rapporterings-API, måste du:

1. Tilldela roller
2. Registrera ett program
3. Bevilja behörigheter
4. Samla in konfigurationsinställningar



## <a name="assign-roles"></a>Tilldela roller

För att få åtkomst till rapporteringsdata via API, måste du ha ett av följande roller:

- Säkerhetsläsare

- Säkerhetsadministratör

- Global administratör




## <a name="register-an-application"></a>Registrera ett program

Du måste registrera en app, även om du försöker komma åt rapporterings-API med hjälp av ett skript. Detta ger dig en **program-ID**, vilket krävs för ett anrop om auktorisering och det gör att din kod för att ta emot tokens.

Om du vill konfigurera din katalog för att komma åt Azure AD reporting API: et, måste du logga in på Azure Portal med en Azure-administratörskonto som också är medlem i den **Global administratör** katalogroll i Azure AD-klienten.

> [!IMPORTANT]
> Program som körs under autentiseringsuppgifterna med ”admin”-behörighet så här kan vara mycket kraftfulla, så var noga med att skydda programmets ID/hemligheten-autentiseringsuppgifter.
> 


**Registrera ett Azure Active Directory-program:**

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret klickar du på **Azure Active Directory**.
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. På den **Azure Active Directory** klickar du på **appregistreringar**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. På den **appregistreringar** , i verktygsfältet högst upp, klickar du på **ny programregistrering**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. På den **skapa** utför följande steg:

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. I den **namn** textrutan typ `Reporting API application`.

    b. Som **programtyp**väljer **webbapp / API**.

    c. I den **inloggnings-URL** textrutan typ `https://localhost`.

    d. Klicka på **Skapa**. 


## <a name="grant-permissions"></a>Bevilja behörigheter 

Beroende på API som du vill komma åt, måste du bevilja följande behörigheter för din app:  

| API | Behörighet |
| --- | --- |
| Windows Azure Active Directory | Läsa katalogdata |
| Microsoft Graph | Läsa alla gransknings-och loggdata |


![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/36.png)


I följande avsnitt visas stegen för både API: er. Om du inte vill ha åtkomst till någon av de API: erna kan du hoppa över de relaterade steg.
 

**Så här ger ditt program behörighet att använda API: erna:**

1. På den **appregistreringar** , i listan över appar, klickar du på **Reporting-API-program**.

2. På den **Reporting-API-program** , i verktygsfältet högst upp, klickar du på **inställningar**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/05.png)

3. På den **inställningar** klickar du på **behörigheter som krävs för**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/06.png)

4. På den **nödvändiga behörigheter** sidan den **API** klickar du på **Windows Azure Active Directory**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/07.png)

5. På den **Aktivera åtkomst** väljer **läsa katalogdata** och avmarkera **logga in och läsa användarprofil**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/08.png)

6. I verktygsfältet högst upp, klickar du på **spara**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/15.png)

7. På den **nödvändiga behörigheter** , i verktygsfältet högst upp, klickar du på **Lägg till**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/32.png)

8. På den **Lägg till API-åtkomst** klickar du på **Välj en API**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/31.png)

9. På den **Välj en API** klickar du på **Microsoft Graph**, och klicka sedan på **Välj**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/33.png)

10. På den **Aktivera åtkomst** väljer **läsa alla gransknings-och loggdata**, och klicka sedan på **Välj**.  

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/34.png)


11. På den **Lägg till API-åtkomst** klickar du på **klar**.  

12. På den **nödvändiga behörigheter** i verktygsfältet högst upp på sidan. Klicka på **bevilja behörigheter**, och klicka sedan på **Ja**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>Samla in konfigurationsinställningar 

Det här avsnittet visar hur du hämtar följande inställningar från din katalog:

- Domännamn
- Klient-ID
- Klienthemlighet

Du behöver dessa värden när du konfigurerar anrop till rapporterings-API. 

### <a name="get-your-domain-name"></a>Hämta ditt domännamn

**Hämta ditt domännamn:**

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret klickar du på **Azure Active Directory**.
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. På den **Azure Active Directory** klickar du på **anpassade domännamn**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Kopiera ditt domännamn i listan över domäner.


### <a name="get-your-applications-client-id"></a>Hämta programmets klient-ID

**Hämta programmets klient-ID:**

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret klickar du på **Azure Active Directory**.
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. På den **appregistreringar** , i listan över appar, klickar du på **Reporting-API-program**.

3. På den **Reporting-API-program** sidan på den **program-ID**, klickar du på **Klicka om du vill kopiera**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/11.png) 



### <a name="get-your-applications-client-secret"></a>Hämta ditt programs klienthemlighet
För att få ditt programs klienthemlighet, måste du skapa en ny nyckel och spara sitt värde vid spara den nya nyckeln eftersom det inte går att hämta det här värdet senare längre.

**Hämta klienthemlighet för ditt program:**

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret klickar du på **Azure Active Directory**.
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. På den **appregistreringar** , i listan över appar, klickar du på **Reporting-API-program**.


3. På den **Reporting-API-program** , i verktygsfältet högst upp, klickar du på **inställningar**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. På den **inställningar** sidan den **APIR åtkomst** klickar du på **nycklar**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/12.png)


5. På den **nycklar** utför följande steg:

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. I den **beskrivning** textrutan typ `Reporting API`.

    b. Som **förfaller**väljer **i 2 år**.

    c. Klicka på **Spara**.

    d. Kopiera nyckelvärdet.


## <a name="next-steps"></a>Nästa steg

* [Hämta data med hjälp av Azure Active Directory reporting API: et med certifikat](tutorial-access-api-with-certificates.md)
* [Få ett första intryck av rapport-API:er](concept-reporting-api.md)
* [Granska API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Inloggningsaktivitet rapport API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
