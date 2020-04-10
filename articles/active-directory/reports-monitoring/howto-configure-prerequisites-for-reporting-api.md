---
title: Förutsättningar för Azure Active Directory-rapporterings-API | Microsoft-dokument
description: Lär dig mer om förutsättningarna för att komma åt Azure AD-rapporterings-API:et
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd3580ca03fa49d428904c6da78fdf9cda202c7
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991270"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Förutsättningar för åtkomst till Azure Active Directory-rapporterings-API:et

[Azure Active Directory reporting API: er](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa dessa API:er från programmeringsspråk och verktyg.

Rapporterings-API:et använder [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) för att auktorisera åtkomst till webb-API:erna.

Om du vill förbereda din åtkomst till rapporterings-API:et måste du:

1. [Tilldela roller](#assign-roles)
2. [Licenskrav](#license-requirements)
3. [Registrera ett program](#register-an-application)
4. [Bevilja behörigheter](#grant-permissions)
5. [Samla in konfigurationsinställningar](#gather-configuration-settings)

## <a name="assign-roles"></a>Tilldela roller

Om du vill få åtkomst till rapporteringsdata via API:et måste du ha en av följande roller tilldelad:

- Säkerhetsläsare

- Säkerhetsadministratör

- Global administratör

## <a name="license-requirements"></a>Licenskrav

För att komma åt inloggningsrapporterna för en klient måste en Azure AD-klient ha associerad Azure AD Premium-licens. Azure AD Premium P1 -licens (eller högre) krävs för att komma åt inloggningsrapporter för alla Azure AD-klienter. Alternativt om katalogtypen är Azure AD B2C är inloggningsrapporterna tillgängliga via API utan ytterligare licenskrav. 


## <a name="register-an-application"></a>Registrera ett program

Registrering behövs även om du använder rapporterings-API:et med ett skript. Registreringen ger dig ett **program-ID**, som krävs för auktoriseringsanrop och gör att din kod kan ta emot token.

Om du vill konfigurera katalogen för åtkomst till Azure AD-rapporterings-API:et måste du logga in på [Azure-portalen](https://portal.azure.com) med ett Azure-administratörskonto som också är medlem i katalogrollen **Global Administratör** i din Azure AD-klientorganisation.

> [!IMPORTANT]
> Program som körs under autentiseringsuppgifter med administratörsbehörighet kan vara mycket kraftfulla, så var noga med att behålla programmets ID och hemliga autentiseringsuppgifter på en säker plats.
> 

**Så här registrerar du ett Azure AD-program:**

1. Välj Azure Active **Directory** i det vänstra navigeringsfönstret i [Azure-portalen.](https://portal.azure.com)
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. På sidan **Azure Active Directory** väljer du **Appregistreringar**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Välj **Ny registrering**på sidan **Appregistreringar** .

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Sidan **Registrering en ansökan:**

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Skriv `Reporting API application`i textrutan **Namn** .

    b. För **konton som stöds**väljer du Konton endast i den här **organisationen**.

    c. Skriv **webbtextrutan** i `https://localhost` **url:en omdirigering** och skriv .

    d. Välj **Registrera**. 


## <a name="grant-permissions"></a>Bevilja behörigheter 

Beroende på vilket API du vill komma åt måste du ge appen följande behörigheter:  

| API | Behörighet |
| --- | --- |
| Windows Azure Active Directory | Läs katalogdata |
| Microsoft Graph | Läs alla granskningsloggdata |


![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/36.png)

I följande avsnitt visas stegen för båda API:erna. Om du inte vill komma åt något av API:erna kan du hoppa över de relaterade stegen.

**Så här beviljar du dina programbehörigheter för att använda API:erna:**


1. Välj **API-behörigheter** och lägg sedan **till en behörighet**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Leta upp **support äldre API** Azure Active Directory **Graph**på **sidan Api-behörigheter**för begäran. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. På sidan **Obligatoriska behörigheter** väljer du **Programbehörigheter**, expandera **Katalogkontrollruta** **Directory.ReadAll**.  Välj **Lägg till behörigheter**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. På sidan **Rapporterings-API-program - API-behörigheter** väljer du **Bevilja administratörsmedgivande**. 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Microsoft **Graph** läggs till som standard under API-registrering.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Samla in konfigurationsinställningar 

I det här avsnittet visas hur du hämtar följande inställningar från katalogen:

- Domännamn
- Klientorganisations-ID
- Klienthemlighet

Du behöver dessa värden när du konfigurerar anrop till rapporterings-API:et. 

### <a name="get-your-domain-name"></a>Hämta ditt domännamn

**Så här hämtar du ditt domännamn:**

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i det vänstra navigeringsfönstret .
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Välj **Anpassade domännamn**på sidan Azure **Active Directory** .

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Kopiera ditt domännamn från listan över domäner.


### <a name="get-your-applications-client-id"></a>Hämta programmets klient-ID

**Så här hämtar du programmets klient-ID:**

1. Klicka på Azure Active Directory i det vänstra navigeringsfönstret i [Azure-portalen](https://portal.azure.com). **Azure Active Directory**
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Välj din ansökan på sidan **Appregistreringar.**

3. På programsidan navigerar du till **program-ID** och väljer **Klicka för att kopiera**.

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Få programmets klienthemlighet
 Undvik fel när du försöker komma åt granskningsloggar eller logga in med API:et.

**Så här hämtar du programmets klienthemlighet:**

1. Klicka på Azure Active Directory i det vänstra navigeringsfönstret i [Azure-portalen](https://portal.azure.com). **Azure Active Directory**
   
    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Välj din ansökan på sidan **Appregistreringar.**

3.  Välj **Certifikat och hemligheter** på **API-programsidan,** klicka på **+ Ny klienthemlighet**i avsnittet **Klienthemligheter** . 

    ![Registrera program](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Lägg **till:**

    a. Skriv `Reporting API`i textrutan **Beskrivning** .

    b. Som **Förfaller**väljer du **Om 2 år**.

    c. Klicka på **Spara**.

    d. Kopiera nyckelvärdet.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Felsöka fel i rapporterings-API:et

I det här avsnittet visas de vanliga felmeddelanden som du kan stöta på när du öppnar aktivitetsrapporter med Hjälp av Microsoft Graph API och steg för deras lösning.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Fel: Det gick inte att hämta användarroller från Microsoft Graph

 Logga in på ditt konto med båda inloggningsknapparna i graph explorer-användargränssnittet för att undvika att få ett felmeddelande när du försöker logga in med Graph Explorer. 

![Graph-testaren](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Fel: Det gick inte att göra premiumlicenskontrollen från Microsoft Graph 

Om du stöter på det här felmeddelandet när du försöker komma åt inloggningar med Graph Explorer väljer du **Ändra behörigheter** under ditt konto på den vänstra navigeringsfältet och väljer **Tasks.ReadWrite** och **Directory.Read.All**. 

![Användargränssnitt för ändring av behörigheter](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Fel: Klienten är inte B2C eller klienten har inte premiumlicens

Åtkomst till inloggningsrapporter kräver en Azure Active Directory premium 1 (P1) licens. Om det här felmeddelandet visas när du öppnar inloggningar kontrollerar du att din klient är licensierad med en Azure AD P1-licens.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Fel: De tillåtna rollerna inkluderar inte Användare. 

 Undvik fel när du försöker komma åt granskningsloggar eller logga in med API:et. Kontrollera att ditt konto är en del av rollen **Säkerhetsläsare** eller **rapportläsare** i din Azure Active Directory-klientorganisation.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fel: Program som saknar behörigheten AAD "Läs katalogdata" 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Fel: Microsoft Graph API saknas med behörigheten Läs alla granskningsloggdata

Följ stegen i [förutsättningar för att komma åt Azure Active Directory-rapporterings-API:et](howto-configure-prerequisites-for-reporting-api.md) för att säkerställa att ditt program körs med rätt uppsättning behörigheter. 

## <a name="next-steps"></a>Nästa steg

* [Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat](tutorial-access-api-with-certificates.md)
* [Referens för gransknings-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [API-referens för inloggningsaktivitetsrapport](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
