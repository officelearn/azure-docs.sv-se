---
title: Hantera åtkomst till molnappar genom att begränsa klienter – Azure | Microsoft Docs
description: Hur du använder klient begränsningar för att hantera vilka användare kan öppna programmen baserat på sina Azure AD-klient.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: yossib
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2018
ms.author: kgremban
ms.openlocfilehash: dae4599db5127ac8fd266d5e0f299e1284fc9b9c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Använd klient begränsningar för att hantera åtkomst till SaaS molnprogram

Stora organisationer som betona säkerhet vill flytta till molntjänster som Office 365, men behöver veta för att användarna bara kan komma åt resurser som är godkända. Företag begränsa traditionellt domännamn eller IP-adresser när de vill hantera åtkomst. Den här metoden misslyckas i en värld där SaaS-appar finns i ett offentligt moln som körs på delade domännamn som outlook.office.com och login.microsoftonline.com. Blockerar adresserna skulle förhindra att användare ansluter till Outlook på webben helt, i stället för bara begränsa dem till godkända identiteter och resurser.

Azure Active Directory-lösningen på denna utmaning är en funktion som kallas klient begränsningar. Klient begränsningar kan organisationer att kontrollera åtkomst till SaaS-molnprogram, baserat på Azure AD-klient som programmen använder för enkel inloggning. Du kanske vill tillåta åtkomst till din organisations Office 365-program, samtidigt som du förhindrar åtkomst till andra organisationer instanser av samma programmen.  

Klient begränsningar ger organisationer möjlighet att ange en lista över klienter som användarna beviljas åtkomst till. Azure AD sedan ger endast åtkomst till dessa tillåtna klienter.

