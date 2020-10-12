---
title: 'Lokaliserings Strängs-ID: n Azure Active Directory B2C | Microsoft Docs'
description: 'Ange ID: n för en innehålls definition med ID: t för API. signuporsignin i en anpassad princip i Azure Active Directory B2C.'
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 611d676f5f588ff32f981692456160e269642a43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87428120"
---
# <a name="localization-string-ids"></a>Sträng-ID för lokalisering

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Lokaliserings** elementet gör att du kan stödja flera språk eller språk i principen för användar resan. Den här artikeln innehåller en lista över lokaliserings-ID: n som du kan använda i principen. För att bekanta dig med användar gränssnitts lokalisering, se [lokalisering](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Sid element för registrering eller inloggning

Följande ID: n används för en innehålls definition med ID `api.signuporsignin` .

| ID | Standardvärde |
| -- | ------------- |
| **local_intro_email** | Logga in med ditt befintliga konto |
| **logonIdentifier_email** | E-postadress |
| **requiredField_email** | Ange din e-postadress |
| **invalid_email** | Ange en giltig e-postadress |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' '*+/=? ^ _ \` { \| } ~-] + @ [a-za-Z0-9-] + (?: \\ . [ a-zA-Z0-9-] +)*$ |
| **local_intro_username** | Logga in med ditt användar namn |
| **logonIdentifier_username** | Användarnamn |
| **requiredField_username** | Ange ditt användar namn |
| **lösenord** | lösenordsinställning |
| **requiredField_password** | Ange ditt lösen ord |
| **invalid_password** | Det angivna lösen ordet har inte det förväntade formatet. |
| **forgotpassword_link** | Glömt ditt lösenord? |
| **createaccount_intro** | Har du inte något konto? |
| **createaccount_link** | Registrera dig nu |
| **divider_title** | ELLER |
| **cancel_message** | Användaren har glömt sitt lösen ord |
| **button_signin** | Logga in |
| **social_intro** | Logga in med ditt sociala konto |
  **remember_me** |Jag vill förbli inloggad|
| **unknown_error** | Vi har problem med att logga in dig. Försök igen senare. |

I följande exempel visas användningen av några av användar gränssnitts elementen på sidan för registrering eller inloggning:

![Registrerings-eller inloggnings sidans UX-element](./media/localization-string-ids/localization-susi.png)

ID: t för identitets leverantörerna konfigureras i användar resan  **ClaimsExchange** -elementet. Vid lokalisering av identitets leverantörens rubrik är **ElementType** inställt på `ClaimsProvider` , medan **StringId** anges till ID för `ClaimsExchange` .

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

I följande exempel lokaliserar Facebook Identity Provider till arabiska:

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Fel meddelanden vid registrering eller inloggning

| ID | Standardvärde |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Lösen ordet är felaktigt. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Vi kan inte hitta ditt konto. |
| **UserMessageIfOldPasswordUsed** | Det verkar som om du använde ett gammalt lösen ord. |
| **DefaultMessage** | Ogiltigt användar namn eller lösen ord. |
| **UserMessageIfUserAccountDisabled** | Ditt konto har låsts. Kontakta supporten för att låsa upp den och försök sedan igen. |
| **UserMessageIfUserAccountLocked** | Ditt konto är tillfälligt låst för att förhindra obehörig användning. Försök igen senare. |
| **AADRequestsThrottled** | Det finns för många begär Anden just nu. Vänta en stund och försök igen. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Användar gränssnitts element för registrering och självkontrollerade sidor

Följande är ID: n för en innehålls definition med ID eller en `api.localaccountsignup` innehålls definition som börjar med `api.selfasserted` , till exempel `api.selfasserted.profileupdate` och `api.localaccountpasswordreset` .

| ID | Standardvärde |
| -- | ------------- |
| **ver_sent** | Verifierings koden har skickats till: |
| **ver_but_default** | Default |
| **cancel_message** | Användaren har avbrutit registrering av självkontrollerad information |
| **preloader_alt** | Vänta |
| **ver_but_send** | Skicka verifierings kod |
| **alert_yes** | Ja |
| **error_fieldIncorrect** | Ett eller flera fält har fyllts i felaktigt. Kontrol lera dina poster och försök igen. |
| **år** | Year |
| **verifying_blurb** | Vänta medan vi bearbetar din information. |
| **button_cancel** | Avbryt |
| **ver_fail_no_retry** | Du har gjort för många felaktiga försök. Försök igen senare. |
| **månaderna** | Månad |
| **ver_success_msg** | E-postadress verifierad. Nu kan du fortsätta. |
| **months** | Januari, februari, mars, april, maj, juni, juli, augusti, september, oktober, november, december |
| **ver_fail_server** | Vi har problem med att verifiera din e-postadress. Ange en giltig e-postadress och försök igen. |
| **error_requiredFieldMissing** | Ett obligatoriskt fält saknas. Fyll i alla obligatoriska fält och försök igen. |
| **initial_intro** | Ange följande information. |
| **ver_but_resend** | Skicka ny kod |
| **button_continue** | Skapa |
| **error_passwordEntryMismatch** | Fälten för lösen ords inmatning stämmer inte överens. Ange samma lösen ord i båda fälten och försök igen. |
| **ver_incorrect_format** | Felaktigt format. |
| **ver_but_edit** | Ändra e-post |
| **ver_but_verify** | Verifiera koden |
| **alert_no** | Inga |
| **ver_info_msg** | Verifierings koden har skickats till din inkorg. Kopiera den till inmatade rutan nedan. |
| **dagen** | Dag |
| **ver_fail_throttled** | Det finns för många begär Anden att verifiera den här e-postadressen. Vänta en stund och försök sedan igen. |
| **helplink_text** | Vad är detta? |
| **ver_fail_retry** | Den koden är felaktig. Försök igen. |
| **alert_title** | Avbryt inmatning av information |
| **required_field** | Den här informationen krävs. |
| **alert_message** | Är du säker på att du vill avbryta inmatningen av din information? |
| **ver_intro_msg** | Verifiering är nödvändig. Klicka på knappen Skicka. |
| **ver_input** | Verifieringskod |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Fel meddelanden för registrering och självkontrollerade sidor

| ID | Standardvärde |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Det finns redan en användare med det angivna ID: t. Välj en annan. |
| **UserMessageIfClaimNotVerified** | Anspråk har inte verifierats: {0} |
| **UserMessageIfIncorrectPattern** | Felaktigt mönster för: {0} |
| **UserMessageIfMissingRequiredElement** | Element som krävs saknas: {0} |
| **UserMessageIfValidationError** | Fel vid verifiering av: {0} |
| **UserMessageIfInvalidInput** | {0} innehåller ogiltiga indatatyper. |
| **ServiceThrottled** | Det finns för många begär Anden just nu. Vänta en stund och försök igen. |

I följande exempel visas användningen av några av användar gränssnitts elementen på registrerings sidan:

![Registrerings sida med dess GRÄNSSNITTs element namn märkta](./media/localization-string-ids/localization-sign-up.png)

I följande exempel visas användningen av några av användar gränssnitts elementen på registrerings sidan när användaren klickar på knappen Skicka verifierings kod:

![Registrerings sidan för e-postverifiering av sidan UX-element](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Användar gränssnitts element för sidan med telefon faktorns autentisering

Följande är ID: n för en innehålls definition med ID för `api.phonefactor` .

| ID | Standardvärde |
| -- | ------------- |
| **button_verify** | Ring mig |
| **country_code_label** | Landskod |
| **cancel_message** | Användaren har avbrutit Multi-Factor Authentication |
| **text_button_send_second_code** | Skicka en ny kod |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Vi har följande nummer för att registrera dig. Vi kan skicka en kod via SMS eller telefon för att autentisera dig. |
| **intro_mixed_p** | Vi har följande nummer för att registrera dig. Välj ett nummer som vi kan ringa eller skicka en kod via SMS för att autentisera dig. |
| **button_verify_code** | Verifiera kod |
| **requiredField_code** | Ange den verifierings kod du fick |
| **invalid_code** | Ange den 6-siffriga kod du fick |
| **button_cancel** | Avbryt |
| **local_number_input_placeholder_text** | Telefonnummer |
| **button_retry** | Försök igen |
| **alternative_text** | Jag har inte min telefon |
| **intro_phone_p** | Vi har följande nummer för att registrera dig. Välj ett tal som vi kan använda för att autentisera dig. |
| **intro_phone** | Vi har följande nummer för att registrera dig. Vi kommer att autentisera dig. |
| **enter_code_text_intro** | Ange din verifierings kod nedan eller  |
| **intro_entry_phone** | Ange ett nummer nedan så att vi kan autentisera dig. |
| **intro_entry_sms** | Ange ett nummer nedan så att vi kan skicka en kod via SMS för att autentisera dig. |
| **button_send_code** | Skicka kod |
| **invalid_number** | Ange ett giltigt telefonnummer |
| **intro_sms** | Vi har följande nummer för att registrera dig. Vi kommer att skicka en kod via SMS för att autentisera dig. |
| **intro_entry_mixed** | Ange ett nummer nedan som vi kan skicka en kod via SMS eller telefon för att autentisera dig. |
| **number_pattern** | ^\\+ (?: [0-9] [ \\ x20-]?) {6,14} [0-9] $ |
| **intro_sms_p** |Vi har följande nummer för att registrera dig. Välj ett nummer som vi kan skicka en kod via SMS för att autentisera dig. |
| **requiredField_countryCode** | Välj landskod |
| **requiredField_number** | Ange ditt telefonnummer |
| **country_code_input_placeholder_text** |Land eller region |
| **number_label** | Telefonnummer |
| **error_tryagain** | Det angivna telefonnumret är upptaget eller inte tillgängligt. Kontrol lera numret och försök igen. |
| **error_incorrect_code** | Den verifierings kod som du har angett matchar inte våra poster. Försök igen eller begär en ny kod. |
| **countryList** | Se [listan över länder](#countries-list). |
| **error_448** | Det angivna telefonnumret kan inte kontaktas. |
| **error_449** | Användaren har överskridit antalet nya försök. |
| **verification_code_input_placeholder_text** | Verifieringskod |

I följande exempel visas användningen av några av användar gränssnitts elementen på sidan MFA-registrering:

![Registrerings sidan för e-postverifiering av sidan UX-element](./media/localization-string-ids/localization-mfa1.png)

I följande exempel visas användningen av vissa användar gränssnitts element på sidan MFA-verifiering:

![Registrerings sidan för e-postverifiering av sidan UX-element](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>Verifiering Visa kontroll element för användar gränssnitt

Följande är ID: n för en [verifierings visnings kontroll](display-control-verification.md)

| ID | Standardvärde |
| -- | ------------- |
|verification_control_but_change_claims |Ändra |
|verification_control_fail_send_code |Det gick inte att skicka koden, försök igen senare. |
|verification_control_fail_verify_code |Det gick inte att verifiera koden, försök igen senare. |
|verification_control_but_send_code |Skicka kod |
|verification_control_but_send_new_code |Skicka ny kod |
|verification_control_but_verify_code |Verifiera kod |
|verification_control_code_sent| Verifierings koden har skickats. Kopiera den till inmatade rutan nedan. |

### <a name="example"></a>Exempel

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Fel meddelanden för RESTful-tjänsten

Följande är ID: n för [RESTful-tjänstens tekniska profil](restful-technical-profile.md) fel meddelanden:

| ID | Standardvärde |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Det gick inte att upprätta en anslutning till RESTful-tjänstens slut punkt. RESTful tjänst-URL: {0} |
|UserMessageIfCircuitOpen | {0} RESTful tjänst-URL: {1} |
|UserMessageIfDnsResolutionFailed | Det gick inte att matcha värd namnet för RESTful-tjänstens slut punkt. RESTful tjänst-URL: {0} |
|UserMessageIfRequestTimeout | Det gick inte att upprätta en anslutning till RESTful-tjänstens slut punkt inom tids gränsen på {0} sekunder. RESTful tjänst-URL: {1} |


### <a name="example"></a>Exempel

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-mfa-error-messages"></a>Fel meddelanden i Azure MFA

Följande är ID: n för fel meddelanden i [Azure MFA Technical Profile](multi-factor-auth-technical-profile.md) :

| ID | Standardvärde |
| -- | ------------- |
|UserMessageIfCouldntSendSms | Det går inte att skicka SMS till telefonen. försök med ett annat telefonnummer. |
|UserMessageIfInvalidFormat | Telefonnumret har ett ogiltigt format, korrigera det och försök igen.|
|UserMessageIfMaxAllowedCodeRetryReached | Fel kod angavs för många gånger, försök igen senare.|
|UserMessageIfServerError | Det går inte att använda MFA-tjänsten, försök igen senare.|
|UserMessageIfThrottled | Din begäran har begränsats, försök igen senare.|
|UserMessageIfWrongCodeEntered|Fel kod angavs, försök igen.|

### <a name="example"></a>Exempel

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Azure AD-SSPR

Följande är ID: n för fel meddelanden i [Azure AD SSPR Technical Profile](aad-sspr-technical-profile.md) :

| ID | Standardvärde |
| -- | ------------- |
|UserMessageIfChallengeExpired | Koden har upphört att gälla.|
|UserMessageIfInternalError | Ett internt fel uppstod i e-posttjänsten, försök igen senare.|
|UserMessageIfThrottled | Du har skickat för många begär Anden, försök igen senare.|
|UserMessageIfVerificationFailedNoRetry | Du har överskridit det maximala antalet verifierings försök.|
|UserMessageIfVerificationFailedRetryAllowed | Verifieringen misslyckades, försök igen.|


### <a name="example"></a>Exempel

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Ett lösen ord fel meddelanden

Följande är ID: n för ett [eng ång slö sen ord –](one-time-password-technical-profile.md) fel meddelanden i teknisk profil

| ID | Standardvärde |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |Verifieringen av ett angivet lösen ord har överskridit maximalt antal försök |
|UserMessageIfSessionDoesNotExist |En session med lösen ords verifiering har upphört att gälla |
|UserMessageIfSessionConflict |En session med lösen ords verifiering har en konflikt |
|UserMessageIfInvalidCode |Ett lösen ord som angetts för verifiering är felaktigt |
|UserMessageIfVerificationFailedRetryAllowed |Den koden är felaktig. Försök igen. | 

### <a name="example"></a>Exempel

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>Fel meddelanden för anspråks omvandlingar

Följande är ID: n för fel meddelanden för anspråks omvandlingar:

| ID | Anspråkstransformering | Standardvärde |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | Jämförelse av värde för boolesk anspråk misslyckades för anspråks typen "inputClaim".| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | Jämförelse av anspråks värde misslyckades: den angivna vänstra operanden är större än den högra operanden.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | Jämförelse av anspråks värde misslyckades med StringComparison "OrdinalIgnoreCase".|

### <a name="example"></a>Exempel

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="countries-list"></a>Lista över länder

Följande är de countryList-värden som används av Multi-Factor Authentication.

```JSON
{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om lokaliserings exempel:

- [Språk anpassning med anpassad princip i Azure Active Directory B2C](custom-policy-localization.md)
- [Språk anpassning med användar flöden i Azure Active Directory B2C](user-flow-language-customization.md)