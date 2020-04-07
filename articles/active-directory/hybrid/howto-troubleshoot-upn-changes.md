---
title: Planera och felsöka ÄNDRINGAR i Azure User Principle name (UPN)
description: Förstå kända problem och mildrande åtgärder för UPN-ändringar
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11be1d971922095d4a1ace1c81c763134b4e58c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743336"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Planera och felsöka ändringar av användarnamn i Azure Active Directory

Ett UPN -namn (User Principal Name) är ett attribut som är en internetkommunikationsstandard för användarkonton. Ett UPN består av ett UPN-prefix (användarkontonamnet) och ett UPN-suffix (ett DNS-domännamn). Prefixet sammanfogar suffixet med symbolen "@". Till exempel someone@example.com. Ett UPN-nummer måste vara unikt bland alla huvudobjekt i en katalogskog. 

> [!NOTE]
> För utvecklare rekommenderar vi att du använder användarobjektID som oföränderlig identifierare i stället för UPN. Om dina program för närvarande använder UPN rekommenderar vi att du ställer in UPN så att den matchar användarens primära e-postadress för att förbättra deras upplevelse.<br> **I en hybridmiljö är det viktigt att UPN för en användare är identiskt i den lokala katalogen och i Azure Active Directory**.

**Den här artikeln förutsätter att du använder UPN som användaridentifierare. Den tar upp planering för UPN-ändringar och återställning från problem som kan uppstå till följd av UPN-ändringar.**

## <a name="learn-about-upns-and-upn-changes"></a>Läs mer om UPN- och UPN-ändringar
Inloggningssidor uppmanar ofta användarna att ange sin e-postadress när det önskade värdet faktiskt är deras UPN. Därför bör du vara säker på att ändra användarnas UPN när som helst deras primära e-postadress ändringar.

Användarnas primära e-postadresser kan ändras av många skäl:

* företag omprofilering

* anställda som flyttar till olika företagsdivisioner 

* fusioner och förvärv

* ändringar av medarbetarens namn

### <a name="types-of-upn-changes"></a>Typer av UPN-ändringar

Du kan ändra ett UPN genom att ändra prefixet, suffixet eller båda.

* **Ändra prefixet**.

   *  Om en persons namn till exempel ändras kan du ändra personens kontonamn:  
BSimon@contoso.com tillBJohnson@contoso.com

   * Du kan också ändra företagsstandarden för prefix:  
Bsimon@contoso.com tillBritta.Simon@contoso.com

* **Ändra suffixet**. <br>

    Om en person till exempel har ändrat divisioner kan du ändra deras domän: 

   * Britta.Simon@contoso.comAttBritta.Simon@contosolabs.com <br>
     Eller<br>
    * Britta.Simon@corp.contoso.comAttBritta.Simon@labs.contoso.com 

Ändra användarens UPN varje gång den primära e-postadressen för en användare uppdateras. Oavsett orsaken till e-poständringen måste UPN alltid uppdateras för att matcha.

Under den första synkroniseringen från Active Directory till Azure AD, se till att användarnas e-postmeddelanden är identiska med deras UPN.

### <a name="upns-in-active-directory"></a>UPN i Active Directory

I Active Directory är standardsuffixet UPN DNS-namnet på den domän där du skapade användarkontot. I de flesta fall är detta domännamnet som du registrerar som företagsdomän på Internet. Om du skapar användarkontot i domänen contoso.com är standardvärdet för UPN

username@contoso.com

 Du kan dock [lägga till fler UPN-suffix](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) med hjälp av Active Directory-domäner och förtroenden. 

Du kanske till exempel vill lägga till labs.contoso.com och låta användarnas UPN och e-post återspegla det. De skulle då bli

username@labs.contoso.com.

>[!IMPORTANT]
> Om UPN i Active Directory och Azure Active Directory inte matchar uppstår problem. Om du [ändrar suffixet i Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)måste du se till att ett matchande anpassat domännamn har lagts till och [verifierats på Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain). 

