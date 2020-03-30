---
title: Felsöka programproxy | Microsoft-dokument
description: Beskriver felsÃ¶kning av fel i Azure AD Application Proxy.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244230"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Felsöka problem med programproxy och felmeddelanden

Vid felsökning av programproxyproblem rekommenderar vi att du börjar med att granska felsökningsflödet, [Debug Application Proxy Connector-problem](application-proxy-debug-connectors.md)för att avgöra om Application Proxy-kopplingar är korrekt konfigurerade. Om du fortfarande har problem med att ansluta till programmet följer du felsökningsflödet i [Problem med felsökningsprogramproxyprogrammet](application-proxy-debug-apps.md).

Om det uppstår fel i åtkomsten till ett publicerat program eller i publiceringsprogram kontrollerar du följande alternativ för att se om Microsoft Azure AD Application Proxy fungerar korrekt:

* Öppna Windows Services-konsolen. Kontrollera att **Tjänsten Microsoft AAD Application Proxy Connector** är aktiverad och körs. Du kanske också vill titta på sidan Egenskaper för tjänsten Programproxy, som visas i följande bild:  
  ![Microsoft AAD Application Proxy Connector Egenskaper fönster skärmdump](./media/application-proxy-troubleshoot/connectorproperties.png)
* Öppna Loggboken och leta efter programproxyanslutningshändelser > i **Program och tjänster loggar****Microsoft** > **AadApplicationProxy** > **Connector** > **Admin**.
* Vid behov finns mer detaljerade loggar tillgängliga genom [att aktivera sessionsloggarna för Application Proxy-anslutning .](application-proxy-connectors.md#under-the-hood)

## <a name="the-page-is-not-rendered-correctly"></a>Sidan återges inte korrekt
Du kan ha problem med att programåtergivningen eller fungerar felaktigt utan att få specifika felmeddelanden. Detta kan inträffa om du har publicerat artikelsökvägen, men programmet kräver innehåll som finns utanför sökvägen.

Om du till exempel `https://yourapp/app` publicerar sökvägen men `https://yourapp/media`programmet anropar bilder i återges de inte. Se till att du publicerar programmet med den väg på högsta nivå som du behöver för att inkludera allt relevant innehåll. I det här exemplet `http://yourapp/`skulle det vara .

Om du ändrar sökvägen till att inkludera refererat innehåll, men ändå behöver användare för att landa på en djupare länk i sökvägen, läser [du blogginlägget Ange rätt länk för programproxyprogram i Azure AD-åtkomstpanelen och Office 365-startprogrammet](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Anslutningsfel

Om registreringen misslyckas under installationen av Anslutningsguiden finns det två sätt att visa orsaken till felet. Titta antingen i händelseloggen under **Program- och tjänstloggar\Microsoft\AadApplicationProxy\Connector\Admin**eller kör följande Windows PowerShell-kommando:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

När du har hittat anslutningsfelet från händelseloggen använder du den här tabellen med vanliga fel för att lösa problemet:

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Anslutningsregistreringen misslyckades: Kontrollera att du har aktiverat programproxy i Azure Management Portal och att du angav ditt Active Directory-användarnamn och lösenord korrekt. Fel: "Ett eller flera fel uppstod." | Om du stängde registreringsfönstret utan att logga in på Azure AD kör du anslutningsguiden igen och registrerar kopplingen. <br><br> Om registreringsfönstret öppnas och sedan omedelbart stängs utan att du kan logga in får du förmodligen det här felet. Det här felet uppstår när det finns ett nätverksfel på datorn. Se till att det är möjligt att ansluta från en webbläsare till en offentlig webbplats och att portarna är öppna enligt vad som anges i [förutsättningar för programproxy](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |
| Ett tydligt fel visas i registreringsfönstret. Det går inte att fortsätta | Om du ser det här felet och sedan fönstret stängs, angav du fel användarnamn eller lösenord. Försök igen. |
| Anslutningsregistreringen misslyckades: Kontrollera att du har aktiverat programproxy i Azure Management Portal och att du angav ditt Active Directory-användarnamn och lösenord korrekt. Fel: 'AADSTS50059: Ingen klientidentifierande information som finns i vare sig begäran eller underförstått av några angivna autentiseringsuppgifter och sökning efter tjänsthuvudnamn URI har misslyckats. | Du försöker logga in med ett Microsoft-konto och inte en domän som ingår i organisations-ID:t för katalogen som du försöker komma åt. Kontrollera att administratören är en del av samma domännamn som klientdomänen, till exempel om Azure admin@contoso.comAD-domänen är contoso.com, bör administratören vara . |
| Det gick inte att hämta den aktuella körningsprincipen för att köra PowerShell-skript. | Om anslutningsinstallationen misslyckas kontrollerar du att PowerShell-körningsprincipen inte är inaktiverad. <br><br>1. Öppna redigeraren för grupprinciper.<br>2. Gå till**Administrativa mallar för** >  **datorkonfiguration** > **Windows-komponenter** > **Windows PowerShell** och dubbelklicka **på Aktivera skriptkörning**.<br>3. Körningsprincipen kan ställas in på antingen **Inte konfigurerad** eller **Aktiverad**. Om den är inställd på **Aktiverad**kontrollerar du att körningsprincipen under Alternativ är inställd på att antingen **tillåta lokala skript och fjärrsignerade skript** eller tillåta alla **skript**. |
| Det gick inte att hämta konfigurationen. | Kopplingens klientcertifikat, som används för autentisering, har upphört att gälla. Detta kan också inträffa om du har anslutningen installerad bakom en proxy. I det här fallet kan anslutningen inte komma åt Internet och kommer inte att kunna tillhandahålla program till fjärranvändare. Förnya förtroendet manuellt `Register-AppProxyConnector` med hjälp av cmdleten i Windows PowerShell. Om din anslutning ligger bakom en proxy är det nödvändigt att ge Internet åtkomst till anslutningskontona "nätverkstjänster" och "lokalt system". Detta kan åstadkommas antingen genom att ge dem tillgång till proxy eller genom att ställa in dem att kringgå proxy. |
| Anslutningsregistreringen misslyckades: Kontrollera att du är programadministratör för Active Directory för att registrera anslutningsappen. Fel: "Registreringsbegäran nekades." | Det alias som du försöker logga in med är inte administratör på den här domänen. Din Anslutning är alltid installerad för den katalog som äger användarens domän. Kontrollera att administratörskontot som du försöker logga in med har i 1.ast programadministratörsbehörighet till Azure AD-klienten. |
| Anslutningen kunde inte ansluta till tjänsten på grund av nätverksproblem. Anslutningen försökte komma åt följande URL. | Anslutningen kan inte ansluta till molntjänsten Application Proxy. Detta kan inträffa om du har en brandväggsregel som blockerar anslutningen. Kontrollera att du har tillåtit åtkomst till rätt portar och URL:er som anges i [förutsättningar för programproxy](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Kerberos-fel

Den här tabellen täcker de vanligaste felen som kommer från Kerberos-konfiguration och konfiguration och innehåller förslag på lösning.

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Det gick inte att hämta den aktuella körningsprincipen för att köra PowerShell-skript. | Om anslutningsinstallationen misslyckas kontrollerar du att Körningsprincipen för PowerShell inte är inaktiverad.<br><br>1. Öppna redigeraren för grupprinciper.<br>2. Gå till**Administrativa mallar för** >  **datorkonfiguration** > **Windows-komponenter** > **Windows PowerShell** och dubbelklicka **på Aktivera skriptkörning**.<br>3. Körningsprincipen kan ställas in på antingen **Inte konfigurerad** eller **Aktiverad**. Om den är inställd på **Aktiverad**kontrollerar du att körningsprincipen under Alternativ är inställd på att antingen **tillåta lokala skript och fjärrsignerade skript** eller tillåta alla **skript**. |
| 12008 - Azure AD överskred det maximala antalet tillåtna Kerberos-autentiseringsförsök till serverförstämmandeservern. | Det här felet kan tyda på felaktig konfiguration mellan Azure AD och serverdprogramservern, eller ett problem i tids- och datumkonfigurationen på båda datorerna. Serveråtkomstservern avböjde Kerberos-biljetten som skapats av Azure AD. Kontrollera att Azure AD och serverdprogramservern är korrekt konfigurerade. Kontrollera att tids- och datumkonfigurationen på Azure AD och serverdprogramservern är synkroniserade. |
| 13016 - Azure AD kan inte hämta en Kerberos-biljett för användaren eftersom det inte finns någon UPN i kanttoken eller i åtkomstcookien. | Det har uppstått ett problem med STS-konfigurationen. Åtgärda UPN-anspråkskonfigurationen i STS. |
| 13019 - Azure AD kan inte hämta en Kerberos-biljett för användaren på grund av följande allmänna API-fel. | Den här händelsen kan tyda på felaktig konfiguration mellan Azure AD och domänkontrollantservern, eller ett problem i tids- och datumkonfigurationen på båda datorerna. Domänkontrollanten nekade Kerberos-biljetten som skapats av Azure AD. Kontrollera att Azure AD och serverdprogrammet är korrekt konfigurerade, särskilt SPN-konfigurationen. Kontrollera att Azure AD är domänen ansluten till samma domän som domänkontrollanten för att säkerställa att domänkontrollanten upprättar förtroende med Azure AD. Kontrollera att tids- och datumkonfigurationen på Azure AD och domänkontrollanten är synkroniserade. |
| 13020 - Azure AD kan inte hämta en Kerberos-biljett för användaren eftersom server-SPN för serverservern inte har definierats. | Den här händelsen kan tyda på felaktig konfiguration mellan Azure AD och domänkontrollantservern, eller ett problem i tids- och datumkonfigurationen på båda datorerna. Domänkontrollanten nekade Kerberos-biljetten som skapats av Azure AD. Kontrollera att Azure AD och serverdprogrammet är korrekt konfigurerade, särskilt SPN-konfigurationen. Kontrollera att Azure AD är domänen ansluten till samma domän som domänkontrollanten för att säkerställa att domänkontrollanten upprättar förtroende med Azure AD. Kontrollera att tids- och datumkonfigurationen på Azure AD och domänkontrollanten är synkroniserade. |
| 13022 - Azure AD kan inte autentisera användaren eftersom serverdservern svarar på Kerberos-autentiseringsförsök med ett HTTP 401-fel. | Den här händelsen kan tyda på felaktig konfiguration mellan Azure AD och serverdprogramservern, eller ett problem i tids- och datumkonfigurationen på båda datorerna. Serveråtkomstservern avböjde Kerberos-biljetten som skapats av Azure AD. Kontrollera att Azure AD och serverdprogramservern är korrekt konfigurerade. Kontrollera att tids- och datumkonfigurationen på Azure AD och serverdprogramservern är synkroniserade. Mer information finns i [Felsöka Kerberos-begränsade delegeringskonfigurationer för programproxy](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Fel för slutanvändare

Den här listan omfattar fel som dina slutanvändare kan stöta på när de försöker komma åt appen och misslyckas. 

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Webbplatsen kan inte visa sidan. | Användaren kan få det här felet när du försöker komma åt appen du publicerade om programmet är ett IWA-program. Det definierade SPN för det här programmet kan vara felaktigt. För IWA-appar kontrollerar du att SPN som konfigurerats för det här programmet är korrekt. |
| Webbplatsen kan inte visa sidan. | Användaren kan få det här felet när du försöker komma åt appen du publicerade om programmet är ett OWA-program. Detta kan orsakas av något av följande:<br><li>Det definierade SPN för det här programmet är felaktigt. Kontrollera att SPN som konfigurerats för det här programmet är korrekt.</li><li>Användaren som försökte komma åt programmet använder ett Microsoft-konto i stället för rätt företagskonto för att logga in, eller så är användaren gästanvändare. Kontrollera att användaren loggar in med sitt företagskonto som matchar domänen för det publicerade programmet. Användare och gäst på Microsoft-konto kan inte komma åt IWA-program.</li><li>Användaren som försökte komma åt programmet har inte definierats korrekt för det här programmet på den lokala sidan. Kontrollera att den här användaren har rätt behörighet enligt definitionen för det här serverningsprogrammet på den lokala datorn. |
| Det går inte att komma åt den här företagsappen. Du har inte behörighet att komma åt det här programmet. Auktoriseringen misslyckades. Se till att tilldela användaren åtkomst till det här programmet. | Användaren kan få det här felet när du försöker komma åt appen du publicerade om de använder Microsoft-konton i stället för sitt företagskonto för att logga in. Gästanvändare kan också få det här felet. Användare och gäster på Microsoft-kontot kan inte komma åt IWA-program. Kontrollera att användaren loggar in med sitt företagskonto som matchar domänen för det publicerade programmet.<br><br>Du kanske inte har tilldelat användaren för det här programmet. Gå till fliken **Program** och under **Användare och grupper**tilldelar du den här användar- eller användargruppen till det här programmet. |
| Det går inte att komma åt den här företagsappen just nu. Försök igen senare... Kontakten har timeats ut. | Användaren kan få det här felet när du försöker komma åt appen du publicerade om de inte är korrekt definierade för det här programmet på den lokala sidan. Kontrollera att användarna har rätt behörighet enligt definitionen för det här serverningsprogrammet på den lokala datorn. |
| Det går inte att komma åt den här företagsappen. Du har inte behörighet att komma åt det här programmet. Auktoriseringen misslyckades. Kontrollera att användaren har en licens för Azure Active Directory Premium. | Användaren kan få det här felet när du försöker komma åt appen du publicerade om de inte uttryckligen tilldelats en Premium-licens av prenumerantens administratör. Gå till fliken Active **Directory-licenser för prenumeranter** och se till att den här användaren eller användargruppen har tilldelats en Premium-licens. |
| Det gick inte att hitta en server med det angivna värdnamnet. | Användaren kan få det här felet när du försöker komma åt appen som du publicerade om programmets anpassade domän inte är korrekt konfigurerad. Kontrollera att du har laddat upp ett certifikat för domänen och konfigurerat DNS-posten korrekt genom att följa stegen i [Arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md) |

## <a name="my-error-wasnt-listed-here"></a>Mitt fel listades inte här

Om du stöter på ett fel eller problem med Azure AD Application Proxy som inte finns med i den här felsökningsguiden vill vi höra om det. Skicka ett e-postmeddelande till vårt [feedbackteam](mailto:aadapfeedback@microsoft.com) med information om felet du stött på.

## <a name="see-also"></a>Se även
* [Aktivera programproxy för Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Publicera program med Application Proxy](application-proxy-add-on-premises-application.md)
* [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
* [Aktivera villkorlig åtkomst](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
