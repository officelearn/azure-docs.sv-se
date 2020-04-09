---
title: Azure AD-autentisering & auktoriseringsfelkoder
description: Lär dig mer om AADSTS-felkoderna som returneras från Azure AD-säkerhetstokentjänsten (STS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 04/07/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 40a7406ea91c95daad2f180b9d0f4620cdbbf454
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875936"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Azure AD-autentiserings- och auktoriseringsfelkoder

Letar du efter information om AADSTS-felkoder som returneras från Azure Active Directory (Azure AD) security token service (STS)? Läs det här dokumentet om du vill hitta AADSTS-felbeskrivningar, korrigeringar och några föreslagna lösningar.

> [!NOTE]
> Den här informationen är preliminär och kan komma att ändras. Har du en fråga eller inte hittar det du letar efter? Skapa ett GitHub-problem eller se [Support- och hjälpalternativ för utvecklare om](active-directory-develop-help-support.md) du vill lära dig mer om andra sätt du kan få hjälp och support.
>
> Den här dokumentationen tillhandahålls för utvecklar- och administratörsvägledning, men bör aldrig användas av klienten själv. Felkoder kan ändras när som helst för att ge mer detaljerade felmeddelanden som är avsedda att hjälpa utvecklaren när de skapar sitt program. Appar som är beroende av text- eller felkodsnummer bryts med tiden.

## <a name="lookup-current-error-code-information"></a>Uppslagsström felkodsinformation
Felkoder och meddelanden kan komma att ändras.  För den senaste informationen, ta `https://login.microsoftonline.com/error` en titt på sidan för att hitta AADSTS felbeskrivningar, korrigeringar och några föreslagna lösningar.  

Sök på den numeriska delen av den returnerade felkoden.  Om du till exempel har fått felkoden "AADSTS16000" söker du efter `https://login.microsoftonline.com/error` "16000".  Du kan också länka direkt till ett specifikt fel genom `https://login.microsoftonline.com/error?code=16000`att lägga till felkodsnumret i URL:en.

## <a name="aadsts-error-codes"></a>AADSTS-felkoder

| Fel | Beskrivning |
|---|---|
| AADSTS16000 | SelectUserAccount - Detta är ett avbrott som genereras av Azure AD, vilket resulterar i användargränssnitt som gör att användaren kan välja bland flera giltiga SSO-sessioner. Det här felet är ganska vanligt och `prompt=none` kan returneras till programmet om det anges. |
| AADSTS16001 | UserAccountSelectionInvalid - Det här felet visas om användaren klickar på en panel som sessionsmarkeringslogiken har avvisat. När det här felet utlöses kan användaren återställa genom att välja från en uppdaterad lista över paneler/sessioner eller genom att välja ett annat konto. Det här felet kan uppstå på grund av ett kodfel eller rastillstånd. |
| AADSTS16002 | AppSessionSelectionInvalid - Det app-specificerade SID-kravet uppfylldes inte.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - Anger att användaren inte uttryckligen har lagts till i klienten. |
| AADSTS17003 | AutentiseringsuppgifterKeyProvisioningFailed - Azure AD kan inte etablera användarnyckeln. |
| AADSTS20001 | WsFedSignInResponseError - Det finns ett problem med din federerade identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS20012 | WsFedMessageInvalid - Det finns ett problem med din federerade identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS20033 | FedMetadataInvalidTenantName - Det finns ett problem med din federerade identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS40008 | OAuth2IdPUnretryableServerError - Det finns ett problem med din federerade identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - Det finns ett problem med din federerade identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS40010 | OAuth2IdPRetryableServerError - Det finns ett problem med din federerade identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - Det finns ett problem med din federerade identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS50000 | TokenIssuanceError - Det finns ett problem med inloggningstjänsten. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att lösa problemet. |
| AADSTS50001 | InvalidResource - Resursen är inaktiverad eller finns inte. Kontrollera appens kod för att se till att du har angett den exakta resurs-URL:en för den resurs du försöker komma åt.  |
| AADSTS50002 | NotAllowedTenant - Inloggning misslyckades på grund av en begränsad proxyåtkomst på klienten. Om det är din egen klientprincip kan du ändra dina begränsade klientinställningar för att åtgärda problemet. |
| AADSTS50003 | MissingSigningKey - Inloggning misslyckades på grund av en saknad signeringsnyckel eller certifikat. Det kan bero på att det inte fanns någon signeringsnyckel konfigurerad i appen. Kolla in de resolutioner [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured)som beskrivs på . Om du fortfarande ser problem kontaktar du appens ägare eller appadministratör. |
| AADSTS50005 | DevicePolicyError - Användaren försökte logga in på en enhet från en plattform som för närvarande inte stöds via principen villkorlig åtkomst. |
| AADSTS50006 | Ogiltigsignatur - Signaturverifiering misslyckades på grund av en ogiltig signatur. |
| AADSTS50007 | PartnerEncryptionCertificateMissing - Partnerkrypteringscertifikatet hittades inte för den här appen. [Öppna en supportbiljett](../fundamentals/active-directory-troubleshooting-support-howto.md) med Microsoft för att få detta åtgärdat. |
| AADSTS50008 | InvalidSamlToken - SAML-påstående saknas eller är felkonfigurerad i token. Kontakta den federerade providern. |
| AADSTS50010 | AudienceUriValidationFailed - Audience URI-validering för appen misslyckades eftersom inga tokenmålningar konfigurerades. |
| AADSTS50011 | InvalidReplyTo - Svarsadressen saknas, är felkonfigurerad eller matchar inte svarsadresser som konfigurerats för appen.  Som en lösning se till att lägga till denna saknade svarsadress till Azure Active Directory-programmet eller har någon med behörighet att hantera ditt program i Active Directory göra detta åt dig.|
| AADSTS50012 | AutentiseringMissilerad - Autentisering misslyckades av något av följande skäl:<ul><li>Undernamnet på signeringscertifikatet är inte tillåtet</li><li>Det gick inte att hitta en matchande princip för betrodda myndigheter för det auktoriserat ämnesnamnet</li><li>Certifikatkedjan är ogiltig</li><li>Signeringscertifikatet är ogiltigt</li><li>Principen är inte konfigurerad för klienten</li><li>Tumavtryck för signeringscertifikatet är inte tillåtet</li><li>Klientpåståendet innehåller en ogiltig signatur</li></ul> |
| AADSTS50013 | Ogiltig identifiering - Påstående är ogiltigt på grund av olika orsaker - Tokenutfärdaren matchar inte api-versionen inom dess giltiga tidsintervall -förfallen - Uppdateringstoken i påståendet är inte en primär uppdateringstoken. |
| AADSTS50014 | GuestUserInPendingState - Användarens inlösen är i ett väntande tillstånd. Gästanvändarkontot är inte helt skapat ännu. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - Användaren kräver samtycke från åldersgruppen. |
| AADSTS50017 | CertifikatvalidationMissilerad - Certifieringsvalidering misslyckades, orsaker till följande skäl:<ul><li>Det går inte att hitta utfärdarcertifikatet i listan med betrodda certifikat</li><li>Det gick inte att hitta förväntat CrlSegment</li><li>Det går inte att hitta utfärdarcertifikatet i listan med betrodda certifikat</li><li>Delta CRL-distributionspunkten har konfigurerats utan motsvarande CRL-distributionspunkt</li><li>Det gick inte att hämta giltiga CRL-segment på grund av ett timeout-problem</li><li>Det gick inte att ladda ned CRL</li></ul>Kontakta administratören för klientorganisationen. |
| AADSTS50020 | UserUnauthorized - Användare är obehöriga att anropa den här slutpunkten. |
| AADSTS50027 | InvalidJwtToken - Ogiltig JWT token på grund av följande skäl:<ul><li>innehåller inte temporärt nonce-anspråk, sub-anspråk</li><li>felaktig matchning av ämnesidentifierare</li><li>duplicerade anspråk i idToken-anspråk</li><li>oväntad utfärdare</li><li>oväntad målgrupp</li><li>inte inom giltigt tidsintervall </li><li>fel tokenformat</li><li>Signaturverifieringen för externa ID-token från utfärdare misslyckades.</li></ul> |
| AADSTS50029 | Ogiltig URI – domännamnet innehåller ogiltiga tecken. Kontakta administratören för klientorganisationen. |
| AADSTS50032 | WeakRsaKey - Anger felaktigt användarförsök att använda en svag RSA-nyckel. |
| AADSTS50033 | RetryableError - Anger ett tillfälligt fel som inte är relaterade till databasåtgärderna. |
| AADSTS50034 | UserAccountNotFound - Om du vill logga in på det här programmet måste kontot läggas till i katalogen. |
| AADSTS50042 | Det går inte Att GenereraPairwiseIdentifierMedMissingSalt - Det salt som krävs för att generera en parvis identifierare saknas i princip. Kontakta administratören för klientorganisationen. |
| AADSTS50043 | Det går inte att skapaPareratPairwiseIdentifierMedMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - Ämne matchar inte Utfärdarens anspråk i klientpåståendet. Kontakta administratören för klientorganisationen. |
| AADSTS50049 | NoSuchInstanceForDiscovery - Okänd eller ogiltig instans. |
| AADSTS50050 | MissformedDiscoveryRequest - Begäran är felaktig. |
| AADSTS50053 | IdsLocked - Kontot är låst eftersom användaren försökte logga in för många gånger med ett felaktigt användar-ID eller lösenord. |
| AADSTS50055 | InvalidPasswordExpiredPassword - Lösenordet har upphört att gälla. |
| AADSTS50056 | Ogiltigt lösenord eller null-lösenord -Lösenordet finns inte i butiken för den här användaren. |
| AADSTS50057 | UserDisabled - Användarkontot är inaktiverat. Kontot har inaktiverats av en administratör. |
| AADSTS50058 | UserInformationNotProvided - Det innebär att en användare inte är inloggad. Detta är ett vanligt fel som förväntas när en användare är oautentiserade och ännu inte har loggat in.</br>Om det här felet uppmuntras i en SSO-kontext där användaren tidigare har loggat in, innebär det att SSO-sessionen antingen inte hittades eller var ogiltig.</br>Det här felet kan returneras till programmet om prompt=none anges. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvIded - Klientidentifierande information hittades inte i vare sig begäran eller underförstått av några angivna autentiseringsuppgifter. Användaren kan kontakta klientadministratören för att lösa problemet. |
| AADSTS50061 | SignoutInvalidRequest - Ut signeringsbegäran är ogiltig. |
| AADSTS50064 | AutentiseringsuppgifterAuthenticationError - Autentiseringsuppgifter validering på användarnamn eller lösenord har misslyckats. |
| AADSTS50068 | SignoutInitiatorNotParticipant - Signout misslyckades. Appen som initierade signering är inte en deltagare i den aktuella sessionen. |
| AADSTS50070 | SignoutUnknownSessionIdentifier - Signout har misslyckats. Signeringsbegäran angav en namnidentifierare som inte matchade de befintliga sessionerna. |
| AADSTS50071 | SignoutMessageExpired - Begäran om utloggning har upphört att gälla. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - Användaren måste registrera sig för andra faktorautentisering (interaktiv). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - Stark autentisering krävs och användaren klarade inte MFA-utmaningen. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - På grund av en konfigurationsändring som gjorts av administratören, eller på grund av att du har flyttat till en ny plats, måste användaren använda multifaktorautentisering för att komma åt resursen. Försök igen med en ny auktorisera begäran om resursen. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - På grund av en konfigurationsändring som gjorts av administratören, eller på grund av att användaren flyttade till en ny plats, är användaren skyldig att använda multifaktorautentisering. |
| AADSTS50085 | Det krävs social IDP-inloggning för att token ska kunna uppdateras. Låt användaren försöka logga in igen med användarnamn och lösenord |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError - Tjänsten är inte tillgänglig för tillfället. Försök igen. |
| AADSTS50089 | Flödestoken har upphört att gälla – autentiseringen misslyckades. Låt användaren försöka logga in igen med användarnamn -lösenord. |
| AADSTS50097 | DeviceAuthenticationRequired - Enhetsautentisering krävs. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized - JWT-signaturen är ogiltig. |
| AADSTS50105 | EntitlementGrantsNotFound - Den inloggade användaren har inte tilldelats en roll för den inloggade appen. Tilldela användaren till appen. För mer[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)information: . |
| AADSTS50107 | InvalidRealmUri - Det begärda federationssfärobjektet finns inte. Kontakta administratören för klientorganisationen. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - Problem med JWT-huvud. Kontakta administratören för klientorganisationen. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - Anspråksomvandling innehåller ogiltig indataparameter. Kontakta administratören för klientorganisationen för att uppdatera principen. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - Inloggning avbröts på grund av en lösenordsåterställning eller lösenordsregistrering. |
| AADSTS50126 | InvalidUserNameOrPassword - Fel validera autentiseringsuppgifter på grund av ogiltigt användarnamn eller lösenord. |
| AADSTS50127 | BrokerAppNotInstalled - Användaren måste installera en mäklarapp för att få tillgång till det här innehållet. |
| AADSTS50128 | Ogiltigt domännamn - Ingen klientidentifierande information som finns i vare sig begäran eller underförstått av några angivna autentiseringsuppgifter. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined - Arbetsplatsanslutning krävs för att registrera enheten. |
| AADSTS50131 | ConditionalAccessFailed - Anger olika villkorliga åtkomstfel, till exempel dåligt Windows-enhetstillstånd, begäran blockerad på grund av misstänkt aktivitet, åtkomstprincip eller säkerhetsprincipbeslut. |
| AADSTS50132 | SsoArtifactInvalidOrExpired - Sessionen är inte giltig på grund av lösenordsförfallodatum eller senaste lösenordsändring. |
| AADSTS50133 | SsoArtifactRevoked - Sessionen är inte giltig på grund av lösenordsförfallodatum eller senaste lösenordsändring. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter - Fel datacenter. Om du vill auktorisera en begäran som initierades av en app i OAuth 2.0-enhetsflödet måste den auktorisera parten be be beta i samma datacenter där den ursprungliga begäran finns. |
| AADSTS50135 | PasswordChangeCompromisedPassword - Lösenordsändring krävs på grund av kontorisk. |
| AADSTS50136 | RedirectMsaSessionToApp - En enda MSA-session har upptäckts. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - Sessionen är ogiltig på grund av en extern uppdateringstoken som saknas. |
| AADSTS50140 | KmsiInterrupt - Det här felet uppstod på grund av att "Håll mig inloggad" avbryter när användaren loggade in. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med korrelations-ID, ID för begäran och felkod för mer information. |
| AADSTS50143 | Sessionsmatchning - Sessionen är ogiltig eftersom användarens klientorganisation inte matchar domäntipset på grund av olika resurser.  [Öppna en supportbiljett](../fundamentals/active-directory-troubleshooting-support-howto.md) med korrelations-ID, begärande-ID och felkod för att få mer information. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - Användarens Active Directory-lösenord har upphört att gälla. Generera ett nytt lösenord för användaren eller be användaren använda självbetjäningsåterställningsverktyget för att återställa sitt lösenord. |
| AADSTS50146 | MissingCustomSigningKey - Den här appen måste konfigureras med en appspecifik signeringsnyckel. Det har antingen inte konfigurerats med en sådan eller så har nyckeln upphört att gälla/är inte giltig än. |
| AADSTS50147 | MissingCodeChallenge - Storleken på parametern kodutmaning är ogiltig. |
| AADSTS50155 | DeviceAuthenticationFailed - Enhetsautentisering misslyckades för den här användaren. |
| AADSTS50158 | ExternalSecurityChallenge - Yttre säkerhet utmaning var inte nöjd. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - Anspråk som skickas av extern leverantör är inte tillräckligt eller saknade anspråk som begärs till extern leverantör. |
| AADSTS50166 | ExternalClaimsProviderThrottled - Det gick inte att skicka begäran till anspråksprovidern. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - Klienten kan hämta en SSO-token via Windows 10-kontotillägget, men token hittades inte i begäran eller den medföljande token har upphört att gälla. |
| AADSTS50169 | InvalidRequestBadRealm - Sfären är inte en konfigurerad sfär för det aktuella tjänstnamnområdet. |
| AADSTS50170 | SaknasExternalClaimsProviderMapping - Den externa kontroller mappningen saknas. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - Extern utmaning stöds inte för vidareströmsanvändare. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - Sessionskontrollen stöds inte för vidareströmningsanvändare. |
| AADSTS50180 | WindowsIntegratedAuthMissing - Integrerad Windows-autentisering behövs. Aktivera klientorganisationen för sömlös SSO. |
| AADSTS50187 | DeviceInformationNotProvided - Tjänsten kunde inte utföra enhetsautentisering. |
| AADSTS50196 | LoopDetected - En klientloop har upptäckts. Kontrollera appens logik för att säkerställa att tokencacheing implementeras och att felvillkor hanteras korrekt.  Appen har gjort för många av samma begäran på för kort tid, vilket indikerar att den är i ett felaktigt tillstånd eller är felaktigt begär token. |
| AADSTS50197 | ConflictingIdentities - Det gick inte att hitta användaren. Försök logga in igen. |
| AADSTS50199 | CmsiInterrupt - Av säkerhetsskäl krävs användarbekräftelse för den här begäran.  Eftersom detta är ett "interaction_required"-fel bör klienten göra interaktiva autentisering.  Detta beror på att en systemwebbvy har använts för att begära en token för ett inbyggt program - användaren måste uppmanas att fråga om detta verkligen var appen de tänkt logga in på.|
| AADSTS51000 | RequiredFeatureNotEnabled - Funktionen är inaktiverad. |
| AADSTS51001 | DomainHintMustbePresent - Domäntips måste finnas med lokal säkerhetsidentifierare eller lokal UPN. |
| AADSTS51004 | UserAccountNotInDirectory - Användarkontot finns inte i katalogen. |
| AADSTS51005 | TemporaryRedirect - Motsvarar HTTP-status 307, vilket anger att den begärda informationen finns vid den URI som anges i platshuvudet. När du får den här statusen följer du platshuvudet som är associerat med svaret. När den ursprungliga begäran metoden var POST, kommer omdirigerad begäran också att använda POST-metoden. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth - Integrerad Windows-autentisering behövs. Användaren loggade in med en sessionstoken som saknar det integrerade Windows-autentiseringsanspråket. Be användaren att logga in igen. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - Användaren har inte gett sitt samtycke till åtkomst till LinkedIn-resurser. |
| AADSTS53000 | DeviceNotCompliant - Principen villkorlig åtkomst kräver en kompatibel enhet och enheten är inte kompatibel. Användaren måste registrera sin enhet med en godkänd MDM-provider som Intune. |
| AADSTS53001 | DeviceNotDomainJoined - Principen villkorlig åtkomst kräver en domänansluten enhet och enheten är inte domänansluten. Be användaren använda en domänansluten enhet. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp - Appen som används är inte en godkänd app för villkorlig åtkomst. Användaren måste använda en av apparna från listan över godkända appar som ska användas för att få åtkomst. |
| AADSTS53003 | BlockedByConditionalAccess - Access har blockerats av principer för villkorlig åtkomst. Åtkomstprincipen tillåter inte tokenutfärdande. |
| AADSTS53004 | ProofUpBlockedDueToRisk - Användaren måste slutföra registreringsprocessen för multifaktorautentisering innan du öppnar det här innehållet. Användaren bör registrera sig för multifaktorautentisering. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist - Användaren eller administratören har inte samtyckt till att använda programmet med ID X. Skicka en interaktiv auktoriseringsbegäran för den här användaren och resursen. |
| AADSTS65004 | UserDeclinedConsent - Användaren avböjde att godkänna åtkomst till appen. Låt användaren logga in igen och ge samtycke till appen|
| AADSTS65005 | Felkonfigureradtillämpning – Listan över resursåtkomst för appen innehåller inte appar som kan identifieras av resursen eller klientappen har begärt åtkomst till resursen, vilket inte angavs i den obligatoriska resursåtkomstlistan eller graph-tjänsten returnerade dålig begäran eller resurs hittades inte. Om appen stöder SAML kan du ha konfigurerat appen med fel identifierare (entitet). Prova upplösningen som anges för SAML med hjälp av länken nedan:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - Autentisering misslyckades. Uppdateringstoken är ogiltig. Felet kan bero på följande:<ul><li>Tokenbindningshuvudet är tomt</li><li>Tokenbindningshh matchar inte</li></ul> |
| AADSTS70001 | UnauthorizedClient - Programmet är inaktiverat. |
| AADSTS70002 | InvalidClient - Fel validera autentiseringsuppgifterna. Den angivna client_secret matchar inte det förväntade värdet för den här klienten. Korrigera client_secret och försök igen. Mer information finns i [Använda auktoriseringskoden för att begära en åtkomsttoken](v2-oauth2-auth-code-flow.md#request-an-access-token). |
| AADSTS70003 | Inte stödsGrantType - Appen returnerade en bidragstyp som inte stöds. |
| AADSTS70004 | InvalidRedirectUri - Appen returnerade en ogiltig omdirigerings-URI. Adressen för omdirigering som angetts av klienten matchar inte några konfigurerade adresser eller någon adress på godkännandelistan för OIDC. |
| AADSTS70005 | ResponseeType som inte stöds – Appen returnerade en svarstyp som inte stöds på grund av följande orsaker:<ul><li>svarstypen "token" är inte aktiverad för appen</li><li>svarstypen "id_token" kräver OpenID-omfång – innehåller ett OAuth-parametervärde som inte stöds i den kodade wctx</li></ul> |
| AADSTS70007 | Svarar inteMode - Appen returnerade ett värde som `response_mode` inte stöds när du begär en token.  |
| AADSTS70008 | ExpiredOrRevokedGrant - Uppdateringstoken har upphört att gälla på grund av inaktivitet. Token utfärdades på XXX och var inaktiv under en viss tid. |
| AADSTS70011 | InvalidScope - Det scope som begärs av appen är ogiltigt. |
| AADSTS70012 | MsaServerError - Ett serverfel uppstod när en MSA-användare (konsument). Försök igen. Om det fortsätter att misslyckas [öppnar du en supportbiljett](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | Auktoriseringsanering - OAuth 2.0-enhetsflödesfel. Auktorisering väntar. Enheten försöker avsöka begäran igen. |
| AADSTS70018 | BadVerificationCode - Ogiltig verifieringskod på grund av att användaren skriver in fel användarkod för enhetskodflödet. Auktorisering är inte godkänd. |
| AADSTS70019 | CodeExpired - Verifieringskoden har upphört att gälla. Be användaren försöka logga in igen. |
| AADSTS75001 | BindingSerializationError - Ett fel uppstod under SAML-meddelandebindning. |
| AADSTS75003 | Binder inte - Appen returnerade ett fel som var relaterat till bindning som inte stöds (SAML-protokollsvaret kan inte skickas via andra bindningar än HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid - Azure AD stöder inte SAML-begäran som skickas av appen för SSO. |
| AADSTS75008 | RequestDeniedError - Begäran från appen nekades eftersom SAML-begäran hade ett oväntat mål. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - Autentiseringsmetoden som användaren autentiserade med tjänsten inte matchar begärd autentiseringsmetod. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - SAML2 Autentiseringsbegäran har ogiltig NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - Autentiseringsagenten kan inte ansluta till Active Directory. Kontrollera att agentservrar är medlemmar i samma AD-skog som de användare vars lösenord måste valideras och de kan ansluta till Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - Lösenord validering begäran timeout. Kontrollera att Active Directory är tillgängligt och svara på begäranden från agenterna. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - Ett okänt fel uppstod vid bearbetning av svaret från autentiseringsagenten. Försök igen. Om det fortsätter att misslyckas öppnar [du en supportbiljett](../fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - Autentiseringsagenten kan inte validera användarens lösenord. Kontrollera agentloggarna för mer information och kontrollera att Active Directory fungerar som förväntat. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - Autentiseringsagenten kan inte dekryptera lösenord. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - Användarna försökte logga in utanför de tillåtna timmarna (detta anges i AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - Autentiseringsförsöket kunde inte slutföras på grund av tidsnedställning mellan datorn som kör autentiseringsagenten och AD. Åtgärda problem med tidssynkronisering. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Kerberos autentiseringsförsök misslyckades. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - Autentiseringspaketet stöds inte. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - Inget auktoriseringshuvud hittades. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - Klienten är inte aktiverad för Sömlös SSO. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - Det går inte att validera användarens Kerberos-biljett. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - Seamless SSO misslyckades eftersom användarens Kerberos-biljett har upphört att gälla eller är ogiltig. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - Det gick inte att hitta användarobjekt baserat på information i användarens Kerberos-biljett. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - Användaren som försöker logga in på Azure AD skiljer sig från användaren som är inloggad på enheten. |
| AADSTS90002 | InvalidTenantName - Klientnamnet hittades inte i datalagret. Kontrollera att du har rätt klient-ID. |
| AADSTS90004 | InvalidRequestFormat - Begäran är inte korrekt formaterad. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements - Det går inte att slutföra begäran. Begäran är inte giltig eftersom identifierings- och inloggningstipset inte kan användas tillsammans.  |
| AADSTS90006 | ExternalServerRetryableError - Tjänsten är inte tillgänglig för tillfället.|
| AADSTS90007 | InvalidSessionId - Felaktig begäran. Det gick att tolka sessions-ID:t. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - Användaren eller administratören har inte samtyckt till att använda programmet. Programmet kräver åtminstone åtkomst till Azure AD genom att ange behörigheten inloggning och läsa användarprofil. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier - Programmet begär en token för sig själv. Det här scenariot stöds bara om den resurs som har angetts använder det GUID-baserade program-ID:et. |
| AADSTS90010 | NotSupported - Det går inte att skapa algoritmen. |
| AADSTS90012 | RequestTimeout - Den begärda har timeout. |
| AADSTS90013 | InvalidUserInput - Indata från användaren är ogiltig. |
| AADSTS90014 | MissingRequiredField - Den här felkoden kan visas i olika fall när ett förväntat fält inte finns i autentiseringsuppgifterna. |
| AADSTS90015 | QueryStringTooLong - Frågesträngen är för lång. |
| AADSTS90016 | MissingRequiredClaim - Åtkomsttoken är inte giltig. Den begärd fordran saknas. |
| AADSTS90019 | MissingTenantRealm - Azure AD kunde inte fastställa klientidentifieraren från begäran. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat - Huvudnamnformatet är ogiltigt eller `name[/host][@realm]` uppfyller inte det förväntade formatet. Huvudnamnet krävs, värd och sfär är valfria och kan ställas in på null. |
| AADSTS90023 | InvalidRequest - Begäran om autentiseringstjänst är ogiltig. |
| AADSTS9002313 | InvalidRequest - Begäran är felaktig eller ogiltig. - (EN) Problemet här är att det var något fel med begäran till en viss slutpunkt. Förslaget till detta problem är att få en spelman spår av felet inträffar och vill se om begäran är faktiskt korrekt formaterad eller inte. |
| AADSTS90024 | RequestBudgetExceededError - Ett tillfälligt fel har inträffat. Försök igen. |
| AADSTS90033 | MsodsServiceUnavailable - Microsoft Online Directory Service (MSODS) är inte tillgänglig. |
| AADSTS90036 | MsodsServiceUnretryableFailure - Ett oväntat, icke-retryable fel från WCF-tjänsten värd MSODS har inträffat. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet. |
| AADSTS90038 | NationalCloudTenantRedirection - Den angivna hyresgästen "Y" tillhör National Cloud "X". Aktuella molninstansen "Z" federerar inte med X. Ett fel i omdirigering av molnet returneras. |
| AADSTS90043 | NationalCloudAuthCodeRedirection - Funktionen är inaktiverad. |
| AADSTS90051 | InvalidNationalCloudId - Den nationella molnidentifieraren innehåller en ogiltig molnidentifierare. |
| AADSTS90055 | TenantThrottlingError - Det finns för många inkommande begäranden. Det här undantaget genereras för blockerade klienter. |
| AADSTS90056 | BadResourceRequest - Om du vill lösa in koden för en `/token` åtkomsttoken ska appen skicka en POST-begäran till slutpunkten. Dessutom bör du innan detta ange en auktoriseringskod `/token` och skicka den i POST-begäran till slutpunkten. Se den här artikeln för en översikt över OAuth [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)2.0 auktoriseringskod flöde: . Rikta användaren till `/authorize` slutpunkten, som returnerar en authorization_code. Genom att publicera `/token` en begäran till slutpunkten får användaren åtkomsttoken. Logga in på Azure-portalen och kontrollera **appregistreringar > slutpunkter** för att bekräfta att de två slutpunkterna har konfigurerats korrekt. |
| AADSTS90072 | PassThroughUserMfaError - Det externa konto som användaren loggar in med finns inte på klienten som de loggade in på. så att användaren inte kan uppfylla MFA-kraven för klienten. Kontot måste läggas till som en extern användare i klienten först. Logga ut och logga in med ett annat Azure AD-användarkonto. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid - Ett fel uppstod när tjänsten försökte bearbeta ett WS-Federation-meddelande. Meddelandet är ogiltigt. |
| AADSTS90082 | OrgIdWsFederationNotSupported - Den valda autentiseringsprincipen för begäran stöds för närvarande inte. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - Gästkonton är inte tillåtna för den här webbplatsen. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed - Tjänsten kan inte utfärda en token eftersom företagsobjektet inte har etablerats ännu. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpirerad - Användarens DA-token har upphört att gälla. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed - Ett fel uppstod när WS-Federation-meddelandet från URI skulle skapas. |
| AADSTS90090 | GraphRetryableError - Tjänsten är inte tillgänglig för tillfället. |
| AADSTS90091 | GraphServiceOåre kan nås |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - Graph returneras med en förbjuden felkod för begäran. |
| AADSTS90094 | AdminConsentRequired - Administratörsmedgivande krävs. |
| AADSTS900382 | Konfidentiell klient stöds inte i Cross Cloud-begäran. |
| AADSTS90100 | InvalidRequestParameter - Parametern är tom eller ogiltig. |
| AADSTS901002 | AADSTS901002: Parametern för resursbegäran stöds inte. |
| AADSTS90101 | InvalidEmailAddress - De angivna uppgifterna är inte en giltig e-postadress. E-postadressen måste vara `someone@example.com`i formatet . |
| AADSTS90102 | InvalidUriParameter - Värdet måste vara en giltig absolut URI. |
| AADSTS90107 | InvalidXml - Begäran är ogiltig. Kontrollera att dina data inte har ogiltiga tecken.|
| AADSTS90114 | InvalidExpiryDate - Tidsstämpeln för förfallodatum för masstoken gör att en token som har upphört att gälla utfärdas. |
| AADSTS90117 | OgiltigtQuestInput |
| AADSTS90119 | InvalidUserCode - Användarkoden är null eller tom.|
| AADSTS90120 | InvalidDeviceFlowRequest - Begäran har redan godkänts eller avvisats. |
| AADSTS90121 | InvalidEmptyRequest - Ogiltig tom begäran.|
| AADSTS90123 | IdentityProviderAccessDenied - Token kan inte utfärdas eftersom identitets- eller anspråksutfärdarprovidern nekade begäran. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported - Resursen stöds `/common` inte `/consumers` över slutpunkterna eller. Använd `/organizations` slutpunkten eller klientspecifikt i stället. |
| AADSTS90125 | DebugModeEnrollTenantNotFound - Användaren finns inte i systemet. Kontrollera att du har angett användarnamnet korrekt. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred - Användartypen stöds inte på den här slutpunkten. Systemet kan inte dra slutsatsen att användarens klientorganisation kommer från användarnamnet. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported - Programmet stöds inte `/common` `/consumers` över slutpunkterna eller. Använd `/organizations` slutpunkten eller klientspecifikt i stället. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | LösenordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | LösenordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated - Ett fel som inte kan försökas igen har inträffat.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | LösenordÄndelse |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound - Användarens huvudnamn har inte NGC ID-nyckeln konfigurerad. |
| AADSTS130005 | NgcInvalidSignature - NGC-nyckelsignaturen misslyckades.|
| AADSTS130006 | NgcTransportKeyNotFound - NGC-transportnyckeln är inte konfigurerad på enheten. |
| AADSTS130007 | NgcDeviceIsabled - Enheten är inaktiverad. |
| AADSTS130008 | NgcDeviceIsNotFound - Enheten som refererades av NGC-nyckeln hittades inte. |
| AADSTS135010 | KeyNotFound (KeyNotFound) |
| AADSTS140000 | InvalidRequestNonce - Begäran nonce tillhandahålls inte. |
| AADSTS140001 | InvalidSessionKey - Sessionsnyckeln är ogiltig.|
| AADSTS165900 | InvalidApiRequest - Ogiltig begäran. |
| AADSTS220450 | Stöds inteAndroidWebViewVersion - Chrome WebView-versionen stöds inte. |
| AADSTS220501 | OgiltigT Nedladdning |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - Resursen är inte konfigurerad för att acceptera token endast för enheter. |
| AADSTS240001 | BulkAADJTokenUnauthorized - Användaren har inte behörighet att registrera enheter i Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing - Id_token kan inte användas `urn:ietf:params:oauth:grant-type:jwt-bearer` som bidrag.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy - Klientadministratören har konfigurerat en säkerhetsprincip som blockerar den här begäran. Kontrollera de säkerhetsprinciper som har definierats på klientnivå för att avgöra om din begäran uppfyller principkraven. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest - Programmet hittades inte i katalogen/klienten. Detta kan inträffa om programmet inte har installerats av klientens administratör eller medgivande från någon användare i klienten. Du kan ha felkonfigurerat identifierare värdet för programmet eller skickat din autentiseringsbegäran till fel klient. |
| AADSTS700020 | InteractionRequired - Åtkomstbidraget kräver interaktion. |
| AADSTS700022 | InvalidMultipleResourcesScope - Det angivna värdet för indataparameteromfånget är inte giltigt eftersom det innehåller mer än en resurs. |
| AADSTS700023 | InvalidResourcelessScope - Det angivna värdet för indataparameteromfånget är inte giltigt när du begär en åtkomsttoken. |
| AADSTS7000222| InvalidClientSecretExpiredKeysProvided - De angivna klienthemliga nycklarna har upphört att gälla. Besök Azure Portal för att skapa nya nycklar för din app, eller överväg att använda certifikatuppgifter för ökad säkerhet:https://aka.ms/certCreds |
| AADSTS700005 | InvalidGrantRedeemAgainstWrongTenant - Förutsatt auktoriseringskod är avsedd att användas mot andra innehavare, och avvisas därför. OAuth2 auktoriseringskod måste lösas in mot samma klient som den förvärvades för (/common eller /{tenant-ID} som är tillämpligt) |
| AADSTS1000000 | UserNotBoundError - Bind API kräver att Azure AD-användaren också autentiserar med ett externt IDP, vilket inte har hänt ännu. |
| AADSTS1000002 | BindCompleteInterruptError - Bindningen har slutförts, men användaren måste informeras. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled - Programmet är inaktiverat. |
| AADSTS7500529 | Värdet "SAMLId-Guid" är inte ett giltigt SAML-ID - Azure AD använder det här attributet för att fylla i InResponseTo-attributet för det returnerade svaret. ID får inte börja med ett tal, så en gemensam strategi är att prepend en sträng som "id" till strängrepresentation av ett GUID. Till exempel, id6c1c178c166d486687be4aaf5e482730 är ett giltigt ID. |

## <a name="next-steps"></a>Nästa steg

* Har du en fråga eller inte hittar det du letar efter? Skapa ett GitHub-problem eller se [Support- och hjälpalternativ för utvecklare om](active-directory-develop-help-support.md) du vill lära dig mer om andra sätt du kan få hjälp och support.
