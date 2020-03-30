---
title: Aktivera fjärråtkomst till Power BI med Azure AD Application Proxy
description: Beskriver grunderna om hur du integrerar en lokal Power BI med Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111586"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Aktivera fjärråtkomst till Power BI Mobile med Azure AD-programproxy

I den hÃ¤r artikeln beskrivs hur du anã¤nder Azure AD Application Proxy fÃ¤r power BI-mobilappen fÃ¤r du ansluter till PBIRS (Power BI Report Server) och SQL Server Reporting Services (SSRS) 2016 och senare. Genom den här integreringen kan användare som inte är i företagsnätverket komma åt sina Power BI-rapporter från Power BI-mobilappen och skyddas av Azure AD-autentisering. Det här skyddet omfattar [säkerhetsfördelar](application-proxy-security.md#security-benefits) som villkorlig åtkomst och multifaktorautentisering.  

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har distribuerat Rapporttjänster och [aktiverat Programproxy](application-proxy-add-on-premises-application.md).

- Aktivera Programproxy kräver att du installerar en anslutningsapp på en Windows-server och slutför [förutsättningarna](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) så att anslutningen kan kommunicera med Azure AD-tjänster.  
- När du publicerar Power BI rekommenderar vi att du använder samma interna och externa domäner. Mer information om anpassade domäner finns [i Arbeta med anpassade domäner i Programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Den här integreringen är tillgänglig för **Power BI Mobile iOS- och Android-programmet.**

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Steg 1: Konfigurera Kerberos-begränsad delegering (KCD)

För lokala program som använder Windows-autentisering kan du uppnå enkel inloggning (SSO) med Kerberos-autentiseringsprotokollet och en funktion som kallas Kerberos-begränsad delegering (KCD). När KCD är konfigurerat kan application proxy-anslutningen hämta en Windows-token för en användare, även om användaren inte har loggat in i Windows direkt. Mer information om KCD finns i [Kerberos Constrained Delegation Overview](https://technet.microsoft.com/library/jj553400.aspx) och [Kerberos Constrained Delegation för enkel inloggning till dina appar med Programproxy](application-proxy-configure-single-sign-on-with-kcd.md).

Det krävs inte många konfigurationer av Reporting Services. Se bara till att ha ett giltigt spn-namn (Service Principal Name) för att aktivera korrekt Kerberos-autentisering. Kontrollera också att Reporting Services-servern är aktiverad för Förhandla-autentisering.

Om du vill konfigurera KCD för rapporteringstjänster fortsätter du med följande steg.

### <a name="configure-the-service-principal-name-spn"></a>Konfigurera tjänstens huvudnamn (SPN)

SPN-namnet är en unik identifierare för en tjänst som använder Kerberos-autentisering. Du måste se till att du har ett korrekt HTTP SPN-program för rapportservern. Information om hur du konfigurerar SPN (tjänstens huvudnamn) för rapportservern finns i [Registrera tjänstens huvudnamn (SPN) för en rapportserver](https://msdn.microsoft.com/library/cc281382.aspx).
Du kan kontrollera att SPN har lagts till genom att köra kommandot Setspn med alternativet -L. Mer information om det här kommandot finns i [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Aktivera Negotiate-autentisering

Om du vill att en rapportserver ska kunna använda Kerberos-autentisering konfigurerar du autentiseringstypen för rapportservern så att den är RSWindowsNegotiate. Konfigurera den här inställningen med filen rsreportserver.config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Mer information finns i [Ändra en Reporting Services-konfigurationsfil](https://msdn.microsoft.com/library/bb630448.aspx) och [konfigurera Windows-autentisering på en rapportserver](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Se till att anslutningsappen är betrodd för delegering till SPN som lagts till i application pool-kontot för Reporting Services
Konfigurera KCD så att Azure AD Application Proxy-tjänsten kan delegera användaridentiteter till Application Pool-kontot för Reporting Services. Konfigurera KCD genom att aktivera Application Proxy-anslutningen för att hämta Kerberos-biljetter för dina användare som har autentiserats i Azure AD. Sedan skickar den servern kontexten till målprogrammet eller Reporting Services i det här fallet.

Om du vill konfigurera KCD upprepar du följande steg för varje kopplingsmaskin:

1. Logga in på en domänkontrollant som domänadministratör och öppna sedan **Active Directory – användare och datorer**.
2. Leta reda på datorn som kontakten körs på.  
3. Dubbelklicka på datorn och välj sedan fliken **Delegering.**
4. Ange delegeringsinställningarna för **att lita på den här datorn för delegering till de angivna tjänsterna .** Välj sedan **Använd ett autentiseringsprotokoll**.
5. Välj **Lägg till**och välj sedan Användare eller **Datorer**.
6. Ange det tjänstkonto som du använder för Reporting Services. Detta är det konto där du har lagt till SPN-namnet i Reporting Services-konfigurationen.
7. Klicka på **OK**. Om du vill spara ändringarna klickar du på **OK** igen.

Mer information finns i [Kerberos Constrained Delegation för enkel inloggning till dina appar med Programproxy](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Steg 2: Publicera rapporttjänster via Azure AD Application Proxy

Nu är du redo att konfigurera Azure AD Application Proxy.

1. Publicera Rapporttjänster via programproxy med följande inställningar. Stegvisa instruktioner om hur du publicerar ett program via programproxy finns i [Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **Intern URL:** Ange URL:en till rapportservern som anslutningen kan nå i företagsnätverket. Kontrollera att den här URL:en kan nås från den server som anslutningen är installerad på. En bästa praxis är att använda `https://servername/` en toppdomän, till exempel för att undvika problem med undersökvägar som publiceras via Programproxy. Till exempel `https://servername/` använda `https://servername/reports/` och `https://servername/reportserver/`inte eller .
     > [!NOTE]
     > Vi rekommenderar att du använder en säker HTTPS-anslutning till rapportservern. Mer information om hur du gör finns i [Konfigurera SSL-anslutningar på en rapportserver i inbyggt läge.](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017)
   - **Extern URL:** Ange den offentliga webbadressen som Power BI-mobilappen ansluter till. Det kan till exempel `https://reports.contoso.com` se ut om en anpassad domän används. Om du vill använda en anpassad domän laddar du upp ett certifikat för domänen och pekar en DNS-post till standarddomänen msappproxy.net för ditt program. Detaljerade steg finns [i Arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

   - **Metod för förautentisering**: Azure Active Directory

2. När appen har publicerats konfigurerar du inställningarna för enkel inloggning med följande steg:

   a. På programsidan i portalen väljer du **Enkel inloggning**.

   b. För **enkel inloggningsläge**väljer du Integrerad **Windows-autentisering**.

   c. Ange **SPN för internt program** till det värde som du angav tidigare.  

   d. Välj den **delegerade inloggningsidentiteten** för kopplingen som ska användas för användarnas räkning. Mer information finns i [Arbeta med olika lokala och molnidentiteter](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Klicka på **Spara** om du vill spara ändringarna.

Om du vill slutföra inrättandet av programmet går du till avsnittet **Användare och grupper** och tilldelar användare att komma åt det här programmet.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Steg 3: Ändra svar URI: s för programmet

Innan Power BI-mobilappen kan ansluta och komma åt Rapporttjänster måste du konfigurera programregistreringen som skapades automatiskt åt dig i steg 2. 

1. Välj **Appregistreringar**på sidan Azure Active Directory **Översikt** .
2. Sök efter programmet som du skapade i steg 2 under fliken **Alla program.**
3. Markera programmet och välj sedan **Autentisering**.
4. Lägg till följande omdirigera URI:er baserat på vilken plattform du använder.

   När du konfigurerar appen för Power BI Mobile **iOS**lägger du till följande omdirigera URI:er av typen Offentlig klient (Mobil & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   När du konfigurerar appen för Power BI Mobile **Android**lägger du till följande omdirigera URI:er av typen Offentlig klient (Mobil &-skrivbord):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Omdirigerings-URI:erna måste läggas till för att programmet ska fungera korrekt. Om du konfigurerar appen för både Power BI Mobile iOS och Android lägger du till följande Omdirigera URI av typen Public Client `urn:ietf:wg:oauth:2.0:oob`(Mobile & Desktop) i listan över omdirigera URI:er som konfigurerats för iOS: .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Steg 4: Anslut från Power BI Mobile App

1. Anslut till din Reporting Services-instans i Power BI-mobilappen. Det gör du genom att ange den **externa URL:en** för det program som du publicerade via Programproxy.

   ![Power BI-mobilapp med extern URL](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Välj **Anslut**. Du dirigeras till inloggningssidan för Azure Active Directory.

3. Ange giltiga autentiseringsuppgifter för användaren och välj **Logga in**. Elementen från Reporting Services-servern visas.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Steg 5: Konfigurera Intune-principen för hanterade enheter (valfritt)

Du kan använda Microsoft Intune för att hantera de klientappar som företagets personal använder. Med Intune kan du använda funktioner som datakryptering och ytterligare åtkomstkrav. Mer information om apphantering via Intune finns i Intune App Management. Om du vill att Power BI-mobilappen ska fungera med Intune-principen följer du stegen nedan.

1. Gå till **Azure Active Directory** och sedan **Appregistreringar**.
2. Välj det program som konfigurerats i steg 3 när du registrerar ditt inbyggda klientprogram.
3. På programmets sida väljer du **API-behörigheter**.
4. Klicka på **Lägg till en behörighet**. 
5. Under **API:er som min organisation använder**söker du efter "Microsoft Mobile Application Management" och väljer det.
6. Lägg till **behörigheten DeviceManagementManagedApps.ReadWrite** i programmet
7. Klicka på **Bevilja administratörsgodkännande** om du vill bevilja behörighetsåtkomst till programmet.
8. Konfigurera den Intune-princip som du vill använda genom att referera till [Så här skapar och tilldelar du principer för appskydd](https://docs.microsoft.com/intune/app-protection-policies).

## <a name="troubleshooting"></a>Felsökning

Om programmet returnerar en felsida efter att ha försökt läsa in en rapport i mer än några minuter kan du behöva ändra timeout-inställningen. Som standard stöder Application Proxy program som tar upp till 85 sekunder att svara på en begäran. Om du vill förlänga den här inställningen till 180 sekunder väljer du timeout för backend till **Lång** på sidan Inställningar för App proxy för programmet. Tips om hur du skapar snabba och tillförlitliga rapporter finns i [Bästa praxis för Power BI-rapporter](https://docs.microsoft.com/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Nästa steg

- [Aktivera inbyggda klientprogram för att interagera med proxyprogram](application-proxy-configure-native-client-application.md)
- [Visa lokala rapportserverrapporter och KPI:er i Power BI-mobilapparna](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
