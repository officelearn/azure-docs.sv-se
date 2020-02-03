---
title: Principer för lösen ords återställning via självbetjäning – Azure Active Directory
description: Konfigurera princip alternativ för återställning av lösen ord för Azure AD-självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd6cacae9c7af705b0de7b59e0f25f25637a5a89
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962500"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Lösen ords principer och begränsningar i Azure Active Directory

Den här artikeln beskriver lösen ords principer och komplexitets krav som är kopplade till användar konton i din Azure Active Directory (Azure AD)-klient.

## <a name="administrator-reset-policy-differences"></a>Återställa princip skillnader i administratörer

**Microsoft tillämpar en stark standard princip för lösen ords återställning med *två grindar* för valfri Azure-administratörs roll** den här principen kan skilja sig från den som du har definierat för dina användare och inte kan ändras. Du bör alltid testa funktionen för återställning av lösen ord som en användare utan tilldelade Azure Administrator-roller.

Med en princip med två-grind funktioner kan **administratörer inte använda säkerhets frågor**.

Principen för två-grind kräver två delar av autentiseringsdata, till exempel en **e-postadress**, en **Authenticator-app**eller ett **telefonnummer**. En princip för två grindar gäller i följande fall:

* Alla följande roller för Azure-administratörer påverkas:
  * Support administratör
  * Tjänst support administratör
  * Faktureringsadministratör
  * Partnersupport, nivå 1
  * Partnersupport, nivå 2
  * Exchange-administratör
  * Skype för företag-administratör
  * Användar administratör
  * Katalog skrivare
  * Global administratör eller företags administratör
  * SharePoint-administratör
  * Administratör för efterlevnad
  * Program administratör
  * Säkerhetsadministratör
  * Privilegie rad roll administratör
  * Intune-administratör
  * Administratör för tjänsten Application Proxy
  * Dynamics 365-administratör
  * Power BI-tjänst administratör
  * Administratör för autentisering
  * Administratör för privilegie rad autentisering

* Om 30 dagar har förflutit i en utvärderings prenumeration; eller
* En anpassad domän har kon figurer ATS för din Azure AD-klient, till exempel *contoso.com*; eller
* Azure AD Connect synkroniserar identiteter från din lokala katalog

### <a name="exceptions"></a>Undantag

En princip för en enskild grind kräver en typ av autentiseringsinformation, till exempel en e-postadress *eller* ett telefonnummer. En princip för en-grind gäller i följande fall:

* Den ligger inom de första 30 dagarna i en utvärderings prenumeration. eller
* En anpassad domän har inte kon figurer ATS för din Azure AD-klient så använder standard * *. onmicrosoft.com*. Observera att standard domänen * *. onmicrosoft.com* rekommenderas inte för produktions användning. särskilt
* Azure AD Connect synkroniserar inte identiteter

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName-principer som gäller för alla användar konton

Varje användar konto som behöver logga in på Azure AD måste ha ett unikt värde för User Principal Name (UPN) som är kopplat till sitt konto. I följande tabell beskrivs de principer som gäller för både lokala Active Directory-användarkonton som är synkroniserade med molnet och endast molnbaserade användar konton:

| Egenskap | UserPrincipalName-krav |
| --- | --- |
| Tillåtna tecken |<ul> <li>A – Ö</li> <li>a-ö</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Tecken tillåts inte |<ul> <li>Valfritt "\@\"-tecknen som inte avgränsar användar namnet från domänen.</li> <li>Får inte innehålla ett punkt tecken "." omedelbart före "\@\"-symbolen</li></ul> |
| Längd begränsningar |<ul> <li>Den totala längden får inte överskrida 113 tecken</li><li>Det kan finnas upp till 64 tecken innan symbolen för\@\"</li><li>Det kan innehålla upp till 48 tecken efter symbolen "\@\"</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Lösen ords principer som endast gäller för moln användar konton

I följande tabell beskrivs de princip inställningar för lösen ord som tillämpas på användar konton som skapas och hanteras i Azure AD:

