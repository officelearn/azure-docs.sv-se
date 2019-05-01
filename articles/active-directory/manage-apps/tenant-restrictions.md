---
title: Använd klientrestriktioner för att hantera åtkomst till SaaS-molnprogram – Azure | Microsoft Docs
description: Så här använder klientrestriktioner att hantera vilka användare kan komma åt appar baserat på deras Azure AD-klient.
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
ms.date: 03/28/2019
ms.author: celested
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa4eeb0a21525d636c7c1193c125d525774fa3fe
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707170"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Använd klientrestriktioner för att hantera åtkomst till SaaS-molnprogram

Stora organisationer som betona säkerhet vill flytta till molntjänster som Office 365, men behöver veta att användarna bara har åtkomst till resurser som är godkända. Traditionellt har begränsa företag domännamn eller IP-adresser när de vill hantera åtkomst. Den här metoden misslyckas i en värld där programvara som en tjänst (eller SaaS)-appar finns i ett offentligt moln, som körs på delade domännamn som [outlook.office.com](https://outlook.office.com/) och [login.microsoftonline.com](https://login.microsoftonline.com/). Blockera dessa adresser skulle hindra användare från att komma åt Outlook på webben helt och hållet, i stället för bara att begränsa dem till godkända identiteter och resurser.

Azure Active Directory (Azure AD)-lösning i den här utmaningen är en funktion som kallas klientrestriktioner. Organisationer kan styra åtkomsten till SaaS-molnprogram, utifrån programmen använder för enkel inloggning för Azure AD-klienten med klient-begränsningar. Du kanske vill tillåta åtkomst till din organisations Office 365-program, samtidigt som du förhindrar åtkomst till andra organisationer instanser av dessa samma program.  

Organisationer kan ange listan över klienter som användarna beviljas åtkomst till klient-begränsningar. Azure AD sedan ger endast åtkomst till dessa tillåtna klienter.

Den här artikeln fokuserar på klientrestriktioner för Office 365, men funktionen ska fungera med alla SaaS-molnappar som använder modern autentiseringsprotokoll med Azure AD för enkel inloggning. Om du använder SaaS-appar med en annan Azure AD-klient från klienten som används av Office 365 kan du se till att alla nödvändiga klienter tillåts. Mer information om SaaS-molnappar finns i den [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Hur det fungerar

Den övergripande lösningen består av följande komponenter:

1. **Azure AD**: Om den `Restrict-Access-To-Tenants: <permitted tenant list>` är närvarande, Azure AD endast problem säkerhetstoken för tillåtna klienter.

2. **En lokal proxy-serverinfrastruktur**: Den här infrastrukturen är en proxy-enheten kan Secure Sockets Layer (SSL)-kontroll. Du måste konfigurera proxyn för att infoga rubriken som innehåller listan över tillåtna klienter till trafik till Azure AD.

3. **Klientprogrammet**: För att stödja klientrestriktioner, måste klientprogrammet begära token direkt från Azure AD så att infrastrukturen som kan komma åt trafik. Webbläsarbaserade Office 365-program stöder för närvarande klientrestriktioner, som gör Office-klienter som använder modern autentisering (till exempel OAuth 2.0).

4. **Modern autentisering**: Molntjänster måste använda modern autentisering för att använda klientrestriktioner och blockera åtkomst till alla klienter som inte är tillåtet. Du måste konfigurera Office 365-molntjänster för att använda moderna autentiseringsprotokoll som standard. Den senaste informationen om Office 365-stöd för modern autentisering kan du läsa [uppdateras Office 365 modern autentisering](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Följande diagram illustrerar övergripande trafikflödet. Klientrestriktioner kräver SSL-kontroll bara på trafik till Azure AD, inte till Office 365-molntjänster. Skillnaden är viktig, eftersom trafikvolym för autentisering till Azure AD är vanligtvis mycket lägre än trafikvolym till SaaS-program som Exchange Online och SharePoint Online.

![Klient-begränsningar trafikflödet – diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Konfigurera klientrestriktioner

Det finns två steg för att komma igång med klientrestriktioner. Kontrollera först att dina klienter kan ansluta till rätt adresser. Dessutom konfigurera proxy-infrastruktur.

### <a name="urls-and-ip-addresses"></a>URL: er och IP-adresser

Om du vill använda klientrestriktioner måste klienterna att kunna ansluta till följande webbadresser som Azure AD för autentisering: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/), och [ login.Windows.NET](https://login.windows.net/). Dessutom kommer åt Office 365, dina klienter måste också vara kan ansluta till de fullständigt kvalificerade domännamn (FQDN), URL: er, och IP-adresser som definieras i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Proxykonfiguration och krav

Följande konfiguration krävs för att aktivera klientrestriktioner via proxy-infrastruktur. Den här vägledningen är allmänna, så du bör referera till dokumentationen från leverantören proxy för specifika genomföra åtgärder.

#### <a name="prerequisites"></a>Förutsättningar

- Proxyn måste kunna utföra SSL avlyssning, HTTP-huvud infogning och filtrera mål med FQDN: er/URL: er.

- Klienter måste lita på certifikatkedjan som presenteras av proxyn för SSL-kommunikation. Till exempel om certifikat från en intern [offentlig nyckelinfrastruktur (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) är används, måste det interna utfärdande certifikat certifikatet för rotutfärdare vara betrodda.

- Den här funktionen ingår i Office 365-prenumerationer, men om du vill använda klientrestriktioner för att styra åtkomst till andra SaaS-appar sedan Azure AD Premium 1 licenser som krävs.

#### <a name="configuration"></a>Konfiguration

Infoga två HTTP-huvuden för varje inkommande begäran till login.microsoftonline.com login.microsoft.com och login.windows.net: *Begränsa-åtkomst-till-klienter* och *begränsa åtkomst-sammanhangsberoende*.

Rubrikerna bör innehålla följande element:

- För *begränsa åtkomst till klienter*, Använd värdet \<tillåts klientlista\>, vilket är en kommaavgränsad lista över klienter som du vill ge användare åtkomst till. Alla domäner som har registrerats hos en klient kan användas för att identifiera klienten i den här listan. Till exempel för att tillåta åtkomst till både Contoso och Fabrikam klienter, namn/värde-par ser ut som: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- För *begränsa åtkomst sammanhangsberoende*, använda ett värde för en enskild katalog-ID, deklarera vilken klientorganisation kan du ställa klienten begränsningar. Till exempel för att deklarera Contoso som den klient som ange begränsningar för Klientprincipen namn/värde-par ser ut som: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Du kan hitta din katalog-ID i den [Azure Active Directory-portalen](https://aad.portal.azure.com/). Logga in som administratör, väljer **Azure Active Directory**och välj sedan **egenskaper**.

Om du vill hindra användare från att lägga till egna HTTP-huvud med icke-godkänd klienter, proxyn måste du ersätta den *begränsa åtkomst till klienter* rubriken om det finns redan i den inkommande begäranden.

Klienter måste vara tvungen att använda proxyn för alla begäranden till login.microsoftonline.com login.microsoft.com och login.windows.net. Till exempel om PAC-filer används för att dirigera klienterna till att använda proxy, bör inte slutanvändare kunna redigera eller inaktivera PAC-filer.

## <a name="the-user-experience"></a>Användarupplevelsen

Det här avsnittet beskrivs upplevelsen för både slutanvändare och administratörer.

### <a name="end-user-experience"></a>Slutanvändarens upplevelse

En exempelanvändare finns i Contoso-nätverket, men försöker komma åt ett delat SaaS-program som Outlook online Fabrikam-instansen. Om Fabrikam är en klient som inte är tillåtet för Contoso-instans, ser användaren ett åtkomst DOS-meddelande som säger att du försöker komma åt en resurs som tillhör en organisation som inte har godkänts av din IT-avdelning.

### <a name="admin-experience"></a>Administratörsupplevelse

Medan konfigurationen av klientrestriktioner är klar på företagets proxy-infrastrukturen kan administratörer har åtkomst till rapporter för klient-begränsningar i Azure portal direkt. Visa rapporter:

1. Logga in på den [Azure Active Directory-portalen](https://aad.portal.azure.com/). Den **Azure Active Directory Administrationscenter** visas instrumentpanelen.

2. Välj **Azure Active Directory** i den vänstra rutan. På översiktssidan för Azure Active Directory visas.

3. I den **andra funktioner** väljer **klient begränsningar**.

Administratör för klienten som har angetts som begränsad åtkomst sammanhangsberoende-klient kan använda den här rapporten för att se inloggningar som blockerats på grund av begränsningar Klientprincipen, inklusive den identitet som används och målkatalogen-ID. Inloggningar ingår om klientinställningen begränsningen är antingen den användare eller resurs-klient för att logga in.

Du kan använda filter som andra rapporter i Azure-portalen för att ange omfattningen för rapporten. Du kan filtrera efter ett visst tidsintervall, användare, program, klienter eller status. Om du väljer den **kolumner** knappen som du kan välja att visa data med valfri kombination av följande fält:

- **Användaren**
- **Programmet**
- **Status**
- **datum**
- **Date (UTC)** (where UTC is Coordinated Universal Time)
- **MFA Auth-metod** (Multi-Factor authentication method)
- **MFA Auth-information** (flerfunktionsautentisering detaljer)
- **MFA-resultat**
- **IP-adress**
- **Klienten**
- **Användarnamn**
- **Plats**
- **Målklients-ID**

## <a name="office-365-support"></a>Stöd för Office 365

Office 365-program måste uppfylla två kriterier för att ge fullt stöd klientrestriktioner:

1. Klienten som används har stöd för modern autentisering.
2. Modern autentisering är aktiverad som standardautentiseringsprotokoll för Molntjänsten.

Referera till [uppdateras Office 365 modern autentisering](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) den senaste informationen på vilka Office klienter stöder för närvarande modern autentisering. Sidan innehåller också länkar till anvisningar för att aktivera modern autentisering på specifika Exchange Online och Skype för Business Online klienter. SharePoint Online aktiverar redan Modern autentisering som standard.

Office 365-webbläsarbaserade program (SharePoint Office-portalen, Yammer, platser, Outlook på webben med mera) klientrestriktioner stöder för närvarande. Tjock klienter (Outlook, Skype för företag, Word, Excel, PowerPoint och mer) kan tillämpa klientrestriktioner endast när du använder modern autentisering.  

Outlook och Skype för företag-klienter som har stöd för modern autentisering kan fortfarande kunna använda äldre protokoll mot klienter där modern autentisering är inte aktiverad, vilket effektivt kringgår klientrestriktioner. Klientrestriktioner kan blockera program som använder äldre protokoll om de kontaktar login.microsoftonline.com, login.microsoft.com eller login.windows.net under autentiseringen.

Kunder kan välja att implementera begränsningar för att förhindra slutanvändare från att lägga till icke-godkänd e-postkonton i sina profiler för Outlook på Windows. Se exempelvis den [förhindra att lägga till konton för icke-standard Exchange](https://gpsearch.azurewebsites.net/default.aspx?ref=1) grupprincipinställning.

## <a name="testing"></a>Testning

Om du vill prova klientrestriktioner innan du implementerar för hela organisationen, har du två alternativ: en värdbaserad metod med ett verktyg som Fiddler eller en stegvis distribution av proxyinställningar.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler för en värdbaserad metod

Fiddler är en kostnadsfri webb-proxy som kan användas för att fånga och ändra HTTP/HTTPS-trafik, inklusive lägga till HTTP-huvuden för felsökning. Utför följande steg för att konfigurera Fiddler för att testa klientrestriktioner:

1. [Ladda ned och installera Fiddler](https://www.telerik.com/fiddler).

2. Konfigurera Fiddler för att dekryptera HTTPS-trafik [Fiddlers hjälpdokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Konfigurera Fiddler för att infoga den *begränsa åtkomst till klienter* och *begränsa åtkomst sammanhangsberoende* rubriker med hjälp av anpassade regler:

   1. I Fiddler Web felsökningsverktyget, väljer du den **regler** menyn och välj **anpassa regler...** att öppna filen CustomRules.

   2. Lägg till följande rader i början av den `OnBeforeRequest` funktion. Ersätt \<klientdomänen\> med en domän som har registrerats med din klient (till exempel `contoso.onmicrosoft.com`). Ersätt \<katalog-ID\> med din klients Azure AD-GUID-identifierare.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Om du vill tillåta flera klienter kan du använda ett kommatecken för att avgränsa klientnamnen. Exempel:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Spara och stäng filen CustomRules.

När du har konfigurerat Fiddler kan du fånga in trafik genom att gå till den **filen** menyn och välja **Capture Traffic**.

### <a name="staged-rollout-of-proxy-settings"></a>Stegvis distribution av proxyinställningar

Beroende på din proxy-infrastruktur kanske du kan mellanlagra för distribution av inställningar för att dina användare. Här följer några avancerade alternativ överväger:

1. Använd PAC-filer för att peka en testinfrastruktur av proxy testanvändare medan normal användare fortsätter att använda proxy produktionsinfrastruktur.
2. Vissa proxyservrar stöder olika konfigurationer med hjälp av grupper.

Specifik information finns i dokumentationen för proxy-server.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [uppdateras Office 365 modern autentisering](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Granska den [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
