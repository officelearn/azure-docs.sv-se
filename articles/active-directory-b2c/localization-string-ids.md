---
title: 'Lokaliserings Strängs-ID: n Azure Active Directory B2C | Microsoft Docs'
description: 'Ange ID: n för en innehålls definition med ID: t för API. signuporsignin i en anpassad princip i Azure Active Directory B2C.'
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: da1390de4e2eb0624032dc490416e7b6e5d61baa
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846836"
---
# <a name="localization-string-ids"></a>Lokaliserings Strängs-ID: n

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Lokaliserings** elementet gör att du kan stödja flera språk eller språk i principen för användar resan. Den här artikeln innehåller en lista över lokaliserings-ID: n som du kan använda i principen. För att bekanta dig med användar gränssnitts lokalisering, se [lokalisering](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Sid element för registrering eller inloggning

Följande ID: n används för en innehålls definition med ID `api.signuporsignin`.

| id | Standardvärde |
| -- | ------------- |
| **local_intro_email** | Logga in med ditt befintliga konto |
| **logonIdentifier_email** | E-postadress |
| **requiredField_email** | Ange din e-postadress |
| **invalid_email** | Ange en giltig e-postadress |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&’' *+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)* $ |
| **local_intro_username** | Logga in med ditt användar namn |
| **logonIdentifier_username** | Användarnamn |
| **requiredField_username** | Ange ditt användar namn |
| **Lösenord** | lösenordsinställning |
| **requiredField_password** | Ange ditt lösen ord |
| **invalid_password** | Det angivna lösen ordet har inte det förväntade formatet. |
| **forgotpassword_link** | Glömt ditt lösenord? |
| **createaccount_intro** | Har du inte något konto? |
| **createaccount_link** | Registrera dig nu |
| **divider_title** | ELLER |
| **cancel_message** | Användaren har glömt sitt lösen ord |
| **button_signin** | Logga in |
| **social_intro** | Logga in med ditt sociala konto |
  **remember_me** |Håll mig inloggad|
| **unknown_error** | Vi har problem med att logga in dig. Försök igen senare. |

I följande exempel visas användningen av några av användar gränssnitts elementen på sidan för registrering eller inloggning:

![Registrerings-eller inloggnings sidans UX-element](./media/localization-string-ids/localization-susi.png)

ID: t för identitets leverantörerna konfigureras i användar resan **ClaimsExchange** -elementet. Vid lokalisering av identitets leverantörens rubrik är **ElementType** inställt på `ClaimsProvider`, medan **StringId** `ClaimsExchange`anges till ID för.

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

I följande exempel lokaliserar Facebook Identity Provider till arabiska:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Fel meddelanden vid registrering eller inloggning

| id | Standardvärde |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Lösen ordet är felaktigt. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Vi kan inte hitta ditt konto. |
| **UserMessageIfOldPasswordUsed** | Det verkar som om du använde ett gammalt lösen ord. |
| **DefaultMessage** | Ogiltigt användar namn eller lösen ord. |
| **UserMessageIfUserAccountDisabled** | Ditt konto har låsts. Kontakta supporten för att låsa upp den och försök sedan igen. |
| **UserMessageIfUserAccountLocked** | Ditt konto är tillfälligt låst för att förhindra obehörig användning. Försök igen senare. |
| **AADRequestsThrottled** | Det finns för många begär Anden just nu. Vänta en stund och försök igen. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Användar gränssnitts element för registrering och självkontrollerade sidor

Följande är ID: n för en innehålls definition med `api.localaccountsignup` ID eller en innehålls definition som börjar med `api.selfasserted`, till exempel `api.selfasserted.profileupdate` och `api.localaccountpasswordreset`.

| id | Standardvärde |
| -- | ------------- |
| **ver_sent** | Verifierings koden har skickats till: |
| **ver_but_default** | Standard |
| **cancel_message** | Användaren har avbrutit registrering av självkontrollerad information |
| **preloader_alt** | Snälla vänta |
| **ver_but_send** | Skicka verifierings kod |
| **alert_yes** | Ja |
| **error_fieldIncorrect** | Ett eller flera fält har fyllts i felaktigt. Kontrol lera dina poster och försök igen. |
| **år** | År |
| **verifying_blurb** | Vänta medan vi bearbetar din information. |
| **button_cancel** | Avbryt |
| **ver_fail_no_retry** | Du har gjort för många felaktiga försök. Försök igen senare. |
| **månaderna** | Månad |
| **ver_success_msg** | E-postadress verifierad. Nu kan du fortsätta. |
| **månader** | Januari, februari, mars, april, maj, juni, juli, augusti, september, oktober, november, december |
| **ver_fail_server** | Vi har problem med att verifiera din e-postadress. Ange en giltig e-postadress och försök igen. |
| **error_requiredFieldMissing** | Ett obligatoriskt fält saknas. Fyll i alla obligatoriska fält och försök igen. |
| **initial_intro** | Ange följande information. |
| **ver_but_resend** | Skicka ny kod |
| **button_continue** | Skapa |
| **error_passwordEntryMismatch** | Fälten för lösen ords inmatning stämmer inte överens. Ange samma lösen ord i båda fälten och försök igen. |
| **ver_incorrect_format** | Felaktigt format. |
| **ver_but_edit** | Ändra e-post |
| **ver_but_verify** | Verifiera kod |
| **alert_no** | Nej |
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

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Fel meddelanden för registrering och självkontrollerade sidor

| id | Standardvärde |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Det finns redan en användare med det angivna ID: t. Välj en annan. |
| **UserMessageIfClaimNotVerified** | Anspråk har inte verifierats:{0} |
| **UserMessageIfIncorrectPattern** | Felaktigt mönster för:{0} |
| **UserMessageIfMissingRequiredElement** | Element som krävs saknas:{0} |
| **UserMessageIfValidationError** | Fel vid verifiering av:{0} |
| **UserMessageIfInvalidInput** | {0}innehåller ogiltiga indatatyper. |
| **ServiceThrottled** | Det finns för många begär Anden just nu. Vänta en stund och försök igen. |

I följande exempel visas användningen av några av användar gränssnitts elementen på registrerings sidan:

![Registrerings sida med dess GRÄNSSNITTs element namn märkta](./media/localization-string-ids/localization-sign-up.png)

I följande exempel visas användningen av några av användar gränssnitts elementen på registrerings sidan när användaren klickar på knappen Skicka verifierings kod:

![Registrerings sidan för e-postverifiering av sidan UX-element](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Användar gränssnitts element för sidan med telefon faktorns autentisering

Följande är ID: n för en innehålls definition med ID för `api.phonefactor`.

| id | Standardvärde |
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
| **number_pattern** | ^\\+ (?: [0-9] [\\x20-]?) {6,14}[0-9] $ |
| **intro_sms_p** |Vi har följande nummer för att registrera dig. Välj ett nummer som vi kan skicka en kod via SMS för att autentisera dig. |
| **requiredField_countryCode** | Välj landskod |
| **requiredField_number** | Ange ditt telefonnummer |
| **country_code_input_placeholder_text** |Land eller region |
| **number_label** | Telefonnummer |
| **error_tryagain** | Det angivna telefonnumret är upptaget eller inte tillgängligt. Kontrol lera numret och försök igen. |
| **error_incorrect_code** | Den verifierings kod som du har angett matchar inte våra poster. Försök igen eller begär en ny kod. |
| **countryList** | {\"Standard\":land/\"region,AF\":\"Afghanistan,\"AX:Åland\"\"\"\"\" Öarna\",\"Al:\"Albanien\",DZ\":Algeriet,som\":\"\"\"\"\"\" Amerikanska Samoa\",\"AD\":Andorra\",\"Ao:\"Angola,AI\":\"\"\"\" \"Anguilla,\"AQ\":Antarktis\",AG: Antigua ochBarbuda\",\"\"\"\"\"\"Ar\":Argentina\",\"am:\"Armenien, AW:\"Aruba\"\"\"\"\" \",AU\":\"Australien,\"kl:Österrike,\"\"\"\"\"\" AZ\":\"Azerbajdzjan,\"BS\":Bahamas\",BH:\"\"\"\"\" Bahrain\",\"BD:\"Bangladesh\",BB\":Barbados,\"\"\"\"\" Av\":\"Vitryssland,\"är\":Belgien\",BZ:\"\"\"\"\" Belize\",\"BJ:\"Benin\",BM\":snygg,\"\"\"\"\"BT\":Bhutan\",\"bo:\"Bolivia,BQ:\"\"\"\"\"\" Bonaire\",\"BA:Bosnien\"ochHercegovina\",\"BW:Botswana\"\"\"<span class="notransla class=""></span class="notransla> Öar som ligger\"på\"öar\",\"vi: USA Jungfru öarna\",\"μg\":Uganda\",\"UA:\"Ukraina,AE:\"\"\"\"\"\" Förenade\"Arabemiraten,GB\": Storbritannien,USA\":USA,uy\"\"\"\"\"\"\" \":Uruguay\",\"uz:\"Uzbekistan, anteckning:Vanuatu\",\"\"\"\"\"\" \"Va:\"Vatikanstaten\",ve\":Venezuela,VN\": Vietnam\"\"\"\"\"\" \",WF\":Wallisoch\"Futuna,julen:\"Jemen,\"ZM\"\"\"\"\"\":Zambia,\"ZW:Zimbabwe\"}\"\"\"\" |
| **error_448** | Det angivna telefonnumret kan inte kontaktas. |
| **error_449** | Användaren har överskridit antalet nya försök. |
| **verification_code_input_placeholder_text** | Verifieringskod |

I följande exempel visas användningen av några av användar gränssnitts elementen på sidan MFA-registrering:

![Registrerings sidan för e-postverifiering av sidan UX-element](./media/localization-string-ids/localization-mfa1.png)

I följande exempel visas användningen av vissa användar gränssnitts element på sidan MFA-verifiering:

![Registrerings sidan för e-postverifiering av sidan UX-element](./media/localization-string-ids/localization-mfa2.png)







