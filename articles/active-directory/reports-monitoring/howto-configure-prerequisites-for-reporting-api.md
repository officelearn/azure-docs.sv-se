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
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 03acd7c283fd1296af06dd19d0170a4b3c65eeb3
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352503"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Förutsättningar för att få åtkomst till Azure Active Directory reporting API

[Azure Active Directory reporting API: er](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg.

Reporting API: et använder [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) att godkänna åtkomst till webb-API: er.

För att förbereda din åtkomst till rapporterings-API, måste du:

1. [Tilldela roller](#assign-roles)
2. [Registrera ett program](#register-an-application)
3. [Bevilja behörigheter](#grant-permissions)
4. [Samla in konfigurationsinställningar](#gather-configuration-settings)

## <a name="assign-roles"></a>Tilldela roller

För att få åtkomst till rapporteringsdata via API, måste du ha ett av följande roller:

- Säkerhetsläsare

- Säkerhetsadministratör

- Global administratör


## <a name="register-an-application"></a>Registrera ett program

Du behöver registrera ett program, även om du försöker komma åt rapporterings-API med hjälp av ett skript. Detta ger dig en **program-ID**, vilket krävs för auktorisering anropar och gör din kod för att ta emot tokens.

Om du vill konfigurera din katalog för att komma åt Azure AD reporting-API som du måste logga in på den [Azure-portalen](https://portal.azure.com) med en Azure-administratörskonto som också är medlem i den **Global administratör** katalogroll i Azure AD-klienten.

> [!IMPORTANT]
> Program som körs under autentiseringsuppgifter med administratörsbehörighet kan vara mycket kraftfulla, så var noga med att hålla programmets ID och hemlighet autentiseringsuppgifter på en säker plats.
> 

**Registrera ett Azure AD-program:**

1. I den [Azure-portalen](https://portal.azure.com)väljer **Azure Active Directory** från det vänstra navigeringsfönstret.
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. I den **Azure Active Directory** väljer **appregistreringar**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Från den **appregistreringar** väljer **ny programregistrering**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. I den **skapa** utför följande steg:

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. I den **namn** textrutan typ `Reporting API application`.

    b. Som **programtyp**väljer **webbapp / API**.

    c. I den **inloggnings-URL** textrutan typ `https://localhost`.

    d. Välj **Skapa**. 


## <a name="grant-permissions"></a>Bevilja behörigheter 

Beroende på API som du vill komma åt, måste du bevilja följande behörigheter för din app:  

| API | Behörighet |
| --- | --- |
| Windows Azure Active Directory | Läsa katalogdata |
| Microsoft Graph | Läsa alla gransknings-och loggdata |


![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/36.png)

I följande avsnitt visas stegen för både API: er. Om du inte vill ha åtkomst till någon av de API: erna kan du hoppa över de relaterade steg.

**Så här ger ditt program behörighet att använda API: erna:**

1. Välj ditt program från den **Appregistreringar** och välj **inställningar**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. På den **inställningar** väljer **behörigheter som krävs för**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. På den **nödvändiga behörigheter** sidan den **API** klickar du på **Windows Azure Active Directory**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. På den **Aktivera åtkomst** väljer **läsa katalogdata** och avmarkera **logga in och läsa användarprofil**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. I verktygsfältet högst upp, klickar du på **spara**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. På den **nödvändiga behörigheter** , i verktygsfältet högst upp, klickar du på **Lägg till**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. På den **Lägg till API-åtkomst** klickar du på **Välj en API**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. På den **Välj en API** klickar du på **Microsoft Graph**, och klicka sedan på **Välj**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. På den **Aktivera åtkomst** väljer **läsa alla gransknings-och loggdata**, och klicka sedan på **Välj**.  

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. På den **Lägg till API-åtkomst** klickar du på **klar**.  

11. På den **nödvändiga behörigheter** i verktygsfältet högst upp på sidan. Klicka på **bevilja behörigheter**, och klicka sedan på **Ja**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>Samla in konfigurationsinställningar 

Det här avsnittet visar hur du hämtar följande inställningar från din katalog:

- Domännamn
- Klientorganisations-ID
- Klienthemlighet

Du behöver dessa värden när du konfigurerar anrop till rapporterings-API. 

### <a name="get-your-domain-name"></a>Hämta ditt domännamn

**Hämta ditt domännamn:**

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret väljer **Azure Active Directory**.
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. På den **Azure Active Directory** väljer **anpassade domännamn**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Kopiera ditt domännamn i listan över domäner.


### <a name="get-your-applications-client-id"></a>Hämta programmets klient-ID

**Hämta programmets klient-ID:**

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret klickar du på **Azure Active Directory**.
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Välj ditt program från den **Appregistreringar** sidan.

3. Från programsidan navigerar du till **program-ID** och välj **Klicka om du vill kopiera**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Hämta ditt programs klienthemlighet
För att få ditt programs klienthemlighet, måste du skapa en ny nyckel och spara sitt värde vid spara den nya nyckeln eftersom det inte går att hämta det här värdet senare längre.

**Hämta klienthemlighet för ditt program:**

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret klickar du på **Azure Active Directory**.
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Välj ditt program från den **Appregistreringar** sidan.

3. På programsidan i verktygsfältet högst upp väljer **inställningar**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. På den **inställningar** sidan den **API-åtkomst** klickar du på **nycklar**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. På den **nycklar** utför följande steg:

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. I den **beskrivning** textrutan typ `Reporting API`.

    b. Som **förfaller**väljer **i 2 år**.

    c. Klicka på **Spara**.

    d. Kopiera nyckelvärdet.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Felsöka fel i rapporterings-API

Det här avsnittet innehåller vanliga felmeddelanden som kan uppstå vid åtkomst till rapporter med MS Graph API och stegen för sin lösning.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 http-internt serverfel vid åtkomst till Microsoft Graph V2-slutpunkten

Vi stöder för närvarande inte v2-slutpunkten för Microsoft Graph – se till att få åtkomst till aktivitetsloggar som använder Microsoft Graph v1-slutpunkten.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Fel: Det gick inte att hämta användarroller från AD Graph

Du kan få detta felmeddelande när du försöker komma åt inloggningar med Graph Explorer. Kontrollera att du har loggat in till ditt konto med båda knapparna logga in i Gränssnittet Graph Explorer enligt följande bild. 

![Graph-testaren](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Fel: Det gick inte att göra premium licenskontrollen från AD Graph 

Om du får detta felmeddelande när du försöker komma åt inloggningar med Graph Explorer, Välj **behörighet att ändra** under ditt konto i det vänstra navigeringsfältet och väljer **Tasks.ReadWrite** och **Directory.Read.All**. 

![Ändra behörigheter för Användargränssnittet](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Fel: Varken klienten är B2C eller klienten har inte premium-licens

Åtkomst till inloggning rapporter kräver en Azure Active Directory premium 1 (P1) licens. Om du ser det här felmeddelandet vid åtkomst till inloggningar, se till att din klient är licensierad med Azure Active Directory P1-licens.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Fel: Användaren är inte tillåtna roller 

Om du ser det här felmeddelandet när du försöker komma åt granskningsloggar eller inloggningar med API: et, se till att ditt konto är en del av den **Säkerhetsläsare** eller **rapportläsare** roll i Azure Active Directory innehavaren. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fel: Program som saknar behörighet för AAD-läsa kataloginformation 

Följ stegen i den [förutsättningar för att få åtkomst till Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md) så att programmet körs med rätt uppsättning behörigheter. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Fel: Programmet MSGraph API läsa alla gransknings-och loggdata behörighet som saknas

Följ stegen i den [förutsättningar för att få åtkomst till Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md) så att programmet körs med rätt uppsättning behörigheter. 

## <a name="next-steps"></a>Nästa steg

* [Hämta data med hjälp av Azure Active Directory reporting API: et med certifikat](tutorial-access-api-with-certificates.md)
* [Granska API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Inloggningsaktivitet rapport API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
