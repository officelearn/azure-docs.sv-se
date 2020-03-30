---
title: Använda klientbegränsningar för att hantera åtkomst till SaaS-appar - Azure AD
description: Så här använder du klientbegränsningar för att hantera vilka användare som kan komma åt appar baserat på deras Azure AD-klientorganisation.
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
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecd49b340810f92727f0fc98f84031c8cbf68179
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481185"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Använda klientbegränsningar för att hantera åtkomst till SaaS-molnprogram

Stora organisationer som betonar säkerhet vill flytta till molntjänster som Office 365, men behöver veta att deras användare bara kan komma åt godkända resurser. Traditionellt begränsar företag domännamn eller IP-adresser när de vill hantera åtkomst. Den här metoden misslyckas i en värld där programvara som en tjänst (eller SaaS) appar finns i ett offentligt moln, som körs på delade domännamn som [outlook.office.com](https://outlook.office.com/) och [login.microsoftonline.com](https://login.microsoftonline.com/). Om du blockerar dessa adresser kan användarna inte komma åt Outlook på webben helt, i stället för att bara begränsa dem till godkända identiteter och resurser.

Azure Active Directory (Azure AD) lösning på den här utmaningen är en funktion som kallas klientbegränsningar. Med klientbegränsningar kan organisationer styra åtkomsten till SaaS-molnprogram, baserat på Azure AD-klienten som programmen använder för enkel inloggning. Du kanske till exempel vill tillåta åtkomst till organisationens Office 365-program, samtidigt som åtkomst till andra organisationers instanser av samma program förhindras.  

Med klientbegränsningar kan organisationer ange en lista över klienter som deras användare har rätt att komma åt. Azure AD ger sedan bara åtkomst till dessa tillåtna klienter.

Den här artikeln fokuserar på klientbegränsningar för Office 365, men funktionen bör fungera med alla SaaS-molnappar som använder moderna autentiseringsprotokoll med Azure AD för enkel inloggning. Om du använder SaaS-appar med en annan Azure AD-klientorganisation från klienten som används av Office 365 kontrollerar du att alla nödvändiga klienter är tillåtna. Mer information om SaaS-molnappar finns i [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Hur det fungerar

Den övergripande lösningen omfattar följande komponenter:

1. **Azure AD**: `Restrict-Access-To-Tenants: <permitted tenant list>` Om det finns, Azure AD utfärdar endast säkerhetstoken för tillåtna klienter.

2. **Lokal proxyserverinfrastruktur**: Den här infrastrukturen är en proxyenhet som kan inspektera Transport Layer Security (TLS). Du måste konfigurera proxyn för att infoga huvudet som innehåller listan över tillåtna klienter i trafik som är avsedd för Azure AD.

3. **Klientprogram:** För att stödja klientbegränsningar måste klientprogramvaran begära token direkt från Azure AD, så att proxyinfrastrukturen kan avlyssna trafik. Webbläsarbaserade Office 365-program stöder för närvarande klientbegränsningar, liksom Office-klienter som använder modern autentisering (som OAuth 2.0).

4. **Modern autentisering:** Molntjänster måste använda modern autentisering för att använda klientbegränsningar och blockera åtkomst till alla icke-tillåtna klienter. Du måste konfigurera Office 365-molntjänster för att kunna använda moderna autentiseringsprotokoll som standard. Den senaste informationen om Office 365-stöd för modern autentisering finns i Uppdaterad modern autentisering för [Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Följande diagram illustrerar trafikflödet på hög nivå. Klientbegränsningar kräver TLS-inspektion endast på trafik till Azure AD, inte till Office 365-molntjänsterna. Den här skillnaden är viktig eftersom trafikvolymen för autentisering till Azure AD vanligtvis är mycket lägre än trafikvolymen till SaaS-program som Exchange Online och SharePoint Online.

![Klientbegränsningar trafikflöde - diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Ställ in klientbegränsningar

Det finns två steg för att komma igång med klientbegränsningar. Kontrollera först att klienterna kan ansluta till rätt adresser. För det andra konfigurerar du proxyinfrastrukturen.

### <a name="urls-and-ip-addresses"></a>Webbadresser och IP-adresser

Om du vill använda klientbegränsningar måste klienterna kunna ansluta till följande Azure AD-URL:er för att autentisera: [login.microsoftonline.com,](https://login.microsoftonline.com/) [login.microsoft.com](https://login.microsoft.com/)och [login.windows.net](https://login.windows.net/). Om du vill komma åt Office 365 måste klienterna dessutom också kunna ansluta till de fullständigt kvalificerade domännamnen (FQDN), webbadresser och IP-adresser som definierats i [Office 365-URL:er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Proxykonfiguration och krav

Följande konfiguration krävs för att aktivera klientbegränsningar via proxyinfrastrukturen. Den här vägledningen är allmän, så du bör läsa proxyleverantörens dokumentation för specifika implementeringssteg.

#### <a name="prerequisites"></a>Krav

- Proxyn måste kunna utföra TLS-avlyssning, HTTP-huvudinfogning och filterdestinationer med FQDN/URL:er.

- Klienter måste lita på certifikatkedjan som visas av proxyn för TLS-kommunikation. Om certifikat från en intern [infrastruktur för offentliga nycklar (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) till exempel används, måste det interna utfärdande rotcertifikatutfärdarcertifikatet vara tillförlitligt.

- Den här funktionen ingår i Office 365-prenumerationer, men om du vill använda klientbegränsningar för att styra åtkomsten till andra SaaS-appar krävs Azure AD Premium 1-licenser.

#### <a name="configuration"></a>Konfiguration

För varje inkommande begäran login.microsoftonline.com, login.microsoft.com och login.windows.net infogar du två *HTTP-huvuden: Begränsa åtkomst till klienter* och *Begränsa-åtkomst-kontext*.

Rubrikerna ska innehålla följande element:

- För *Begränsa-åtkomst till klienter*använder du \<värdet\>för tillåten klientlista , som är en kommaavgränsad lista över klienter som du vill tillåta användare att komma åt. Alla domäner som är registrerade hos en klient kan användas för att identifiera klienten i den här listan. Om du till exempel vill tillåta åtkomst till både Contoso- och Fabrikam-klienter ser namn-/värdeparet ut: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- För *Begränsa-åtkomst-kontext*använder du ett värde för ett enda katalog-ID och deklarerar vilken klient som anger klientbegränsningarna. Om du till exempel vill deklarera Contoso som klienten som anger klientbegränsningsprincipen ser namn/värde-par ut som: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Du hittar ditt katalog-ID i [Azure Active Directory-portalen](https://aad.portal.azure.com/). Logga in som administratör, välj **Azure Active Directory**och välj sedan **Egenskaper**.

Om du vill förhindra att användare infogar sitt eget HTTP-huvud med klienter som inte är godkända måste proxyn ersätta huvudet *Begränsa åtkomst till klienter* om det redan finns i den inkommande begäran.

Klienter måste tvingas använda proxyn för alla begäranden till login.microsoftonline.com, login.microsoft.com och login.windows.net. Om PAC-filer till exempel används för att styra klienter att använda proxyn bör slutanvändare inte kunna redigera eller inaktivera PAC-filerna.

## <a name="the-user-experience"></a>Användarupplevelsen

I det här avsnittet beskrivs upplevelsen för både slutanvändare och administratörer.

### <a name="end-user-experience"></a>Upplevelse för slutanvändaren

En exempelanvändare finns i Contoso-nätverket, men försöker komma åt Fabrikam-instansen av ett delat SaaS-program som Outlook online. Om Fabrikam är en icke-tillåten klient för Contoso-instansen ser användaren ett meddelande om nekad åtkomst, där det står att du försöker komma åt en resurs som tillhör en organisation som inte godkänts av IT-avdelningen.

### <a name="admin-experience"></a>Administratörsupplevelse

Konfiguration av klientbegränsningar görs på företagets proxyinfrastruktur, men administratörer kan komma åt rapporterna för klientbegränsningar i Azure-portalen direkt. Så här visar du rapporterna:

1. Logga in på [Azure Active Directory-portalen](https://aad.portal.azure.com/). Instrumentpanelen i **Administrationscentret för Azure Active Directory** visas.

2. Välj **Azure Active Directory** i den vänstra rutan. Översiktssidan för Azure Active Directory visas.

3. I rubriken **Andra funktioner** väljer du **Klientbegränsningar**.

Administratören för klienten som anges som klienten med begränsad åtkomst-kontext kan använda den här rapporten för att se inloggningar blockerade på grund av klientbegränsningsprincipen, inklusive den identitet som används och målkatalog-ID. Inloggningar inkluderas om klientinställningen begränsningen är antingen användarens klientorganisation eller resursklient för inloggningen.

> [!NOTE]
> Rapporten kan innehålla begränsad information, till exempel målkatalog-ID, när en användare som är i en annan klient än klienten Med begränsad åtkomst-kontext loggar in. I det här fallet maskeras användaridentifierbar information, till exempel namn och användarnamn, för att skydda användardata i andra klienter.

Precis som andra rapporter i Azure-portalen kan du använda filter för att ange rapportens omfattning. Du kan filtrera på ett visst tidsintervall, användare, program, klient eller status. Om du väljer knappen **Kolumner** kan du välja att visa data med valfri kombination av följande fält:

- **Användare**
- **Program**
- **Status**
- **Datum**
- **Datum (UTC)** (där UTC är samordnad universell tid)
- **MFA Auth-metod** (multifaktorautentiseringsmetod)
- **MFA Auth-detalj** (multifaktorautentiseringsdetalj)
- **MFA-resultat**
- **IP-adress**
- **Klient**
- **Användarnamn**
- **Location**
- **Målklient-ID**

## <a name="office-365-support"></a>Office 365-stöd

Office 365-program måste uppfylla två kriterier för att fullt ut stödja klientbegränsningar:

1. Klienten som används stöder modern autentisering.
2. Modern autentisering är aktiverad som standardautentiseringsprotokoll för molntjänsten.

Se [Uppdaterad modern autentisering för Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) för den senaste informationen som Office-klienter för närvarande stöder modern autentisering om. Den sidan innehåller också länkar till instruktioner för hur du aktiverar modern autentisering på specifika Exchange Online- och Skype för företag – Online-klienter. SharePoint Online aktiverar redan modern autentisering som standard.

Webbläsarbaserade program baserade på Office 365 (Office Portal, Yammer, SharePoint-webbplatser, Outlook på webben med mera) stöder för närvarande klientbegränsningar. Tjocka klienter (Outlook, Skype för företag, Word, Excel, PowerPoint med mera) kan endast tillämpa klientbegränsningar när du använder modern autentisering.  

Outlook- och Skype för företag-klienter som stöder modern autentisering kan fortfarande använda äldre protokoll mot klienter där modern autentisering inte är aktiverad, vilket effektivt kringgår klientbegränsningar. Klientbegränsningar kan blockera program som använder äldre protokoll om de kontaktar login.microsoftonline.com, login.microsoft.com eller login.windows.net under autentiseringen.

För Outlook i Windows kan kunderna välja att implementera begränsningar som hindrar slutanvändare från att lägga till icke godkända e-postkonton i sina profiler. Se till exempel gruppprincipinställningen [Förhindra att icke-standardutröra Exchange-konton.](https://gpsearch.azurewebsites.net/default.aspx?ref=1)

## <a name="testing"></a>Testning

Om du vill prova klientbegränsningar innan du implementerar den för hela organisationen har du två alternativ: en värdbaserad metod med ett verktyg som Fiddler eller en stegvis distribution av proxyinställningar.

### <a name="fiddler-for-a-host-based-approach"></a>Spelman för en värdbaserad metod

Fiddler är en gratis webbfelsökning proxy som kan användas för att fånga och ändra HTTP / HTTPS-trafik, inklusive att infoga HTTP-huvuden. Så här konfigurerar du Fiddler för att testa klientbegränsningar:

1. [Ladda ner och installera Fiddler](https://www.telerik.com/fiddler).

2. Konfigurera Fiddler för att dekryptera HTTPS-trafik, enligt [Fiddlers hjälpdokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Konfigurera Fiddler för att infoga huvuden *Begränsa åtkomst till klienter* och begränsa *åtkomst-sammanhang* med hjälp av anpassade regler:

   1. I verktyget Felsökare för Fiddler-webb väljer du menyn **Regler** och väljer **Anpassa regler...** för att öppna filen CustomRules.

   2. Lägg till följande rader i `OnBeforeRequest` början av funktionen. Ersätt \<klientdomän\> med en domän som `contoso.onmicrosoft.com`är registrerad hos din klient (till exempel ). Ersätt \<katalog-ID\> med klientens Azure AD GUID-identifierare.

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

      Om du behöver tillåta flera klienter använder du ett kommatecken för att avgränsa klientnamnen. Ett exempel:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Spara och stäng CustomRules-filen.

När du har konfigurerat Fiddler kan du fånga upp trafik genom att gå till **Arkiv-menyn** och välja **Fånga trafik**.

### <a name="staged-rollout-of-proxy-settings"></a>Stegvis utrullning av proxyinställningar

Beroende på funktionerna i proxyinfrastrukturen kan du kanske arrangera utrullningen av inställningar för användarna. Här är ett par hög nivå alternativ för övervägande:

1. Använd PAC-filer för att peka ut testanvändare till en testproxyinfrastruktur, medan vanliga användare fortsätter att använda produktionsproxyinfrastrukturen.
2. Vissa proxyservrar kan ha stöd för olika konfigurationer med hjälp av grupper.

Mer information finns i dokumentationen till proxyservern.

## <a name="next-steps"></a>Nästa steg

- Läs om [uppdaterad modern autentisering i Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Granska [Office 365-URL:erna och IP-adressintervallen](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
