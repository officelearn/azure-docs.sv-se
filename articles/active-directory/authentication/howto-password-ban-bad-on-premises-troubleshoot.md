---
title: Felsökning i Azure AD-lösenordsskydd – Azure Active Directory
description: Förstå Azure AD lösenord protection felsökning av vanliga problem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 108ead982529d2ac6549cceffd9d2177ab6456bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414774"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Felsökning av Azure AD-lösenordsskydd

Efter distributionen av Azure AD-lösenordsskydd, kan felsökning krävas. Den här artikeln innehåller information för att förstå vanliga felsökningssteg.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC-agenten inte kan hitta en proxy i katalogen

Den huvudsakliga symtom på det här problemet är 30017 händelser i händelseloggen för DC agent-administratör.

Vanliga orsaker till problemet är att en proxy inte har ännu har registrerats. Om en proxy har registrerats, kan det finnas en fördröjning på grund av AD-replikeringsfördröjning tills ett visst DC-agenten är kan du se att proxyinställningarna.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC-agenten kan inte kommunicera med en proxy

Den huvudsakliga symtom på det här problemet är 30018 händelser i händelseloggen för DC agent-administratör. Detta kan ha flera möjliga orsaker:

1. DC-agenten finns i en isolerad del av det nätverk som inte tillåter nätverksanslutning till den registrerade proxy(s). Det här problemet kan därför vara expected\benign så länge andra DC-agenter kan kommunicera med proxy(s) för att kunna hämta lösenordsprinciper från Azure, vilket sedan hämtas av isolerade domänkontrollanten via replikering av principfiler i sysvol-resursen.

1. Värddatorn proxy blockerar åtkomst till RPC-slutpunkt mapper slutpunkt (port 135)

   Azure AD-lösenord Protection Proxy installationsprogrammet skapar automatiskt en Windows-brandväggen inkommande regel som tillåter åtkomst till port 135. Om den här regeln senare har tagits bort eller inaktiverats går DC agenter inte att kommunicera med Proxy-tjänsten. Om inbyggt Windows-brandväggen har inaktiverats i stället för en annan brandvägg, måste du konfigurera brandväggen för att tillåta åtkomst till port 135.

