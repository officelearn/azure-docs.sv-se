---
title: Principer för återställning av lösenord med självbetjäning – Azure Active Directory
description: Lär dig mer om de olika policyalternativen för återställning av lösenordsjustering av Självbetjäning i Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f5987bee82dc22d3742cb5d87040930e5d2c52d
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393034"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>Principer och begränsningar för återställning av lösenord med självbetjäning i Azure Active Directory

I den här artikeln beskrivs lösenordsprinciper och komplexitetskrav som är associerade med användarkonton i din Azure Active Directory-klient (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Skillnader i återställningsprincip för administratörer

**Microsoft tillämpar en stark standardprincip för återställning av lösenord med två portar för alla *Azure-administratörsroller* **. Den här principen kan skilja sig från den du har definierat för användarna och den här principen kan inte ändras. Du bör alltid testa funktionen för återställning av lösenord som användare utan några Azure-administratörsroller som tilldelats.

Med en två-gate princip, **administratörer har inte möjlighet att använda säkerhetsfrågor**.

Principen med två portar kräver två autentiseringsdata, till exempel en *e-postadress,* *en autentiseringsapp*eller ett *telefonnummer*. En policy med två portar gäller under följande omständigheter:

* Alla följande Azure-administratörsroller påverkas:
  * Helpdesk-administratör
  * Tjänstsupportadministratör
  * Faktureringsadministratör
  * Support för partnernivå1
  * Support för partnernivå2
  * Exchange-administratör
  * Skype for Business-administratör
  * Användaradministratör
  * Katalogförfattare
  * Global administratör eller företagsadministratör
  * SharePoint-administratör
  * Administratör för efterlevnad
  * Programadministratör
  * Säkerhetsadministratör
  * Administratör för privilegierad roll
  * Intune-administratör
  * Administratör för programproxytjänsten
  * Dynamics 365-administratör
  * Power BI-tjänstadministratör
  * Administratör för autentisering
  * Administratör för privilegierad autentisering

* Om 30 dagar har förflutit i en provprenumeration; Eller
* En anpassad domän har konfigurerats för din Azure AD-klient, till exempel *contoso.com*; Eller
* Azure AD Connect synkroniserar identiteter från din lokala katalog

### <a name="exceptions"></a>Undantag

En en-gate-princip kräver en del autentiseringsdata, till exempel en e-postadress eller ett telefonnummer. En policy med en grind gäller under följande omständigheter:

* Det är inom de första 30 dagarna efter en provprenumeration; Eller
* En anpassad domän har inte konfigurerats för din Azure AD-klientorganisation, så använder standard **.onmicrosoft.com*. *Standarddomänen* * .onmicrosoft.com rekommenderas inte för produktionsanvändning. Och
* Azure AD Connect synkroniserar inte identiteter

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName-principer som gäller för alla användarkonton

Varje användarkonto som måste logga in på Azure AD måste ha ett unikt UPN-attributvärde (User Principal Name) som är associerat med deras konto. I följande tabell beskrivs de principer som gäller för både lokala Active Directory Domain Services-användarkonton som är synkroniserade med molnet och användarkonton som endast är molnet:

| Egenskap | UserPrincipalName-krav |
| --- | --- |
| Tillåtna tecken |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Tecken är inte tillåtna |<ul> <li>Alla\@ \" " tecken som inte skiljer användarnamnet från domänen.</li> <li>Det går inte att innehålla ett punkttecken\@ \" "." omedelbart före symbolen "</li></ul> |
| Längdbegränsningar |<ul> <li>Den totala längden får inte överstiga 113 tecken</li><li>Det kan finnas upp till 64 tecken före "\@ \" symbolen</li><li>Det kan finnas upp till 48 tecken efter "\@ \" symbolen</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Lösenordsprinciper som endast gäller för molnanvändarkonton

I följande tabell beskrivs de lösenordsprincipinställningar som tillämpas på användarkonton som skapas och hanteras i Azure AD:

| Egenskap | Krav |
| --- | --- |
| Tillåtna tecken |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \`~ " ( ) ;</li> <li>tomt utrymme</li></ul> |
| Tecken är inte tillåtna | Unicode-tecken. |
| Begränsningar av lösenord |<ul><li>Minst 8 tecken och högst 256 tecken.</li><li>Kräver tre av fyra av följande:<ul><li>Gemener.</li><li>Versaler.</li><li>Siffror (0-9).</li><li>Symboler (se tidigare lösenordsbegränsningar).</li></ul></li></ul> |
| Varaktighet för för att gälla för lösenord (högsta ålder för lösenord) |<ul><li>Standardvärde: **90** dagar.</li><li>Värdet kan konfigureras med `Set-MsolPasswordPolicy` hjälp av cmdleten från Azure Active Directory Module för Windows PowerShell.</li></ul> |
| Meddelande om att lösenordet upphör att gälla (När användare meddelas om att lösenordet upphör att gälla) |<ul><li>Standardvärde: **14** dagar (innan lösenordet upphör att gälla).</li><li>Värdet kan konfigureras med `Set-MsolPasswordPolicy` hjälp av cmdleten.</li></ul> |
| Lösenordsutgången (Låt lösenord aldrig upphöra) |<ul><li>Standardvärde: **falskt** (anger att lösenordet har ett utgångsdatum).</li><li>Värdet kan konfigureras för enskilda användarkonton `Set-MsolUser` med hjälp av cmdleten.</li></ul> |
| Historik för ändring av lösenord | Det sista lösenordet *kan inte* användas igen när användaren ändrar ett lösenord. |
| Historik för återställning av lösenord | Det sista lösenordet *kan* användas igen när användaren återställer ett glömt lösenord. |
| Kontoutelåsning | Efter 10 misslyckade inloggningsförsök med fel lösenord är användaren utelåst i en minut. Ytterligare felaktiga inloggningsförsök låser användaren för ökande tidsperioder. [Smart utelåsning](howto-password-smart-lockout.md) spårar de tre senaste felaktiga lösenordsharna för att undvika att låsa in spärrräknaren för samma lösenord. Om någon anger samma felaktiga lösenord flera gånger kommer det här beteendet inte att leda till att kontot stängs ute. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Ange principer för förfallodatum för lösenord i Azure AD

En *global administratör* eller *användaradministratör* för en Microsoft-molntjänst kan använda Microsoft Azure AD Module för Windows *PowerShell för* att ange att användarlösenord inte ska upphöra att gälla. Du kan också använda Windows PowerShell-cmdletar för att ta bort konfigurationen för aldrig upphör att gälla eller för att se vilka användarlösenord som aldrig upphör att gälla.

Den här vägledningen gäller för andra leverantörer, till exempel Intune och Office 365, som också är beroende av Azure AD för identitets- och katalogtjänster. Lösenordsförfallodatum är den enda delen av principen som kan ändras.

> [!NOTE]
> Endast lösenord för användarkonton som inte synkroniseras via katalogsynkronisering kan konfigureras så att de inte upphör att gälla. Mer information om katalogsynkronisering finns i [Ansluta AD med Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Ange eller kontrollera lösenordsprinciper med hjälp av PowerShell

Hämta och [installera Azure AD PowerShell-modulen](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)för att komma igång . När modulen har installerats använder du följande steg för att konfigurera varje fält.

### <a name="check-the-expiration-policy-for-a-password"></a>Kontrollera förfalloprincipen för ett lösenord

1. Anslut till Windows PowerShell med hjälp av användaradministratören eller företagets administratörsautentiseringsuppgifter.
1. Kör något av följande kommandon:

   * Om du vill se om en enskild användares lösenord aldrig upphör att gälla kör du följande cmdlet med hjälp av UPN (till exempel *aprilr\@contoso.onmicrosoft.com*) eller användar-ID för den användare som du vill kontrollera:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Om du vill se inställningen **Lösenord upphör aldrig att gälla** för alla användare kör du följande cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Ange att ett lösenord ska upphöra att gälla

1. Anslut till Windows PowerShell med hjälp av användaradministratören eller företagets administratörsautentiseringsuppgifter.
1. Kör något av följande kommandon:

   * Om du vill ange lösenordet för en användare så att lösenordet upphör att gälla kör du följande cmdlet med hjälp av UPN eller användarens användar-ID:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Om du vill ange lösenord för alla användare i organisationen så att de upphör att gälla använder du följande cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Ange att ett lösenord aldrig ska upphöra att gälla

1. Anslut till Windows PowerShell med hjälp av användaradministratören eller företagets administratörsautentiseringsuppgifter.
1. Kör något av följande kommandon:

   * Om du vill att lösenordet för en användare aldrig ska upphöra att gälla kör du följande cmdlet med hjälp av UPN eller användarens användar-ID:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Om du vill att lösenorden för alla användare i en organisation aldrig ska upphöra att gälla kör du följande cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Lösenord inställd `-PasswordPolicies DisablePasswordExpiration` på att fortfarande `pwdLastSet` ålder baserat på attributet. Baserat på `pwdLastSet` attributet, om du `-PasswordPolicies None`ändrar förfallodatum till , alla lösenord som har en `pwdLastSet` äldre än 90 dagar kräver användaren att ändra dem nästa gång de loggar in. Den här ändringen kan påverka ett stort antal användare.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med SSPR finns i [Självstudiekurs: Gör det möjligt för användare att låsa upp sitt konto eller återställa lösenord med hjälp av Azure Active Directory självbetjäningslösenordsåterställning](tutorial-enable-sspr.md).

Om du eller användarna har problem med SSPR läser [du Felsöka återställning av lösenord för självbetjäning](active-directory-passwords-troubleshoot.md)
