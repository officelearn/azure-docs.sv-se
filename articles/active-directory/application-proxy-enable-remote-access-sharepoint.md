---
title: "Aktivera fjärråtkomst och SharePoint med Azure AD Application Proxy | Microsoft Docs"
description: "Beskriver grunderna för hur du integrerar en lokal SharePoint-server med Azure AD Application Proxy."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 2b4ad3e7bda1346e606b2c185c204154b8f19f87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Aktivera fjärråtkomst och SharePoint med Azure AD Application Proxy

Den här artikeln beskrivs hur du integrerar en lokal SharePoint-server med Azure Active Directory (Azure AD) Application Proxy.

Följ avsnitten i den här artikeln steg för steg för att aktivera fjärråtkomst och SharePoint med Azure AD Application Proxy.

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har SharePoint 2013 eller senare i din miljö. Dessutom Överväg följande krav:

* SharePoint innehåller inbyggt stöd för Kerberos. Användare som ansluter till interna webbplatser via fjärranslutning via Azure AD Application Proxy kan därför anta för att ha en enkel inloggning (SSO)-upplevelse.

* Det här scenariot innehåller konfigurationsändringar till SharePoint-servern. Vi rekommenderar att du använder en fristående miljö. På så sätt kan du kan göra uppdateringar testservern och underlätta en tester cykel innan du fortsätter till produktionen.

* Vi kräva SSL på publicerade URL: en. Du måste ha aktiverats på din interna plats för att säkerställa att länkar skickas/mappas korrekt SSL. Om du inte har konfigurerat SSL, se [Konfigurera SSL för SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) anvisningar. Kontrollera också att kopplingen datorn litar på certifikatet som du skickar. (Certifikaten behöver inte vara offentligt utfärdat.)

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>Steg 1: Konfigurera enkel inloggning till SharePoint

