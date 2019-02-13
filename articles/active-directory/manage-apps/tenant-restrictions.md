---
title: Hantera åtkomst till molnappar genom att begränsa klienter – Azure | Microsoft Docs
description: Hur du använder Klientrestriktioner för att hantera vilka användare kan komma åt appar baserat på deras Azure AD-klient.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: celested
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2dc03b329ce8a4b42f44b958aee96654dafb098
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56197750"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Använd Klientrestriktioner för att hantera åtkomst till SaaS-molnprogram

Stora organisationer som betona säkerhet vill flytta till molntjänster som Office 365, men behöver veta att användarna bara har åtkomst till resurser som är godkända. Traditionellt har begränsa företag domännamn eller IP-adresser när de vill hantera åtkomst. Den här metoden misslyckas i en värld där SaaS-appar finns i ett offentligt moln, som körs på delade domännamn som outlook.office.com och login.microsoftonline.com. Blockera dessa adresser skulle hindra användare från att komma åt Outlook på webben helt och hållet, i stället för bara att begränsa dem till godkända identiteter och resurser.

Azure Active Directorys lösning i den här utmaningen är en funktion som kallas Klientrestriktioner. Klientrestriktioner gör det möjligt för organisationer att styra åtkomsten till SaaS-molnprogram, utifrån programmen använder för enkel inloggning för Azure AD-klienten. Du kanske vill tillåta åtkomst till din organisations Office 365-program, samtidigt som du förhindrar åtkomst till andra organisationer instanser av dessa samma program.  

Klientrestriktioner ger organisationer möjlighet att ange en lista över klienter som användarna beviljas åtkomst till. Azure AD sedan ger endast åtkomst till dessa tillåtna klienter.

