---
title: Felsöka Application Proxy | Microsoft Docs
description: Beskriver hur du felsöker fel i Azure AD-programproxy.
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
ms.date: 06/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017; it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7be9a17bed2a39d16f813332c2d6effc03393264
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244230"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Felsöka problem med Application Proxy och felmeddelanden

När du felsöker problem med programproxyn rekommenderar vi att du börjar med att granska fel söknings flödet, [Felsöka anslutnings problem med Application Proxy](application-proxy-debug-connectors.md)för att avgöra om Application Proxy-kopplingar är korrekt konfigurerade. Om du fortfarande har problem med att ansluta till programmet följer du fel söknings flödet i fel söknings program för [programproxy](application-proxy-debug-apps.md).

Om fel uppstår i komma åt ett publicerat program eller publicera program kontrollerar du följande alternativ för att se om Microsoft Azure AD-programproxyn fungerar korrekt:

* Öppna konsolen Windows-tjänster. Kontrol lera att **Connector-tjänsten Microsoft AAD Application Proxy** är aktive rad och körs. Du kan också vill titta på egenskapssidan för Application Proxy-tjänsten enligt följande bild:  
  ![Microsoft AAD Application Proxy Connector Fönstret Egenskaper skärm bild](./media/application-proxy-troubleshoot/connectorproperties.png)
