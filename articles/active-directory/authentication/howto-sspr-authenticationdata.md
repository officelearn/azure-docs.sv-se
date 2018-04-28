---
title: Krav för Azure AD SSPR data | Microsoft Docs
description: Uppgifter som krävs för Azure AD-lösenordet för självbetjäning återställning och hur du uppfylla dem.
services: active-directory
keywords: ''
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 790ca2ccb2d365876e15ca57e1aa199ac519fd73
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Distribuera utan slutanvändarregistrering för återställning av lösenord

Autentiseringsdata måste finnas för att distribuera Azure Active Directory (AD Azure) lösenordsåterställning via självbetjäning (SSPR). Vissa organisationer har användarna ange sina autentiseringsdata sig själva. Men många organisationer föredrar att synkronisera med data som redan finns i Active Directory. Synkroniserade data görs tillgänglig för Azure AD och SSPR utan interaktion från användaren, om du:
   * Korrekt formatera data i din lokala katalog.
   * Konfigurera [Azure AD Connect med hjälp av inställningarna för snabb](./../connect/active-directory-aadconnect-get-started-express.md).

För att fungera korrekt telefonnummer måste vara i formatet *+ CountryCode PhoneNumber*, till exempel + 1 4255551234.

> [!NOTE]
> Det måste finnas blanksteg mellan landskoden och telefonnumret.
>
> Återställning av lösenord stöder inte telefonanknytningar. Även i formatet + 1 4255551234 X 12345 tas tillägg bort innan anropet görs.

## <a name="fields-populated"></a>Fält

Om du använder standardinställningarna i Azure AD Connect, görs följande avbildningar:

| Lokalt Active Directory | Azure AD | Azure AD authentication kontaktinformation |
| --- | --- | --- |
| telephoneNumber | Arbetstelefon | Alternativ telefon |
| mobila | Mobiltelefon | Telefon |

Fälten kan visas tom tills användaren bekräftar sitt autentiseringsdata.

En Global administratör kan manuellt ange kontaktinformation för autentisering för användaren som visas i följande skärmbild.

![Kontakta][Contact]

## <a name="security-questions-and-answers"></a>Säkerhetsfrågor och svar

Säkerhetsfrågor och svar lagras på ett säkert sätt i Azure AD-klienten och är bara tillgänglig för användare via den [SSPR-registreringsportalen](https://aka.ms/ssprsetup). Administratörer kan inte se eller ändra innehållet i en annan användare frågor och svar.

### <a name="what-happens-when-a-user-registers"></a>Vad händer när en användare som registrerar

När en användare som registrerar anger registreringssidan följande fält:

* **Telefon för autentisering**
* **E-post för autentisering**
* **Säkerhetsfrågor och svar**

Om du har angett ett värde för **mobiltelefon** eller **alternativ e-postadress**, omedelbart fjärranvändning av dessa värden kan återställa sina lösenord, även om de inte har registrerats för tjänsten. Dessutom kan se användare dessa värden när de registrerar för första gången, och de kan ändra dem om de vill. När de registrerar har dessa värden kommer att sparas i den **telefon för autentisering** och **autentisering e-post** respektive fält.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Ange och läsa autentiseringsdata via PowerShell

Följande fält kan ställas in via PowerShell:

* **Alternativ e-postadress**
* **Mobiltelefon**
* **Arbetstelefon**: kan bara anges om du inte synkroniserar med en lokal katalog

### <a name="use-powershell-version-1"></a>Använd PowerShell version 1

Om du vill komma igång behöver du [ladda ned och installera Azure AD PowerShell-modulen](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). När du har installerats kan använda du de steg som följer för att konfigurera varje fält.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Ange autentiseringsdata med PowerShell version 1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Läsa autentiseringsdata med PowerShell version 1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Läsa alternativen telefon för autentisering och autentisering e-post

Att läsa den **telefon för autentisering** och **autentisering e-post** när du använder PowerShell version 1, Använd följande kommandon:

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Använd PowerShell version 2

Om du vill komma igång behöver du [ladda ned och installera PowerShell-modulen för Azure AD version 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). När du har installerats kan använda du de steg som följer för att konfigurera varje fält.

Kör följande kommandon för att snabbt vill installera från senare versioner av PowerShell som stöder installera modulen. (Den första raden kontrollerar om modulen är installerad.)

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Ange autentiseringsdata med PowerShell version 2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Läsa autentiseringsdata med PowerShell version 2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](../active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Globala administratörer kan ändra en användares kontaktinformation för autentisering"