| Egenskap | Krav |
| --- | --- |
| Tillåtna tecken |<ul><li>A – Ö</li><li>a-ö</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ "();</li> <li>tomt utrymme</li></ul> |
| Tecken tillåts inte | Unicode-tecken. |
| Lösen ords begränsningar |<ul><li>Minst 8 tecken och högst 256 tecken.</li><li>Kräver tre av fyra av följande:<ul><li>Gemener.</li><li>Versaler.</li><li>Tal (0-9).</li><li>Symboler (se tidigare lösen ords begränsningar).</li></ul></li></ul> |
| Giltighets tid för lösen ord (högsta ålder för lösen ord) |<ul><li>Standardvärde: **90** dagar.</li><li>Värdet kan konfigureras med hjälp av cmdleten `Set-MsolPasswordPolicy` från modulen Azure Active Directory för Windows PowerShell.</li></ul> |
| Meddelande om förfallo datum för lösen ord (när användare får ett meddelande om förfallo datum för lösen ord) |<ul><li>Standardvärde: **14** dagar (innan lösen ordet upphör att gälla).</li><li>Värdet kan konfigureras med hjälp av `Set-MsolPasswordPolicy`-cmdleten.</li></ul> |
| Lösen ordets giltighets tid (Tillåt att lösen ordet aldrig upphör att gälla) |<ul><li>Standardvärde: **falskt** (indikerar att lösen ordet har ett förfallo datum).</li><li>Värdet kan konfigureras för enskilda användar konton med hjälp av `Set-MsolUser`-cmdleten.</li></ul> |
| Ändrings historik för lösen ord | Det sista lösen ordet *kan inte* användas igen när användaren ändrar ett lösen ord. |
| Historik för lösen ords återställning | Det senaste lösen ordet *kan* användas igen när användaren återställer ett bortglömt lösen ord. |
| Konto utelåsning | Efter 10 misslyckade inloggnings försök med fel lösen ord är användaren utelåst i en minut. Ytterligare felaktiga inloggnings försök låser användaren för att öka tiden. [Smart utelåsning](howto-password-smart-lockout.md) spårar de tre senaste Felaktiga hasharna för lösen ord för att undvika ökning av utelåsnings räknaren för samma lösen ord. Om någon anger samma Felaktiga lösen ord flera gånger, kommer det här beteendet inte att orsaka att kontot kan låsas. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Ange principer för förfallo datum för lösen ord i Azure AD

En global administratör eller användar administratör för en Microsoft-moln tjänst kan använda Microsoft Azure AD-modul för Windows PowerShell för att ange att användar lösen ord inte upphör att gälla. Du kan också använda Windows PowerShell-cmdlets för att ta bort konfigurationen för aldrig upphör att gälla eller se vilka användar lösen ord som aldrig upphör att gälla. 

Den här vägledningen gäller för andra leverantörer, till exempel Intune och Office 365, som också förlitar sig på Azure AD för identitets-och katalog tjänster. Lösen ordets giltighets tid är den enda delen av principen som kan ändras.

> [!NOTE]
> Endast lösen ord för användar konton som inte har synkroniserats med en katalog synkronisering kan konfigureras så att de inte upphör att gälla. Mer information om Active Directory-synkronisering finns i [ansluta AD med Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Ange eller kontrol lera lösen ords principerna med hjälp av PowerShell

För att komma igång måste du [Ladda ned och installera Azure AD PowerShell-modulen](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). När du har installerat det kan du använda följande steg för att konfigurera varje fält.

### <a name="check-the-expiration-policy-for-a-password"></a>Kontrol lera förfallo principen för ett lösen ord

1. Anslut till Windows PowerShell genom att använda användar administratören eller företagets administratörs behörighet.
1. Kör något av följande kommandon:

   * Om du vill se om en enskild användares lösen ord är inställda på att aldrig upphöra att gälla kör du följande cmdlet med hjälp av UPN (till exempel *april\@contoso.onmicrosoft.com*) eller användar-ID för den användare som du vill kontrol lera:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Om du vill se att **lösen ordet aldrig upphör att gälla** för alla användare kör du följande cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Ange ett lösen ord som upphör att gälla

1. Anslut till Windows PowerShell genom att använda användar administratören eller företagets administratörs behörighet.
1. Kör något av följande kommandon:

   * Om du vill ange lösen ordet för en användare så att lösen ordet upphör att gälla kör du följande cmdlet med hjälp av UPN eller användar-ID för användaren:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Använd följande cmdlet för att ange lösen orden för alla användare i organisationen så att de upphör att gälla:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Ange att ett lösen ord aldrig upphör att gälla

1. Anslut till Windows PowerShell genom att använda användar administratören eller företagets administratörs behörighet.
1. Kör något av följande kommandon:

   * Om du vill ange ett lösen ord för en användare som aldrig upphör att gälla kör du följande cmdlet med hjälp av UPN eller användar-ID för användaren:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Om du vill ange lösen orden för alla användare i en organisation som aldrig upphör att gälla kör du följande cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Lösen orden har angetts till `-PasswordPolicies DisablePasswordExpiration` fortfarande ålder baserat på attributet `pwdLastSet`. Om du anger att användar lösen ord aldrig upphör att gälla och sedan 90 + dagar går det att ange lösen orden. Om du ändrar förfallo datum till `-PasswordPolicies None`, baserat på `pwdLastSet`-attributet, kräver alla lösen ord som har en `pwdLastSet` som är äldre än 90 dagar användaren att ändra dem nästa gång de loggar in. Den här ändringen kan påverka ett stort antal användare.

## <a name="next-steps"></a>Nästa steg

Följande artiklar innehåller ytterligare information om lösen ords återställning via Azure AD:

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md).
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något är brutet. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)
