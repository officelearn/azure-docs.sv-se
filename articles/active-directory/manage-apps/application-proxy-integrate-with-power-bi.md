---
title: Få fjärråtkomst till Power BI med Azure AD-programproxy | Microsoft Docs
description: Beskriver grunderna för hur du integrerar en lokal Power BI med Azure AD-programproxy.
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
ms.openlocfilehash: 2148d6ea869a87571008c1f84c5b1000d4030bbb
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175944"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Få fjärråtkomst till Power BI Mobile med Azure AD-programproxy

Den här artikeln beskriver hur du använder Azure AD-programproxy för att aktivera Power BI mobilappen för att ansluta till Power BI-rapportserver (PBIRS) och SQL Server Reporting Services (SSRS) 2016 och senare. Med den här integrationen kan användare som är borta från företags nätverket komma åt sina Power BI rapporter från Power BI mobilapp och skyddas av Azure AD-autentisering. Det här skyddet omfattar [säkerhets förmåner](application-proxy-security.md#security-benefits) som villkorlig åtkomst och Multi-Factor Authentication.  

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har distribuerat rapport tjänster och [aktiverat Application Proxy](application-proxy-add-on-premises-application.md).

- Om du aktiverar programproxy måste du installera en anslutning på en Windows-Server och slutföra [kraven](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) så att anslutnings programmet kan kommunicera med Azure AD-tjänsterna.  
- När du publicerar Power BI rekommenderar vi att du använder samma interna och externa domäner. Mer information om anpassade domäner finns i [arbeta med anpassade domäner i Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Den här integreringen är tillgänglig för **Power BI Mobile iOS-och Android** -program.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Steg 1: Konfigurera Kerberos-begränsad delegering (KCD)

För lokala program som använder Windows-autentisering kan du använda enkel inloggning (SSO) med Kerberos-autentiseringsprotokollet och en funktion som kallas Kerberos-begränsad delegering (KCD). När KCD har kon figurer ATS kan du använda Application Proxy Connector för att hämta en Windows-token för en användare, även om användaren inte har loggat in i Windows direkt. Mer information om KCD finns i [Översikt över Kerberos-begränsad delegering](https://technet.microsoft.com/library/jj553400.aspx) och [Kerberos-begränsad delegering för enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md).

Det finns inte mycket att konfigurera på repor ting Services-sidan. Se bara till att ha ett giltigt SPN (Service Principal Name) för att möjliggöra korrekt Kerberos-autentisering. Kontrol lera också att repor ting Services-servern är aktive rad för Negotiate-autentisering.

Fortsätt med följande steg för att konfigurera KCD för repor ting Services.

### <a name="configure-the-service-principal-name-spn"></a>Konfigurera tjänstens huvud namn (SPN)

SPN är en unik identifierare för en tjänst som använder Kerberos-autentisering. Du måste kontrol lera att du har rätt HTTP SPN för rapport servern. Information om hur du konfigurerar rätt SPN (Service Principal Name) för rapport servern finns i [Registrera ett tjänst huvud namn (SPN) för en rapport Server](https://msdn.microsoft.com/library/cc281382.aspx).
Du kan kontrol lera att SPN har lagts till genom att köra Setspn-kommandot med alternativet-L. Mer information om det här kommandot finns i [setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Aktivera Negotiate-autentisering

Om du vill aktivera en rapport Server för att använda Kerberos-autentisering konfigurerar du autentiseringstypen för rapport servern så att den blir RSWindowsNegotiate. Konfigurera den här inställningen med hjälp av filen Rsreportserver. config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Mer information finns i [ändra en repor ting Services-konfigurationsfil](https://msdn.microsoft.com/library/bb630448.aspx) och [Konfigurera Windows-autentisering på en rapport Server](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Se till att anslutningen är betrodd för delegering till det SPN som lagts till i repor ting Services-programpoolens konto
Konfigurera KCD så att Azure AD-programproxy-tjänsten kan delegera användar identiteter till repor ting Services-programpoolens konto. Konfigurera KCD genom att aktivera Application Proxy Connector för att hämta Kerberos-biljetter för användare som har autentiserats i Azure AD. Sedan skickar servern kontexten till mål programmet eller repor ting Services i det här fallet.

Om du vill konfigurera KCD upprepar du följande steg för varje kopplings dator:

1. Logga in på en domänkontrollant som domän administratör och öppna sedan **Active Directory användare och datorer**.
2. Hitta den dator som anslutningen körs på.  
3. Dubbelklicka på datorn och välj sedan fliken **delegering** .
4. Ställ in Delegerings inställningarna på **den här datorn som betrodd för delegering till de angivna tjänsterna**. Välj sedan **Använd valfritt autentiseringsprotokoll**.
5. Välj **Lägg till**och välj sedan **användare eller datorer**.
6. Ange det tjänst konto som du använder för repor ting Services. Detta är det konto som du har lagt till SPN-namnet i repor ting Services-konfigurationen.
7. Klicka på **OK** Klicka på **OK** igen för att spara ändringarna.

Mer information finns i [Kerberos-begränsad delegering för enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Steg 2: publicera rapport tjänster via Azure AD-programproxy

Nu är du redo att konfigurera Azure AD-programproxy.

1. Publicera rapport tjänster via Application Proxy med följande inställningar. Stegvisa instruktioner om hur du publicerar ett program via programproxy finns i [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **Intern URL**: Ange URL: en till den rapport server som anslutningen kan uppnå i företags nätverket. Kontrol lera att den här URL: en kan kontaktas från den server som anslutningen är installerad på. Ett bra tips är att använda en toppnivå domän som `https://servername/` för att undvika problem med under Sök vägar (till exempel `https://servername/reports/` och `https://servername/reportserver/`) som inte har publicerats via programproxyn.
     > [!NOTE]
     > Vi rekommenderar att du använder en säker HTTPS-anslutning till rapport servern. Mer information finns i [Konfigurera SSL-anslutningar på en rapport server i enhetligt läge](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) .
   - **Extern URL**: Ange den offentliga url som Power BI mobilappen ska ansluta till. Det kan till exempel se ut som `https://reports.contoso.com` om en anpassad domän används. Om du vill använda en anpassad domän, laddar du upp ett certifikat för domänen och pekar en DNS-post till standard domänen msappproxy.net för ditt program. Detaljerade anvisningar finns [i arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md).

   - **Metod för förautentisering**: Azure Active Directory

2. När din app har publicerats konfigurerar du inställningarna för enkel inloggning med följande steg:

   a. På sidan program i portalen väljer du **enkel inloggning**.

   b. För **läge för enkel inloggning**väljer du **integrerad Windows-autentisering**.

   c. Ange det **interna programmets SPN** till det värde som du angav tidigare.  

   d. Välj den **delegerade inloggnings identitet** för anslutningen som du vill använda för användarens räkning. Mer information finns i [arbeta med olika lokala och molnbaserade identiteter](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Klicka på **Spara** för att spara ändringarna.

Slutför konfigurationen av programmet genom att gå till avsnittet **användare och grupper** och tilldela användare åtkomst till det här programmet.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Steg 3: ändra svars-URI: n för programmet

Innan Power BI mobilappen kan ansluta och komma åt rapport tjänster måste du konfigurera program registreringen som skapades automatiskt i steg 2. 

1. På sidan Azure Active Directory **Översikt** väljer du **Appregistreringar**.
2. Under fliken **alla program** söker du efter det program som du skapade i steg 2.
3. Välj programmet och välj sedan **autentisering**.
4. Lägg till följande omdirigerings-URI: er baserat på vilken plattform du använder.

   När du konfigurerar appen för Power BI Mobile **iOS**lägger du till följande omdirigerings-URI: er av typen offentlig klient (mobil & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   När du konfigurerar appen för Power BI Mobile **Android**lägger du till följande omdirigerings-URI: er av typen offentlig klient (mobil & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Omdirigerings-URI: erna måste läggas till för att programmet ska fungera korrekt. Om du konfigurerar appen för både Power BI Mobile iOS och Android lägger du till följande omdirigerings-URI av typen offentlig klient (mobil & Desktop) i listan över omdirigerings-URI: er som kon figurer ATS för iOS: `urn:ietf:wg:oauth:2.0:oob`.

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Steg 4: Anslut från Power BI Mobile-appen

1. I Power BI mobilapp ansluter du till repor ting Services-instansen. Det gör du genom att ange den **externa URL:** en för det program som du har publicerat via programproxyn.

   ![Power BI mobilapp med extern URL](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Välj **Anslut**. Du dirigeras till Azure Active Directory inloggnings sida.

3. Ange giltiga autentiseringsuppgifter för användaren och välj **Logga**in. Du ser elementen från din repor ting Services-server.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Steg 5: Konfigurera Intune-princip för hanterade enheter (valfritt)

> [!NOTE]
> Den här funktionen är för närvarande bara tillgänglig i iOS.

Du kan använda Microsoft Intune för att hantera de klient program som företagets personal använder. Med Intune kan du använda funktioner som data kryptering och ytterligare åtkomst krav. Mer information om hantering av appar via Intune finns i hantering av Intune-appar. Använd följande steg för att aktivera Power BI mobil program som fungerar med Intune-principen.

1. Gå till **Azure Active Directory** och sedan **app-registreringar**.
2. Välj det program som konfigurerades i steg 3 när du registrerar det interna klient programmet.
3. På programmets sida väljer du API- **behörigheter**.
4. Klicka på **Lägg till en behörighet**. 
5. Under **API: er som min organisation använder**söker du efter "Microsoft Mobile Application Management" och väljer det.
6. Lägg till **DeviceManagementManagedApps. readwrite** -behörighet för programmet
7. Klicka på **bevilja administrativt medgivande** för att ge behörighet till programmet.
8. Konfigurera den Intune-princip som du vill ha genom att referera till [hur du skapar och tilldelar skydds principer för appar](https://docs.microsoft.com/intune/app-protection-policies).

## <a name="troubleshooting"></a>Felsöka

Om programmet returnerar en felsida när du försöker läsa in en rapport i mer än några minuter kan du behöva ändra inställningen för timeout. Som standard stöder Application Proxy program som tar upp till 85 sekunder att svara på en begäran. Om du vill förlänga den här inställningen till 180 sekunder väljer du timeout för Server delen till **lång** på sidan App proxy-inställningar för programmet. Tips om hur du skapar snabba och tillförlitliga rapporter finns i [metod tips för Power BI-rapporter](https://docs.microsoft.com/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Nästa steg

- [Aktivera interna klient program för att interagera med proxy-program](application-proxy-configure-native-client-application.md)
- [Visa rapporter och KPI: er för lokala rapport servrar i Power BI mobila appar](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
