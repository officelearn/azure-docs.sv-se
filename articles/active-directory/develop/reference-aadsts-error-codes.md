---
title: Azure Active Directory autentisering och auktorisering-felkoder | Microsoft Docs
description: Läs mer om AADSTS felkoder som returneras från Azure AD säkerhetstokentjänst (STS).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/30/2018
ms.author: celested
ms.reviewer: hirsin, justhu
ms.custom: aaddev
ms.openlocfilehash: 97f884a45a0a07e2b4c48f39483c70248e7a492e
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620410"
---
# <a name="authentication-and-authorization-error-codes"></a>Autentisering och felkoder vid autentisering

Letar du efter information om felkoder AADSTS som returneras från Azure Active Directory (Azure AD) säkerhetstokentjänst (STS)? Läs det här dokumentet för att hitta AADSTS förslag felbeskrivningar, korrigeringar och några på lösningar.

> [!NOTE]
> Den här informationen är preliminär och kan ändras. Har du en fråga eller kan inte hitta det du letar efter? Skapa ett GitHub-ärende eller se [Support och hjälper dig att alternativ för utvecklare](active-directory-develop-help-support.md) vill veta mer om andra sätt du kan få hjälp och support.

## <a name="aadsts-error-codes"></a>AADSTS felkoder

| Fel | Beskrivning |
|---|---|
| AADSTS16000 | SelectUserAccount – det här är ett avbrott som utlöses av Azure AD, vilket resulterar i Användargränssnittet som användaren kan välja bland flera giltiga SSO-sessioner. Det här felet är ganska gemensam och kan returneras till programmet om `prompt=none` har angetts. |
| AADSTS16001 | UserAccountSelectionInvalid - visas det här felet om användaren klickar på en panel som väljer logiken sessionen har avvisats. När det utlöses, gör att användaren kan återställa genom att välja från en uppdaterad lista över paneler/sessioner eller genom att välja ett annat konto i det här felet. Det här felet kan inträffa på grund av ett kod defekt eller RAS-villkor. |
| AADSTS16002 | AppSessionSelectionInvalid - app-angivna SID-krav var inte uppfyllt.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - anger att användaren inte har uttryckligen lagts till klienten. |
| AADSTS17003 | CredentialKeyProvisioningFailed – Azure AD kan inte etablera nyckeln. |
| AADSTS20001 | WsFedSignInResponseError - det finns ett problem med din extern identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS20012 | WsFedMessageInvalid - det finns ett problem med din extern identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS20033 | FedMetadataInvalidTenantName - det finns ett problem med din extern identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS40008 | OAuth2IdPUnretryableServerError - det finns ett problem med din extern identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - det finns ett problem med din extern identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS40010 | OAuth2IdPRetryableServerError - det finns ett problem med din extern identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - det finns ett problem med din extern identitetsprovider. Kontakta din IDP för att lösa problemet. |
| AADSTS50000 | TokenIssuanceError - det finns ett problem med tjänsten logga in. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att lösa problemet. |
| AADSTS50001 | InvalidResource - resursen är inaktiverat eller så finns inte. Kontrollera din Apps kod för att säkerställa att du har angett exakta resurs-URL för den resurs som du försöker komma åt.  |
| AADSTS50002 | NotAllowedTenant - inloggning misslyckades på grund av en begränsad proxy-åtkomst på klienten. Du kan ändra din begränsade klientinställningar för att åtgärda problemet om det är ditt eget Klientprincipen. |
| AADSTS50003 | MissingSigningKey - inloggning misslyckades på grund av ett saknas signeringsnyckeln eller certifikat. Det kan vara eftersom det inte fanns inga signeringsnyckel som konfigurerats i appen. Läs igenom lösningarna som beskrivs i [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Om du fortfarande ser ett problem, kontakta appägaren eller en app-administratör. |
| AADSTS50005 | DevicePolicyError - användaren försökte logga in på en enhet från en plattform som inte stöds för närvarande via princip för villkorlig åtkomst. |
| AADSTS50006 | InvalidSignature - signaturverifiering misslyckades på grund av en ogiltig signatur. |
| AADSTS50007 | PartnerEncryptionCertificateMissing - krypteringscertifikat partner hittades inte för den här appen. [Öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med Microsoft för att få det åtgärdat. |
| AADSTS50008 | InvalidSamlToken - SAML-kontrollen saknas eller är felkonfigurerad i token. Kontakta den federerade providern. |
| AADSTS50010 | AudienceUriValidationFailed - målgruppen URI-verifiering för appen kunde inte utföras eftersom inga tokenmålgrupper konfigurerades. |
| AADSTS50011 | InvalidReplyTo - svarsadressen saknas, felaktigt konfigurerat, eller matchar inte svarsadresserna som har konfigurerats för appen. Prova att använda lösningen som listas vid [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Om du fortfarande ser ett problem, kontaktar du appägare eller app-administratör. |
| AADSTS50012 | AuthenticationFailed - autentiseringen misslyckades för en av följande orsaker:<ul><li>Ämnesnamnet för signeringscertifikatet har inte behörighet</li><li>Gick inte att hitta en matchande princip för betrodd utfärdare för behöriga ämnesnamnet</li><li>Certifikatkedjan är inte giltig</li><li>Signeringscertifikatet är inte giltig</li><li>Principen har inte konfigurerats på klienten</li><li>Tumavtryck för signeringscertifikatet har inte behörighet</li><li>Klientförsäkran innehåller en ogiltig signatur</li></ul> |
| AADSTS50013 | InvalidAssertion - kontrollen är ogiltigt på grund av olika anledningar - tokenutfärdaren matchar inte api version inom dess giltig tid intervall - har upphört att gälla - fel format - uppdateringstoken i kontrollen är inte en primär uppdateringstoken. |
| AADSTS50014 | GuestUserInPendingState - användarens inlösen är i ett väntande tillstånd. Gästanvändarkontot skapas fullständigt inte ännu. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - användaren kräver juridiska ålder grupp medgivande. |
| AADSTS50017 | CertificateValidationFailed - certifiering regelverifieringen misslyckades, orsaker av följande skäl:<ul><li>Det går inte att hitta utfärdarcertifikatet i listan med betrodda certifikat</li><li>Det gick inte att hitta förväntat CrlSegment</li><li>Det går inte att hitta utfärdarcertifikatet i listan med betrodda certifikat</li><li>Delta CRL-distributionspunkten har konfigurerats utan motsvarande CRL-distributionspunkt</li><li>Det går inte att hämta giltiga CRL-segment på grund av ett timeout-problem</li><li>Det gick inte att ladda ned CRL</li></ul>Kontakta administratören för klientorganisationen. |
| AADSTS50020 | UserUnauthorized - användare har inte behörighet att anropa den här slutpunkten. |
| AADSTS50027 | InvalidJwtToken - ogiltig JWT-token på grund av följande orsaker:<ul><li>innehåller inte temporärt nonce-anspråk, sub-anspråk</li><li>felaktig matchning av ämnesidentifierare</li><li>duplicerade anspråk i idToken-anspråk</li><li>oväntad utfärdare</li><li>oväntad målgrupp</li><li>inte inom giltigt tidsintervall </li><li>fel tokenformat</li><li>Signaturverifieringen för externa ID-token från utfärdare misslyckades.</li></ul> |
| AADSTS50029 | Ogiltig URI – domännamnet innehåller ogiltiga tecken. Kontakta administratören för klientorganisationen. |
| AADSTS50032 | WeakRsaKey - anger felaktiga användaren försöker använda en svag RSA-nyckel. |
| AADSTS50033 | RetryableError - anger ett tillfälligt fel som inte rör databasåtgärderna. |
| AADSTS50034 | UserAccountNotFound - att logga in på det här programmet kontot måste läggas till katalogen. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt - salt som krävs för att skapa en pairwise identifierare saknas i huvudnamn. Kontakta administratören för klientorganisationen. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - ämne matchningsfel utfärdare anspråk i klientförsäkran. Kontakta administratören för klientorganisationen. |
| AADSTS50049 | NoSuchInstanceForDiscovery - okänt eller ogiltigt instans. |
| AADSTS50050 | MalformedDiscoveryRequest - begäran är felaktig. |
| AADSTS50053 | IdsLocked - kontot är låst eftersom användaren försökte logga in för många gånger med ett felaktigt användar-ID eller lösenord. |
| AADSTS50055 | InvalidPasswordExpiredPassword - lösenordet har upphört att gälla. |
| AADSTS50056 | Ogiltig eller null lösenord-lösenordet finns inte i arkivet för den här användaren. |
| AADSTS50057 | UserDisabled - användarkontot är inaktiverat. Kontot har inaktiverats av en administratör. |
| AADSTS50058 | UserInformationNotProvided – detta innebär att användaren inte är inloggad. Det här är ett vanligt fel som kan förväntas när en användare är oautentiserad och ännu inte har loggat in.</br>Om det här felet rekommenderas i en SSO-kontext där användaren har tidigare loggat in, innebär det som SSO-session har antingen inte finns eller är ogiltig.</br>Det här felet kan returneras till programmet om fråga = inget har angetts. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - klient-identifieringsinformation kunde inte hittas i antingen begäran eller underförstådda med angivna autentiseringsuppgifter. Användaren kan kontakta en administratör för att hjälpa dig att lösa problemet. |
| AADSTS50061 | SignoutInvalidRequest - utloggning begäran är ogiltig. |
| AADSTS50064 | CredentialAuthenticationError - autentiseringsuppgift verifieringen misslyckades. |
| AADSTS50068 | SignoutInitiatorNotParticipant - utloggning misslyckades. Appen som initierade utloggning är inte en deltagare i den aktuella sessionen. |
| AADSTS50070 | SignoutUnknownSessionIdentifier - utloggning misslyckades. Utloggning begäran angav en namnidentifierare som inte matchade den befintliga spårningssession. |
| AADSTS50071 | SignoutMessageExpired - utloggningsbegäran har upphört att gälla. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - användaren måste registrera sig för tvåfaktorsautentisering (Interaktiv). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - stark autentisering krävs och användaren godkändes inte vid MFA-kontrollen. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - på grund av en konfigurationsändring som gjorts av administratören eller eftersom du flyttas till en ny plats, måste användaren använda multifaktorautentisering för åtkomst till resursen. Försök igen med en ny auktorisera-begäran för resursen. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - på grund av en konfigurationsändring som gjorts av administratören eller eftersom användaren flyttas till en ny plats, måste användaren använda multifaktorautentisering. |
| AADSTS50085 | Det krävs social IDP-inloggning för att token ska kunna uppdateras. Låt användaren försöka logga in igen med användarnamn och lösenord |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError - tjänsten är inte tillgänglig för tillfället. Försök igen. |
| AADSTS50089 | Flödestoken har upphört att gälla – autentiseringen misslyckades. Användaren försöka logga in igen med användarnamn-lösenord. |
| AADSTS50097 | DeviceAuthenticationRequired - autentisering krävs. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized - JWT-signaturen är ogiltig. |
| AADSTS50105 | EntitlementGrantsNotFound - den inloggade användaren har inte tilldelats en roll för inloggad appen. Tilldela användaren till appen. Mer information:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri - den begärda federationssfärsobjekt finns inte. Kontakta administratören för klientorganisationen. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - problem med JWT-huvud. Kontakta administratören för klientorganisationen. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - anspråkstransformering innehåller ogiltiga indataparametrar. Kontakta administratören för klientorganisationen för att uppdatera principen. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - inloggningen avbröts på grund av en återställning av lösenord eller en post för registrering av lösenord. |
| AADSTS50126 | InvalidUserNameOrPassword - fel vid validering av autentiseringsuppgifter på grund av ogiltigt användarnamn eller lösenord. |
| AADSTS50127 | BrokerAppNotInstalled - användare behöver installera en koordinatorapp för att få åtkomst till det här innehållet. |
| AADSTS50128 | Ogiltigt domännamn – ingen information om identifiering av klient finns i antingen begäran eller underförstådda med angivna autentiseringsuppgifter. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined - anslutning till arbetsplats är obligatoriskt att registrera enheten. |
| AADSTS50131 | ConditionalAccessFailed - anger olika fel för villkorlig åtkomst, till exempel felaktiga Windows enhetens tillstånd, begäran som blockerats på grund av misstänkt aktivitet, åtkomstprincip eller säkerhet policybeslut. |
| AADSTS50132 | SsoArtifactInvalidOrExpired - sessionen är inte giltig på grund av lösenordet upphör att gälla eller lösenordsändring. |
| AADSTS50133 | SsoArtifactRevoked - sessionen är inte giltig på grund av lösenordet upphör att gälla eller lösenordsändring. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter - fel datacenter. Auktorisering part måste finnas i samma datacenter där den ursprungliga begäran finns för att godkänna en begäran som initierades av en app i flödet för OAuth 2.0-enhet. |
| AADSTS50135 | PasswordChangeCompromisedPassword – ändra lösenord krävs på grund av risken för kontot. |
| AADSTS50136 | RedirectMsaSessionToApp - enda MSA-session har identifierats. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - sessionen är ogiltig på grund av en extern uppdateringstoken som saknas. |
| AADSTS50140 | KmsiInterrupt – det här felet uppstod på grund av ”Håll mig inloggad” avbrott när användaren loggar in. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med korrelations-ID, ID för begäran och felkod för mer information. |
| AADSTS50143 | Felaktig matchning av Session - Session är ogiltig eftersom användaren klientorganisationen inte matchar tips för domänen på grund av en annan resurs.  [Öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med Korrelations-ID, ID för begäran och fel kod att få mer information. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - användarens Active Directory-lösenord har upphört att gälla. Skapa ett nytt lösenord för användaren eller att användaren använda lösenordsåterställning via Självbetjäning för att återställa sina lösenord. |
| AADSTS50146 | MissingCustomSigningKey - den här appen krävs att konfigureras med en appspecifik signeringsnyckel. Det har antingen inte konfigurerats med en sådan eller så har nyckeln upphört att gälla/är inte giltig än. |
| AADSTS50147 | MissingCodeChallenge - storleken på parametern kod utmaning är inte giltig. |
| AADSTS50155 | DeviceAuthenticationFailed - autentisering misslyckades för den här användaren. |
| AADSTS50158 | ExternalSecurityChallenge - externa säkerhetskontrollen uppfylldes inte. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - anspråk som skickas av en extern provider är inte tillräckligt eller saknas anspråk begärt till extern provider. |
| AADSTS50166 | Det gick inte att skicka begäran till anspråksleverantören ExternalClaimsProviderThrottled. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - klienten är kompatibel för att hämta en token för enkel inloggning via Windows 10-konton-tillägget men token hittades inte i förfrågan eller den angivna token har upphört att gälla. |
| AADSTS50169 | InvalidRequestBadRealm - området är inte en konfigurerade området aktuella namnområde för tjänsten. |
| AADSTS50170 | MissingExternalClaimsProviderMapping - externa kontrollerna mappning saknas. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - externa utmaning stöds inte för genomströmning användare. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - sessionskontrollen stöds inte för genomströmning användare. |
| AADSTS50180 | WindowsIntegratedAuthMissing - integrerad Windows-autentisering krävs. Aktivera klientorganisationen för sömlös SSO. |
| AADSTS50187 | DeviceInformationNotProvided - tjänsten kunde inte utföra autentisering. |
| AADSTS51000 | RequiredFeatureNotEnabled - funktionen är inaktiverad. |
| AADSTS51001 | DomainHintMustbePresent - tips för domänen måste finnas med lokala säkerhetsidentifierare eller lokala UPN. |
| AADSTS51004 | UserAccountNotInDirectory - användarkontot finns inte i katalogen. |
| AADSTS51005 | TemporaryRedirect - motsvarighet till HTTP-status 307, vilket betyder att den begärda informationen finns på den URI som anges i location-huvudet. När du får den här statusen kan du följa location-huvudet som är associerade med svaret. När begärandemetoden var INLÄGG, använder också omdirigerade begäran POST-metoden. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth - integrerad Windows-autentisering krävs. Användaren har loggat in med ett sessionstoken som saknar anspråk för integrerad Windows-autentisering. Be användaren att logga in igen. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - användaren har inte tillhandahållits medgivande för åtkomst till LinkedIn-resurser. |
| AADSTS53000 | DeviceNotCompliant - princip för villkorlig åtkomst kräver en kompatibel enhet och enheten är inte kompatibel. Användaren måste registrera sina enheter med en godkänd MDM-provider som Intune. |
| AADSTS53001 | DeviceNotDomainJoined - princip för villkorlig åtkomst kräver en domänansluten enhet och enheten är inte ansluten till en domän. Att användaren använder en domän har anslutit enheten. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp - appen som används är inte en godkänd app för villkorlig åtkomst. Användaren måste använda en av apparna i listan över godkända appar för att använda för att få åtkomst. |
| AADSTS53003 | BlockedByConditionalAccess - åtkomst har blockerats av principer för villkorlig åtkomst. Åtkomstprincipen tillåter inte utfärdande. |
| AADSTS53004 | ProofUpBlockedDueToRisk - användare måste slutföra registreringen för multifaktorautentisering innan du använder det här innehållet. Användaren bör registrera sig för multifaktorautentisering. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist - användaren eller administratören har inte godkänt att använda programmet med ID X. Send en interaktiv auktoriseringsbegäran för den här användaren och resursen. |
| AADSTS65004 | UserDeclinedConsent - användaren nekade samtycker till att komma åt appen. Låt användaren logga in igen och ge samtycke till appen|
| AADSTS65005 | MisconfiguredApplication - appen krävs åtkomst resurslistan innehåller inte appar som kan upptäckas av resursen eller klientappen har begärt åtkomst till resursen, som inte har angetts i dess nödvändig resurs åtkomstlista eller diagramtjänsten returnerade felaktiga begäran eller resursen hittades inte. Om appen stöder SAML, har du konfigurerat appen med fel-ID (entitet). Testa lösningen för SAML via länken nedan: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - autentiseringen misslyckades. Uppdateringstoken är inte giltig. Felet kan bero på följande orsaker:<ul><li>Token-bindning är tom</li><li>Token bindning hash matchar inte</li></ul> |
| AADSTS70001 | UnauthorizedClient - programmet har inaktiverats. |
| AADSTS70002 | InvalidClient - fel vid validering av autentiseringsuppgifterna. Angiven client_secret matchar inte det förväntade värdet för den här klienten. Korrigera client_secret och försök igen. Mer information finns i [använda Auktoriseringskoden för att begära en åtkomsttoken](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType - appen returnerade en beviljandetyp som inte stöds. |
| AADSTS70004 | InvalidRedirectUri - appen returnerade en ogiltig omdirigerings-URI. Adressen för omdirigering som angetts av klienten matchar inte några konfigurerade adresser eller någon adress på godkännandelistan för OIDC. |
| AADSTS70005 | UnsupportedResponseType - appen returnerade ett som inte stöds svarstypen på grund av följande orsaker:<ul><li>svarstypen ”token” har inte aktiverats för appen</li><li>svarstypen "id_token" kräver OpenID-omfång – innehåller ett OAuth-parametervärde som inte stöds i den kodade wctx</li></ul> |
| AADSTS70007 | UnsupportedResponseMode - appen returnerade ett värde som inte stöds av `response_mode` när du begär en token.  |
| AADSTS70008 | ExpiredOrRevokedGrant - uppdateringstoken har upphört att gälla på grund av inaktivitet. Token har utfärdats på XXX och har varit inaktiv under en viss tidsperiod. |
| AADSTS70011 | InvalidScope - med appen begärda omfång är ogiltig. |
| AADSTS70012 | MsaServerError - ett serverfel uppstod när en MSA (konsument)-användare. Försök igen. Om det fortsättningsvis misslyckas, [öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending - flöde för OAuth 2.0 enhetsfel. Auktorisering är väntande. Enheten försöker avsökning begäran. |
| AADSTS70018 | BadVerificationCode - ogiltig Verifieringskod på grund av användare att skriva in fel användarkod för kodflöde för enheten. Auktorisering godkänns inte. |
| AADSTS70019 | CodeExpired - Verifieringskod har upphört att gälla. Låt användaren försök logga in. |
| AADSTS75001 | BindingSerializationError – ett fel uppstod under bindningen för SAML-meddelande. |
| AADSTS75003 | UnsupportedBindingError - appen returnerade ett fel relaterat till bindning som inte stöds (SAML-protokollsvar kan inte skickas via bindningar än HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid – Azure AD stöder inte SAML-begäran som skickas av appen för enkel inloggning. |
| AADSTS75008 | RequestDeniedError - begäran från appen nekades eftersom SAML-begäran hade ett oväntat mål. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - autentiseringsmetoden som den användare som autentiseras med tjänsten inte matchar begärt autentiseringsmetod. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - SAML2 autentiseringsförfrågningar har ogiltig NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - autentisering-agenten kan inte ansluta till Active Directory. Se till att agentservrar är medlemmar i samma AD-skog som de användare vars lösenord behöver verifieras och kunna ansluta till Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - begäran om verifiering av lösenord tidsgränsen. Se till att Active Directory är tillgänglig och svarar på begäranden när du från agenter. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - ett okänt fel uppstod under bearbetningen av svaret från den Autentiseringsagenten. Gör om begäran. Om det fortsättningsvis misslyckas, [öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) att få mer information om felet. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - autentisering-agenten kan inte verifiera användarens lösenord. Kontrollera agentloggarna för mer information och verifiera att Active Directory fungerar som förväntat. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - autentisering-agenten kan inte dekryptera lösenordet. |
| AADSTS80012 | Användarna försökte logga in utanför det tillåtna OnPremisePasswordValidationAccountLogonInvalidHours - timmar (det har angetts i AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - Autentiseringsförsöket kunde inte slutföras på grund av för att tid skeva mellan den dator som kör autentiseringsagenten och AD. Åtgärda tid synkronisera problem. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Kerberos-autentiseringsförsök misslyckades. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - autentiseringspaketet stöds inte. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader – ingen auktoriseringsrubrik hittades. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - klienten har inte aktiverats för sömlös enkel inloggning. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - det går inte att verifiera användarens Kerberos-biljett. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - sömlös SSO misslyckades eftersom användarens Kerberos-biljett har upphört att gälla eller är ogiltig. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - det går inte att hitta användarobjektet baserat på informationen i användarens Kerberos-biljett. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - användaren som försöker logga in på Azure AD skiljer sig från användaren som har loggat in på enheten. |
| AADSTS90002 | InvalidTenantName - innehavarens namn hittades inte i datalagret. Kontrollera att du har rätt klient-ID. |
| AADSTS90004 | InvalidRequestFormat - begäran är inte korrekt formaterat. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements - det går inte att slutföra begäran. Begäran är inte giltig eftersom identifierare och inloggnings-tipset inte kan användas tillsammans.  |
| AADSTS90006 | ExternalServerRetryableError - tjänsten är inte tillgänglig för tillfället.|
| AADSTS90007 | InvalidSessionId - felaktig begäran. Skickade sessions-ID kan inte parsas. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - användaren eller administratören har inte godkänt att använda programmet. Minimikrav är programmet kräver åtkomst till Azure AD genom att ange inloggningen och läsbehörighet i användarens profil. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier - programmet begär en token för sig själva. Det här scenariot stöds endast om den resurs som har angetts använder GUID-baserade program-ID. |
| AADSTS90010 | NotSupported - det går inte att skapa algoritmen. |
| AADSTS90012 | RequestTimeout - den begärda har nått tidsgränsen. |
| AADSTS90013 | InvalidUserInput - indata från användaren är inte giltiga. |
| AADSTS90014 | MissingRequiredField - den här felkoden kan visas i olika fall när ett förväntat fält inte finns i autentiseringsuppgifterna. |
| AADSTS90015 | QueryStringTooLong - frågesträngen är för långt. |
| AADSTS90016 | MissingRequiredClaim - åtkomsttoken är inte giltig. Det nödvändiga anspråket saknas. |
| AADSTS90019 | MissingTenantRealm – Azure AD kunde inte fastställa klient-ID från begäran. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat - formatet huvudnamn är inte giltigt eller uppfyller inte den förväntade `name[/host][@realm]` format. Huvudnamnet krävs, värden och sfär är valfria och kan anges till null. |
| AADSTS90023 | InvalidRequest |
| AADSTS90024 | RequestBudgetExceededError - ett tillfälligt fel har uppstått. Försök igen. |
| AADSTS90033 | MsodsServiceUnavailable - Microsoft Online Directory Service (MSODS) är inte tillgänglig. |
| AADSTS90036 | MsodsServiceUnretryableFailure – en oväntad, icke-återförsöksbar fel från WCF-tjänst som tillhandahålls genom MSODS har uppstått. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet. |
| AADSTS90038 | NationalCloudTenantRedirection - den angivna innehavaren ”Y” tillhör den nationella moln ”X”. Aktuella molnbaserad instans ”Z” inte federera med X. Ett moln omdirigerings-fel returneras. |
| AADSTS90043 | NationalCloudAuthCodeRedirection - funktionen är inaktiverad. |
| AADSTS90051 | InvalidNationalCloudId - nationella moln-ID innehåller ett ogiltigt ID. |
| AADSTS90055 | TenantThrottlingError - det finns för många förfrågningar. Den här undantagsfel för blockerade klienter. |
| AADSTS90056 | BadResourceRequest - att lösa in koden för appen en åtkomst-token ska skicka en POST-begäran till den `/token` slutpunkt. Dessutom tidigare du ska ge en auktoriseringskod och skicka den i POST-begäran till den `/token` slutpunkt. I den här artikeln för en översikt av OAuth 2.0-auktoriseringskodflödet: [ https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Dirigera användare till den `/authorize` slutpunkt som returnerar en authorization_code. Genom att publicera en begäran om att den `/token` slutpunkt kan användaren får åtkomst-token. Logga in på Azure-portalen och kontrollera **appregistreringar > slutpunkter** att bekräfta att två slutpunkter har konfigurerats korrekt. |
| AADSTS90072 | PassThroughUserMfaError - det externa kontot som användaren loggar in med finns inte på den klient som de har loggat in på; så att användaren inte kan utföra MFA-kraven för klienten. Kontot måste läggas till som en extern användare i klientorganisationen först. Logga ut och logga in med en annan Azure AD-användarkonto. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid – ett fel uppstod när tjänsten försökte behandla ett meddelande om WS-Federation. Meddelandet är inte giltig. |
| AADSTS90082 | OrgIdWsFederationNotSupported - valda autentiseringsprincipen för begäran stöds inte för närvarande. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - gästkonton är inte tillåtna för den här platsen. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed - tjänsten kan inte utfärda en token eftersom företagets objektet inte har etablerats ännu. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired - användartoken DA har upphört att gälla. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed – ett fel inträffade när WS-Federation-meddelande från URI: N. |
| AADSTS90090 | GraphRetryableError - tjänsten är inte tillgänglig för tillfället. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - diagram som returneras med en otillåten felkod för begäran. |
| AADSTS90094 | AdminConsentRequired - administratörens godkännande krävs. |
| AADSTS90100 | InvalidRequestParameter - parametern är tomt eller ogiltigt. |
| AADSTS90101 | InvalidEmailAddress - angivna informationen är inte en giltig e-postadress. E-postadressen måste vara i formatet `someone@example.com`. |
| AADSTS90102 | InvalidUriParameter - värdet måste vara en giltig absolut URI. |
| AADSTS90107 | InvalidXml - begäran är inte giltig. Kontrollera att dina data inte har ogiltiga tecken.|
| AADSTS90114 | InvalidExpiryDate - giltighetstid för token-tidsstämpel samtidigt genereras en utgångna token utfärdas. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode - användarkoden som är null eller tomt.|
| AADSTS90120 | InvalidDeviceFlowRequest - begäran har redan behörighet eller nekas. |
| AADSTS90121 | InvalidEmptyRequest - ogiltig tom begäran.|
| AADSTS90123 | IdentityProviderAccessDenied - token inte kan utfärdas eftersom identitet eller anspråk utfärdande providern nekade begäran. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported - resurs stöds inte över den `/common` eller `/consumers` slutpunkter. Använd den `/organizations` eller klientspecifik slutpunkt i stället. |
| AADSTS90125 | DebugModeEnrollTenantNotFound - användaren finns inte i systemet. Kontrollera att du har angett användarnamnet korrekt. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred - användartypen stöds inte på den här slutpunkten. Systemet kan inte sluta användarens klient från användarnamnet. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported - programmet stöds inte över den `/common` eller `/consumers` slutpunkter. Använd den `/organizations` eller klientspecifik slutpunkt i stället. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated – en icke-återförsöksbar fel har uppstått.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound - användares huvudnamn har inte NGC ID-nyckel som har angetts. |
| AADSTS130005 | Det gick inte att NgcInvalidSignature - NGC nyckel signaturen verifieras.|
| AADSTS130006 | NgcTransportKeyNotFound - NGC transport nyckel har inte konfigurerats på enheten. |
| AADSTS130007 | NgcDeviceIsDisabled - enheten är inaktiverad. |
| AADSTS130008 | NgcDeviceIsNotFound - enheten som refererar till NGC nyckeln hittades inte. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce - nonce för begäran har inte angetts. |
| AADSTS140001 | InvalidSessionKey - nyckeln är inte giltig.|
| AADSTS165900 | InvalidApiRequest - ogiltig begäran. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion - Chrome WebView-versionen stöds inte. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - resursen har inte konfigurerats för att acceptera endast token. |
| AADSTS240001 | BulkAADJTokenUnauthorized - användaren behörighet inte att registrera enheter i Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing - id_token kan inte användas som `urn:ietf:params:oauth:grant-type:jwt-bearer` bevilja.|
| AADSTS700020 | InteractionRequired - beviljad åtkomst kräver interaktion. |
| AADSTS700022 | InvalidMultipleResourcesScope - det angivna värdet för Indataparametern omfånget är inte giltig eftersom den innehåller fler än en resurs. |
| AADSTS700023 | InvalidResourcelessScope - det angivna värdet för Indataparametern omfånget är inte giltig när begär en åtkomsttoken. |
| AADSTS1000000 | UserNotBoundError - API: et binda kräver Azure AD-användare kan också autentisera med en extern IDP som inte har utförts än. |
| AADSTS1000002 | BindCompleteInterruptError - bindning har slutförts, men användaren måste informeras. |

## <a name="next-steps"></a>Nästa steg

* Har du en fråga eller kan inte hitta det du letar efter? Skapa ett GitHub-ärende eller se [Support och hjälper dig att alternativ för utvecklare](active-directory-develop-help-support.md) vill veta mer om andra sätt du kan få hjälp och support.