* Öppna Loggboken och leta efter Application Proxy Connector-händelser **i program-och tjänst loggar** > **Microsoft** > **AadApplicationProxy** > **Connector** > **admin**.
* Om det behövs kan du använda mer detaljerade loggar genom [att aktivera sessionerna för Application Proxy Connector](application-proxy-connectors.md#under-the-hood).

## <a name="the-page-is-not-rendered-correctly"></a>Sidan renderas inte korrekt
Du kan ha problem med programmet återgivning eller felaktigt fungerar utan att du får felmeddelanden. Detta kan inträffa om du har publicerat den artikel sökvägen, men programmet kräver innehåll som finns utanför den sökvägen.

Om du till exempel publicerar sökvägen `https://yourapp/app` men programmet anropar bilder i `https://yourapp/media`, återges de inte. Se till att du publicerar program med den högsta nivån sökvägen måste du inkludera alla relevant innehåll. I det här exemplet är det `http://yourapp/`.

Om du ändrar sökvägen så att den innehåller innehåll som refereras till, men fortfarande behöver användare på en djupare länk i sökvägen, kan du läsa blogg inlägget [Ange rätt länk för program proxy-program i Azure AD Access-panelen och Office 365 App Launcher](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Felmeddelanden

Om registreringen misslyckas under guiden kopplingsinstallationen, finns det två sätt att se orsaken till felet. Titta i händelse loggen under **program-och tjänst Logs\Microsoft\AadApplicationProxy\Connector\Admin**eller kör följande kommando i Windows PowerShell:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

När du har hittat Connector-fel från händelseloggen, kan du använda den här tabellen över vanliga fel för att lösa problemet:

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Registreringen av anslutningsverktyget misslyckades: Kontrollera att du har aktiverat Application Proxy i Azure-hanteringsportalen och att du angett ditt Active Directory-användarnamn och lösenord korrekt. Fel: ”ett eller flera fel uppstod”. | Om du har stängt fönstret registreringen utan att logga in till Azure AD, kör guiden koppling igen och registrera anslutningsverktyget. <br><br> Om registrerings fönstret öppnas och stängs omedelbart utan att du behöver logga in får du förmodligen det här felet. Det här felet uppstår när det finns ett nätverksfel på datorn. Kontrol lera att det är möjligt att ansluta från en webbläsare till en offentlig webbplats och att portarna är öppna enligt vad som anges i [Programproxyns krav](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |
| Rensa fel visas i fönstret registrering. Det går inte att fortsätta | Om du ser detta fel och sedan fönstret stängs, du har angett felaktigt användarnamn eller lösenord. Försök igen. |
| Registreringen av anslutningsverktyget misslyckades: Kontrollera att du har aktiverat Application Proxy i Azure-hanteringsportalen och att du angett ditt Active Directory-användarnamn och lösenord korrekt. Fel: ' AADSTS50059: ingen information om identifiering av klient finns i antingen begäran eller underförstådda på valfri angivna autentiseringsuppgifter och Sök efter service principal URI har misslyckats. | Du försöker logga in med ett Microsoft-konto och inte en domän som är en del av organisations-ID: t för den katalog som du försöker få åtkomst till. Se till att administratören är en del av samma domän namn som klient domänen, till exempel om Azure AD-domänen är contoso.com ska administratören vara admin@contoso.com. |
| Det gick inte att hämta aktuella körningsprincipen för att köra PowerShell-skript. | Om installationen av anslutningen Miss lyckas kontrollerar du att PowerShell-körnings principen inte är inaktive rad. <br><br>1. öppna grupprincip redigeraren.<br>2. gå till **dator konfiguration** > **administrativa mallar** > **Windows-komponenter** > **Windows PowerShell** och dubbelklicka på **Aktivera skript körning**.<br>3. körnings principen kan anges till antingen **inte konfigurerad** eller **aktive rad**. Om inställningen är **aktive rad**, se till att körnings principen är inställd på att antingen **tillåta lokala skript och fjärrsignerade skript** eller **tillåta alla skript**under alternativ. |
| Det gick inte att hämta konfigurationen av anslutningen. | Kopplingens klientcertifikat som används för autentisering, upphört att gälla. Detta kan också inträffa om du har anslutningstjänsten installerad bakom en proxyserver. I det här fallet anslutningstjänsten inte kan komma åt Internet och kan inte tillhandahålla program till fjärranslutna användare. Förnya förtroende manuellt med hjälp av `Register-AppProxyConnector` cmdlet i Windows PowerShell. Om din Anslutningsapp finns bakom en proxyserver, är det nödvändigt att bevilja Internetåtkomst till Connector konton ”nätverkstjänster” och ”lokalt system”. Detta kan åstadkommas genom att ge dem åtkomst till proxyservern eller genom att ange att kringgå proxyn. |
| Det gick inte att registrera anslutningen: kontrol lera att du är program administratör för din Active Directory att registrera anslutningen. Fel: ”registreringsbegäran nekades”. | Det alias som du försöker logga in med är inte en administratör på den här domänen. Din Anslutningsapp är alltid installerad för den katalog som äger användarens domän. Kontrol lera att det administratörs konto som du försöker logga in med har minst program administratörs behörighet till Azure AD-klienten. |
| Anslutningen kunde inte ansluta till tjänsten på grund av nätverks problem. Anslutnings försöket försökte få åtkomst till följande URL. | Anslutningen kan inte ansluta till Application Proxy-moln tjänsten. Detta kan inträffa om du har en brand Väggs regel som blockerar anslutningen. Kontrol lera att du har tillåtit åtkomst till rätt portar och URL: er som anges i [Programproxyns krav](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Kerberos-fel

Den här tabellen beskriver mer vanliga fel som kommer från Kerberos-installationen och konfigurationen tillsammans med förslag för matchning av.

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Det gick inte att hämta aktuella körningsprincipen för att köra PowerShell-skript. | Om anslutningen misslyckas, kontrollera att PowerShell-körningsprincipen inte är inaktiverat.<br><br>1. öppna grupprincip redigeraren.<br>2. gå till **dator konfiguration** > **administrativa mallar** > **Windows-komponenter** > **Windows PowerShell** och dubbelklicka på **Aktivera skript körning**.<br>3. körnings principen kan anges till antingen **inte konfigurerad** eller **aktive rad**. Om inställningen är **aktive rad**, se till att körnings principen är inställd på att antingen **tillåta lokala skript och fjärrsignerade skript** eller **tillåta alla skript**under alternativ. |
| 12008 – azure AD överskred det maximala antalet tillåtna Kerberos-autentiseringsförsök till backend-servern. | Detta fel kan bero på felaktig konfiguration mellan Azure AD och backend-servern för programmet, eller ett problem i tid och datum-konfigurationen på båda datorerna. Backend-servern avvisade Kerberos-biljetten som skapats av Azure AD. Verifiera Azure AD och programmet backend-servern är rätt konfigurerade. Se till att tid och datum-konfigurationen på Azure AD och backend-programserver ska synkroniseras. |
| 13016 – azure AD kan inte hämta en Kerberos-biljett för användarens räkning eftersom det finns inget UPN i kant-token eller i åtkomst-cookie. | Det finns ett problem med STS-konfigurationen. Åtgärda Anspråkskonfiguration UPN i STS. |
| 13019 – azure AD kan inte hämta en Kerberos-biljett för användarens räkning på grund av följande allmänna API-fel. | Den här händelsen kan tyda på felaktig konfiguration mellan Azure AD och Domänkontrollantservern eller ett problem i tid och datum-konfigurationen på båda datorerna. Domänkontrollanten avvisade Kerberos-biljetten som skapats av Azure AD. Verifiera Azure AD och backend-programserver har konfigurerats korrekt, särskilt SPN-konfigurationen. Kontrollera att Azure AD är domänansluten till samma domän som domänkontrollanten för att se till att domänkontrollanten upprättar förtroende med Azure AD. Se till att tid och datum-konfigurationen på Azure AD och domänkontrollanten ska synkroniseras. |
| 13020 – azure AD kan inte hämta en Kerberos-biljett för användarens räkning eftersom SPN för backend-servern inte har definierats. | Den här händelsen kan tyda på felaktig konfiguration mellan Azure AD och Domänkontrollantservern eller ett problem i tid och datum-konfigurationen på båda datorerna. Domänkontrollanten avvisade Kerberos-biljetten som skapats av Azure AD. Verifiera Azure AD och backend-programserver har konfigurerats korrekt, särskilt SPN-konfigurationen. Kontrollera att Azure AD är domänansluten till samma domän som domänkontrollanten för att se till att domänkontrollanten upprättar förtroende med Azure AD. Se till att tid och datum-konfigurationen på Azure AD och domänkontrollanten ska synkroniseras. |
| 13022 – azure AD autentisera inte användaren eftersom backend-servern svarar på Kerberos-autentiseringsförsök med ett HTTP 401-fel. | Den här händelsen kan tyda på felaktig konfiguration mellan Azure AD och backend-servern för programmet, eller ett problem i tid och datum-konfigurationen på båda datorerna. Backend-servern avvisade Kerberos-biljetten som skapats av Azure AD. Verifiera Azure AD och programmet backend-servern är rätt konfigurerade. Se till att tid och datum-konfigurationen på Azure AD och backend-programserver ska synkroniseras. Mer information finns i [Felsöka konfigurationer med Kerberos-begränsad delegering för Application Proxy](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Slutanvändaren fel

Den här listan innehåller fel som slutanvändarna kan stöta på när de försöker få åtkomst till appen och misslyckas. 

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Webbplatsen kan inte visa sidan. | Användaren kan få det här felet när du försöker få åtkomst till appen som du har publicerat om programmet är ett IWA-program. Definierade SPN-namnet för det här programmet kan vara felaktig. Kontrollera att tjänstens Huvudnamn som konfigurerats för det här programmet är korrekt för IWA appar. |
| Webbplatsen kan inte visa sidan. | Användaren kan få det här felet när du försöker få åtkomst till appen som du har publicerat om programmet är ett program för OWA. Detta kan bero på något av följande:<br><li>Definierade SPN-namnet för det här programmet är felaktigt. Kontrollera att tjänstens Huvudnamn som konfigurerats för det här programmet är korrekt.</li><li>Den användare som försökte komma åt programmet använder ett Microsoft-konto i stället för på rätt företagskontot för att logga in eller användaren är en gästanvändare. Kontrollera att användaren loggar in med sitt företagskonto som matchar domänen för det publicerade programmet. Account användare och gäst kan inte komma åt IWA program.</li><li>Användaren som försökte komma åt programmet har inte definierats korrekt för det här programmet på den lokala sidan. Se till att den här användaren har rätt behörigheter som definierats för det här server dels programmet på den lokala datorn. |
| Den här företagets appen kan inte nås. Du har inte behörighet att komma åt det här programmet. Auktoriseringen misslyckades. Se till att tilldela användare med åtkomst till det här programmet. | Användaren kan få det här felet när du försöker komma åt appen som du har publicerat om de använder Microsoft-konton i stället för företagets konto för att logga in. Gästanvändare kan också få det här felet. Account användare och gäster kan inte komma åt IWA program. Kontrollera att användaren loggar in med sitt företagskonto som matchar domänen för det publicerade programmet.<br><br>Du har inte tilldelat användaren för det här programmet. Gå till fliken **program** och tilldela användaren eller användar gruppen till det här programmet under **användare och grupper**. |
| Den här företagets appen är inte tillgänglig just nu. Försök igen senare... Tidsgränsen för anslutningen har uppnåtts. | Användaren kan få det här felet när du försöker komma åt appen som du har publicerat om de inte är korrekt definierade för det här programmet på den lokala sidan. Se till att användarna har rätt behörigheter enligt definitionen för det här server dels programmet på den lokala datorn. |
| Den här företagets appen kan inte nås. Du har inte behörighet att komma åt det här programmet. Auktoriseringen misslyckades. Se till att användaren har en licens för Azure Active Directory Premium. | Användaren kan få det här felet när du försöker få åtkomst till appen som du har publicerat om de inte explicit hade tilldelats en Premium-licens av prenumerantens administratör. Gå till fliken Active Directory **licenser** för prenumerant och kontrol lera att den här användaren eller användar gruppen har tilldelats en Premium-licens. |
| Det gick inte att hitta en server med det angivna värd namnet. | Användaren kan få det här felet när du försöker komma åt appen som du har publicerat om programmets anpassade domän inte är korrekt konfigurerad. Se till att du har överfört ett certifikat för domänen och konfigurerat DNS-posten korrekt genom att följa stegen i [arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md) |

## <a name="my-error-wasnt-listed-here"></a>Mina fel visas inte här

Om det uppstår ett fel eller problem med Azure AD Application Proxy som inte visas i den här felsökningsguiden kan vill vi få information om den. Skicka ett e-postmeddelande till vårt [feedback-team](mailto:aadapfeedback@microsoft.com) med information om det fel som du har påträffat.

## <a name="see-also"></a>Se även
* [Aktivera programproxy för Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Publicera program med Application Proxy](application-proxy-add-on-premises-application.md)
* [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
* [Aktivera villkorlig åtkomst](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
