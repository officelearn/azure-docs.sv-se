---
title: Använd aktivitetsrapporten för att flytta AD FS-appar till Azure Active Directory | Microsoft Dokument"
description: Med AD FS-programaktivitetsrapporten (Active Directory Federation Services) kan du snabbt migrera program från AD FS till Azure Active Directory (Azure AD). Det här migreringsverktyget för AD FS identifierar kompatibilitet med Azure AD och ger migreringsvägledning.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978035"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Använda AD FS-programaktivitetsrapporten (förhandsversion) för att migrera program till Azure AD

Många organisationer använder AD FS (Active Directory Federation Services) för att tillhandahålla enkel inloggning till molnprogram. Det finns betydande fördelar med att flytta dina AD FS-program till Azure AD för autentisering, särskilt när det gäller kostnadshantering, riskhantering, produktivitet, efterlevnad och styrning. Men att förstå vilka program som är kompatibla med Azure AD och identifiera specifika migreringssteg kan vara tidskrävande.

Ad FS-programaktivitetsrapporten (förhandsversion) i Azure-portalen gör att du snabbt kan identifiera vilka av dina program som kan migreras till Azure AD. Den bedömer alla AD FS-program för kompatibilitet med Azure AD, söker efter eventuella problem och ger vägledning om hur du förbereder enskilda program för migrering. Med AD FS-programaktivitetsrapporten kan du:

* **Upptäck AD FS-program och begränsa migreringen.** AD FS-programaktivitetsrapporten listar alla AD FS-program i organisationen och anger deras beredskap för migrering till Azure AD.
* **Prioritera program för migrering.** Få antalet unika användare som har loggat in på programmet under de senaste 1, 7 eller 30 dagarna för att avgöra hur allvarlig eller risk som är att migrera programmet.
* **Kör migreringstester och åtgärda problem.** Rapporteringstjänsten kör automatiskt tester för att avgöra om ett program är redo att migrera. Resultaten visas i AD FS-programaktivitetsrapporten som migreringsstatus. Om potentiella migreringsproblem identifieras får du specifik vägledning om hur du åtgärdar problemen.

AD FS-programaktivitetsdata är tillgänglig för användare som har tilldelats någon av dessa administratörsroller: global administratör, rapportläsare, säkerhetsläsare, programadministratör eller molnprogramadministratör.

## <a name="prerequisites"></a>Krav

