---
title: Självbetjäning för lösenordsåterställning principer - Azure Active Directory
description: Alternativ för återställning av lösenord för självbetjäning av Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 5952d0b1568e2554f53797515cde5cb23ad95a9b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33867450"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Lösenordsprinciper och begränsningar i Azure Active Directory

Den här artikeln beskriver lösenordsprinciper och krav på komplexitet som är associerade med användarkonton lagras i Azure Active Directory (Azure AD)-klient.

## <a name="administrator-password-policy-differences"></a>Administratören lösenord princip skillnader

Microsoft tillämpar en stark standard *två gate* principen för alla Azure administratörsroll för återställning av lösenord. 

Med en princip för två-gate inte administratörer möjligheten att använda säkerhetsfrågor.

 En princip för två gate kräver två typer av autentiseringsdata, till exempel en e-postadress *och* ett telefonnummer. En två-gate-princip som tillämpas under följande omständigheter:

* Alla följande Azure-administratörsroller påverkas:
  * Supportavdelningen administratör
  * Tjänstsupportadministratör
  * Faktureringsadministratör
  * Partnersupport, nivå 1
  * Partnersupport, nivå 2
  * Exchange-tjänstadministratör
  * Lync-tjänstadministratör
  * Kontoadministratör för användaren
  * Katalogskrivare
  * Global administratör eller företagsadministratör
  * Tjänstadministratör för SharePoint
  * Efterlevnadsadministratör
  * Programadministratör
  * Säkerhetsadministratör
  * Privilegierad rolladministratör
  * Microsoft Intune-tjänstadministratören
  * Tjänstadministratör för Application proxy
  * CRM-tjänstadministratör
  * Tjänstadministratör för Power BI
  
* Om 30 dagar har i en utvärderingsprenumeration

  eller

* En alternativa domän finns t.ex contoso.com

  eller

* Azure AD Connect synkroniserar identiteter från din lokala katalog

### <a name="exceptions"></a>Undantag
En princip för en gate kräver en dataenhet autentisering, till exempel en e-postadress *eller* telefonnummer. En princip för en gate gäller under följande omständigheter:

* Det är inom en utvärderingsprenumeration första 30 dagar

  eller

* En alternativa domän saknas (*. onmicrosoft.com) 

  och 

  Azure AD Connect synkroniserar inte identiteter


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName principer som gäller för alla användarkonton

Varje användarkonto som behöver logga in på Azure AD måste ha ett unikt huvudnamn (UPN)-attributvärde som associeras med deras konto. I följande tabell beskrivs de principer som gäller för både lokala Active Directory-användarkonton som synkroniseras till molnet och för att endast molnbaserad användarkonton:

| Egenskap | UserPrincipalName krav |
| --- | --- |
| Tecken som tillåts |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Tecken som tillåts inte |<ul> <li>Alla ”\@ \" tecken som inte avgränsa användarnamnet från domänen.</li> <li>Får inte innehålla en punkt ””. omedelbart före den ”\@ \" symbol</li></ul> |
| Längden begränsningar |<ul> <li>Den totala längden får inte överskrida 113 tecken</li><li>Det kan vara upp till 64 tecken innan den ”\@ \" symbol</li><li>Det kan vara upp till 48 tecken efter den ”\@ \" symbol</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>De principer som gäller endast för användarkonton i molnet

I följande tabell beskrivs de tillgängliga lösenordsprincip som kan tillämpas på konton som skapas och hanteras i Azure AD:

