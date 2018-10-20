---
title: Aktivera fjärråtkomst till SharePoint med Azure AD Application Proxy | Microsoft Docs
description: Beskriver grunderna för hur du integrerar en lokal SharePoint-server med Azure AD-programproxy.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: ad0ab8790c9d66dbba87d466e9661ef8cf33eef3
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456906"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Aktivera fjärråtkomst till SharePoint med Azure AD Application Proxy

Den här artikeln beskrivs hur du integrerar en lokal SharePoint-server med Azure Active Directory (Azure AD) Application Proxy.

Följ avsnitten i den här artikeln steg för steg om du vill aktivera fjärråtkomst till SharePoint med Azure AD-programproxy.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har SharePoint 2013 eller senare i din miljö. Dessutom kan du överväga att följande krav:

* SharePoint innehåller inbyggt stöd för Kerberos. Användare som kommer åt via en fjärranslutning interna webbplatser via Azure AD Application Proxy kan därför anta för att ha en enkel inloggning (SSO)-upplevelse.

* Det här scenariot innehåller konfigurationsändringar till SharePoint-servern. Vi rekommenderar att du använder en mellanlagringsmiljö. På så sätt kan du göra uppdateringar till testservern först, och underlätta en testcykel innan du påbörjar produktionen.

* Vi kräver SSL på publicerade URL: en. Du måste ha SSL aktiverat på webbplatsen internt för att säkerställa att länkar skickas/mappas korrekt. Om du inte har konfigurerat SSL, se [Konfigurera SSL för SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) anvisningar. Kontrollera också att det certifikat som du utfärdar litar på connector-datorn. (Certifikaten som inte behöver utfärdas offentligt.)

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>Steg 1: Ställ in enkel inloggning till SharePoint