Den här artikeln fokuserar på Klientrestriktioner för Office 365, men funktionen ska fungera med alla SaaS-molnappar som använder modern autentiseringsprotokoll med Azure AD för enkel inloggning. Om du använder SaaS-appar med en annan Azure AD-klient från klienten som används av Office 365 kan du se till att alla nödvändiga klienter tillåts. Mer information om SaaS-molnappar finns i den [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Hur det fungerar

Den övergripande lösningen består av följande komponenter: 

1. **Azure AD** – om den `Restrict-Access-To-Tenants: <permitted tenant list>` är närvarande, Azure AD endast problem säkerhetstoken för tillåtna klienter. 

2. **En lokal proxy-serverinfrastruktur** – en proxy-enheten kan SSL-kontroll som har konfigurerats för att infoga rubriken som innehåller listan över tillåtna klienter till trafik till Azure AD. 

3. **Klientprogrammet** – för att stödja Klientrestriktioner, klientprogrammet måste begära token direkt från Azure AD så att trafik kan fångas upp av proxy-infrastruktur. Klientrestriktioner stöds för närvarande genom webbläsarbaserade Office 365-program och Office-klienter när modern autentisering (till exempel OAuth 2.0) används. 

4. **Modern autentisering** – molntjänster måste använda modern autentisering för att använda Klientrestriktioner och blockera åtkomst till alla klienter som inte är tillåtet. Office 365 molntjänster måste konfigureras för att använda moderna autentiseringsprotokoll som standard. Den senaste informationen om Office 365-stöd för modern autentisering kan du läsa [uppdateras Office 365 modern autentisering](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/).

Följande diagram illustrerar övergripande trafikflödet. SSL-kontroll krävs endast för trafik till Azure AD, inte till Office 365-molntjänster. Skillnaden är viktig eftersom trafikvolym för autentisering till Azure AD är vanligtvis mycket lägre än trafikvolym till SaaS-program som Exchange Online och SharePoint Online.

![Klient-begränsningar trafikflödet – diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Konfigurera Klientrestriktioner

Det finns två steg för att komma igång med Klientrestriktioner. Det första steget är att se till att dina klienter kan ansluta till rätt adresser. Andra är att konfigurera proxy-infrastruktur.

### <a name="urls-and-ip-addresses"></a>URL: er och IP-adresser

Om du vill använda Klientrestriktioner måste klienterna att kunna ansluta till följande webbadresser som Azure AD för autentisering: login.microsoftonline.com, login.microsoft.com och login.windows.net. Dessutom kommer åt Office 365, dina klienter måste också vara kan ansluta till FQDN: er/URL: er och IP-adresser som definieras i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Proxykonfiguration och krav

Följande konfiguration krävs för att aktivera Klientrestriktioner via proxy-infrastruktur. Den här vägledningen är allmänna, så du bör referera till dokumentationen från leverantören proxy för specifika genomföra åtgärder.

#### <a name="prerequisites"></a>Förutsättningar

- Proxyn måste kunna utföra SSL avlyssning, HTTP-huvud infogning och filtrera mål med FQDN: er/URL: er. 

- Klienter måste lita på certifikatkedjan som presenteras av proxyn för SSL-kommunikation. Till exempel om certifikat från en intern PKI används måste det interna utfärdande certifikat certifikatet för rotutfärdare vara betrodda.

- Den här funktionen ingår i Office 365-prenumerationer, men om du vill använda Klientrestriktioner för att styra åtkomst till andra SaaS-appar sedan Azure AD Premium 1 licenser som krävs.

#### <a name="configuration"></a>Konfiguration

Infoga två HTTP-huvuden för varje inkommande begäran till login.microsoftonline.com login.microsoft.com och login.windows.net: *Begränsa-åtkomst-till-klienter* och *begränsa åtkomst-sammanhangsberoende*.

Rubrikerna bör innehålla följande element: 
- För *begränsa åtkomst till klienter*, värdet \<tillåts klientlista\>, vilket är en kommaavgränsad lista över klienter som du vill ge användare åtkomst till. Alla domäner som har registrerats hos en klient kan användas för att identifiera klienten i den här listan. Till exempel för att tillåta åtkomst till både Contoso och Fabrikam klienter, namn/värde-par ser ut som:  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- För *begränsa åtkomst sammanhangsberoende*, ett värde av en enskild katalog-ID, deklarera vilken klientorganisation kan du ställa den Klientrestriktioner. Till exempel för att deklarera Contoso som den klient som anger principen Klientrestriktioner namn/värde-par ser ut som: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Du kan hitta din katalog-ID i den [Azure-portalen](https://portal.azure.com). Logga in som administratör, väljer **Azure Active Directory**och välj sedan **egenskaper**.

Om du vill hindra användare från att lägga till egna HTTP-huvud med icke-godkänd klienter, måste proxyn ersätta rubriken Begränsa åtkomst till klienter om det finns redan i den inkommande begäranden. 

Klienter måste vara tvungen att använda proxyn för alla begäranden till login.microsoftonline.com login.microsoft.com och login.windows.net. Till exempel om PAC-filer används för att dirigera klienterna till att använda proxy, bör slutanvändare inte kunna redigera eller inaktivera PAC-filer.

## <a name="the-user-experience"></a>Användarupplevelsen

Det här avsnittet visar upplevelsen för både slutanvändare och administratörer.

### <a name="end-user-experience"></a>Slutanvändarens upplevelse

En exempelanvändare finns i Contoso-nätverket, men försöker komma åt ett delat SaaS-program som Outlook online Fabrikam-instansen. Om Fabrikam är en klient som inte är tillåtet för Contoso-instans, ser användaren följande sida:

![Åtkomst nekades för användare i icke-tillåtna klienter](./media/tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>Administratörsupplevelse

När konfigurationen av Klientrestriktioner är klar på företagets proxy-infrastrukturen kan administratörer kan komma åt Klientrestriktioner rapporterna i Azure portal direkt. Om du vill visa rapporter, gå till sidan översikt av Azure Active Directory och tittar du under andra funktioner.

Administratör för klienten som har angetts som begränsad åtkomst sammanhangsberoende-klient kan använda den här rapporten för att se inloggningar som blockerats på grund av Klientrestriktioner principen, inklusive den identitet som används och målkatalogen-ID. Inloggningar ingår om klientinställningen begränsningen är antingen den användare eller resurs-klient för att logga in.

![Använda Azure-portalen för att visa begränsade inloggningsförsök](./media/tenant-restrictions/portal-report.png)

Du kan använda filter som andra rapporter i Azure-portalen för att ange omfattningen för rapporten. Du kan filtrera efter en viss användare, program, klienter eller tidsintervall.

## <a name="office-365-support"></a>Stöd för Office 365

Office 365-program måste uppfylla två kriterier för att ge fullt stöd Klientrestriktioner:

1. Klienten som används har stöd för modern autentisering
2. Modern autentisering är aktiverad som standardautentiseringsprotokoll för Molntjänsten.

Referera till [uppdateras Office 365 modern autentisering](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) den senaste informationen på vilka Office klienter stöder för närvarande modern autentisering. Sidan innehåller också länkar till anvisningar för att aktivera modern autentisering på specifika Exchange Online och Skype för Business Online klienter. Modern autentisering är redan aktiverat som standard i SharePoint Online.

Klientrestriktioner stöds för närvarande av Office 365-webbläsarbaserade program (SharePoint Office-portalen, Yammer, webbplatser, Outlook på webben, osv.). För tjock klienter (Outlook, Skype för företag, Word, Excel, PowerPoint, osv.) Klientrestriktioner kan endast tillämpas när modern autentisering används.  

Outlook och Skype för företag-klienter som har stöd för modern autentisering kan fortfarande kunna använda äldre protokoll mot klienter där modern autentisering inte är aktiverad, vilket effektivt kringgår Klientrestriktioner. Program som använder äldre protokoll blockeras av Klientrestriktioner om de kontaktar login.microsoftonline.com, login.microsoft.com eller login.windows.net under autentiseringen.

Kunder kan välja att implementera begränsningar för att förhindra slutanvändare från att lägga till icke-godkänd e-postkonton i sina profiler för Outlook på Windows. Se exempelvis den [förhindra att lägga till konton för icke-standard Exchange](https://gpsearch.azurewebsites.net/default.aspx?ref=1) grupprincipinställning.

## <a name="testing"></a>Testning

Om du vill prova Klientrestriktioner innan du implementerar för hela organisationen, det finns två alternativ: en värdbaserad metod med ett verktyg som Fiddler eller en stegvis distribution av proxyinställningar.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler för en värdbaserad metod

Fiddler är en kostnadsfri webb-proxy som kan användas för att fånga och ändra HTTP/HTTPS-trafik, inklusive lägga till HTTP-huvuden för felsökning. Utför följande steg för att konfigurera Fiddler för att testa Klientrestriktioner:

1.  [Ladda ned och installera Fiddler](https://www.telerik.com/fiddler).
2.  Konfigurera Fiddler för att dekryptera HTTPS-trafik [Fiddlers hjälpdokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Konfigurera Fiddler för att infoga den *begränsa åtkomst till klienter* och *begränsa åtkomst sammanhangsberoende* rubriker med hjälp av anpassade regler:
    1. I Fiddler Web felsökningsverktyget, väljer du den **regler** menyn och välj **anpassa regler...** att öppna filen CustomRules.
    2. Lägg till följande rader i början av den *OnBeforeRequest* funktion. Ersätt \<klientdomänen\> med en domän registrerad med din klient, till exempel contoso.onmicrosoft.com. Ersätt \<katalog-ID\> med din klients Azure AD-GUID-identifierare.

    ```
    if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
    ```

    Om du vill tillåta flera klienter kan du använda ett kommatecken för att avgränsa klientnamnen. Exempel:

    ```
    oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
    ```

4. Spara och stäng filen CustomRules.

När du har konfigurerat Fiddler kan du fånga in trafik genom att gå till den **filen** menyn och välja **Capture Traffic**.

### <a name="staged-rollout-of-proxy-settings"></a>Stegvis distribution av proxyinställningar

Beroende på din proxy-infrastruktur kanske du kan mellanlagra för distribution av inställningar för att dina användare. Här följer några avancerade alternativ överväger:

1.  Använd PAC-filer för att peka en testinfrastruktur av proxy testanvändare medan normal användare fortsätter att använda proxy produktionsinfrastruktur.
2.  Vissa proxyservrar stöder olika konfigurationer med hjälp av grupper.

I proxy server-dokumentationen för specifik information.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [uppdateras Office 365 modern autentisering](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)

- Granska den [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