![En skärmbild av verifierade domäner](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPN i Azure Active Directory

Användare loggar in på Azure AD med värdet i attributet userPrincipalName. 

När du använder Azure AD tillsammans med din lokala Active Directory synkroniseras användarkonton med hjälp av Azure AD Connect-tjänsten. Som standard använder Azure AD Connect-guiden attributet userPrincipalName från den lokala Active Directory som UPN i Azure AD. Du kan ändra det till ett annat attribut i en anpassad installation.

Det är viktigt att du har en definierad process när du uppdaterar ett UPN-namn (User Principal Name) för en enskild användare eller för hela organisationen. 

Se kända problem och lösningar i det här dokumentet.

När du synkroniserar användarkonton från Active Directory till Azure AD ska du se till att UPN:erna i Active Directory mappas till verifierade domäner i Azure AD.

![Skärmbild av verifierade domäner](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Om värdet för attributet userPrincipalName inte motsvarar en verifierad domän i Azure AD ersätter synkroniseringsprocessen suffixet med ett standardvärde för onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Utrullning av UPN-massförändringar

Följ [metodtipsen för en pilot](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) för mass-UPN-ändringar. Har också en testad återställningsplan för att återställa UPN om du hittar problem som inte snabbt kan lösas. När piloten är igång kan du börja rikta in dig på små uppsättningar användare med olika organisationsroller och deras specifika uppsättningar av appar eller enheter.

Att gå igenom denna första delmängd av användare ger dig en god uppfattning om vad användarna bör förvänta sig som en del av förändringen. Inkludera den här informationen om din användarkommunikation.

Skapa en definierad procedur för att ändra UPN-nätverk för enskilda användare som en del av normala åtgärder. Vi rekommenderar att du har en testad procedur som innehåller dokumentation om kända problem och lösningar.

I följande avsnitt beskrivs potentiella kända problem och lösningar när UPN ändras.

## <a name="apps-known-issues-and-workarounds"></a>Kända problem och lösningar för appar

[Program som en tjänst (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) och Line of Business (LoB) applikationer förlitar sig ofta på UPN för att hitta användare och lagra information om användarprofiler, inklusive roller. Program som använder [Just in Time-etablering](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) för att skapa en användarprofil när användare loggar in på appen för första gången kan påverkas av UPN-ändringar.

**Känt problem**<br>
Om du ändrar en användares UPN kan relationen mellan Azure AD-användaren och användarprofilen som skapats i programmet brytas. Om programmet använder [Just in Time-etablering](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)kan det skapa en helt ny användarprofil. Detta kräver att programadministratören gör manuella ändringar för att åtgärda den här relationen.

**Workaround**<br>
[Azure AD Automated User Provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) kan du automatiskt skapa, underhålla och ta bort dina användaridentiteter i molnprogram som stöds. Om du konfigurerar automatisk användaretablering på dina program uppdateras automatiskt UPN-nätverk för programmen. Testa programmen som en del av den progressiva distributionen för att verifiera att de inte påverkas av UPN-ändringar.
Om du är utvecklare kan du [överväga att lägga till SCIM-stöd i ditt program](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) för att aktivera automatisk användaretablering från Azure Active Directory. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Kända problem och lösningar för hanterade enheter

Genom [att föra dina enheter till Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview)maximerar du användarnas produktivitet genom enkel inloggning (SSO) i molnet och lokala resurser.

### <a name="azure-ad-joined-devices"></a>Azure AD-anslutna enheter

[Azure AD-anslutna](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join) enheter ansluts direkt till Azure AD och tillåter användare att logga in på enheten med hjälp av organisationens identitet.

**Kända problem** <br>
Användare kan uppleva enstaka inloggningsproblem med program som är beroende av Azure AD för autentisering.

**Workaround** <br>
Tillåt tillräckligt med tid för UPN-ändringen att synkronisera med Azure AD. När du har verifierat att det nya UPN-programmet återspeglas på Azure AD-portalen ber du användaren att välja panelen "Annan användare" för att logga in med sitt nya UPN. Du kan också verifiera via [PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0). När du har loggat in med sitt nya UPN kan referenser till det gamla UPN fortfarande visas på Windows-inställningen "Access work or school".

![Skärmbild av verifierade domäner](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

[Hybrid Azure AD-anslutna](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid) enheter är anslutna till Active Directory och Azure AD. Du kan implementera Hybrid Azure AD-koppling om din miljö har ett lokalt Active Directory-fotavtryck och du också vill dra nytta av funktionerna som tillhandahålls av Azure AD.

**Kända problem** 

Windows 10 Hybrid Azure AD-anslutna enheter kommer sannolikt att drabbas av oväntade omstarter och åtkomstproblem.

Om användare loggar in i Windows innan det nya UPN-programmet har synkroniserats med Azure AD, eller fortsätter att använda en befintlig Windows-session, kan de uppleva engångsproblem med program som använder Azure AD för autentisering om villkorlig åtkomst har konfigurerats för att framtvinga användning av Hybrid-anslutna enheter för åtkomst till resurser. 

Dessutom visas följande meddelande, vilket tvingar fram en omstart efter en minut. 

"Datorn startas om automatiskt på en minut. Windows stötte på ett problem och måste starta om. Du bör stänga det här meddelandet nu och spara ditt arbete".

**Workaround** 

Enheten måste inte vara adinerad från Azure AD och startas om. Efter omstart kommer enheten automatiskt att ansluta till Azure AD igen och användaren måste logga in med den nya UPN genom att välja panelen "Annan användare". Om du vill ta upp en enhet från Azure AD kör du följande kommando i en kommandotolk:

**dsregcmd /lämna**

Användaren måste [registrera sig](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) igen för Windows Hello för företag om den används. Windows 7- och 8.1-enheter påverkas inte av det här problemet efter UPN-ändringar.

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Kända problem och lösningar i Microsoft Authenticator

Din organisation kan kräva att [Microsoft Authenticator-appen](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) används för att logga in och komma åt organisationsprogram och data. Även om ett användarnamn kan visas i appen är kontot inte inställt på att fungera som en verifieringsmetod förrän användaren har slutfört registreringsprocessen.

[Microsoft Authenticator-appen](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) har fyra huvudfunktioner:

* Multifaktorautentisering via ett push-meddelande eller verifieringskod

* Fungera som en autentiseringsmäklare på iOS- och Android-enheter för att tillhandahålla enkel inloggning för program som använder [brokered-autentisering](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)

* Enhetsregistrering (kallas även Arbetsplatsanslutning) till Azure AD, vilket är ett krav för andra funktioner som Intune App Protection och Enhetsregistrering/-hantering,

* Telefon logga in, vilket kräver MFA och enhetsregistrering.

### <a name="multi-factor-authentication-with-android-devices"></a>Multifaktorautentisering med Android-enheter

Microsoft Authenticator-appen erbjuder ett alternativ för out-of-band-verifiering. I stället för att skicka ett automatiskt telefonsamtal eller SMS till användaren under inloggningen skickar [MFA (Multi-Factor Authentication)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) ett meddelande till Microsoft Authenticator-appen på användarens smartphone eller surfplatta. Användaren trycker helt enkelt på Godkänn (eller anger en PIN-kod eller biometrisk och trycker på "Autentisera") i appen för att slutföra sin inloggning.

**Kända problem** 

När du ändrar en användares UPN visas fortfarande det gamla UPN på användarkontot och ett meddelande kanske inte tas emot. [Verifieringskoder](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq) fortsätter att fungera.

**Workaround**

Om ett meddelande tas emot instruerar du användaren att avvisa meddelandet, öppnar appen Autentiseringsperson, trycker på alternativet "Sök efter meddelanden" och godkänner MFA-prompten. Därefter uppdateras UPN som visas på kontot. Observera att det uppdaterade UPN kan visas som ett nytt konto, detta beror på att andra Authenticator-funktioner används. Mer information finns i ytterligare kända problem i den här artikeln.

### <a name="brokered-authentication"></a>Förmedlad autentisering

På Android- och iOS-mäklare som Microsoft Authenticator kan du aktivera:

* Enkel inloggning (SSO) - Användarna behöver inte logga in på varje program.

* Enhetsidentifiering - Mäklaren kommer åt enhetscertifikatet som skapades på enheten när den var ansluten på arbetsplatsen.

* Programidentifieringsverifiering - När ett program anropar mäklaren skickar den sin omdirigerings-URL och mäklaren verifierar den.

Dessutom kan program delta i mer avancerade funktioner som [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/)och stöder [Microsoft Intune-scenarier](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps).

**Kända problem**<br>
Användaren presenteras med mer interaktiva autentiseringsmeddelanden om nya program som använder mäklarassisterad inloggning på grund av en obalans mellan login_hint som skickas av programmet och UPN som lagras på mäklaren.

**Workaround** <br> Användaren måste manuellt ta bort kontot från Microsoft Authenticator och starta en ny inloggning från ett mäklarassisterat program. Kontot läggs automatiskt till efter den första autentiseringen.

### <a name="device-registration"></a>Enhetsregistrering

Microsoft Authenticator-appen ansvarar för att registrera enheten till Azure AD. Enhetsregistrering gör att enheten kan autentisera till Azure AD och är ett krav för följande scenarier:

* Intune App Protection

* Registrering av Intune-enhet

* Logga in via telefon

**Kända problem**<br>
När du ändrar UPN visas ett nytt konto med det nya UPN som visas i Microsoft Authenticator-appen, medan kontot med det gamla UPN fortfarande finns med i listan. Dessutom visas det gamla UPN-programmet i avsnittet Enhetsregistrering i appinställningarna. Det finns ingen förändring i den normala funktionaliteten i Enhetsregistrering eller beroende scenarier.

**Workaround** <br> Om du vill ta bort alla referenser till det gamla UPN-programmet i Microsoft Authenticator-appen instruerar du användaren att manuellt ta bort både gamla och nya konton från Microsoft Authenticator, registrera dig för MFA och ansluta till enheten igen.

### <a name="phone-sign-in"></a>Logga in via telefon

Med telefon inloggning kan användare logga in på Azure AD utan lösenord. För att aktivera telefonloggning måste användaren registrera sig för MFA med hjälp av appen Authenticator och sedan aktivera telefonloggning direkt på Authenticator. Som en del av konfigurationen registrerar enheten med Azure AD.

**Kända problem** <br>
Användare kan inte använda Telefon inloggning eftersom de inte får något meddelande. Om användaren trycker på Sök efter meddelanden får de ett felmeddelande.

**Workaround**<br>
Användaren måste välja den nedrullningsbara menyn på kontot som är aktiverad för Telefonloggning och välja Inaktivera telefonloggning. Om så önskas kan telefon inloggning aktiveras igen.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Kända problem och lösningar (Security Key) (FIDO2)

**Kända problem** <br>
När flera användare är registrerade på samma tangent visar inloggningsskärmen en kontovalssida där det gamla UPN visas. Inloggningar med säkerhetsnycklar påverkas inte av UPN-ändringar.  

**Workaround**<br>
Om du vill ta bort referenser till gamla UPN måste användarna [återställa säkerhetsnyckeln och registrera om](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>Kända problem och lösningar på OneDrive

OneDrive-användare är kända för att uppleva problem efter UPN-ändringar. Mer information finns i Så här [påverkar UPN-ändringarna OneDrive-URL:en och OneDrive-funktionerna](https://docs.microsoft.com/onedrive/upn-changes).

## <a name="next-steps"></a>Nästa steg

Se dessa resurser:
* [Azure AD Connect: Designkoncept](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Ifyllnad av UserPrincipalName för Azure AD](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Microsoft-identitetsplattforms-ID-token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