1. Värddatorn proxy blockerar åtkomst till RPC-slutpunkt (dynamisk eller statisk) lyssnade på av Proxy-tjänsten

   Azure AD-lösenord Protection Proxy installationsprogrammet skapar automatiskt en Windows-brandväggen inkommande regel som tillåter åtkomst till alla inkommande portar lyssnat på av Azure AD-lösenord Protection Proxy-tjänsten. Om den här regeln senare har tagits bort eller inaktiverats går DC agenter inte att kommunicera med Proxy-tjänsten. Om inbyggt Windows-brandväggen har inaktiverats i stället för en annan brandvägg, måste du konfigurera som brandväggen att tillåta åtkomst till några ingående portar lyssnat på av Azure AD-lösenord Protection Proxy-tjänsten. Den här konfigurationen kan göras mer specifika om tjänsten Proxy har konfigurerats för att lyssna på en viss statiska RPC-port (med hjälp av den `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet).

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>Proxy-tjänsten kan ta emot samtal från DC-agenter i domänen men kan inte kommunicera med Azure

1. Kontrollera proxy-datorn är ansluten till de slutpunkter som anges i den [distributionskrav](howto-password-ban-bad-on-premises-deploy.md).

1. Se till att skogen och alla proxy-servrar är registrerade mot samma Azure-klient.

   Du kan kontrollera detta genom att köra den `Get-AzureADPasswordProtectionProxy` och `Get-AzureADPasswordProtectionDCAgent` PowerShell-cmdletar, jämför den `AzureTenant` egenskapen för varje objekt returneras. Dessa måste vara samma i en skog, på alla DC-agenter och proxyservrar för att fungera korrekt.

   Ett Azure-klient registrering matchningsfel villkor finns, kan du reparera det genom att köra den `Register-AzureADPasswordProtectionProxy` och/eller `Register-AzureADPasswordProtectionForest` PowerShell-cmdletar efter behov, se till att använda autentiseringsuppgifter från samma Azure-klient för alla registreringar.

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>DC-agenten kan inte att kryptera eller dekryptera lösenordet principfiler och andra tillstånd

Det här problemet kan visas med en rad olika symptom men har vanligtvis ett vanliga grundläggande orsaken.

Azure AD-lösenordsskydd har ett kritiskt beroende på kryptering och dekryptering funktionerna som tillhandahålls av Microsoft Key Distribution Service som är tillgänglig på domänkontrollanter som kör Windows Server 2012 och senare. Tjänsten KDS måste vara aktiverad och fungerar på alla Windows Server 2012 och senare domänkontrollanter i en domän.

Som standard KDS konfigureras tjänstens startläget för tjänsten som manuell (utlösare Start). Den här konfigurationen innebär att den första gången en klient försöker använda tjänsten, den startas på begäran. Den här tjänsten start som är standardläget är godkänd för Azure AD-lösenord skyddet ska fungera.

Om startläget för KDS-tjänsten har konfigurerats på inaktiverad, måste den här konfigurationen åtgärdas innan Azure AD-lösenordsskydd kommer att fungera korrekt.

Ett enkelt test för det här problemet är att starta tjänsten KDS via MMC tjänsthanteringskonsolen manuellt eller använda andra hanteringsverktyg för tjänsten (till exempel köra ”net start kdssvc” från en kommandotolk-konsol). Tjänsten KDS förväntas startas och körs.

De vanligaste grundorsaken till tjänsten KDS att det inte går att starta är där Active Directory-domänkontrollantobjekt befinner sig utanför standard Organisationsenheten domänkontrollanter. Den här konfigurationen stöds inte av tjänsten KDS och är inte en begränsning som införts av Azure AD-lösenordsskydd. Korrigering för det här villkoret är att flytta domänkontrollantobjekt till en plats under standard Organisationsenheten domänkontrollanter.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Svaga lösenord tas emot, men får inte vara

Det här problemet kan ha flera orsaker.

1. DC-agenter kan inte hämta en princip eller gick inte att dekryptera befintliga principer. Sök efter möjliga orsaker i senare avsnitt.

1. Lösenord för tvinga principläge är fortfarande inställd på granska. Om den här konfigurationen används kan du konfigurera om den att tvinga med hjälp av Azure AD-lösenordsskydd-portalen. Se [aktivera lösenordsskydd](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Lösenordsprincipen har inaktiverats. Om den här konfigurationen används kan du konfigurera om den aktiverat med hjälp av Azure AD-lösenordsskydd-portalen. Se [aktivera lösenordsskydd](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Du har inte installerat DC klientprogrammet på alla domänkontrollanter i domänen. I det här fallet är det svårt att se till att Windows-fjärrklienter rikta en viss domänkontrollant under en åtgärd för lösenordsbyte. Om du har tror att du har har mål en viss Domänkontrollant där DC klientprogrammet är installerat, kan du kontrollera av kollar DC agenten admin-händelseloggen: oavsett utfall kan, det ska finnas minst en händelse att dokumentera resultatet av lösenordet validering. Om det finns ingen händelse som är tillgänglig för användaren vars lösenord har ändrats, bearbetades sannolikt lösenordsändringen av en annan domänkontrollant.

   Som ett alternativt test försöka setting\changing lösenord när du är inloggad direkt till en Domänkontrollant där DC klientprogrammet är installerat. Den här metoden rekommenderas inte för produktion Active Directory-domäner.

   Stegvis distribution av DC klientprogrammet har stöd för omfattas av begränsningarna rekommenderar Microsoft att DC klientprogrammet har installerats på alla domänkontrollanter i en domän så snart som möjligt.

1. Verifieringsalgoritm lösenord kanske faktiskt fungerar som förväntat. Se [hur utvärderas lösenord](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Reparationsläge för katalogtjänster

Om domänkontrollanten startas i reparationsläge för katalogtjänster, DC-agenttjänsten identifierar det här tillståndet och leder till alla lösenordsverifieringen eller tvingande aktiviteter inaktiveras oavsett aktiva principkonfigurationen.

## <a name="emergency-remediation"></a>Vid akutfall reparation

Om en situation där DC-agenttjänsten orsakar problem visas kan DC agent-tjänsten vara stängs omedelbart. DLL-filen DC agenten lösenord filtret fortfarande försöker anropa tjänsten inte körs och loggar händelser (10012, 10013), men alla inkommande lösenord godkänns under den tiden. DC-Agenttjänsten kan sedan också konfigureras via Windows Service Control Manager med en starttyp ”inaktiverad” efter behov.

Ett annat mått för reparation är att ange aktivera läge på Nej i Azure AD-lösenordsskydd-portalen. När den uppdaterade policyn har hämtats, DC-agenttjänsten hamnar i ett overksamt läge där alla lösenord godkänns som – är. Mer information finns i [läget tvinga](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Degraderingen av domänkontrollanten

Det går för att degradera en domänkontrollant som fortfarande kör DC-agentprogramvaran. Administratörer bör vara medveten men att DC klientprogrammet fortsätter att genomdriva lösenordsprinciper för aktuella under degraderingen proceduren. Nya lokala administratörslösenordet (anges som en del av degraderingen) verifieras som andra lösenord. Microsoft rekommenderar att väljas säkra lösenord för lokala administratörskonton som en del av en DC degradering procedur; men verifiering av ny lokala administratörslösenordet av DC-agentprogramvaran kan vara söndrande för befintlig degradering operativa procedurer.

När degraderingen har slutförts och domänkontrollanten har startats och körs igen som en normal medlemsserver, återgår DC-agentprogramvaran till som körs i passivt läge. Det kan sedan att avinstallera när som helst.

## <a name="removal"></a>Borttagning

Om det är valt att avinstallera protection-programmet för Azure AD-lösenord och rensa alla relaterade tillstånd från de domäner och skog, kan den här uppgiften utföras med hjälp av följande steg:

> [!IMPORTANT]
> Det är viktigt att utföra dessa steg i ordning. Om valfri instans av tjänsten Proxy lämnas körs skapas med jämna mellanrum igen dess serviceConnectionPoint-objektet. Om valfri instans av DC-agenttjänsten lämnas körs skapas med jämna mellanrum igen dess serviceConnectionPoint-objektet och sysvol-status.

1. Avinstallera proxyprogrammet från alla datorer. Det här steget har **inte** kräver en omstart.
2. Avinstallera klientprogrammet DC från alla domänkontrollanter. Det här steget **kräver** en omstart.
3. Ta manuellt bort alla tjänstanslutningspunkter för Proxy i varje domännamngivningskontexten. Platsen för de här objekten kan identifieras med följande Active Directory PowerShell-kommando:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Utelämna inte asterisken (”*”) i slutet av $keywords variabelvärdet.

   De resulterande objekten som hittades den `Get-ADObject` kommando kan sedan skickas till `Remove-ADObject`, eller tagits bort manuellt.

4. Ta bort alla anslutningspunkter för DC-agenten manuellt i varje domännamngivningskontexten. Det kan finnas en dessa objekt per domänkontrollant i skogen, beroende på hur ofta programmet har distribuerats. Platsen för det objektet kan identifieras med följande Active Directory PowerShell-kommando:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   De resulterande objekten som hittades den `Get-ADObject` kommando kan sedan skickas till `Remove-ADObject`, eller tagits bort manuellt.

   Utelämna inte asterisken (”*”) i slutet av $keywords variabelvärdet.

5. Ta manuellt bort Konfigurationsstatus för skogsnivå. Konfigurationsstatus för skogen underhålls i en behållare i Active Directory konfigurationsnamngivningen. Den kan identifieras och tas bort enligt följande:

   ```powershell
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