Lokalt program som använder Windows-autentisering, kan du uppnå enkel inloggning (SSO) med Kerberos-autentiseringsprotokollet och en funktion som kallas Kerberos-begränsad delegering (KCD). KCD när konfigurerad, kan Application Proxy connector få en Windows-token för en användare, även om användaren inte har loggat in till Windows direkt. Läs mer om KCD i [Kerberos-begränsad delegering översikt](https://technet.microsoft.com/library/jj553400.aspx).

Använd procedurerna i avsnitten nedan sekventiella om du vill konfigurera KCD för en SharePoint-server:

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Kontrollera att SharePoint körs under ett tjänstkonto

Kontrollera först att SharePoint körs under en definierad tjänstkonto--inte lokalt system, lokal tjänst eller Nätverkstjänst. Gör så att du kan koppla tjänstens huvudnamn (SPN) till ett giltigt konto. SPN-namn är hur Kerberos-protokollet identifierar olika tjänster. Och du behöver konto senare för att konfigurera KCD.

> [!NOTE]
Du måste ha en tidigare skapade Azure AD-kontot för tjänsten. Vi rekommenderar att du tillåter en automatisk ändring av lösenord. Mer information om alla steg och felsökningsproblem finns [konfigurera automatisk ändring av lösenord i SharePoint 2013](https://technet.microsoft.com/library/ff724280.aspx).

För att säkerställa att dina platser körs under ett definierade tjänstekonto, utför du följande steg:

1. Öppna den **Central Administration av SharePoint 2013** plats.
2. Gå till **säkerhet** och välj **Konfigurera tjänstkonton**.
3. Välj **Web programpoolen - SharePoint - 80**. Alternativen kan avvika något baserat på namnet på din webbserver-pool eller om SSL används som standard i web-poolen.

  ![Alternativ för att konfigurera ett tjänstkonto](./media/application-proxy-remote-sharepoint/service-web-application.png)

4. Om **Markera ett konto för den här komponenten** fältet är inställt på **lokal tjänst** eller **nätverkstjänsten**, måste du skapa ett konto. Om inte du är klar och kan flytta till nästa avsnitt.
5. Välj **registrera nytt hanterat konto**. När ditt konto har skapats måste du ange **webbprogrampoolen** innan du kan använda kontot.

### <a name="configure-sharepoint-for-kerberos"></a>Konfigurera SharePoint för Kerberos

Du kan använda KCD för att utföra enkel inloggning till SharePoint-servern.

Konfigurera SharePoint-webbplatsen för Kerberos-autentisering:

1. Öppna den **Central Administration av SharePoint 2013** plats.
2. Gå till **programhantering**väljer **hantera webbprogram**, och välj din SharePoint-webbplats. I det här exemplet är det **SharePoint - 80**.

  ![Välja den SharePoint-webbplatsen](./media/application-proxy-remote-sharepoint/manage-web-applications.png)

3. Klicka på **autentiseringsproviders** i verktygsfältet.
4. I den **autentiseringsproviders** klickar du på **standardzonen** vill visa inställningarna.
5. I den **Redigera autentisering** dialogrutan rutan, bläddra nedåt tills du ser **Anspråksautentiseringstyper**. Se till att både **aktivera Windows-autentisering** och **integrerad Windows-autentisering** har valts.
6. I den nedrullningsbara listrutan för fältet integrerad Windows-autentisering, se till att **förhandla (Kerberos)** är markerad.

  ![Dialogrutan Redigera autentisering](./media/application-proxy-remote-sharepoint/service-edit-authentication.png)

7. Längst ned i den **Redigera autentisering** dialogrutan klickar du på **spara**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Ange tjänstens huvudnamn för SharePoint-tjänstkontot

Innan du konfigurerar KCD som du behöver identifiera SharePoint-tjänster som körs som kontot som du har konfigurerat. Identifiera tjänsten genom att ange ett SPN-namn. Mer information finns i [Service Principal Names](https://technet.microsoft.com/library/cc961723.aspx).

SPN-formatet är:

```
<service class>/<host>:<port>
```

SPN-format:

* _tjänsten klassen_ är ett unikt namn för tjänsten. För SharePoint, använder du **HTTP**.

* _värden_ är det fullständigt kvalificerade domännamnet eller NetBIOS-namnet på den värd som tjänsten körs på. För en SharePoint-webbplats kan den här texten måste vara URL för webbplatsen, beroende på vilken version av IIS som du använder.

* _port_ är valfritt.

Om det fullständiga Domännamnet för SharePoint-servern är:

```
sharepoint.demo.o365identity.us
```

Sedan är SPN:

```
HTTP/sharepoint.demo.o365identity.us demo
```

Du kan också behöva ange SPN för specifika webbplatser på servern. Mer information finns i [konfigurera Kerberos-autentisering](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Noggrann till avsnittet ”Skapa tjänstens huvudnamn för ditt webbprogram med hjälp av Kerberos-autentisering”.

Det enklaste sättet att ange SPN-namn är att följa de SPN-format som kanske redan finns i dina platser. Kopiera dessa SPN-namn för att registrera mot tjänstkontot. Gör så här:

1. Bläddra till platsen med SPN från en annan dator.
 När du gör cachelagras den relevanta uppsättningen Kerberos-biljetter på datorn. Dessa biljetter innehåller SPN för målwebbplatsen som du har öppnat.

2. Du kan dra SPN för den platsen med hjälp av verktyget [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). Kör följande kommando i ett kommandofönster som körs i samma kontext som den användare som kan komma åt webbplatsen i webbläsaren:
```
Klist
```
Klist returnerar en uppsättning mål SPN-namn. I det här exemplet är det markerade värdet SPN som behövs:

  ![Exempel Klist resultat](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)

4. Nu när du har SPN, se till att den är rätt konfigurerat på det tjänstkonto som du angett för webbprogrammet tidigare. Kör följande kommando i Kommandotolken som administratör för domänen:

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 Det här kommandot anger SPN för SharePoint service-kontot som kör _demo\sp_svc_.

 Ersätt _http/sharepoint.demo.o365identity.us_ med SPN för servern och _demo\sp_svc_ med kontot i din miljö. Kommandot Setspn söker efter SPN innan den lägger till den. I det här fallet kan du se en **dubbla SPN-värdet** fel. Om du ser det här felet, se till att värdet är associerat med kontot.

Du kan verifiera att SPN har lagts till genom att köra kommandot Setspn -l-alternativet. Mer information om det här kommandot finns [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Kontrollera att anslutningen har angetts som en betrodd ombud till SharePoint

Konfigurera KCD så att tjänsten Azure AD Application Proxy kan delegera användaridentiteter till SharePoint-tjänsten. Konfigurera KCD genom att aktivera Application Proxy connector att hämta Kerberos-biljetter för dina användare som har verifierats i Azure AD. Servern skickar sedan kontexten till målprogrammet eller SharePoint i det här fallet.

Upprepa följande steg för att konfigurera KCD för varje koppling dator:

1. Logga in som domänadministratör på en Domänkontrollant och sedan öppna **Active Directory-användare och datorer**.
2. Hitta som kopplingen körs på datorn. I det här exemplet är det samma SharePoint-servern.
3. Dubbelklicka på datorn och klicka sedan på den **delegering** fliken.
4. Se till att inställningarna för standarddelegering är inställda **den här datorn betrodd för delegering endast till de angivna tjänsterna**. Markera **Använd valfritt autentiseringsprotokoll**.

  ![Inställningarna för standarddelegering](./media/application-proxy-remote-sharepoint/delegation-box.png)

5. Klicka på den **Lägg till** , klicka på **användare eller datorer**, och leta upp kontot.

  ![Lägga till SPN för kontot](./media/application-proxy-remote-sharepoint/users-computers.png)

6. Markera det som du skapade tidigare för kontot i listan över SPN-namn.
7. Klicka på **OK**. Klicka på **OK** igen för att spara ändringarna.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>Steg 2: Aktivera fjärråtkomst till SharePoint

Nu när du har aktiverat SharePoint för Kerberos och konfigurerade KCD, är du redo att publicera SharePoint-servergruppen för fjärråtkomst via Azure AD Application Proxy.

1. Publicera din SharePoint-webbplats med följande inställningar. Stegvisa instruktioner finns [publicering av program med hjälp av Azure AD Application Proxy](application-proxy-publish-azure-portal.md). 
   - **Intern URL**: URL: en för SharePoint-webbplatsen internt, såsom **https://SharePoint/**. Se till att använda i det här exemplet **https**
   - **Förautentiseringsmetod**: Azure Active Directory
   - **Översätta URL: en i sidhuvuden**: Nej

   >[!TIP]
   >SharePoint används den _värdadressen_ värde att söka efter platsen. Den genererar även länkar baserat på det här värdet. Net effekten är att alla länkar som SharePoint genererar en publicerade URL som är korrekt konfigurerad att använda en extern URL. Ange värdet till **Ja** kan också kopplingen för att vidarebefordra begäran till backend-programmet. Dock ange värdet till **nr** innebär att anslutningen inte ska skicka interna värdnamnet. I stället skickar kopplingen värdadressen som publicerade URL: en till backend-programmet.

   ![Publicera SharePoint som programmet](./media/application-proxy-remote-sharepoint/publish-app.png)

2. När appen har publicerats kan du konfigurera inställningar för enkel inloggning med följande steg:

   1. Välj på sidan program i portalen **enkel inloggning**.
   2. Enkel inloggning läge, Välj **integrerad Windows-autentisering**.
   3. Ange SPN för interna program till det värde som du tidigare har angetts. I det här exemplet som skulle vara **http/sharepoint.demo.o365identity.us**.

   ![Konfigurera integrerad Windows-autentisering för enkel inloggning](./media/application-proxy-remote-sharepoint/configure-iwa.png)

3. Om du vill slutföra konfigureringen av ditt program går du till den **användare och grupper** avsnittet och tilldela användare till programmet. 

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>Steg 3: Kontrollera att SharePoint känner till en extern URL

Det sista steget är att säkerställa att SharePoint kan hitta webbplatsen baserat på den externa URL: en så att den återger länkar baserat på den externa URL: en. Det gör du genom att konfigurera alternativa åtkomstmappningar för SharePoint-webbplatsen.

1. Öppna den **Central Administration av SharePoint 2013** plats.
2. Under **systeminställningar**väljer **konfigurera alternativa åtkomstmappningar**. Den alternativa åtkomstmappningar öppnas.

  ![Alternativa åtkomstmappningar rutan](./media/application-proxy-remote-sharepoint/alternate-access1.png)

3. I listrutan bredvid **alternativa åtkomst mappning samling**väljer **ändra alternativa åtkomst mappning samlingen**.
4. Välj plats – till exempel **SharePoint - 80**.
5. Du kan välja att lägga till den publicerade URL: en som en intern URL eller en offentlig URL. Det här exemplet använder en offentlig URL som extranätet.
6. Klicka på **redigera offentliga URL: er** i den **extranät** sökväg, och sedan ange den externa Webbadressen som skapades när du har publicerat programmet. Ange till exempel **https://sharepoint-iddemo.msappproxy.net**.

  ![Att ange sökvägen](./media/application-proxy-remote-sharepoint/alternate-access3.png)

7. Klicka på **Spara**.

Du kan nu komma åt SharePoint-webbplatsen externt via Azure AD Application Proxy.

## <a name="next-steps"></a>Nästa steg

- [Arbeta med anpassade domäner i Azure AD Application Proxy](active-directory-application-proxy-custom-domains.md)
- [Förstå Azure AD Application Proxy-kopplingar](application-proxy-understand-connectors.md)

