---
title: 'Lokalisering sträng ID: N – Azure Active Directory B2C | Microsoft Docs'
description: 'Ange ID: N för en innehållsdefinition med Id api.signuporsignin i en anpassad princip i Azure Active Directory B2C.'
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: cbc96f7f7c5f8020472708caaead3faff7a26673
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405734"
---
# <a name="localization-string-ids"></a>Lokalisering sträng ID: N

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den **lokalisering** elementet låter dig stöd för flera språk eller språk i principen för användaren resor. Den här artikeln innehåller en lista över lokalisering ID: N som du kan använda i din princip. Om du vill bekanta dig med Användargränssnittet lokalisering, se [lokalisering](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Registrering eller inloggning sidelement

Följande-ID som används för en innehållsdefinitionen vid ID `api.signuporsignin`.

| ID | Standardvärde |
| -- | ------------- |
| **local_intro_email** | Logga in med ditt befintliga konto |
| **logonIdentifier_email** | E-postadress |
| **requiredField_email** | Ange din e-postadress |
| **invalid_email** | Ange en giltig e-postadress |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&’'*+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)*$ |
| **local_intro_username** | Logga in med ditt användarnamn |
| **logonIdentifier_username** | Användarnamn |
| **requiredField_username** | Ange ditt användarnamn |
| **Lösenord** | Lösenord |
| **requiredField_password** | Ange ditt lösenord |
| **invalid_password** | Det angivna lösenordet är inte i förväntat format. |
| **forgotpassword_link** | Har du glömt lösenordet? |
| **createaccount_intro** | Har du inte något konto? |
| **createaccount_link** | Registrera dig nu |
| **divider_title** | ELLER |
| **cancel_message** | Användaren har glömt sitt lösenord |
| **button_signin** | Logga in |
| **social_intro** | Logga in med ditt sociala konto |
  **remember_me** |Jag vill förbli inloggad|
| **unknown_error** | Vi har problem med att logga in dig. Försök igen senare. |

I följande exempel visar användningen av vissa av användaren i gränssnittet på sidan registrering eller inloggning:

![Registrering eller inloggning UX sidelement](./media/localization-string-ids/localization-susi.png)

ID för identitetsprovidrarna som har konfigurerats i användarresan **ClaimsExchange** element. För att hitta titeln på identitetsprovidern, och den **ElementType** är inställd på `ClaimsProvider`, medan den **StringId** har angetts till ID: T för den `ClaimsExchange`.

```XML
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

I följande exempel localizes Facebook identitetsprovider till arabiska:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Registrering eller inloggning felmeddelanden

| ID | Standardvärde |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Ditt lösenord är fel. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Vi verkar inte kunna hitta ditt konto. |
| **UserMessageIfOldPasswordUsed** | Det ser ut som om du har använt ett gammalt lösenord. |  
| **DefaultMessage** | Ogiltigt användarnamn eller lösenord. |  
| **UserMessageIfUserAccountDisabled** | Ditt konto har låsts. Kontakta supporten om du vill låsa upp det och försök igen. |  
| **UserMessageIfUserAccountLocked** | Ditt konto har låsts temporärt för att förhindra obehörig användning. Försök igen senare. |  
| **AADRequestsThrottled** | Det finns för många förfrågningar just nu. Vänta en stund och försök igen. |  

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Registrera dig och självsignerat verifieringsvillkor sidor element för användargränssnitt

Följande är de ID: N för en innehållsdefinition med ID `api.localaccountsignup` eller alla innehållsdefinition som börjar med `api.selfasserted`, till exempel `api.selfasserted.profileupdate` och `api.localaccountpasswordreset`.

| ID | Standardvärde |
| -- | ------------- |
| **ver_sent** | Verifieringskoden har skickats till: |
| **ver_but_default** | Standard |
| **cancel_message** | Användaren har avbrutit att ange självkontrollerad information |
| **preloader_alt** | Vänta |
| **ver_but_send** | Skicka verifieringskod |
| **alert_yes** | Ja |
| **error_fieldIncorrect** | En eller flera fält är ifyllda felaktigt. Kontrollera posterna och försök igen. |
| **År** | År |
| **verifying_blurb** | Vänta medan vi bearbetar din information. |
| **button_cancel** | Avbryt |
| **ver_fail_no_retry** | Du har gjort för många felaktiga försök. Försök igen senare. |
| **Månad** | Månad |
| **ver_success_msg** | E-postadress som verifierats. Du kan nu fortsätta. |
| **månader** | Januari, februari, mars, april, maj, juni, juli, augusti, september, oktober, november, december |
| **ver_fail_server** | Vi har problem med att verifiera din e-postadress. Ange en giltig e-postadress och försök igen. |
| **error_requiredFieldMissing** | Ett obligatoriskt fält saknas. Fyll i alla obligatoriska fält och försök igen. |
| **initial_intro** | Ange följande information. |
| **ver_but_resend** | Skicka ny kod |
| **button_continue** | Skapa |
| **error_passwordEntryMismatch** | Inmatningsfält lösenordet stämmer inte överens. Ange samma lösenord i bägge fälten och försök igen. |
| **ver_incorrect_format** | Felaktigt format. |
| **ver_but_edit** | Ändra e-post |
| **ver_but_verify** | Verifiera koden |
| **alert_no** | Nej |
| **ver_info_msg** | Verifieringskod har skickats till din inkorg. Kopiera den till rutan nedan. |
| **dag** | Dag |
| **ver_fail_throttled** | Det har förekommit för många begäranden för att verifiera det här e-postadress. . Vänta en stund och försök igen. |
| **helplink_text** | Vad är detta? |
| **ver_fail_retry** | Koden är felaktig. Försök igen. |
| **alert_title** | Avbryt inmatningen av din information |
| **required_field** | Den här informationen är obligatorisk. |
| **alert_message** | Är du säker på att du vill avbryta inmatningen av din information? |
| **ver_intro_msg** | Verifiering krävs. Klicka på knappen Skicka. |
| **ver_input** | Verifieringskod |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Registrera dig och självsignerat verifieringsvillkor sidor felmeddelanden

| ID | Standardvärde |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Det finns redan en användare med det angivna ID: T. Välj ett annat namn. |
| **UserMessageIfClaimNotVerified** | Anspråk som inte verifieras: {0} |
| **UserMessageIfIncorrectPattern** | Ogiltigt mönster för: {0} |
| **UserMessageIfMissingRequiredElement** | Saknar obligatoriska element: {0} |
| **UserMessageIfValidationError** | Fel vid validering av: {0} |
| **UserMessageIfInvalidInput** | {0} har ogiltiga indata. |
| **ServiceThrottled** | Det finns för många förfrågningar just nu. Vänta en stund och försök igen. |

I följande exempel visar användningen av vissa av användargränssnittets delar i registreringssidan för:

![Registreringssidan UX-element](./media/localization-string-ids/localization-sign-up.png)

I följande exempel visar användningen av vissa av användargränssnittets delar i registreringssidan, när användaren klickar på Skicka knappen kod för verifiering:

![Registreringssida för e-verifiering UX element](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Phone factor authentication-sidan användare element för användargränssnitt

Följande är de ID: N för en innehållsdefinition med ID `api.phonefactor`. 

| ID | Standardvärde |
| -- | ------------- |
| **button_verify** | Ring mig |
| **country_code_label** | Landskod |
| **cancel_message** | Användaren har avbrutit multifaktorautentisering |
| **text_button_send_second_code** | skicka en ny kod |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Vi har följande numret på posten för dig. Vi kan skicka en kod via SMS eller telefonnummer för att autentisera dig. |
| **intro_mixed_p** | Vi har följande nummer på posten för dig. Välj ett tal att vi kan phone eller skicka en kod via SMS för att autentisera dig. |
| **button_verify_code** | Verifiera koden |
| **requiredField_code** | Ange den verifieringskod du fick |
| **invalid_code** | Ange den sexsiffriga koden du fick |
| **button_cancel** | Avbryt |
| **local_number_input_placeholder_text** | Telefonnummer |
| **button_retry** | Försök igen |
| **alternative_text** | Jag har inte min telefon |
| **intro_phone_p** | Vi har följande nummer på posten för dig. Välj ett tal som vi kan telefonnummer för att autentisera dig. |
| **intro_phone** | Vi har följande numret på posten för dig. Vi kommer telefonnummer för att autentisera dig. |
| **enter_code_text_intro** | Ange verifieringskoden nedan eller  |
| **intro_entry_phone** | Ange ett nummer nedan så att vi kan ringa upp för att autentisera dig. |
| **intro_entry_sms** | Ange ett nummer nedan, så att vi kan autentisera dig genom att skicka en kod via SMS. |
| **button_send_code** | Skicka kod |
| **invalid_number** | Ange ett giltigt telefonnummer |
| **intro_sms** | Vi har följande numret på posten för dig. Vi skickar en kod via SMS för att autentisera dig. |
| **intro_entry_mixed** | Ange ett nummer nedan så att vi kan autentisera dig genom skicka en kod via SMS eller ringa upp dig. |
| **number_pattern** | ^\\+ (?: [0-9] [\\x20-]?) {6,14}[0-9] $ |
| **intro_sms_p** |Vi har följande nummer på posten för dig. Välj ett tal att vi kan skicka en kod via SMS för att autentisera dig. |
| **requiredField_countryCode** | Välj din landskod |
| **requiredField_number** | Ange ditt telefonnummer |
| **country_code_input_placeholder_text** |Land eller region |
| **number_label** | Telefonnummer |
| **error_tryagain** | Det angivna telefonnumret är upptagen eller otillgänglig. Kontrollera numret och försök igen. |
| **error_incorrect_code** | Den Verifieringskod du angett matchar inte våra poster. Försök igen eller begära en ny kod. |
| **countryList** | {\"Standard\":\"Land/Region\",\"AF\":\"Afghanistan\",\"AX\":\"Åland Islands\",\"AL\":\"Albanien\",\"DZ\":\"Algeriet\",\"AS\":\" Amerikanska Samoa\",\"AD\":\"Andorra\",\"AO\":\"Angola\",\"AI\": \"Anguilla\",\"AQ\":\"Antarktis\",\"AG\":\"Antigua och Barbuda\",\"AR\":\"Argentina\",\"AM\":\"Armenien\",\"AW\":\"Aruba \",\"AU\":\"Australien\",\"på\":\"Österrike\",\" AZ\":\"Azerbajdzjan\",\"BS\":\"Bahamas\",\"BH\":\" Bahrain\",\"BD\":\"Bangladesh\",\"BB\":\"Barbados\",\" GENOM att\":\"Vitryssland\",\"BE\":\"Belgien\",\"BZ\":\" Belize\",\"BJ\":\"Benin\",\"BM\":\"Bermudaöarna\",\"BT\":\"Bhutan\",\"BO\":\"Bolivia\",\"BQ\":\" Bonaire\",\"BA\":\"Bosnien och Hercegovina\",\"BW\":\"Botswana<span class="notransla class=""></span class="notransla> : S yttre öar\",\"VI\":\"USA Jungfruöarna\",\"UG\":\"Uganda\",\"UA\":\"Ukraina\",\"AE\":\" Förenade Arabemiraten\",\"GB\":\"Storbritannien\",\"USA\":\"USA\",\"UY \":\"Uruguay\",\"UZ\":\"Uzbekistan\",\"Anteckning\":\"Vanuatu\", \"VA\":\"Vatikanstaten\",\"Stött\":\"Venezuela\",\"VN\":\"Vietnam \",\"WF\":\"Wallis och Futuna\",\"YE\":\"Jemen\",\"ZM\":\"Zambia\",\"ZW\":\"Zimbabwe\"} |
| **error_448** | Det angivna telefonnumret går inte att nå. |
| **error_449** | Användaren har överskridit antalet tillåtna återförsök. |
| **verification_code_input_placeholder_text** | Verifieringskod |

I följande exempel visar användningen av vissa av användargränssnittets delar på sidan för MFA-registrering:

![Registreringssida för e-verifiering UX element](./media/localization-string-ids/localization-mfa1.png)

I följande exempel visar användningen av vissa av användargränssnittets delar på sidan för MFA-verifiering:

![Registreringssida för e-verifiering UX element](./media/localization-string-ids/localization-mfa2.png)