Den här artikeln fokuserar på klient begränsningar för Office 365, men funktionen ska fungera med alla SaaS-molnappar som använder modern autentiseringsprotokoll med Azure AD för enkel inloggning. Om du använder SaaS-appar med en annan Azure AD-klient från klienten som används av Office 365, se till att alla nödvändiga klienter tillåts. Mer information om SaaS molnappar finns i [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Hur det fungerar

Virtualiseringslösningen består av följande komponenter: 

1. **Azure AD** – om den `Restrict-Access-To-Tenants: <permitted tenant list>` är finns, Azure AD endast problem säkerhetstoken för tillåtna klienter. 

2. **Lokal infrastruktur för server** – en proxy-enhet kan SSL-kontroll som konfigurerats för att infoga huvudet som innehåller listan över tillåtna hyresgäster i trafiken till Azure AD. 

3. **Klientprogrammet** – för att stödja klienten begränsningar klientprogrammet måste begära token direkt från Azure AD så att trafik kan avlyssnas av proxy-infrastruktur. Begränsningar för klienten stöds för närvarande genom webbläsarbaserad Office 365-program och Office-klienter när du använder modern autentisering (till exempel OAuth 2.0). 

4. **Modern autentisering** – molntjänster måste använda modern autentisering för att använda begränsningar för klient och blockera åtkomst till alla klienter som inte tillåts. Office 365 molntjänster måste konfigureras för att använda protokoll för modern autentisering som standard. Den senaste informationen på Office 365-stöd för modern autentisering kan läsa [uppdateras Office 365 modern autentisering](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/).

Följande diagram illustrerar övergripande trafikflöde. SSL-kontroll krävs bara på trafik till Azure AD, inte till Office 365-molntjänster. Skillnaden är viktig eftersom trafikvolym för autentisering till Azure AD är vanligtvis mycket lägre än trafikvolym för SaaS-program som Exchange Online och SharePoint Online.

![Klient begränsningar trafikflöde – diagram](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Konfigurera begränsningar för klient

Det finns två steg för att komma igång med klient-begränsningar. Det första steget är att se till att klienterna kan ansluta till rätt adresser. Andra är att konfigurera din infrastruktur.

### <a name="urls-and-ip-addresses"></a>URL: er och IP-adresser

Om du vill använda klient begränsningar klienterna måste vara kan ansluta till följande webbadresser med Azure AD autentisera: login.microsoftonline.com, login.microsoft.com och login.windows.net. Dessutom att få åtkomst till Office 365 klienterna måste också kunna ansluta till FQDN/URL-adresser och IP-adresser som definierats i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Proxykonfigurationen och krav

Följande konfiguration krävs för att aktivera begränsningar för klient via proxy-infrastruktur. Den här vägledningen är generisk, så du bör se proxy leverantörens dokumentation för specifika stegen.

#### <a name="prerequisites"></a>Förutsättningar

- Proxyservern måste kunna utföra SSL avlyssning, HTTP-huvudet infogning och filtrera mål med hjälp av FQDN/URL: er. 

- Klienter måste lita certifikatkedjan presenteras av proxyn för SSL-kommunikation. Till exempel om certifikat från en intern PKI används måste de interna utfärdande certifikat certifikatet för rotutfärdare vara betrodda.

- Den här funktionen ingår i Office 365-prenumerationer, men om du vill använda klient begränsningar för att styra åtkomst till andra SaaS-appar sedan Azure AD Premium 1 licenser krävs.

#### <a name="configuration"></a>Konfiguration

Infoga två HTTP-huvuden för varje inkommande begäran att login.microsoftonline.com och login.microsoft.com login.windows.net: *begränsa åtkomst till klienter* och *begränsa åtkomst kontext*.

Sidhuvuden bör innehålla följande element: 
- För *begränsa åtkomst till klienter*, värdet \<tillåts klienten listan\>, vilket är en kommaavgränsad lista över klienter som du vill ge användare åtkomst till. Alla domäner som har registrerats med en klient kan användas för att identifiera klienten i den här listan. Till exempel om du vill tillåta åtkomst till både Contoso och Fabrikam hyresgäster namn/värde-par ser ut som:  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- För *begränsa åtkomst kontext*, ett värde av en enskild katalog-ID, deklarerar vilken är att lägga till begränsningar för innehavare. Till exempel för att deklarera Contoso som klienten som anger klient begränsningar principen namn/värde-par ser ut som: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Du kan hitta din katalog-ID i den [Azure-portalen](https://portal.azure.com). Logga in som administratör, Välj **Azure Active Directory**och välj **egenskaper**.

Om du vill hindra användare från att lägga till egna HTTP-huvud med icke-godkända klienter, behöver proxyn ersätta rubriken Begränsa åtkomst till klienter om det finns redan i den inkommande begäranden. 

Klienter måste tvingas att använda proxy för alla förfrågningar till login.microsoftonline.com och login.microsoft.com login.windows.net. Till exempel om PAC-filer används för att dirigera klienterna att använda proxy, får slutanvändare inte vara att redigera eller inaktivera PAC-filer.

## <a name="the-user-experience"></a>Användarens upplevelse

Detta avsnitt visar upplevelsen för både användare och administratörer.

### <a name="end-user-experience"></a>Slutanvändarens upplevelse

Ett exempel på användare i Contoso-nätverket, men försöker komma åt ett delat SaaS-program som Outlook online Fabrikam-instansen. Om Contoso är en klient som inte tillåts för instansen, ser användaren följande sida:

![Åtkomst nekades för användare i ej tillåten klienter](./media/active-directory-tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>Administratörsupplevelse

När konfigurationen av klient begränsningar görs på företagets proxy-infrastruktur, Administratörer kan komma åt rapporterna klient begränsningar i Azure portal direkt. Gå till sidan översikt av Azure Active Directory för att visa rapporter, och sedan tittar du under andra funktioner.

Administratör för klienten som angetts som begränsad åtkomst kontext klienten kan använda den här rapporten för att se alla inloggningar blockerats på grund av begränsningar för klient-princip, inklusive den identitet som används och målkatalogen-ID.

![Använda Azure portal för att visa begränsade inloggningsförsök](./media/active-directory-tenant-restrictions/portal-report.png)

Du kan använda filter som andra rapporter i Azure-portalen för att ange omfånget för rapporten. Du kan filtrera efter en viss användare, program, klienter eller tidsintervall.

## <a name="office-365-support"></a>Stöd för Office 365

Office 365-program måste uppfylla två kriterier för att fullständigt stöd för klient begränsningar:

1. Klienten använde stöder modern autentisering
2. Modern autentisering är aktiverat som standardautentiseringsprotokoll för Molntjänsten.

Referera till [uppdateras Office 365 modern autentisering](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) för den senaste informationen om vilka Office klienter för närvarande stöder modern autentisering. Sidan innehåller också länkar till instruktioner för att aktivera modern autentisering på specifika Exchange Online och Skype för företag – Online-innehavare. Modern autentisering är redan aktiverat som standard i SharePoint Online.

Klient-begränsningar för närvarande stöds av Office 365-webbläsarbaserade program (Office-portalen Yammer, SharePoint-platser, Outlook Web osv.). För tjock klienter (Outlook, Skype för företag, Word, Excel, PowerPoint, etc.) Klient begränsningar kan bara tillämpas när modern autentisering används.  

Outlook och Skype för företag-klienter som stöder modern autentisering kan fortfarande kan använda äldre protokoll mot klienter där modern autentisering är inte aktiverat effektivt kringgå klient begränsningar. Program som använder äldre protokoll blockeras av klient begränsningar om de kontaktar login.microsoftonline.com, login.microsoft.com eller login.windows.net under autentiseringen.

För Outlook på Windows kan kunderna välja att implementera begränsningar som hindrar användare från att lägga till icke-godkända e-postkonto till deras profiler. Se exempelvis den [förhindra att lägga till konton som inte är standard Exchange](http://gpsearch.azurewebsites.net/default.aspx?ref=1) grupprincipinställning. Fullständigt stöd för klienten är inte tillgängliga för Outlook på Windows-plattformar och för Skype för företag på alla plattformar.

## <a name="testing"></a>Testning

Om du vill testa klient begränsningar innan du implementerar för hela organisationen, det finns två alternativ: en värdbaserad metod med ett verktyg som Fiddler eller en stegvis distribution av proxy-inställningar.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler för en värdbaserad metod

Fiddler är en gratis webbplatser felsökning proxy som kan användas för att samla in och modifiera HTTP/HTTPS-trafik, inklusive lägga till HTTP-huvuden. Utför följande steg för att konfigurera Fiddler för att testa klienten begränsningar:

1.  [Hämta och installera Fiddler](http://www.telerik.com/fiddler).
2.  Konfigurera Fiddler för att dekryptera HTTPS-trafik [Fiddlers hjälpdokumentationen](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Konfigurera Fiddler att infoga den *begränsa åtkomst till klienter* och *begränsa åtkomst kontext* huvuden med hjälp av anpassade regler:
  1. I verktyget Fiddler Web Debugger väljer du den **regler** -menyn och välj **anpassa regler...** att öppna filen CustomRules.
  2. Lägg till följande rader i början av den *OnBeforeRequest* funktion. Ersätt \<klientdomänen\> för en domän registrerats med din klient, till exempel contoso.onmicrosoft.com. Ersätt \<katalog-ID\> med din klients Azure AD-GUID-identifierare.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

  Om du vill tillåta flera klienter kan du använda ett kommatecken för att separera namn för innehavaren. Exempel:

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. Spara och stäng filen CustomRules.

När du har konfigurerat Fiddler kan du fånga in trafik genom att gå till den **filen** menyn och välja **fånga in trafik**.

### <a name="staged-rollout-of-proxy-settings"></a>Stegvis distribution av proxy-inställningar

Beroende på din infrastruktur, kan du kunna mellanlagra driftsättningen av inställningar till användarna. Här följer några avancerade alternativ hänsyn till:

1.  Använd PAC filer för att peka en proxy infrastruktur test testanvändare medan normal användare fortsätter att använda proxy produktionsinfrastruktur.
2.  Vissa proxyservrar kanske stöder olika konfigurationer med hjälp av grupper.

Referera till proxy server-dokumentationen för mer information.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [uppdateras Office 365 modern autentisering](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)

- Granska de [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