* Din organisation måste för närvarande använda AD FS för att komma åt program.
* Azure AD Connect Health måste vara aktiverat i din Azure AD-klientorganisation.
   * [Läs mer om Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Komma igång med att konfigurera Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Upptäck AD FS-program som kan migreras 

AD FS-programaktivitetsrapporten är tillgänglig i Azure-portalen under Azure AD **Usage & insights-rapportering.** AD FS-programaktivitetsrapporten analyserar varje AD FS-program för att avgöra om det kan migreras enligt den, eller om ytterligare granskning behövs. 

1. Logga in på [Azure-portalen](https://portal.azure.com) med en administratörsroll som har åtkomst till AD FS-programaktivitetsdata (global administratör, rapportläsare, säkerhetsläsare, programadministratör eller molnprogramadministratör).

2. Välj **Azure Active Directory**och välj sedan **Enterprise-program**.

3. Under **Aktivitet**väljer du **Användning & Insights (Preview)** och väljer sedan **AD FS-programaktivitet** för att öppna en lista över alla AD FS-program i organisationen.

   ![AD FS-programaktivitet](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. För varje program i AD FS-programaktivitetslistan visar du **migreringsstatus:**

   * **Redo att migrera** innebär att AD FS-programkonfigurationen stöds fullt ut i Azure AD och kan migreras som den är.

   * **Behovsgranskning** innebär att vissa av programmets inställningar kan migreras till Azure AD, men du måste granska inställningarna som inte kan migreras som de är.

   * **Ytterligare steg som krävs** innebär att Azure AD inte stöder vissa av programmets inställningar, så att programmet inte kan migreras i sitt aktuella tillstånd.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Utvärdera beredskapen för ett program för migrering 

1. I ad FS-programaktivitetslistan klickar du på statusen i kolumnen **Migreringsstatus** för att öppna migreringsinformation. Du ser en sammanfattning av konfigurationstesterna som har godkänts, tillsammans med eventuella migreringsproblem.

   ![Information om migrering](media/migrate-adfs-application-activity/migration-details.png)

2. Klicka på ett meddelande om du vill öppna ytterligare information om migreringsregeln. En fullständig lista över de egenskaper som testats finns i tabellen [AD FS-programkonfigurationstest](#ad-fs-application-configuration-tests) nedan.

   ![Information om migreringsregel](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>KONFIGURATIONSTESTER FÖR AD FS-program

I följande tabell visas alla konfigurationstester som utförs på AD FS-program.

|Resultat  |Godkänd/varning/underkänd  |Beskrivning  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Minst en regel som inte kan migreras upptäcktes för additionalauthentication.       | Skicka/varna          | Den förlitande parten har regler för att fråga efter multifaktorautentisering (MFA). Om du vill flytta till Azure AD översätter du dessa regler till principer för villkorlig åtkomst. Om du använder en lokal MFA rekommenderar vi att du flyttar till Azure MFA. [Läs mer om villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Den förlitande parten har AdditionalWSFedEndpoint inställd på true.       | Godkänn/Underkänn          | Den förlitande parten i AD FS tillåter flera WS-Fed-kontrollslutpunkter.För närvarande stöder Azure AD bara en.Kontakta [oss om](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)du har ett scenario där resultatet blockerar migreringen.     |
|Test-ADFSRPTillåtauthenticationClassReferences <br> Den förlitande parten har angett AllowedAuthenticationClassReferences.       | Godkänn/Underkänn          | Med den här inställningen i AD FS kan du ange om programmet är konfigurerat för att bara tillåta vissa autentiseringstyper. Vi rekommenderar att du använder villkorlig åtkomst för att uppnå den här funktionen. Kontakta [oss om](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)du har ett scenario där resultatet blockerar migreringen.  [Läs mer om villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|Test-ADFSRPPå vilket finns som vill få en autentisering <br> AlltidRequireAuthenticationCheckResult      | Godkänn/Underkänn          | Med den här inställningen i AD FS kan du ange om programmet är konfigurerat för att ignorera SSO-cookies och **alltid fråga efter autentisering**. I Azure AD kan du hantera autentiseringssessionen med principer för villkorlig åtkomst för att uppnå liknande beteende. Läs mer om hur du [konfigurerar hantering av autentiseringssessioner med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Test-ADFSRPAutoUpdateEnabled <br> Relying Party har AutoUpdateEnabled inställd på true       | Skicka/varna          | Med den här inställningen i AD FS kan du ange om AD FS är konfigurerat för att automatiskt uppdatera programmet baserat på ändringar i federationsmetadata. Azure AD stöder inte detta i dag men bör inte blockera migreringen av programmet till Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> Förlitande part har flera ClaimsProviders aktiverat       | Godkänn/Underkänn          | Den här inställningen i AD FS ropar de identitetsleverantörer som den förlitande parten accepterar anspråk från. I Azure AD kan du aktivera externt samarbete med Azure AD B2B. [Läs mer om Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b).          |
|Test-ADFSRPDelegationAuthorizationRules      | Godkänn/Underkänn          | Programmet har anpassade delegeringsauktoriseringsregler definierade. Det här är ett WS-Trust-koncept som Azure AD stöder med hjälp av moderna autentiseringsprotokoll, till exempel OpenID Connect och OAuth 2.0. [Läs mer om Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPImpersonationAuthorizationRules       | Skicka/varna          | Programmet har anpassade auktoriseringsregler för personifiering definierat.Det här är ett WS-Trust-koncept som Azure AD stöder med hjälp av moderna autentiseringsprotokoll, till exempel OpenID Connect och OAuth 2.0. [Läs mer om Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPIssuanceauthorizationRules <br> Minst en regel som inte kan migreras upptäcktes för Utfärdandeauktorisering.       | Skicka/varna          | Programmet har regler för anpassad utfärdandeauktorisering som definieras i AD FS.Azure AD stöder den här funktionen med Azure AD Conditional Access. [Läs mer om villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). <br> Du kan också begränsa åtkomsten till ett program för användare eller grupper som tilldelats programmet. [Läs mer om hur du tilldelar användare och grupper för att komma åt program](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups).            |
|Test-ADFSRPIssuanceTransformRules <br> Minst en regel som inte kan migreras upptäcktes för IssuanceTransform.       | Skicka/varna          | Programmet har anpassade regler för utfärdandetransformering som definieras i AD FS. Azure AD stöder anpassning av anspråk som utfärdats i token. Mer information finns [i Anpassa anspråk som utfärdats i SAML-token för företagsprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).           |
|Test-ADFSRPÖvervakaEnbar <br> Relying Party har MonitoringEnabled inställd på true.       | Skicka/varna          | Med den här inställningen i AD FS kan du ange om AD FS är konfigurerat för att automatiskt uppdatera programmet baserat på ändringar i federationsmetadata. Azure AD stöder inte detta i dag men bör inte blockera migreringen av programmet till Azure AD.           |
|Test-ADFSRPInte FöreSkep <br> InteBeforeSkewCheckResult      | Skicka/varna          | AD FS tillåter en tidssnedställning baserat på inteför- och NotOnOrAfter-tiderna i SAML-token. Azure AD hanterar automatiskt detta som standard.          |
|Test-ADFSRPRequestMFAFrånClaimsProviders <br> Relying Party har RequestMFAFromClaimsProviders inställd på true.       | Skicka/varna          | Den här inställningen i AD FS avgör beteendet för MFA när användaren kommer från en annan anspråksprovider. I Azure AD kan du aktivera externt samarbete med Azure AD B2B. Sedan kan du använda principer för villkorlig åtkomst för att skydda gäståtkomst. Läs mer om [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) och [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Relying Party har undertecknatSamlRequestsRequired inställd på sann       | Godkänn/Underkänn          | Programmet konfigureras i AD FS för att verifiera signaturen i SAML-begäran. Azure AD accepterar en undertecknad SAML-begäran. Den verifierar dock inte signaturen. Azure AD har olika metoder för att skydda mot skadliga anrop. Azure AD använder till exempel svarsadresser som konfigurerats i programmet för att validera SAML-begäran. Azure AD skickar bara en token för att svara webbadresser som konfigurerats för programmet. Kontakta [oss om](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)du har ett scenario där resultatet blockerar migreringen.          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Skicka/varna         | Programmet är konfigurerat för en anpassad tokenlivslängd. STANDARDINSTÄLLNINGEN AD FS är en timme.Azure AD stöder den här funktionen med villkorlig åtkomst. Mer information finns i [Konfigurera hantering av autentiseringssessioner med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Förlitande part är inställd på att kryptera anspråk. Detta stöds av Azure AD       | Passera          | Med Azure AD kan du kryptera token som skickas till programmet. Mer information finns i [Konfigurera Azure AD SAML-tokenkryptering](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).          |
|KrypteratnameUtrequiredCheckResult      | Godkänn/Underkänn          | Programmet är konfigurerat för att kryptera nameID-anspråket i SAML-token.Med Azure AD kan du kryptera hela token som skickas till programmet.Kryptering av specifika anspråk stöds ännu inte. Mer information finns i [Konfigurera Azure AD SAML-tokenkryptering](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Kontrollera resultaten av anspråksregeltester

Om du har konfigurerat en anspråksregel för programmet i AD FS ger upplevelsen en detaljerad analys för alla anspråksregler. Du ser vilka anspråksregler som kan flyttas till Azure AD och vilka som behöver granskas ytterligare.

1. I ad FS-programaktivitetslistan klickar du på statusen i kolumnen **Migreringsstatus** för att öppna migreringsinformation. Du ser en sammanfattning av konfigurationstesterna som har godkänts, tillsammans med eventuella migreringsproblem.

2. På sidan **Information om migreringsregel** expanderar du resultaten för att visa information om potentiella migreringsproblem och för att få ytterligare vägledning. En detaljerad lista över alla anspråksregler som testats finns i tabellen [Kontrollera resultaten av anspråksregeltester](#check-the-results-of-claim-rule-tests) nedan.

   Exemplet nedan visar information om migreringsregeln för regeln IssuanceTransform. Den listar de specifika delar av anspråket som måste granskas och åtgärdas innan du kan migrera programmet till Azure AD.

   ![Information om migreringsregel ytterligare vägledning](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Anspråksregeltester

I följande tabell visas alla anspråksregeltester som utförs på AD FS-program.

|Egenskap  |Beskrivning  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | Villkorssatsen använder reguljära uttryck för att utvärdera om anspråket matchar ett visst mönster.Om du vill uppnå en liknande funktion i Azure AD kan du använda fördefinierad omvandling, till exempel IfEmpty(), StartWith(), Innehåller(), bland annat. Mer information finns [i Anpassa anspråk som utfärdats i SAML-token för företagsprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_CONDITION_CLASS      | Villkorssatsen har flera villkor som måste utvärderas innan utgivningssatsen körs.Azure AD kan stödja den här funktionen med anspråkets omvandlingsfunktioner där du kan utvärdera flera anspråksvärden.Mer information finns [i Anpassa anspråk som utfärdats i SAML-token för företagsprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_RULE_TYPE      | Anspråksregeln kunde inte identifieras. Mer information om hur du konfigurerar anspråk i Azure AD finns [i Anpassa anspråk som utfärdats i SAML-token för företagsprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Villkorssatsen använder en utfärdare som inte stöds i Azure AD.Azure AD köper för närvarande inte anspråk från andra butiker än Active Directory eller Azure AD. Kontakta [oss om](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)detta blockerar dig från att migrera program till Azure AD.         |
|UNSUPPORTED_CONDITION_FUNCTION      | Villkorssatsen använder en mängdfunktion för att utfärda eller lägga till ett enskilt anspråk oavsett antalet matchningar.I Azure AD kan du utvärdera attributet för en användare för att bestämma vilket värde som ska användas för anspråket med funktioner som IfEmpty(), StartWith(), Contains(), bland annat.Mer information finns [i Anpassa anspråk som utfärdats i SAML-token för företagsprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|RESTRICTED_CLAIM_ISSUED      | Villkorssatsen använder ett anspråk som är begränsat i Azure AD. Du kanske kan utfärda ett begränsat anspråk, men du kan inte ändra dess källa eller tillämpa någon omvandling. Mer information finns [i Anpassa anspråk som skickas ut i token för en viss app i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).          |
|EXTERNAL_ATTRIBUTE_STORE      | Utgivningssatsen använder ett attributarkiv som skiljer sig från Active Directory. Azure AD köper för närvarande inte anspråk från andra butiker än Active Directory eller Azure AD. Kontakta [oss](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)om resultatet blockerar dig från att migrera program till Azure AD.          |
|UNSUPPORTED_ISSUANCE_CLASS      | Utgivningssatsen använder ADD för att lägga till anspråk i den inkommande anspråksuppsättningen. I Azure AD kan detta konfigureras som flera anspråksomvandlingar.Mer information finns [i Anpassa anspråk som utfärdats i SAML-token för företagsprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | Utgivningssatsen använder reguljära uttryck för att omvandla värdet på det anspråk som ska avges.För att uppnå liknande funktioner i Azure AD kan du använda fördefinierad omvandling som Extract(), Trim(), ToLower, bland annat. Mer information finns [i Anpassa anspråk som utfärdats i SAML-token för företagsprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |


## <a name="next-steps"></a>Nästa steg

- [Video: Så här använder du AD FS-aktivitetsrapporten för att migrera ett program](https://www.youtube.com/watch?v=OThlTA239lU)
- [Hantera program med Azure Active Directory](what-is-application-management.md)
- [Hantera åtkomst till appar](what-is-access-management.md)
- [Azure AD Connect-federation](../hybrid/how-to-connect-fed-whatis.md)
