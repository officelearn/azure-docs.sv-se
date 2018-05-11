---
title: Felsöka Application Proxy | Microsoft Docs
description: Beskriver hur du felsöker fel i Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 96e7361ce66caaa653706d22f767e1a109fdacf7
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Felsökning av problem med Application Proxy och felmeddelanden
Om fel uppstår i att komma åt ett publicerat program eller publicera program kontrollerar du följande alternativ för att se om Microsoft Azure AD Application Proxy fungerar:

* Öppna konsolen Windows-tjänster och kontrollera att den **Microsoft AAD Application Proxy Connector** tjänsten är aktiverad och körs. Du kanske också vill titta på egenskapssidan för Application Proxy-tjänst som visas i följande bild:  
  ![Egenskaper för Microsoft AAD Application Proxy Connector skärmbild](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)
* Öppna Loggboken och leta efter Application Proxy connector-händelser i **program- och tjänstloggar** > **Microsoft** > **AadApplicationProxy** > **Connector** > **Admin**.
* Om det behövs mer detaljerade loggar är tillgängliga som [aktivera Application Proxy connector-sessionsloggar](application-proxy-understand-connectors.md#under-the-hood).

Mer information om verktyget Azure AD-felsökning finns [felsökningsverktyget för att verifiera anslutningen nätverk krav](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/03/troubleshooting-tool-to-validate-connector-networking-prerequisites).

## <a name="the-page-is-not-rendered-correctly"></a>Sidan renderas inte korrekt
Du kan ha problem med programmet återgivning eller felaktigt fungerar utan att få felmeddelanden. Detta kan inträffa om du har publicerat artikel sökväg, men programmet kräver innehåll som finns utanför sökvägen.

Om du publicerar sökvägen till exempel https://yourapp/app men programmet anropar bilder https://yourapp/media, inte återges. Kontrollera att du publicerar program med den högsta nivån sökvägen måste du inkludera alla relevant innehåll. Det vore i det här exemplet http://yourapp/.

Om du ändrar sökvägen för att inkludera refererat innehåll, men fortfarande behöver användare att hamna på en djupare länk i sökvägen finns i bloggposten [inställningen rätt länk för Application Proxy-program i Azure AD åtkomst till Kontrollpanelen och Office 365 app starta](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Connector-fel

Använd den [Azure AD Application Proxy Connector portar Test Tool](https://aadap-portcheck.connectorporttest.msappproxy.net/) att verifiera att din connector kan nå tjänsten Application Proxy. Kontrollera att den centrala USA och region som är närmast dig har alla gröna bockmarkeringarna minimum. Utöver det kan innebär mer gröna bockmarkeringarna större flexibilitet. 

Om registreringen misslyckas under guiden kopplingsinstallationen, finns det två sätt att visa orsaken till felet. Antingen titta i Loggboken under **program och tjänster Logs\Microsoft\AadApplicationProxy\Connector\Admin**, eller kör följande Windows PowerShell-kommando:

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

När du har hittat Connector-fel från händelseloggen kan du använda den här tabellen över vanliga fel för att lösa problemet:

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Registreringen av anslutningsverktyget misslyckades: Kontrollera att du har aktiverat Application Proxy i Azure-hanteringsportalen och att du angett din Active Directory-användarnamn och lösenord korrekt. Fel: 'ett eller flera fel uppstod ”. | Om du har stängt fönstret registreringen utan att logga in till Azure AD, kör guiden koppling igen och registrera anslutningsverktyget. <br><br> Om fönstret registrering öppnar och stänger sedan omedelbart utan att du kan logga in, får du förmodligen det här felet. Det här felet uppstår vid ett nätverksfel på datorn. Kontrollera att det är möjligt att ansluta från en webbläsare till en offentlig webbplats och att portarna är öppna som anges i [krav för Application Proxy](active-directory-application-proxy-enable.md). |
| Rensa fel visas i fönstret registrering. Det går inte att fortsätta | Om du ser detta fel och sedan stängs fönstret, du har angett felaktigt användarnamn eller lösenord. Försök igen. |
| Registreringen av anslutningsverktyget misslyckades: Kontrollera att du har aktiverat Application Proxy i Azure-hanteringsportalen och att du angett din Active Directory-användarnamn och lösenord korrekt. Fel: ' AADSTS50059: ingen klient identifierande information finns i antingen begäran eller underförstås av alla angivna autentiseringsuppgifter och Sök efter service principal URI har misslyckats. | Du försöker logga in med ett Account och inte en domän som är en del av organisations-ID för den katalog som du försöker komma åt. Se till att administratören ingår i samma domännamn som klient-domän, till exempel om Azure AD-domänen är contoso.com, administratören vara admin@contoso.com. |
| Det gick inte att hämta aktuella körningsprincipen för att köra PowerShell-skript. | Om anslutningen installationen misslyckas, kontrollera att PowerShell-körningsprincipen inte är inaktiverad. <br><br>1. Öppna Redigeraren för grupprinciper.<br>2. Gå till **Datorkonfiguration** > **Administrationsmallar** > **Windows-komponenter** > **Windows PowerShell** och dubbelklicka på **aktivera skriptkörningen**.<br>3. Körningsprincipen kan vara inställd på antingen **inte konfigurerad** eller **aktiverad**. Om värdet **aktiverad**, se till att under Alternativ, körningsprincipen är inställd på antingen **Tillåt lokala skript och fjärranslutna signerade skript** eller **alla skript**. |
| Det gick inte att hämta konfigurationen Connector. | Kopplingens klientcertifikat som används för autentisering, upphört att gälla. Detta kan också inträffa om du har anslutningstjänsten installerad bakom en proxyserver. I det här fallet kopplingen kan inte komma åt Internet och kommer inte att kunna tillhandahålla program till fjärranslutna användare. Förnya manuellt med hjälp av den `Register-AppProxyConnector` cmdlet i Windows PowerShell. Om din koppling är bakom en proxyserver, är det nödvändigt att bevilja åtkomst till Connector konton ”nätverkstjänster” och ”lokalt system”. Detta kan åstadkommas genom att ge dem åtkomst till proxyservern eller genom att kringgå proxyn. |
| Registreringen av anslutningsverktyget misslyckades: Kontrollera att du är Global administratör för din Active Directory för att registrera anslutningsverktyget. Fel: ' registreringsbegäran nekades ”. | Det alias som du försöker logga in med är inte en administratör på den här domänen. Din Connector installeras alltid för den katalog som äger användarens domän. Se till att det administratörskonto som du försöker logga in med har globala behörigheter till Azure AD-klient. |

## <a name="kerberos-errors"></a>Kerberos-fel

Den här tabellen omfattar de vanliga fel som kommer från Kerberos-installation och konfiguration samt förslag för matchning.

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Det gick inte att hämta aktuella körningsprincipen för att köra PowerShell-skript. | Om anslutningen installationen misslyckas, kontrollera att PowerShell-körningsprincipen inte är inaktiverad.<br><br>1. Öppna Redigeraren för grupprinciper.<br>2. Gå till **Datorkonfiguration** > **Administrationsmallar** > **Windows-komponenter** > **Windows PowerShell** och dubbelklicka på **aktivera skriptkörningen**.<br>3. Körningsprincipen kan vara inställd på antingen **inte konfigurerad** eller **aktiverad**. Om värdet **aktiverad**, se till att under Alternativ, körningsprincipen är inställd på antingen **Tillåt lokala skript och fjärranslutna signerade skript** eller **alla skript**. |
| 12008 - azure AD överskred maximalt antal tillåtna Kerberos autentiseringsförsök till backend-servern. | Detta fel kan bero på felaktig konfiguration mellan Azure AD och backend-programserver eller ett problem i konfigurationen för datum och tid på båda datorerna. Backend-servern avvisade Kerberos-biljetten som skapats av Azure AD. Kontrollera att Azure AD och backend-Programserver är korrekt konfigurerade. Kontrollera att tid och datum konfigurationen på Azure AD och backend-Programserver är synkroniserade. |
| 13016 - azure AD kan inte hämta en Kerberos-biljett för användarens räkning eftersom det finns inga UPN i kant-token eller åtkomst-cookie. | Det finns ett problem med STS-konfigurationen. Åtgärda Anspråkskonfiguration UPN i STS. |
| 13019 - azure AD kan inte hämta en Kerberos-biljett för användarens räkning på grund av följande allmänna API-fel. | Den här händelsen kan tyda på felaktig konfiguration mellan Azure AD och Domänkontrollantservern eller ett problem i konfigurationen för datum och tid på båda datorerna. Domänkontrollanten avvisade Kerberos-biljetten som skapats av Azure AD. Kontrollera att Azure AD och backend-Programserver är korrekt konfigurerade, särskilt SPN-konfigurationen. Kontrollera att Azure AD är anslutna till samma domän som domänkontrollanten för att se till att domänkontrollanten upprättar förtroende med Azure AD-domän. Kontrollera att tid och datum konfigurationen på Azure AD och domänkontrollanten ska synkroniseras. |
| 13020 - azure AD kan inte hämta en Kerberos-biljett för användarens räkning eftersom SPN för backend-server inte har definierats. | Den här händelsen kan tyda på felaktig konfiguration mellan Azure AD och Domänkontrollantservern eller ett problem i konfigurationen för datum och tid på båda datorerna. Domänkontrollanten avvisade Kerberos-biljetten som skapats av Azure AD. Kontrollera att Azure AD och backend-Programserver är korrekt konfigurerade, särskilt SPN-konfigurationen. Kontrollera att Azure AD är anslutna till samma domän som domänkontrollanten för att se till att domänkontrollanten upprättar förtroende med Azure AD-domän. Kontrollera att tid och datum konfigurationen på Azure AD och domänkontrollanten ska synkroniseras. |
| 13022 - azure AD kan autentisera användaren eftersom backend-servern svarar på Kerberos autentiseringsförsök med ett HTTP 401-fel. | Den här händelsen kan tyda på felaktig konfiguration mellan Azure AD och backend-programserver eller ett problem i konfigurationen för datum och tid på båda datorerna. Backend-servern avvisade Kerberos-biljetten som skapats av Azure AD. Kontrollera att Azure AD och backend-Programserver är korrekt konfigurerade. Kontrollera att tid och datum konfigurationen på Azure AD och backend-Programserver är synkroniserade. Mer information finns i [felsökning av Kerberos-begränsad delegering konfigurationer för Application Proxy](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Slutanvändarens fel

Den här listan innehåller fel som slutanvändarna kan uppstå när de försöker komma åt appen och misslyckas. 

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Webbplatsen kan inte visa sidan. | Användaren får felet när du försöker komma åt appen som du har publicerat om programmet är en IWA. Definierade SPN för det här programmet kan vara felaktigt. Kontrollera att tjänstens Huvudnamn som konfigurerats för tillämpningsprogrammet är korrekt för IWA appar. |
| Webbplatsen kan inte visa sidan. | Användaren får felet när du försöker komma åt appen som du har publicerat om programmet är ett program för OWA. Detta kan orsakas av något av följande:<br><li>Definierade SPN för det här programmet är felaktigt. Kontrollera att tjänstens Huvudnamn som konfigurerats för tillämpningsprogrammet är korrekt.</li><li>Användaren som försökte komma åt programmet använder ett Microsoft-konto i stället för företagskontot som rätt för att logga in eller användaren är en gästanvändare. Kontrollera att användaren loggar in med sitt företagskonto som matchar domänen för det publicerade programmet. Account användare och gäst kan inte komma åt IWA program.</li><li>Användaren som försökte komma åt programmet har inte definierats korrekt för det här programmet på den lokala sidan. Kontrollera att den här användaren har rätt behörigheter som har definierats för det här backend-programmet på den lokala datorn. |
| Den här företagets appen kan inte nås. Du har inte behörighet att komma åt programmet. Auktoriseringen misslyckades. Se till att tilldela användare med åtkomst till det här programmet. | Användarna kan få det här felet när du försöker komma åt appen som du har publicerat om de använder Microsoft-konton i stället för sitt företagskonto för att logga in. Gästanvändare kan också få det här felet. Account användare och gäster kan inte komma åt IWA program. Kontrollera att användaren loggar in med sitt företagskonto som matchar domänen för det publicerade programmet.<br><br>Du har inte tilldelat användaren för det här programmet. Gå till den **programmet** fliken och under **användare och grupper**, tilldela den här användaren eller användargruppen till det här programmet. |
| Den här företagets appen är inte tillgänglig just nu. Försök igen senare... Kopplingen tidsgränsen. | Användarna kan få det här felet när du försöker komma åt appen som du har publicerat om de inte definierats för det här programmet på den lokala sidan korrekt. Se till att användarna har rätt behörigheter som har definierats för det här backend-programmet på den lokala datorn. |
| Den här företagets appen kan inte nås. Du har inte behörighet att komma åt programmet. Auktoriseringen misslyckades. Kontrollera att användaren har en licens för Azure Active Directory Premium eller Basic. | Användarna kan få det här felet när du försöker komma åt appen som du har publicerat om de inte uttryckligen tilldelade med en licens för Premium/enkel av prenumerantens administratör. Gå till Active Directory för prenumerantens **licenser** fliken och se till att den här användaren eller användargruppen är tilldelad en Premium eller Basic-licens. |

## <a name="my-error-wasnt-listed-here"></a>Mina fel listas inte här

Om det uppstår ett fel eller problem med Azure AD Application Proxy som inte finns i den här felsökningsguiden vill vi höra om den. Skicka ett e-postmeddelande till vår [feedback team](mailto:aadapfeedback@microsoft.com) med information om de fel.

## <a name="see-also"></a>Se också
* [Aktivera Application Proxy för Azure Active Directory](active-directory-application-proxy-enable.md)
* [Publicera program med Application Proxy](application-proxy-publish-azure-portal.md)
* [Aktivera enkel inloggning](active-directory-application-proxy-sso-using-kcd.md)
* [Aktivera villkorlig åtkomst](application-proxy-enable-remote-access-sharepoint.md)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
