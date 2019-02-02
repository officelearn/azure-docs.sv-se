---
title: Felsökning i förhandsversionen av Azure AD lösenord protection
description: Förstå Azure AD lösenord protection preview felsökning av vanliga problem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: a1d06919ae0a76647fafeb9c8499476e533bfebf
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656404"
---
# <a name="preview-azure-ad-password-protection-troubleshooting"></a>Förhandsversion: Felsökning av Azure AD-lösenordsskydd

|     |
| --- |
| Azure AD-lösenordsskydd är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Efter distributionen av Azure AD-lösenordsskydd, kan felsökning krävas. Den här artikeln innehåller information för att förstå vanliga felsökningssteg.

## 

## <a name="weak-passwords-are-not-getting-rejected-as-expected"></a>Svaga lösenord komma inte avvisas som förväntat

Detta kan ha flera möjliga orsaker:

1. DC-agenter har ännu inte hämta en princip. Symtom på detta är 30001 händelser i händelseloggen för DC agent-administratör.

    Möjliga orsaker till det här problemet är:
    1. Skog har ännu inte registrerats
    2. Proxy har ännu inte registrerats
    3. Problem med nätverksanslutningen förhindrar Proxy-tjänsten från att kommunicera med Azure (kontrollera HTTP-Proxy krav)

2. Lösenord för tvinga principläge är fortfarande inställd på granska. Om så är fallet bara konfigurera om den att tvinga med hjälp av Azure AD-lösenordsskydd-portalen.
3. Verifieringsalgoritm lösenord kanske fungerar som förväntat.  Se [hur utvärderas lösenord](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Reparationsläge för katalogtjänster

Om domänkontrollanten startas i reparationsläge för katalogtjänster, DC-agenttjänsten identifierar detta och leder till alla lösenordsverifieringen eller tvingande aktiviteter inaktiveras oavsett aktiva principkonfigurationen.

## <a name="emergency-remediation"></a>Vid akutfall reparation

Om en situation där DC-agenttjänsten orsakar problem visas kan DC agent-tjänsten vara stängs omedelbart. DLL-filen DC agenten lösenord filtret fortfarande försöker anropa tjänsten inte körs och loggar händelser (10012, 10013), men alla inkommande lösenord godkänns under den tiden. DC-Agenttjänsten kan sedan också konfigureras via Windows Service Control Manager med en starttyp ”inaktiverad” efter behov.

Ett annat mått för reparation är att ange aktivera läge på Nej i Azure AD-lösenordsskydd-portalen. När den uppdaterade policyn har hämtats, varje DC agenter tjänst hamnar i ett overksamt läge där alla lösenord godkänns som – är. Mer information finns i [läget tvinga](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Degraderingen av domänkontrollanten

Det går för att degradera en domänkontrollant som fortfarande kör DC-agentprogramvaran. Administratörer bör vara medveten men att DC klientprogrammet fortsätter att genomdriva lösenordsprinciper för aktuella under degraderingen proceduren. Nya lokala administratörslösenordet (anges som en del av degraderingen) verifieras som andra lösenord. Microsoft rekommenderar att väljas säkra lösenord för lokala administratörskonton som en del av en DC degradering procedur; men verifiering av ny lokala administratörslösenordet av DC-agentprogramvaran kan vara söndrande för befintlig degradering operativa procedurer.

När degraderingen har slutförts och domänkontrollanten har startats och körs igen som en normal medlemsserver, återgår DC-agentprogramvaran till som körs i passivt läge. Det kan sedan att avinstallera när som helst.

## <a name="removal"></a>Borttagning

Om det är valt att avinstallera den allmänna förhandsversionen av programvaran och rensa alla relaterade tillstånd från de domäner och skog, kan den här uppgiften utföras med hjälp av följande steg:

> [!IMPORTANT]
> Det är viktigt att utföra dessa steg i ordning. Om valfri instans av tjänsten Proxy lämnas körs skapas med jämna mellanrum igen dess serviceConnectionPoint-objektet. Om valfri instans av DC-agenttjänsten lämnas körs skapas med jämna mellanrum igen dess serviceConnectionPoint-objektet och sysvol-status.

1. Avinstallera proxyprogrammet från alla datorer. Det här steget har **inte** kräver en omstart.
2. Avinstallera klientprogrammet DC från alla domänkontrollanter. Det här steget **kräver** en omstart.
3. Ta manuellt bort alla tjänstanslutningspunkter för Proxy i varje domännamngivningskontexten. Platsen för de här objekten kan identifieras med följande Active Directory PowerShell-kommando:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Utelämna inte asterisken (”*”) i slutet av $keywords variabelvärdet.

   De resulterande objekten som hittades den `Get-ADObject` kommando kan sedan skickas till `Remove-ADObject`, eller tagits bort manuellt. 

4. Ta bort alla anslutningspunkter för DC-agenten manuellt i varje domännamngivningskontexten. Det kan finnas en dessa objekt per domänkontrollant i skogen, beroende på hur mycket den allmänna förhandsversionen av programvaran har distribuerats. Platsen för det objektet kan identifieras med följande Active Directory PowerShell-kommando:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   De resulterande objekten som hittades den `Get-ADObject` kommando kan sedan skickas till `Remove-ADObject`, eller tagits bort manuellt.

   Utelämna inte asterisken (”*”) i slutet av $keywords variabelvärdet.

5. Ta manuellt bort Konfigurationsstatus för skogsnivå. Konfigurationsstatus för skogen underhålls i en behållare i Active Directory konfigurationsnamngivningen. Den kan identifieras och tas bort enligt följande:

   ```PowerShell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Manuellt ta bort alla sysvol relaterade tillstånd genom att manuellt ta bort följande mapp och dess innehåll:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Om det behövs kan den här sökvägen även komma åt lokalt på en viss domänkontrollant; Standardplatsen är något som liknar följande sökväg:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Den här sökvägen skiljer sig om sysvol-resursen har konfigurerats i en icke-standardplats.

## <a name="next-steps"></a>Nästa steg

[Vanliga frågor om Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-faq.md)

Mer information om listor med globala och anpassade förbjudna lösenord, finns i artikeln [förbjuda felaktiga lösenord](concept-password-ban-bad.md)