För lokala program som använder Windows-autentisering, kan du få enkel inloggning (SSO) med Kerberos-autentiseringsprotokollet och en funktion som kallas Kerberos-begränsad delegering (KCD). KCD när konfigurerad, kan programproxy-kopplingen till en Windows-token för en användare, även om användaren inte har loggat in till Windows direkt. Läs mer om KCD i [Kerberos-begränsad delegering översikt](https://technet.microsoft.com/library/jj553400.aspx).

Använda procedurerna i följande sekventiella avsnitt om du vill konfigurera KCD för en SharePoint-server:

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Kontrollera att SharePoint körs under ett tjänstkonto

Kontrollera först att SharePoint körs under en definierad tjänstkonto--inte lokalt system, lokal tjänst och nätverkstjänst. Gör så att du kan koppla tjänstens huvudnamn (SPN) till ett giltigt konto. SPN-namn är hur Kerberos-protokollet identifierar olika tjänster. Och du behöver konto senare för att konfigurera KCD.

> [!NOTE]
Du måste ha ett befintligt Azure AD-konto för tjänsten. Vi rekommenderar att du tillåter för en automatisk ändring av lösenord. Mer information om en fullständig uppsättning steg och felsökning av problem finns i [konfigurera automatisk ändring av lösenord i SharePoint 2013](https://technet.microsoft.com/library/ff724280.aspx).

För att säkerställa att dina webbplatser körs under ett tjänstkonto som är definierade, utför du följande steg:

1. Öppna den **Central Administration av SharePoint 2013** plats.
2. Gå till **Security** och välj **Konfigurera tjänstkonton**.
3. Välj **Web programpoolen - SharePoint – 80**. Alternativen kan vara annorlunda ut baserat på namnet på din webb-pool eller om poolen web använder SSL som standard.

  ![Alternativ för att konfigurera ett tjänstkonto](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Om **väljer du ett konto för den här komponenten** anges till **lokal tjänst** eller **nätverkstjänst**, måste du skapa ett konto. Om inte, du är klar och gå till nästa avsnitt.
5. Välj **registrera nytt hanterat konto**. När ditt konto har skapats måste du ange **webbprogrampoolen** innan du kan använda kontot.

### <a name="configure-sharepoint-for-kerberos"></a>Konfigurera SharePoint för Kerberos

Du kan använda KCD för att utföra enkel inloggning till SharePoint-servern.

Konfigurera din SharePoint-webbplats för Kerberos-autentisering:

1. Öppna den **Central Administration av SharePoint 2013** plats.
2. Gå till **programhantering**väljer **hantera webbprogram**, och välj din SharePoint-webbplats. I det här exemplet är det **SharePoint – 80**.

  ![Välja den SharePoint-webbplatsen](./media/application-proxy-integrate-with-sharepoint-server/manage-web-applications.png)

3. Klicka på **autentiseringsproviders** i verktygsfältet.
4. I den **autentiseringsproviders** klickar du på **standardzonen** vill visa inställningarna.
5. I den **Redigera autentisering** dialogrutan rutan, rulla nedåt tills du ser **Anspråksautentiseringstyper**. Se till att båda **aktivera Windows-autentisering** och **integrerad Windows-autentisering** har valts.
6. I den nedrullningsbara listrutan för fältet integrerad Windows-autentisering, se till att **förhandla (Kerberos)** har valts.

  ![Dialogrutan Redigera autentisering](./media/application-proxy-integrate-with-sharepoint-server/service-edit-authentication.png)

7. Längst ned på den **Redigera autentisering** dialogrutan klickar du på **spara**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Ange tjänstens huvudnamn för SharePoint-tjänstkonto

Innan du konfigurerar KCD som du behöver identifiera SharePoint-tjänster som körs som det tjänstkonto som du har konfigurerat. Identifiera tjänsten genom att ange ett SPN. Mer information finns i [Service Principal Names](https://technet.microsoft.com/library/cc961723.aspx).

SPN-formatet är:

```
<service class>/<host>:<port>
```

SPN-format:

* _tjänsten klass_ är ett unikt namn för tjänsten. SharePoint, använder du **HTTP**.

* _värden_ är fullständigt kvalificerade domännamnet eller NetBIOS-namnet på den värd som tjänsten körs på. För en SharePoint-webbplats kan den här texten måste vara URL: en för platsen, beroende på vilken version av IIS som du använder.

* _port_ är valfritt.

Om det fullständiga Domännamnet för SharePoint-servern är:

```
sharepoint.demo.o365identity.us
```

SPN-namnet är:

```
HTTP/sharepoint.demo.o365identity.us demo
```

Du kan även behöva ange SPN för specifika webbplatser på servern. Mer information finns i [konfigurera Kerberos-autentisering](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Var uppmärksam till avsnittet ”Skapa tjänstens huvudnamn för dina webbprogram med hjälp av Kerberos-autentisering”.

Det enklaste sättet för dig att ställa in SPN: er är att följa de SPN-format som kan redan finnas för dina platser. Kopiera dessa SPN: er för att registrera mot kontot. Gör så här:

1. Bläddra till platsen med SPN från en annan dator.
 När du gör, cachelagras relevanta uppsättningen med Kerberos-biljetter på datorn. Dessa biljetter innehåller SPN för målwebbplatsen som du bläddrat till.

2. Du kan hämta SPN-namnet för den platsen med hjälp av verktyget [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). Kör följande kommando i ett kommandofönster som körs i samma kontext som den användare som anropade webbplatsen i webbläsaren:
```
Klist
```
Klist returnerar sedan uppsättning target SPN-namn. I det här exemplet är det markerade värdet SPN som behövs:

  ![Exempel på Klist resultat](./media/application-proxy-integrate-with-sharepoint-server/remote-sharepoint-target-service.png)

4. Nu när du har SPN kan du kontrollera att den är korrekt konfigurerad på det tjänstkonto som du har konfigurerat för webbprogrammet tidigare. Kör följande kommando från Kommandotolken som administratör för domänen:

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 Det här kommandot anger SPN för SharePoint service-kontot som kör _demo\sp_svc_.

 Ersätt _http/sharepoint.demo.o365identity.us_ med SPN-namnet för servern och _demo\sp_svc_ med kontot i din miljö. Kommandot Setspn söker efter SPN innan den lägger till den. I det här fallet visas en **duplicera SPN-värdet** fel. Om du ser det här felet, se till att värdet är associerade med kontot.

Du kan verifiera att SPN har lagts till genom att köra kommandot Setspn -l-alternativet. Mer information om det här kommandot finns [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Kontrollera att anslutningen har angetts som en betrodd ombud till SharePoint

Konfigurera KCD så att Azure AD Application Proxy-tjänsten kan delegera användaridentiteter till SharePoint-tjänsten. Konfigurera KCD genom att aktivera programproxy-kopplingen att hämta Kerberos-biljetter för de användare som har verifierats i Azure AD. Servern skickar sedan kontexten till målprogrammet eller SharePoint i det här fallet.

Upprepa följande steg för att konfigurera KCD, för varje connector-datorn:

1. Logga in som domänadministratör på en Domänkontrollant och öppna sedan **Active Directory-användare och datorer**.
2. Hitta den dator som kör anslutningstjänsten på. I det här exemplet är det samma SharePoint-servern.
3. Dubbelklicka på datorn och klicka sedan på den **delegering** fliken.
4. Se till att inställningarna för standarddelegering är inställda på **lita på den här datorn för delegering till angivna tjänster endast**. Välj **Använd valfritt autentiseringsprotokoll**.

  ![Delegeringsinställningarna](./media/application-proxy-integrate-with-sharepoint-server/delegation-box.png)

5. Klicka på den **Lägg till** knapp, klickar du på **användare eller datorer**, och leta upp kontot.

  ![Att lägga till SPN för tjänstkontot](./media/application-proxy-integrate-with-sharepoint-server/users-computers.png)

6. I listan över SPN-namn, väljer du det konto som du skapade tidigare för tjänstkontot.
7. Klicka på **OK**. Klicka på **OK** igen för att spara ändringarna.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>Steg 2: Aktivera fjärråtkomst till SharePoint

Nu när du har aktiverat SharePoint för Kerberos- och konfigurerade KCD, är du redo att publicera SharePoint-servergruppen för fjärråtkomst via Azure AD-programproxy.

1. Publicera din SharePoint-webbplats med följande inställningar. Stegvisa instruktioner finns i [publicera program med Azure AD Application Proxy](application-proxy-publish-azure-portal.md). 
   - **Intern URL**: URL: en för SharePoint-webbplatsen internt, såsom **https://SharePoint/**. I det här exemplet, se till att använda **https**
   - **Förautentiseringsmetoden**: Azure Active Directory
   - **Översätt URL i rubriker**: Nej

   >[!TIP]
   >SharePoint använder den _värdhuvud_ värde att leta upp platsen. Det genererar även länkar som baseras på det här värdet. Nettoeffekten är att alla länkar som genereras av SharePoint är en publicerade URL som är korrekt konfigurerad att använda den externa URL: en. Ställer in värdet på **Ja** kan också anslutningen så att vidarebefordra begäran till backend-programmet. Dock ställer in värdet på **nr** innebär att anslutningen inte ska skicka interna värdnamnet. I stället skickar anslutningsappen värdhuvudet som publicerade URL: en för backend-programmet.

   ![Publicera SharePoint som programmet](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. När din app har publicerats kan du konfigurera inställningar för enkel inloggning med följande steg:

   1. På programsidan i portalen väljer **enkel inloggning**.
   2. Enkel inloggningsläge, Välj **integrerad Windows-autentisering**.
   3. Intern program-SPN inställd på värdet som du angav tidigare. I det här exemplet som skulle vara **http/sharepoint.demo.o365identity.us**.

   ![Konfigurera integrerad Windows-autentisering för enkel inloggning](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Om du vill slutföra konfigurationen av ditt program, går du till den **användare och grupper** avsnittet och tilldela användare åtkomst till det här programmet. 

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>Steg 3: Kontrollera att SharePoint känner till den externa URL: en

Din sista steget är att säkerställa att SharePoint hittar webbplatsen baserat på den externa URL: en, så att det återges länkar baserat på den externa URL: en. Du kan göra detta genom att konfigurera alternativa åtkomstmappningar för SharePoint-webbplatsen.

1. Öppna den **Central Administration av SharePoint 2013** plats.
2. Under **systeminställningar**väljer **konfigurera alternativa åtkomstmappningar**. Alternativa åtkomstmappningar rutan öppnas.

  ![Alternativa åtkomstmappningar box](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

3. I listrutan bredvid **alternativa åtkomst mappning samling**väljer **ändra alternativa åtkomst mappning samling**.
4. Välj din plats – till exempel **SharePoint – 80**.
5. Du kan välja att lägga till publicerade URL: en som en intern Webbadress eller en offentlig URL. Det här exemplet använder en offentlig URL som extranätet. Om du använder en anpassad port måste du inkludera den anpassa porten i Webbadressen.
6. Klicka på **redigera offentliga URL: er** i den **extranät** sökväg, och sedan ange den externa URL: en som skapades när du har publicerat programmet. Ange till exempel **https://sharepoint-iddemo.msappproxy.net**.

  ![Att ange sökvägen](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

7. Klicka på **Spara**.

Du kan nu komma åt SharePoint-webbplatsen externt via Azure AD-programproxy.

## <a name="next-steps"></a>Nästa steg

- [Arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md)
- [Förstå Azure AD Application Proxy-anslutningar](application-proxy-connectors.md)

