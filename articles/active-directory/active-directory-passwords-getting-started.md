---
title: "Kom igång: Azure AD-lösenordshantering | Microsoft Docs"
description: "Låt användare återställa sina egna lösenord, identifiera kraven för lösenordsåterställning och aktivera tillbakaskrivning av lösenord i Active Directory."
services: active-directory
keywords: "Active Directory-lösenordshantering, lösenordshantering, återställa Azure AD-lösenord"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: c2c46637ccccd01c1c3056d6a25ef605cfd68f2d
ms.lasthandoff: 03/28/2017


---
# <a name="getting-started-with-password-management"></a>Komma igång med lösenordshantering
> [!IMPORTANT]
> **Är du här eftersom du har problem med att logga in?** I så fall är det [här som du ser hur du kan ändra och återställa ditt eget lösenord](active-directory-passwords-update-your-own-password.md#reset-your-password).
>
>

Konfigurera systemet i några få enkla steg så att användarna kan hantera sina Azure Active Directory-lösenord i molnet eller sina lokala Active Directory-lösenord. När du har kontrollerat att du uppfyller några enkla krav kan du snabbt implementera funktioner för lösenordsändring och lösenordsåterställning för hela organisationen. I den här artikeln går vi igenom följande:

* [**Bra tips från våra kunder att läsa innan du börjar**](#top-tips-from-our-customers-to-read-before-you-begin)
 * [**TOPPTIPS: DOKUMENTATIONSNAVIGERING** – Använd vår innehållsförteckning och webbläsarens sökfunktion för att hitta svar](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
 * [**Tips 1: LICENSIERING** – Kontrollera att du förstår licenskraven](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
 * [**Tips 2: TESTA** – Testa med en slutanvändare, inte en administratör, och kör en pilotversion med en liten uppsättning användare](#tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
 * [**Tips 3: DISTRIBUTION** – Fyll i data i förväg för dina användare så att de inte behöver registrera sig](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
 * [**Tips 4: DISTRIBUTION** – Använd lösenordsåterställning så behöver du inte kommunicera tillfälliga lösenord](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
 * [**Tips 5: TILLBAKASKRIVNING** – Titta på programmets händelselogg på AAD Connect-datorn om du vill felsöka tillbakaskrivning av lösenord](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
 * [**Tips 6: TILLBAKASKRIVNING** – Kontrollera att du aktiverar rätt behörigheter, brandväggsregler och anslutningsinställningar för tillbakaskrivning av lösenord](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
 * [**Tips 7: RAPPORTERING** – Se vem som registrerar eller återställer lösenord med Azure AD SSPR-granskningsloggar](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
 * [**Tips 8: FELSÖKNING** – Läs vår felsökningsguide och vanliga frågor och svar så kan du lösa många problem](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
 * [**Tips 9: FELSÖKNING** – Om du fortfarande behöver hjälp är det viktigt att du tar med tillräckligt med information så att vi kan hjälpa dig](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)
* [**Hur du konfigurerar systemet så att användarna kan återställa sina Azure Active Directory-lösenord**](#enable-users-to-reset-their-azure-ad-passwords)
 * [Krav för lösenordsåterställning via självbetjäning](#prerequisites)
 * [Steg 1: Konfigurera en princip för återställning av lösenord](#step-1-configure-password-reset-policy)
 * [Steg 2: Lägga till kontaktdata för testanvändaren](#step-2-add-contact-data-for-your-test-user)
 * [Steg 3: Återställa ditt lösenord som en användare](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Konfigurera systemet så att användarna kan återställa eller ändra sina lokala Active Directory-lösenord**](#enable-users-to-reset-or-change-their-ad-passwords)
 * [Krav för tillbakaskrivning av lösenord](#writeback-prerequisites)
 * [Steg 1: Ladda ned den senaste versionen av Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
 * [Steg 2: Aktivera tillbakaskrivning av lösenord i Azure AD Connect via användargränssnittet eller PowerShell och verifiera](#step-2-enable-password-writeback-in-azure-ad-connect)
 * [Steg 3: Konfigurera brandväggen](#step-3-configure-your-firewall)
 * [Steg 4: Konfigurera lämpliga behörigheter](#step-4-set-up-the-appropriate-active-directory-permissions)
 * [Steg 5: Återställa ditt AD-lösenord som en användare och verifiera](#step-5-reset-your-ad-password-as-a-user)

## <a name="top-tips-from-our-customers-to-read-before-you-begin"></a>Bra tips från våra kunder att läsa innan du börjar
Här är några av de bästa tipsen som vi har sett är användbara för kunder som distribuerar lösenordshantering inom organisationen.

* [**TOPPTIPS: DOKUMENTATIONSNAVIGERING** – Använd vår innehållsförteckning och webbläsarens sökfunktion för att hitta svar](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
* [**Tips 1: LICENSIERING** – Kontrollera att du förstår licenskraven](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
* [**Tips 2: TESTA** – Testa med en slutanvändare, inte en administratör, och kör en pilotversion med en liten uppsättning användare](#tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
* [**Tips 3: DISTRIBUTION** – Fyll i data i förväg för dina användare så att de inte behöver registrera sig](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
* [**Tips 4: DISTRIBUTION** – Använd lösenordsåterställning så behöver du inte kommunicera tillfälliga lösenord](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
* [**Tips 5: TILLBAKASKRIVNING** – Titta på programmets händelselogg på AAD Connect-datorn om du vill felsöka tillbakaskrivning av lösenord](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
* [**Tips 6: TILLBAKASKRIVNING** – Kontrollera att du aktiverar rätt behörigheter, brandväggsregler och anslutningsinställningar för tillbakaskrivning av lösenord](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
* [**Tips 7: RAPPORTERING** – Se vem som registrerar eller återställer lösenord med Azure AD SSPR-granskningsloggar](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
* [**Tips 8: FELSÖKNING** – Läs vår felsökningsguide och vanliga frågor och svar så kan du lösa många problem](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
* [**Tips 9: FELSÖKNING** – Om du fortfarande behöver hjälp är det viktigt att du tar med tillräckligt med information så att vi kan hjälpa dig](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)

### <a name="top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers"></a>TOPPTIPS: DOKUMENTATIONSNAVIGERING – Använd vår innehållsförteckning och webbläsarens sökfunktion för att hitta svar
Om du använder någon av vår dokumentation har vi försökt att tillhandahålla snabblänkar till alla intressanta platser där administratörer kan få veta mer i innehållsförteckningen. 

Titta på innehållsförteckningen nedan: 
* [Återställning av lösenord för Azure AD: Innehållsförteckning för dokumentation](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)

### <a name="tip-1-licensing---make-sure-you-understand-the-licensing-requirements"></a>Tips 1: LICENSIERING – Kontrollera att du förstår licenskraven
För att återställning av lösenordet till Azure AD ska fungera måste du ha minst en engångslicens tilldelad inom organisationen. Vi kräver inte licensiering per användare för själva lösenordsåterställningen, men om du använder funktionen utan att en licens har tilldelats en användare kan det betraktas som att du inte följer Microsofts licensavtal, och du måste i så fall tilldela dessa användare licenser.

Här följer några dokument som kan hjälpa dig att förstå vilka licenser som krävs för återställning av lösenord.
* [Allmän licensinformation för lösenordsåterställning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-customize#what-customization-options-are-available)
* [Per-funktion-licensinformation för lösenordsåterställning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability)
* [Scenarier som stöds för tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#scenarios-supported-for-password-writeback)

### <a name="tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users"></a>Tips 2: TESTA – Testa med en slutanvändare, inte en administratör, och kör en pilotversion med en liten uppsättning användare
När du testar med en administratör tvingar vi principen för återställning av administratörslösenord, som definieras nedan.  Det innebär att du INTE ser de förväntade resultaten av den princip som du har konfigurerat för dina slutanvändare.

Principerna som konfigureras i det administrativa UX gäller ENBART för slutanvändare, och inte administratörer. Microsoft använder starka standardprinciper för lösenordsåterställning för dina administratörer – som kan skilja sig från de principer som du angett för dina slutanvändare – för att se till att din organisation hålls skyddad.

#### <a name="administrator-password-reset-policy"></a>Återställningsprincip för administratörslösenord
* **Gäller för** – alla administratörsroller (global administratör, supportavdelningens administratör, lösenordsadministratör osv.)
* **Enportsprincipen gäller...**
 * ... under de första 30 dagarna efter att en utvärderingsversion har startats skapats **ELLER**
 * ... om en anpassad domän inte finns **OCH** identiteter inte synkroniseras med Azure AD Connect
 * **_Kräver_**: att **något av alternativen** e-postadress för autentisering, alternativ e-postadress, telefonnummer för autentisering, mobiltelefon eller arbetstelefon har ett värde
* **Tvåportsprincipen gäller...** 
 * ... när de första 30 dagarna av en utvärdering har passerat **ELLER**
 * ... när en anpassad domän finns **ELLER** 
 * ... du har aktiverat Azure AD Connect för att synkronisera identiteter från din lokala miljö
 * _**Kräver**_: att **två av alternativen** e-postadress för autentisering, alternativ e-postadress, telefon för autentisering, mobiltelefon eller arbetstelefon har ett värde

### <a name="tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register"></a>Tips 3: DISTRIBUTION – Fyll i data i förväg för dina användare så att de inte behöver registrera sig
Många inser inte att användarna inte behöver registrera sig för lösenordsåterställning för att kunna använda funktionen.  Genom att ange telefon eller e-postegenskaper för användarna i förväg kan du omedelbart distribuera lösenordsåterställning för hela organisationen **utan att användarna behöver göra något!**

Läs igenom dokumentationen nedan om du vill veta hur du gör detta med hjälp av ett API, PowerShell eller Azure AD Connect:
* [Distribuera lösenordsåterställning utan att användarna behöver registrera sig](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [Vilka data som används vid återställning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords"></a>Tips 4: DISTRIBUTION – Använd lösenordsåterställning så behöver du inte kommunicera tillfälliga lösenord
Det här är en fortsättning på tips 3. När du har förkonfigurerat användarna för lösenordsåterställning, tänk dig ett scenarion där en medarbetare anställs vid företaget för första gången. I stället för att kommunicera det tillfälliga lösenordet åt honom eller henne kan du nu bara låta personen navigera till [portalen för Azure AD-lösenordsåterställning](https://passwordreset.microsoftonline.com) så att de kan återställa sina lösenord.

Om användaren använder en [Windows 10 Azure AD-domänansluten enhet](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) kan han eller hon även skaffa den här behörigheten från Windows 10 Out of Box-inloggningsskärmen och få åtkomst till en helt ny PC utan att du behöver göra någonting.

Läs igenom dokumentationen nedan om du vill veta hur du gör detta med hjälp av ett API, PowerShell eller Azure AD Connect. Om du fyller i uppgifterna i förväg kan du bara uppmana dina användare att återställa sina lösenord, så får de tillgång till sina konton direkt:
* [Distribuera lösenordsåterställning utan att användarna behöver registrera sig](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [Vilka data som används vid återställning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback"></a>Tips 5: TILLBAKASKRIVNING – Titta på programmets händelselogg på AAD Connect-datorn om du vill felsöka tillbakaskrivning av lösenord
Azure AD Connect-programhändelseloggen innehåller en omfattande uppsättning loggningsinformation som beskriver mycket av vad som händer med tjänsten för tillbakaskrivning av lösenord i realtid. Följ stegen nedan för att få åtkomst till den här loggen:

1. Logga in på din **Azure AD Connect**-dator
2. Öppna den **Loggboken i Windows** genom att trycka på **Start** och skriva **”Loggboken”**
3. Öppna händelseloggen **Program**
4. Leta efter händelser från följande källor: **PasswordResetService** eller **ADSync** för att ta reda på mer om vilket problem det kan handla om

Se en fullständig lista över händelser som kan visas i den här loggen samt mycket mer felsökningsinformation för tillbakaskrivning av lösenord:
* [Felsök: tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [Felkoder för tillbakaskrivning av händelseloggen](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [Felsök: anslutning för tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [Distribution av tillbakaskrivning – Steg 3: Konfigurera brandväggen](#step-3-configure-your-firewall)
* [Distribution av tillbakaskrivning – Steg 4: Konfigurera lämpliga behörigheter](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback"></a>Tips 6: TILLBAKASKRIVNING – Kontrollera att du aktiverar rätt behörigheter, brandväggsregler och anslutningsinställningar för tillbakaskrivning av lösenord
För att tillbakaskrivning ska fungera korrekt måste du kontrollera följande:

1. Rätt **Active Directory-behörigheter** har angetts för användare som använder funktionen för tillbakaskrivning av lösenord så att de har behörighet att ändra sina egna lösenord och kontoupplåsningsflaggor i AD
2. Rätt **portar i brandväggen** har öppnats för att tillåta tjänsten för tillbakaskrivning av lösenord att kommunicera säkert med världen utanför via en utgående anslutning
3. Rätt **undantag i brandväggen** har gjorts för återställning av nyckellösenord till tjänst-URL: er, till exempel Service Bus
4. Din **proxy och brandvägg avbryter inte inaktiva utgående anslutningar**, vi rekommenderar 10 minuter eller längre

Se en fullständig lista över felsökningsvägledning och specifika riktlinjer för att konfigurera behörigheter och brandväggsregler för tillbakaskrivning av lösenord:
* [Felsök: tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [Felkoder för tillbakaskrivning av händelseloggen](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [Felsök: anslutning för tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [Distribution av tillbakaskrivning – Steg 3: Konfigurera brandväggen](#step-3-configure-your-firewall)
* [Distribution av tillbakaskrivning – Steg 4: Konfigurera lämpliga behörigheter](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs"></a>Tips 7: RAPPORTERING – Se vem som registrerar eller återställer lösenord med Azure AD SSPR-granskningsloggar 
När lösenordsåterställningen har distribuerats och fungerar är nästa logiska steg att se hur den fungerar och analysera vem som fortfarande behöver registreras, de vanligaste problem som användarna stöter på när de återställer och avkastningen på investeringen för funktionen.

Med granskningsloggarna för Azure AD-lösenordsåterställning kan du göra detta och mycket mer från Azure Portal, PowerBI, Azure AD Reporting Event-API eller PowerShell.  Mer information om hur du använder dessa rapporteringsfunktioner finns i:
* [Översikt över rapporter om lösenordshantering](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#overview-of-password-management-reports)
* [Så här visar du lösenordshanteringsrapporter i Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-view-password-management-reports)
* [Självbetjäning av aktivitetstyper för lösenordshantering i Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#self-service-password-management-activity-types-in-the-new-azure-portal)
* [Så här hämtar du lösenordshanteringshändelser från Azure AD-rapporter och händelse-API](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
* [Så här hämtar du snabbt registreringshändelser för lösenordsåterställning med PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-download-password-reset-registration-events-quickly-with-powershell)

### <a name="tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues"></a>Tips 8: FELSÖKNING – Läs vår felsökningsguide och vanliga frågor och svar så kan du lösa många problem
Visste du att lösenordsåterställning har en omfattande uppsättning felsökningsvägledning och svar på vanliga frågor? Det finns goda chanser att du hittar svaret på eventuella frågor via länkarna nedan.

Utöver detta kan du också använda bladet **Support & Troubleshooting** (Support och felsökning) i [Azure Portal](https://portal.azure.com) och få en omfattande uppsättning felsökningsinnehåll direkt från administratörsanvändarens lösenordshantering som finns under **Azure Active Directory** -> **Användare och grupper** -> **Lösenordsåterställning** -> **Support och felsökning** i det vänstra navigeringsfönstret.

Länkar till felsökningsvägledning samt vanliga frågor och svar om återställning av lösenord:
* [Felsök lösenordshantering](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot)
* [Vanliga frågor och svar om lösenordshantering](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-faq)

### <a name="tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you"></a>Tips 9: FELSÖKNING – Om du fortfarande behöver hjälp är det viktigt att du tar med tillräckligt med information så att vi kan hjälpa dig
Om du fortfarande behöver hjälp med felsökning finns vi här för dig. Du kan antingen öppna ett supportärende eller kontakta kontohanteringsteamet för att nå till oss direkt. Vi vill gärna höra vad du har att säga!

Men innan du tar kontakt bör du kontrollera att **du har samlat in all information som begärs nedan** så att vi kan hjälpa dig snabbt!
* [Information som ska inkluderas om du behöver hjälp](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#information-to-include-when-you-need-help)

#### <a name="ways-to-provide-password-reset-feedback"></a>Sätt att ge feedback för lösenordsåterställning
* [Funktionsbegäranden eller felsökning – posta på Azure AD MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)
* [Funktionsbegäranden eller felsökning – post på StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
* [Funktionsbegäranden eller felsökning – Tweet @azuread!](https://twitter.com/azuread)
* [Funktionsbegäranden enbart – Lämna en kommentar på UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory)

## <a name="enable-users-to-reset-their-azure-ad-passwords"></a>Konfigurera systemet så att användarna kan återställa sina Azure AD-lösenord
Det här avsnittet beskriver hur du aktiverar lösenordsåterställning via självbetjäning för din AAD-molnkatalog, hur du registrerar användare för lösenordsåterställning via självbetjäning och hur du testar en lösenordsåterställning via självbetjäning som en användare.

* [Krav för lösenordsåterställning via självbetjäning](#prerequisites)
* [Steg 1: Konfigurera en princip för återställning av lösenord](#step-1-configure-password-reset-policy)
* [Steg 2: Lägga till kontaktdata för testanvändaren](#step-2-add-contact-data-for-your-test-user)
* [Steg 3: Återställa ditt lösenord som en användare](#step-3-reset-your-azure-ad-password-as-a-user)

### <a name="prerequisites"></a>Krav
Innan du kan aktivera och använda lösenordsåterställning via självbetjäning måste du uppfylla följande krav:

* Skapa en AAD-klient. Mer information finns i [Komma igång med Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/)
* Skaffa en Azure-prenumeration. Mer information finns i [Vad är en Azure AD-klient?](active-directory-administer.md#what-is-an-azure-ad-tenant).
* Koppla din AAD-klient till din Azure-prenumeration. Mer information finns i [Hur Azure-prenumerationer är associerade med Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).
* Uppgradera till Azure AD Premium eller Basic eller använd en O365-betallicens. Mer information finns i [Azure Active Directory-versioner](https://azure.microsoft.com/pricing/details/active-directory/).

  > [!NOTE]
  > Om du vill aktivera återställning av lösenord via självbetjäning för dina molnanvändare måste du uppgradera till Azure AD Premium, Azure AD Basic eller en betald O365-licens.  Om du vill aktivera återställning av lösenord via självbetjäning för dina lokala användare måste du uppgradera till Azure AD Premium. Mer information finns i [Azure Active Directory-versioner](https://azure.microsoft.com/pricing/details/active-directory/). Den här informationen innehåller detaljerade anvisningar för hur du registrerar dig för Azure AD Premium eller Basic, hur du aktiverar din licensplan och din åtkomst till Azure AD samt hur du tilldelar åtkomst till administratörs- och användarkonton.
  >
  >
* Skapa minst ett administratörskonto och ett användarkonto i AAD-katalogen.
* Tilldela en licens för AAD Premium eller Basic eller en O365-betallicens till administratörs- och användarkontot som du skapat.

### <a name="step-1-configure-password-reset-policy"></a>Steg 1: Konfigurera en princip för återställning av lösenord
Konfigurera en princip för lösenordsåterställning för användare genom att följa dessa steg:

1. Öppna valfri webbläsare och gå till den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. I den [klassiska Azure-portalen](https://manage.windowsazure.com), letar du upp **Active Directory-tillägget** i navigeringsfältet till vänster.

   ![Lösenordshantering i Azure AD][001]
3. Under fliken **Katalog** klickar du på den katalog där du vill konfigurera principen för lösenordsåterställning för användare, till exempel Wingtip Toys.

    ![][002]
4. Klicka på fliken **Konfigurera**.

   ![][003]

5. Under fliken **Konfigurera** bläddrar du ned till avsnittet **Princip för lösenordsåterställning för användare**.  Här konfigurerar du alla aspekter av principen för lösenordsåterställning för användare för en viss katalog. *Om du inte ser fliken Konfigurera kontrollerar du att du har registrerat dig för Azure Active Directory Premium eller Basic och har __tilldelat en licens__ till administratörskontot som konfigurerar den här funktionen.*  

   > [!NOTE]
   > **Principen du konfigurerar gäller bara för slutanvändare i din organisation, inte administratörer**. Av säkerhetsskäl kontrollerar Microsoft principen för lösenordsåterställning för administratörer. Den aktuella principen för administratörer kräver två utmaningar – mobiltelefon och e-postadress.

   ![][004]
6. Du konfigurerar principen för lösenordsåterställning för användare genom att dra växlingsknappen **Användare som har aktiverats för lösenordsåterställning** till inställningen **Ja**.  Nu visas fler kontroller som du kan använda för att konfigurera hur den här funktionen fungerar i din katalog.  Anpassa lösenordsåterställningen som det passar dig.  Om du vill ha mer information om vad de olika kontrollerna för principer för lösenordsåterställning gör läser du [Anpassa: Lösenordshantering i Azure AD](active-directory-passwords-customize.md).

   ![][005]
7. När du har konfigurerat principen för lösenordsåterställning för användare för din klient klickar du på **Spara** längst ned på skärmen.

   > [!NOTE]
   > En princip för lösenordsåterställning med dubbel kontroll rekommenderas så att du ser hur funktionen fungerar i det mest komplexa fallet.
   >
   >

   ![][006]

   > [!NOTE]
   > **Principen du konfigurerar gäller bara för slutanvändare i din organisation, inte administratörer**. Av säkerhetsskäl kontrollerar Microsoft principen för lösenordsåterställning för administratörer. Den aktuella principen för administratörer kräver två utmaningar – mobiltelefon och e-postadress.
   >
   >

### <a name="step-2-add-contact-data-for-your-test-user"></a>Steg 2: Lägga till kontaktdata för testanvändaren
Du kan välja mellan flera alternativ när du ska lägga till data för användare i organisationen som ska användas för lösenordsåterställning.

* Redigera användare i den [klassiska Azure-portalen](https://manage.windowsazure.com) eller [Office 365-administrationsportalen](https://portal.microsoftonline.com)
* Använd AAD Connect för att synkronisera användaregenskaper till Azure AD från en lokal Active Directory-domän.
* Redigera användaregenskaper med hjälp av Windows PowerShell.
* Tillåt att användare registrerar sina egna data genom att uppmana dem att gå till registreringsportalen på [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup).
* Kräv att användarna registrerar sig för lösenordsåterställning när de loggar in på åtkomstpanelen på [http://myapps.microsoft.com](http://myapps.microsoft.com) genom att ändra SSPR-konfigurationsalternativet **Kräv att användare registrerar sig när de loggar in på åtkomstpanelen** till **Ja**.

Om du vill lära dig mer om vilka data som används för lösenordsåterställning, och eventuella formateringskrav för dessa data, läser du [Vilka data används av lösenordsåterställning?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### <a name="to-add-user-contact-data-via-the-user-registration-portal"></a>Så här lägger du till kontaktdata för användare via användarregistreringsportalen
1. Om användarna i din organisation ska kunna använda registreringsportalen för lösenordsåterställning måste du ge dem en länk till den här sidan ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) eller aktivera alternativet som kräver att användarna registrerar sig.  När användarna klickar på den här länken uppmanas de att logga in med organisationskontot.  När de har gjort det visas följande sida:

   ![][007]
2. Här kan användarna uppge och verifiera sina mobiltelefoner, alternativa e-postadresser eller säkerhetsfrågor.  Så här ser det ut när en användare verifierar sin mobiltelefon.

   ![][008]
3. När en användare har uppgett den här informationen uppdateras sidan för att ange att informationen är giltig (den har dolts nedan).  När användaren klickar på **Slutför** eller **Avbryt** öppnas åtkomstpanelen.

   ![][009]
4. När en användare har verifierat båda dessa uppgifter uppdateras användarens profil med de data som han eller hon har angett.  Eftersom **Arbetstelefon** har angetts manuellt i det här exemplet så kan även detta användas för att återställa användarens lösenord.

   ![][010]

### <a name="step-3-reset-your-azure-ad-password-as-a-user"></a>Steg 3: Återställa ditt Azure AD-lösenord som en användare
Nu när du har konfigurerat en återställningsprincip för användare och angett kontaktuppgifterna för användaren kan användaren utföra en lösenordsåterställning via självbetjäning.

#### <a name="to-perform-a-self-service-password-reset"></a>Så här utför du en lösenordsåterställning via självbetjäning
1. Om du går till en webbplats som [**portal.microsoftonline.com**](http://portal.microsoftonline.com) visas en inloggningsskärm som den nedan.  Klicka på länken **Kan du inte komma åt ditt konto?** för att testa användargränssnittet för lösenordsåterställning.

   ![][011]
2. När du klickar på **Kan du inte komma åt ditt konto?** kommer du till en ny sida där du uppmanas att ange ett **användar-ID** som du vill återställa ett lösenord för.  Ange ditt **testanvändar-ID** här, utför captcha-verifieringen och klicka på **Nästa**.

   ![][012]
3. Eftersom användaren har angett en **arbetstelefon**, en **mobiltelefon** och en **alternativ e-postadress** i vårt exempel så visas alla dessa uppgifter som alternativ för den första kontrollen.

   ![][013]
4. I vårt exempel väljer du att **ringa upp** **arbetstelefonen** först.  När användare väljer en telefonbaserad metod uppmanas de att **verifiera sina telefonnummer** innan de kan återställa sina lösenord.  Avsikten med detta är att förhindra att obehöriga användare spammar telefonnumren till användare i din organisation.

   ![][014]
5. När användaren bekräftar sitt telefonnummer och klickar på Ring upp visas en rotationsruta och hans eller hennes telefon ringer.  Ett meddelande spelas upp när användaren tar upp luren och uppmanar **användaren att trycka på ”#”** för att verifiera sitt konto.  När användaren trycker på den här knappen verifieras användaren i den första kontrollen och tas automatiskt till nästa verifieringssteg.

   ![][015]
6. När användaren har klarat den första kontrollen uppdateras användargränssnittet automatiskt och kontrollen tas bort från listan med tillgängliga alternativ.  I vårt exempel, eftersom du valde **Arbetstelefon** först, finns bara **Mobiltelefon** och **Alternativ e-postadress** kvar som giltiga alternativ för verifiering i det andra verifieringssteget.  Klicka på e-postalternativet **Skicka e-post till min alternativa e-postadress**.  När du har gjort det och trycker på Skicka-knappen skickas ett e-postmeddelande till den registrerade alternativa e-postadressen.

   ![][016]
7. Här är ett exempel på ett e-postmeddelande som skickas till användaren – observera företagsanpassningen:

   ![][017]
8. När e-postmeddelandet tas emot uppdateras sidan och du kan ange verifieringskoden från e-postmeddelandet i inmatningsrutan som visas nedan.  När rätt kod har angetts börjar nästa knapp lysa och du kan slutföra det andra verifieringssteg.

   ![][018]
9. När du har uppfyllt kraven för organisationens lösenordsprincip kan du välja ett nytt lösenord.  Lösenordet godkänns beroende på om det uppfyller kraven för ”starka” AAD-lösenord (se [Lösenordsprincip i Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx)). En kontroll för lösenordsstyrka visas och anger om det angivna lösenordet uppfyller kraven i principen eller inte.

   ![][019]
10. När du har angett matchande lösenord som uppfyller organisationens princip återställs ditt lösenord. Du kan logga in med det nya lösenordet direkt.

    ![][020]

## <a name="enable-users-to-reset-or-change-their-ad-passwords"></a>Konfigurera systemet så att användarna kan återställa eller ändra sina AD-lösenord
Det här avsnittet beskriver hur du konfigurerar lösenordsåterställning för tillbakaskrivning av lösenord till en lokal Active Directory.

* [Krav för tillbakaskrivning av lösenord](#writeback-prerequisites)
* [Steg 1: Ladda ned den senaste versionen av Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
* [Steg 2: Aktivera tillbakaskrivning av lösenord i Azure AD Connect via användargränssnittet eller PowerShell och verifiera](#step-2-enable-password-writeback-in-azure-ad-connect)
* [Steg 3: Konfigurera brandväggen](#step-3-configure-your-firewall)
* [Steg 4: Konfigurera lämpliga behörigheter](#step-4-set-up-the-appropriate-active-directory-permissions)
* [Steg 5: Återställa ditt AD-lösenord som en användare och verifiera](#step-5-reset-your-ad-password-as-a-user)

### <a name="writeback-prerequisites"></a>Krav för tillbakaskrivning
Innan du kan aktivera och använda tillbakaskrivning av lösenord måste du kontrollera att du uppfyller följande krav:

* Du har en Azure AD-klient med Azure AD Premium aktiverat.  Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md).
* Du måste ansluta Azure AD Connect till den primära domänkontrollantemulatorn för att tillbakaskrivningen av lösenord ska fungera.  Om du behöver kan du konfigurera Azure AD Connect för användning av den primära domänkontrollanten genom att högerklicka på **egenskaperna** för Active Directory-anslutningsappen för synkronisering och sedan välja **konfigurera katalogpartitioner**. Därifrån kan du söka efter avsnittet med **anslutningsinställningarna för domänkontrollanten** och markera rutan för att **endast använda prioriterade domänkontrollanter**.  Obs! Azure AD Connect kontaktar PDC för tillbakaskrivning av lösenord, även om den prioriterade domänkontrollanten inte är en PDC-emulator.
* Återställning av lösenord har konfigurerats och aktiverats i din klient.  Mer information finns i [Konfigurera systemet så att användarna kan återställa sina Azure AD-lösenord](#enable-users-to-reset-their-azure-ad-passwords)
* Du har minst ett administratörskonto och ett testanvändarkonto med en Azure AD Premium-licens som du kan använda för att testa den här funktionen.  Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md).

  > [!NOTE]
  > Se till att det administratörskonto som du använder för att aktivera tillbakaskrivning av lösenord är ett molnadministratörskonto (skapat i Azure AD), inte ett federerat konto (skapat i lokala AD och synkroniserat till Azure AD).
  >
  >
* Du har en lokal AD-distribution med en enda eller flera skogar som kör Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 eller Windows Server 2012 R2 med de senaste service pack-versionerna installerade.

  > [!NOTE]
  > Om du kör en äldre version av Windows Server 2008 och 2008 R2 kan du fortfarande använda den här funktionen, men du måste [ladda ned och installera KB 2386717](https://support.microsoft.com/kb/2386717) innan du kan genomdriva din lokala AD-lösenordsprincip i molnet.
  >
  >
* Azure AD Connect-verktyget är installerat och du har förberett AD-miljön för synkronisering till molnet.  Mer information finns i [Använda din lokala identitetsinfrastruktur i molnet](connect/active-directory-aadconnect.md).

  > [!NOTE]
  > Innan du testar tillbakaskrivning av lösenord bör du slutföra en fullständig import och en fullständig synkronisering från både AD och Azure AD i Azure AD Connect.
  >
  >
* Om du använder Azure AD Sync eller Azure AD Connect måste **TCP 443** för utgående trafik (och i vissa fall **TCP 9350 9354**) vara öppen.  Mer information finns i [Steg 3: Konfigurera brandväggen](#step-3-configure-your-firewall). Du kan inte längre använda DirSync för det här scenariot.  Om du fortfarande använder DirSync uppgraderar du till den senaste versionen av Azure AD Connect innan du distribuerar tillbakaskrivning av lösenord.

  > [!NOTE]
  > Vi rekommenderar starkt att alla som använder Azure AD Sync eller DirSync uppgraderar till den senaste versionen av Azure AD Connect för att få bästa möjliga upplevelse och tillgång till nya funktioner efter hand som de ges ut.
  >
  >

### <a name="step-1-download-the-latest-version-of-azure-ad-connect"></a>Steg 1: Ladda ned den senaste versionen av Azure AD Connect
Tillbakaskrivning av lösenord är tillgängligt i versioner av Azure AD Connect eller i Azure AD Sync med versionsnummer **1.0.0419.0911** eller högre.  Tillbakaskrivning av lösenord med automatisk kontoupplåsning är tillgängligt i versioner av Azure AD Connect eller i Azure AD Sync med versionsnummer **1.0.0485.0222** eller högre. Om du kör en äldre version uppgraderar du till minst den här versionen innan du fortsätter. [Klicka här om du vill ladda ned den senaste versionen av Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect).

#### <a name="to-check-the-version-of-azure-ad-sync"></a>Så här kontrollerar du versionen av Azure AD Sync
1. Gå till **%ProgramFiles%\Azure Active Directory Sync\**.
2. Leta upp den körbara filen **ConfigWizard.exe**.
3. Högerklicka på den körbara filen och välj alternativet **Egenskaper** på snabbmenyn.
4. Klicka på fliken **Information**.
5. Leta upp fältet **Filversion**.

   ![][021]

Om det här numret är större än eller lika med **1.0.0419.0911**, eller om du installerar Azure AD Connect så kan du gå vidare till [Steg 2: Aktivera tillbakaskrivning av lösenord i Azure AD Connect via användargränssnittet eller PowerShell och verifiera](#step-2-enable-password-writeback-in-azure-ad-connect).

> [!NOTE]
> Om det här är första gången som du installerar verktyget Azure AD Connect rekommenderar vi att du följer några metodtips för att förbereda din miljö för katalogsynkronisering.  Innan du installerar Azure AD Connect-verktyget så måste du aktivera katalogsynkronisering antingen i [Office 365-administrationsportalen](https://portal.microsoftonline.com) eller den [klassiska Azure-portalen](https://manage.windowsazure.com).  Mer information finns i [Hantera Azure AD Connect](active-directory-aadconnect-whats-next.md).
>
>

### <a name="step-2-enable-password-writeback-in-azure-ad-connect"></a>Steg 2: Aktivera tillbakaskrivning av lösenord i Azure AD Connect
Nu när du har laddat ned verktyget Azure AD Connect kan du aktivera tillbakaskrivning av lösenord.  Du kan göra det på två sätt.  Du kan antingen aktivera tillbakaskrivning av lösenord på skärmen för valfria funktioner i Azure AD Connect-konfigurationsguiden eller via Windows PowerShell.

#### <a name="to-enable-password-writeback-in-the-configuration-wizard"></a>Så här aktivera du tillbakaskrivning av lösenord i konfigurationsguiden
1. Öppna konfigurationsguiden för **Azure AD Connect** på **datorn för katalogsynkronisering**.
2. Klicka dig igenom stegen tills du kommer till konfigurationsskärmen för **valfria funktioner**.
3. Markera alternativet **Tillbakaskrivning av lösenord**.

   ![][022]
4. Slutför guiden. På den sista sidan sammanfattas ändringarna, inklusive konfigurationsändringen för tillbakaskrivning av lösenord.

> [!NOTE]
> Du kan inaktivera tillbakaskrivning av lösenord när som helst genom att antingen köra guiden igen och avmarkera funktionen eller genom att ändra inställningen **Skriv tillbaka lösenord till lokal katalog** till **Nej** i avsnittet **Princip för lösenordsåterställning för användare** på fliken **Konfigurera** för din katalog på den [klassiska Azure-portalen](https://manage.windowsazure.com).  Mer information om hur du anpassar lösenordsåterställningsmiljön finns i [Anpassa: Lösenordshantering i Azure AD](active-directory-passwords-customize.md).
>
>

#### <a name="to-enable-password-writeback-using-windows-powershell"></a>Så här aktiverar du tillbakaskrivning av lösenord med Windows PowerShell
1. Öppna ett nytt **upphöjt Windows PowerShell-fönster** på **datorn för katalogsynkronisering**.
2. Om modulen inte redan har lästs in skriver du `import-module ADSync`-kommandot för att läsa in modulen med Azure AD Connect-cmdlets i den aktuella sessionen.
3. Hämta listan med Azure AD-anslutningsappar i systemet genom att köra `Get-ADSyncConnector`-cmdleten och spara resultaten i `$aadConnectorName`, som `$aadConnectorName = Get-ADSyncConnector|where-object {$_.name -like "*AAD"}`
4. Hämta tillbakaskrivningsstatusen för den aktuella anslutningen genom att köra följande cmdlet:`Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name`
5. Aktivera tillbakaskrivning av lösenord genom att köra cmdleten:`Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name –Enable $true`

> [!NOTE]
> Om du uppmanas att ange autentiseringsuppgifter ser du till att administratörskontot som du anger för AzureADCredential är ett **molnadministratörskonto (som skapats i Azure AD)**, inte ett federerat konto (som skapats i lokala AD och som synkroniseras med Azure AD).
>
> [!NOTE]
> Du kan inaktivera tillbakaskrivning av lösenord via PowerShell genom att upprepa samma instruktioner som ovan men i stället skicka `$false` i steget eller genom att ändra inställningen **Skriv tillbaka lösenord till lokal katalog** till **Nej** i avsnittet **Princip för lösenordsåterställning för användare** på fliken **Konfigurera** för din katalog på den [klassiska Azure-portalen](https://manage.windowsazure.com).
>
>

#### <a name="verify-that-the-configuration-was-successful"></a>Kontrollera att konfigurationen lyckades
Om konfigurationen lyckades visas ett meddelande i Windows PowerShell-fönstret eller i konfigurationsgränssnittet som anger att tillbakaskrivning av lösenord har aktiverats eller att åtgärden lyckades.

Du kan också kontrollera att tjänsten har installerats korrekt genom att öppna Loggboken, navigera till programmets händelselogg och leta upp händelsen **31005 – OnboardingEventSuccess** från källan **PasswordResetService**.

  ![][023]

### <a name="step-3-configure-your-firewall"></a>Steg 3: Konfigurera brandväggen
När du har aktiverat tillbakaskrivning av lösenord måste du kontrollera att den dator som kör Azure AD Connect kan nå Microsofts molntjänster och ta emot förfrågningar för tillbakaskrivning av lösenord. Det här steget inbegriper uppgradering av anslutningsreglerna för dina nätverksinstallationer (proxyservrar, brandväggar osv.), vilka tillåter utgående anslutningar till vissa [webbadresser och IP-adresser som ägs av Microsoft](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US) via vissa specifika nätverksportar. Dessa ändringar kan variera beroende på vilken version av Azure AD Connect-verktyget som du använder. Om du vill ha mer sammanhang kan du läsa [Hur tillbakaskrivning av lösenord fungerar](active-directory-passwords-learn-more.md#how-password-writeback-works) och [Säkerhetsmodell för tillbakaskrivning av lösenord](active-directory-passwords-learn-more.md#password-writeback-security-model).

#### <a name="why-do-i-need-to-do-this"></a>Varför måste jag göra detta?

För att tillbakaskrivning av lösenord ska fungera korrekt måste datorn som kör Azure AD Connect kunna kommunicera med tjänsten för återställning av lösenord och med Azure Service Bus.

För Azure AD Connect **1.1.443.0** och senare:

- Den senaste versionen av verktyget Azure AD Connect behöver åtkomst till **utgående HTTPS** till:
    - *passwordreset.microsoftonline.com*
    - *servicebus.Windows.NET*

För versionerna **1.0.8667.0** till **1.1.380.0** av verktyget Azure AD Connect:

- **Alternativ 1:** Tillåt utgående HTTPS-anslutningar över port 443 (med URL-adress eller IP-adress).
    - När du ska använda detta:
        - Använd det här alternativet om du vill ha den enklaste konfiguration som inte behöver uppdateras i takt med att Azure Datacenter IP-intervallen ändras över tid.
    - Nödvändiga steg:
        - Tillåt utgående HTTPS-anslutningar över port 443 (med URL-adress eller IP-adress).
<br><br>
- **Alternativ 2:** Tillåt utgående HTTPS-anslutningar till specifika IP-intervall och URL: er
    - När du ska använda detta:
        - Använd det här alternativet om du befinner dig i en begränsad nätverksmiljö, eller av någon annan anledning inte känner dig bekväm med att tillåta utgående anslutningar.
        - Om du vill att tillbakaskrivning av lösenord ska fortsätta att fungera i den här konfigurationen, måste du se till att dina nätverksresurser hålls uppdaterade varje vecka med de senaste IP-adresserna från listan över IP-intervall i Microsoft Azure Datacenter. Dessa IP-intervall som är tillgängliga som en XML-fil som uppdateras varje onsdag (Stillahavstid) och träder i kraft följande måndag (Stillahavstid).
    - Nödvändiga steg:
        - Tillåt alla utgående HTTPS-anslutningar till *. servicebus.windows.net
        - Tillåt alla utgående HTTPS-anslutningar till alla IP-adresser i listan över IP-intervall i Microsoft Azure Datacenter och håll den här konfigurationen uppdaterad varje vecka. Du kan ladda ned listan [här](https://www.microsoft.com/download/details.aspx?id=41653).

> [!NOTE]
> Om du har konfigurerat tillbakaskrivning av lösenord genom att följa anvisningarna ovan och inte ser några fel i händelseloggen för Azure AD Connect, men får anslutningsfel när du testar, så kan det t.ex. bero på att någon nätverksutrustning i din miljö blockerar HTTPS-anslutningar till IP-adresser. Samtidigt som t.ex. en anslutning till *https://*. servicebus.windows.net* tillåts, så kan en anslutning till en specifik IP-adress inom intervallet blockeras. Om du ska kunna lösa det här problemet måste du antingen konfigurera din nätverksmiljö så att den tillåter alla utgående HTTPS-anslutningar via port 443 till alla URL-eller IP-adresser (alternativ 1 ovan) eller så måste du arbeta med ditt nätverksteam och explicit tillåta HTTPS-anslutningar till vissa specifika IP-adresser (alternativ 2 ovan).

**För äldre versioner:**

- Tillåt utgående TCP-anslutningar via port 443, 9350-9354 och 5671
- Tillåt utgående anslutningar till *https://ssprsbprodncu-sb.accesscontrol.windows.net/*

> [!NOTE]
> Om du har en version av Azure AD Connect som är äldre än 1.0.8667.0 så rekommenderar Microsoft starkt att du uppgraderar till den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594). Den innehåller ett antal förbättringar vad gäller nätverkstillbakaskrivning, vilket underlättar konfigurationen.

När nätverksutrustningen har konfigurerats kan du starta om den dator där Azure AD Connect-verktyget körs.

#### <a name="idle-connections-on-azure-ad-connect-114430-and-up"></a>Inaktiva anslutningar i Azure AD Connect (1.1.443.0 och senare)
Verktyget Azure AD Connect skickar regelbundet pingar/keepalive-överföringar till ServiceBus-slutpunkterna så att anslutningarna förblir aktiva. Om verktyget identifierar att för många anslutningar avslutas så ökas automatiskt pingfrekvensen till slutpunkten. Det kortaste pingintervallet är 1 ping var 60:e sekund, men **vi rekommenderar att proxyservrar/brandväggar tillåter inaktiva anslutningar i minst 2–3 minuter.** \*För äldre versioner rekommenderar vi 4 minuter eller mer.

### <a name="step-4-set-up-the-appropriate-active-directory-permissions"></a>Steg 4: Konfigurera Active Directory-behörigheter
För varje skog som innehåller användare vars lösenord ska återställas, om X är det konto som angavs för skogen i konfigurationsguiden (vid den ursprungliga konfigurationen), måste X tilldelas följande utökade behörigheter för antingen rotobjektet i varje domän i skogen: **Återställ lösenord**, **Ändra lösenord**, **Skrivbehörighet** för `lockoutTime` och **Skrivbehörighet** för `pwdLastSet` ELLER användarens OU som du vill ska finnas med i omfånget för SSPR.  Du kan använda det senare alternativet om du vill att återställningsbehörigheten enbart ska omfatta en specifik uppsättning användarobjekt om det inte är möjligt att göra det mot domänroten. Rättigheten måste konfigureras att ärvas av alla användarobjekt.  

Om du inte är säker på vilket konto som avses ovan öppnar du konfigurationsgränssnittet för Azure Active Directory Connect och klickar på alternativet **Granska din lösning**.  Det konto som du ska lägga till behörigheter till är understruket i rött i skärmbilden nedan.

**<font color="red">Se till att du anger den här behörigheten för varje domän i varje skog i systemet, annars kommer tillbakaskrivning av lösenord inte att fungera korrekt.</font>**

  ![][032]

  När du anger dessa behörigheter kan MA-tjänstkontot för varje skog hantera lösenord för användarkonton i skogen. Om du inte tilldelar dessa behörigheter kanske det ser ut som tillbakaskrivning av lösenord är korrekt konfigurerat, men användarna kommer att få problem när de försöker hantera sina lokala lösenord från molnet. Här följer detaljerade anvisningar för hur du kan göra detta med hjälp av snapin-modulen **Active Directory -- användare och datorer**:

> [!NOTE]
> Det kan ta upp till en timme för behörigheterna att replikeras till alla objekt i katalogen.
>
>

#### <a name="to-set-up-the-right-permissions-for-writeback-to-occur"></a>Så här konfigurerar du rätt behörigheter för att tillbakaskrivningen ska fungera korrekt
1. Öppna **Active Directory -- användare och datorer** med ett konto som har nödvändig behörighet för domänadministration.
2. Kontrollera att **Avancerade funktioner** är aktiverat i alternativet på **Visa-menyn**.
3. Högerklicka på objektet som representerar roten för domänen i den vänstra rutan.
4. Klicka på fliken **Säkerhet**.
5. Klicka på **Avancerat**.

   ![][024]
6. Klicka på **Lägg till** på fliken **Behörighet**.

   ![][025]
7. Välj det konto som du vill lägga till behörigheter för (det är samma konto som angavs när synkroniseringen konfigurerades för skogen).
8. Välj **Underordnade objekt** i listrutan överst.
9. I dialogrutan **Behörighetspost** som visas markerar du kryssrutan för **Återställ lösenord**, **Ändra lösenord**, **Skrivbehörighet** för `lockoutTime` och **Skrivbehörighet** för `pwdLastSet`.

   ![][026]
   ![][027]
   ![][028]
10. Klicka på **Använd/OK** i alla öppna dialogrutor.

### <a name="step-5-reset-your-ad-password-as-a-user"></a>Steg 5: Återställa ditt AD-lösenord som en användare
Nu när tillbakaskrivning av lösenord har aktiverats kan du testa att det fungerar genom att återställa lösenordet för en användare vars konto har synkroniserats med din molnklient.

#### <a name="to-verify-password-writeback-is-working-properly"></a>Så här kontrollerar du att tillbakaskrivning av lösenord fungerar korrekt
1. Gå till [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) eller gå till valfri inloggningssida som kräver ditt organisations-ID och klicka på länken **Kan du inte komma åt ditt konto?**.

   ![][029]
2. Nu öppnas en ny sida som uppmanar dig att ange ett användar-ID vars lösenord du vill återställa. Ange ditt testanvändar-ID och gå igenom stegen för lösenordsåterställning.
3. När du har återställt lösenordet visas en skärm som den nedan. Det betyder att ditt lösenord har återställts i dina lokala kataloger och/eller molnkataloger.

   ![][030]
4. Kontrollera att åtgärden lyckades eller diagnostisera eventuella fel genom att gå till **katalogsynkroniseringsdatorn**, öppna **Loggboken**, navigera till **programhändelseloggen** och leta upp händelsen **31002 – PasswordResetSuccess** från källan **PasswordResetService** för din testanvändare.

   ![][031]



## <a name="next-steps"></a>Nästa steg
Nedan finns länkar till alla sidor med dokumentation om lösenordsåterställning i Azure AD:

* **Är du här eftersom du har problem med att logga in?** I så fall är det [här som du ser hur du kan ändra och återställa ditt eget lösenord](active-directory-passwords-update-your-own-password.md#reset-your-password).
* [**Så här fungerar det**](active-directory-passwords-how-it-works.md) – lär dig om de sex olika komponenterna i tjänsten och vad de gör
* [**Anpassa**](active-directory-passwords-customize.md) – lär dig hur du anpassar tjänstens utseende, känsla och beteende efter din organisations behov
* [**Metodtips**](active-directory-passwords-best-practices.md) – lär dig hur du snabbt distribuerar och effektivt hanterar lösenord i din organisation
* [**Få insikter**](active-directory-passwords-get-insights.md) – lär dig mer om våra integrerade rapporteringsfunktioner
* [**Vanliga frågor och svar**](active-directory-passwords-faq.md) – få svar på vanliga frågor
* [**Felsökning**](active-directory-passwords-troubleshoot.md) – lär dig hur du snabbt felsöker problem med tjänsten
* [**Lär dig mer**](active-directory-passwords-learn-more.md) – gräv djupare i de tekniska detaljerna för tjänsten

[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