| Egenskap | Krav |
| --- | --- |
| Tecken som tillåts |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Tecken som tillåts inte |<ul><li>Unicode-tecken.</li><li>Blanksteg.</li><li> Starka lösenord</li></ul> |
| Begränsningar för lösenord |<ul><li>Minst 8 tecken och högst 16 tecken.</li><li>Starka lösenord: kräver tre av de fyra av följande:<ul><li>Gemener.</li><li>Versaler.</li><li>Siffror (0-9).</li><li>Symboler (se föregående begränsningar för lösenord).</li></ul></li></ul> |
| Giltighetstiden för lösenord |<ul><li>Standardvärde: **90** dagar.</li><li>Värdet kan konfigureras med hjälp av den `Set-MsolPasswordPolicy` cmdlet från Azure Active Directory-modulen för Windows PowerShell.</li></ul> |
| Meddelande om lösenords upphör att gälla |<ul><li>Standardvärde: **14** dagar (tills lösenordet upphör att gälla).</li><li>Värdet kan konfigureras med hjälp av den `Set-MsolPasswordPolicy` cmdlet.</li></ul> |
| Lösenordet upphör att gälla |<ul><li>Standardvärde: **FALSKT** dagar (anger att lösenordet upphör att gälla är aktiverad).</li><li>Värdet kan konfigureras för enskilda användarkonton med hjälp av den `Set-MsolUser` cmdlet.</li></ul> |
| Ändra lösenord |Det sista lösenordet *kan* användas igen när användaren ändrar ett lösenord. |
| Historik för återställning av lösenord | Det sista lösenordet *kan* användas igen när användaren återställer ett nytt lösenord. |
| Kontoutelåsning |Efter 10 misslyckade inloggningsförsök med fel lösenord låses användaren i en minut. Ytterligare inloggningsförsök felaktiga Lås ut användaren för att öka varaktigheten för tid. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Ange lösenordet upphör att gälla principer i Azure AD

En global administratör för en Microsoft-molntjänst kan använda Microsoft Azure AD-modulen för Windows PowerShell för att ange lösenord inte upphör att gälla. Du kan också använda Windows PowerShell-cmdlets för att ta bort den-upphör aldrig configuration eller för att se vilka användare ställs lösenord aldrig upphör att gälla. 

Den här vägledningen gäller andra leverantörer, till exempel Intune och Office 365 som också är beroende av Azure AD för identitets- och directory services. Lösenordets giltighetstid är endast en del av den princip som kan ändras.

> [!NOTE]
> Endast lösenord för användarkonton som inte är synkroniserade med katalogsynkronisering kan konfigureras för att inte upphör för att gälla. Mer information om katalogsynkronisering finns [Anslut AD med Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Ange eller kontrollera principer för lösenord med hjälp av PowerShell

Om du vill komma igång behöver du [ladda ned och installera Azure AD PowerShell-modulen](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). När du har installerat kan du använda följande steg för att konfigurera varje fält.

### <a name="how-to-check-the-expiration-policy-for-a-password"></a>Hur du kontrollerar förfalloprincipen för lösenord
1. Ansluta till Windows PowerShell med administratörsbehörighet för ditt företag.
2. Kör något av följande kommandon:

   * Kör följande cmdlet för att se om en enskild användares lösenord upphör att gälla aldrig, med hjälp av UPN-namnet (till exempel *aprilr@contoso.onmicrosoft.com*) eller användar-ID för den användare som du vill kontrollera: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Se den **lösenordet upphör aldrig att gälla** inställningen för alla användare kör du följande cmdlet: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Ange ett lösenord ska upphöra att gälla

1. Ansluta till Windows PowerShell med administratörsbehörighet för ditt företag.
2. Kör något av följande kommandon:

   * Kör följande cmdlet för att ange lösenordet för en användare så att lösenordet upphör att gälla, med hjälp av UPN-namnet eller ID för användaren: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * För att ange lösenord för alla användare i organisationen så att de upphör att gälla, använder du följande cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Ange ett lösenord aldrig upphör att gälla

1. Ansluta till Windows PowerShell med administratörsbehörighet för ditt företag.
2. Kör något av följande kommandon:

   * Kör följande cmdlet för att ange lösenordet för en användare att aldrig upphöra, med hjälp av UPN-namnet eller ID för användaren: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * För att ange lösenord för alla användare i en organisation aldrig upphör att gälla, kör du följande cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > Lösenord har angetts till `-PasswordNeverExpires $true` fortfarande ålder baserat på den `pwdLastSet` attribut. Om du ställer in användarlösenord aldrig går ut och sedan 90 dagar gå genom lösenord upphör att gälla. Baserat på de `pwdLastSet` attributet, om du ändrar upphör att gälla till `-PasswordNeverExpires $false`, alla lösenord som har en `pwdLastSet` äldre än 90 dagar användaren måste ändra dem nästa gång de loggar in. Den här ändringen kan påverka ett stort antal användare. 

## <a name="next-steps"></a>Nästa steg

I följande artiklar innehåller ytterligare information om lösenordsåterställning via Azure AD:

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](../active-directory-passwords-reset-register.md).
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)
