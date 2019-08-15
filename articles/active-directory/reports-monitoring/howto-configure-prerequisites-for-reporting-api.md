---
title: 'Krav för att få åtkomst till API: et för Azure Active Directory rapportering | Microsoft Docs'
description: Läs om förutsättningarna för att få åtkomst till Azure AD repor ting API
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f925a86504d68fd08b83c63e4da8b37b4aa25f85
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989907"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Krav för att få åtkomst till API: et för Azure Active Directory rapportering

[Azure Active Directory reporting API: er](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg.

Rapporterings-API: et använder [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) för att ge åtkomst till webb-API: er.

För att förbereda din åtkomst till rapporterings-API: et måste du:

1. [Tilldela roller](#assign-roles)
2. [Registrera ett program](#register-an-application)
3. [Bevilja behörigheter](#grant-permissions)
4. [Samla in konfigurations inställningar](#gather-configuration-settings)

## <a name="assign-roles"></a>Tilldela roller

Du måste ha någon av följande roller tilldelade för att få åtkomst till rapporterings data via API: t:

- Säkerhetsläsare

- Säkerhetsadministratör

- Global administratör


## <a name="register-an-application"></a>Registrera ett program

Du måste registrera ett program även om du har åtkomst till rapporterings-API: et med hjälp av ett skript. Detta ger dig ett **program-ID**, vilket krävs för auktoriseringen och gör att din kod kan ta emot tokens.

Om du vill konfigurera din katalog för att få åtkomst till Azure AD repor ting API måste du logga in på [Azure Portal](https://portal.azure.com) med ett Azure-administratörskonto som också är medlem i katalog rollen **Global administratör** i din Azure AD-klient.

> [!IMPORTANT]
> Program som körs under autentiseringsuppgifter med administratörs behörighet kan vara mycket kraftfulla, så se till att behålla programmets ID och hemliga autentiseringsuppgifter på en säker plats.
> 

**Registrera ett Azure AD-program:**

1. I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory** i det vänstra navigerings fönstret.
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. På sidan **Azure Active Directory** väljer du **Appregistreringar**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. På sidan **Appregistreringar** väljer du **ny program registrering**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Utför följande steg på sidan **skapa** :

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. I text rutan **namn** skriver `Reporting API application`du.

    b. Som **program typ**väljer du **webbapp/API**.

    c. I text rutan **inloggnings-URL** skriver `https://localhost`du.

    d. Välj **Skapa**. 


## <a name="grant-permissions"></a>Bevilja behörigheter 

Beroende på vilket API du vill ha åtkomst till måste du ge appen följande behörigheter:  

| API | Behörighet |
| --- | --- |
| Windows Azure Active Directory | Läsa katalogdata |
| Microsoft Graph | Läs alla Gransknings logg data |


![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/36.png)

I följande avsnitt visas stegen för båda API: erna. Om du inte vill använda något av API: erna kan du hoppa över de relaterade stegen.

**Så här beviljar du dina program behörigheter att använda API: erna:**

1. Välj ditt program på sidan **registreringar för appen** och välj **Inställningar**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. På sidan **Inställningar** väljer du **nödvändiga behörigheter**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. På sidan **behörigheter som krävs** klickar du på **Windows Azure Active Directory**i **API** -listan. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. På sidan **Aktivera åtkomst** väljer du **Läs katalog data** och avmarkerar **Logga in och Läs användar profil**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. I verktygsfältet högst upp klickar du på **Spara**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. Klicka på **Lägg till**i verktygsfältet högst upp på sidan **behörigheter som krävs** .

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. På sidan **Lägg till API-åtkomst** klickar du på **Välj ett API**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. På sidan **Välj ett API** klickar du på **Microsoft Graph**och klickar sedan på **Välj**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. På sidan **Aktivera åtkomst** väljer du **Läs alla Gransknings logg data**och klickar sedan på **Välj**.  

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. På sidan **Lägg till API-åtkomst** klickar du på **Slutför**.  

11. I verktygsfältet högst upp på sidan **nödvändiga behörigheter** . Klicka på **bevilja behörigheter**och klicka sedan på **Ja**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>Samla in konfigurations inställningar 

I det här avsnittet visas hur du hämtar följande inställningar från din katalog:

- Domännamn
- Klientorganisations-ID
- Klienthemlighet

Du behöver dessa värden när du konfigurerar anrop till rapporterings-API: et. 

### <a name="get-your-domain-name"></a>Hämta ditt domän namn

**Så här hämtar du ditt domän namn:**

1. I [Azure Portal](https://portal.azure.com)i det vänstra navigerings fönstret väljer du **Azure Active Directory**.
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. På sidan **Azure Active Directory** väljer du **anpassade domän namn**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Kopiera domän namnet från listan över domäner.


### <a name="get-your-applications-client-id"></a>Hämta klient-ID för ditt program

**Hämta klient-ID: t för ditt program:**

1. I [Azure Portal](https://portal.azure.com)i det vänstra navigerings fönstret klickar du på **Azure Active Directory**.
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Välj ditt program på sidan **registreringar för appen** .

3. Från program sidan, navigerar du till **program-ID** och väljer **Klicka för att kopiera**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Hämta programmets klient hemlighet
För att få ditt programs klient hemlighet måste du skapa en ny nyckel och spara värdet när du sparar den nya nyckeln eftersom det inte går att hämta det här värdet senare längre.

**Så här hämtar du programmets klient hemlighet:**

1. I [Azure Portal](https://portal.azure.com)i det vänstra navigerings fönstret klickar du på **Azure Active Directory**.
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Välj ditt program på sidan **registreringar för appen** .

3. På sidan program i verktygsfältet högst upp väljer du **Inställningar**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. På sidan **Inställningar** , i avsnittet **API-åtkomst** , klickar du på **nycklar**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Utför följande steg på sidan **nycklar** :

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. I text rutan **Beskrivning** skriver `Reporting API`du.

    b. Välj **i två år**som förfaller.

    c. Klicka på **Spara**.

    d. Kopiera nyckelvärdet.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Felsöka fel i rapporterings-API: et

Det här avsnittet innehåller vanliga fel meddelanden som du kan köra i när du får åtkomst till aktivitets rapporter med hjälp av MS Graph API och steg för att lösa problemet.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP internt Server fel vid åtkomst till Microsoft Graph v2-slutpunkt

Vi stöder för närvarande inte Microsoft Graph v2-slutpunkten – se till att få åtkomst till aktivitets loggarna med hjälp av Microsoft Graph v1-slutpunkten.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Fel: Det gick inte att hämta användar roller från AD Graph

Du kan få det här fel meddelandet när du försöker få åtkomst till inloggningar med Graph Explorer. Kontrol lera att du är inloggad på ditt konto med hjälp av båda inloggnings knapparna i graphs användar gränssnitt, som du ser i följande bild. 

![Graph-testaren](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Fel: Det gick inte att kontrol lera Premium-licens från AD Graph 

Om du stöter på det här fel meddelandet när du försöker få åtkomst till inloggningar med Graph Explorer väljer du **ändra behörigheter** under ditt konto i det vänstra navigerings fältet och väljer **Tasks. readwrite** och **Directory. Read. all**. 

![Ändra behörighets gränssnitt](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Fel: Ingen klient organisation är B2C eller klient organisationen har ingen Premium-licens

Åtkomst till inloggnings rapporter kräver en licens för Azure Active Directory Premium 1 (P1). Om du ser det här fel meddelandet vid åtkomst till inloggningar kontrollerar du att klienten är licensierad med en Azure AD P1-licens.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Fel: Användaren finns inte i de tillåtna rollerna 

Om du ser det här fel meddelandet när du försöker få åtkomst till gransknings loggar eller inloggningar med hjälp av API: et kontrollerar du att ditt konto är en del av rollen **säkerhets läsare** eller **rapport läsare** i Azure Active Directory klient organisationen. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fel: Program som saknar AAD-behörighet för Läs katalog data 

Följ stegen i [kraven för att få åtkomst till Azure Active Directory rapporterings-API: et](howto-configure-prerequisites-for-reporting-api.md) för att se till att programmet körs med rätt behörighets uppsättning. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Fel: Program saknar MSGraph API ' Läs alla Gransknings logg data ' behörighet

Följ stegen i [kraven för att få åtkomst till Azure Active Directory rapporterings-API: et](howto-configure-prerequisites-for-reporting-api.md) för att se till att programmet körs med rätt behörighets uppsättning. 

## <a name="next-steps"></a>Nästa steg

* [Hämta data med hjälp av Azure Active Directory rapporterings-API med certifikat](tutorial-access-api-with-certificates.md)
* [Granska API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Rapport-API-referens för inloggnings aktivitet](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
