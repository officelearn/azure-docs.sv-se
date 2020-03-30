---
title: Vanliga frågor och svar om Azure Active Directory Connect-hälsotillstånd – Azure | Microsoft-dokument
description: Den här vanliga frågor och svar på frågor om Azure AD Connect Health. Avsnittet ger svar på frågor om hur du använder tjänsten, inklusive faktureringsmodellen, funktioner, begränsningar och support.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0c6484f46731e0ff2d16d00cb0038202511d193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331081"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Vanliga frågor och svar på Azure AD Connect-hälsa
Den här artikeln innehåller svar på vanliga frågor och svar (Vanliga frågor) om Azure Active Directory (Azure AD) Connect Health. Dessa vanliga frågor täcker frågor om hur du använder tjänsten, som inkluderar faktureringsmodellen, funktioner, begränsningar och support.

## <a name="general-questions"></a>Allmänna frågor
**F: Jag hanterar flera Azure AD-kataloger. Hur växlar jag till den som har Azure Active Directory Premium?**

Om du vill växla mellan olika Azure AD-klienter väljer du det inloggade **användarnamnet** i det övre högra hörnet och väljer sedan lämpligt konto. Om kontot inte visas här väljer du **Logga ut**och använder sedan de globala administratörsautentiseringsuppgifterna för katalogen som har Azure Active Directory Premium aktiverat för att logga in.

**F: Vilken version av identitetsroller stöds av Azure AD Connect Health?**

I följande tabell visas roller och operativsystemversioner som stöds.

|Roll| Operativsystem / Version|
|--|--|
|Active Directory Federation Services (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Version 1.0.9125 eller senare|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Observera att de funktioner som tillhandahålls av tjänsten kan skilja sig beroende på rollen och operativsystemet. Med andra ord kanske alla funktioner inte är tillgängliga för alla operativsystemversioner. Mer information finns i funktionsbeskrivningarna.

**F: Hur många licenser behöver jag för att övervaka min infrastruktur?**

* Den första Connect Health Agent kräver minst en Azure AD Premium-licens.
* Varje ytterligare registrerad agent kräver ytterligare 25 Azure AD Premium-licenser.
* Agentantal motsvarar det totala antalet agenter som är registrerade i alla övervakade roller (AD FS, Azure AD Connect och/eller AD DS).
* AAD Connect Health-licensiering kräver inte att du tilldelar licensen till specifika användare. Du behöver bara ha det antal giltiga licenser som krävs.

Licensieringsinformation finns också på [sidan Azure AD-prissättning](https://aka.ms/aadpricing).

Exempel:

| Registrerade ombud | Licenser som behövs | Exempel på övervakningskonfiguration |
| ------ | --------------- | --- |
| 1 | 1 | 1 Azure AD Connect-server |
| 2 | 26| 1 Azure AD Connect-server och 1 domänkontrollant |
| 3 | 51 | 1 AD FS-server (Active Directory Federation Services), 1 AD FS-proxy och 1 domänkontrollant |
| 4 | 76 | 1 AD FS-server, 1 AD FS-proxy och 2 domänkontrollanter |
| 5 | 101 | 1 Azure AD Connect-server, 1 AD FS-server, 1 AD FS-proxy och 2 domänkontrollanter |

**F: Stöder Azure AD Connect Health Azure Germany Cloud?**

Azure AD Connect Health stöds inte i Germany Cloud förutom [rapportfunktionen synkroniseringsfel](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Roller | Funktioner | Stöds i tyska molnet |
| ------ | --------------- | --- |
| Anslut hälsohälsa för synkronisering | Övervakning / Insikt / Varningar / Analys | Inga |
|  | Felrapport för synkronisering | Ja |
| Anslut hälsotillstånd för ADFS | Övervakning / Insikt / Varningar / Analys | Inga |
| Anslut hälsa för TILLÄGG | Övervakning / Insikt / Varningar / Analys | Inga |

Konfigurera [installationskravet](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) i enlighet med detta för att säkerställa agentanslutningen för Connect Health för synkronisering.

## <a name="installation-questions"></a>Frågor om installation

**F: Vad påverkar du installationen av Azure AD Connect Health Agent på enskilda servrar?**

Effekten av installation av Microsoft Azure AD Connect Health Agent, AD FS, proxyservrar för webbprogram, Azure AD Connect-servrar (sync), domänkontrollanter är minimal med avseende på processor, minnesförbrukning, nätverksbandbredd och lagring.

Följande siffror är en approximation:

* CPU-förbrukning: ~1-5% ökning.
* Minnesförbrukning: Upp till 10 % av det totala systemminnet.

> [!NOTE]
> Om agenten inte kan kommunicera med Azure lagrar agenten data lokalt för en definierad maxgräns. Agenten skriver över "cachelagrade" data på en "minst nyligen servad" basis.
>
>

* Lokal buffertlagring för Azure AD Connect-hälsoagenter: ~20 MB.
* För AD FS-servrar rekommenderar vi att du etablerar ett diskutrymme på 1 024 MB (1 GB) för AD FS-granskningskanalen för Azure AD Connect Health Agents för att bearbeta alla granskningsdata innan de skrivs över.

**F: Måste jag starta om mina servrar under installationen av Azure AD Connect Health Agents?**

Nej. Installationen av agenterna kräver inte att du startar om servern. Installation av vissa nödvändiga steg kan dock kräva en omstart av servern.

På Windows Server 2008 R2 kräver installation av .NET 4.5 Framework till exempel en omstart av servern.

**F: Fungerar Azure AD Connect Health via en viaströms HTTP-proxy?**

Ja. För pågående åtgärder kan du konfigurera hälsoagenten så att en HTTP-proxy används för att vidarebefordra utgående HTTP-begäranden.
Läs mer om [hur du konfigurerar HTTP-proxy för hälsoagenter](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Om du behöver konfigurera en proxy under agentregistreringen kan du behöva ändra proxyinställningarna för Internet Explorer i förväg.

1. Öppna Internet Explorer > **Inställningar** > **Internet Alternativ** > **Anslutningar** > **LAN-inställningar**.
2. Välj **Använd en proxyserver för ditt LAN**.
3. Välj **Avancerat** om du har olika proxyportar för HTTP och HTTPS/Secure.

**F: Stöder Azure AD Connect Health grundläggande autentisering vid anslutning till HTTP-proxyservrar?**

Nej. En mekanism för att ange ett godtyckligt användarnamn och lösenord för grundläggande autentisering stöds för närvarande inte.

**F: Vilka brandväggsportar måste jag öppna för att Azure AD Connect Health Agent ska fungera?**

Se [avsnittet krav](how-to-connect-health-agent-install.md#requirements) för listan över brandväggsportar och andra anslutningskrav.

**F: Varför visas två servrar med samma namn i Azure AD Connect Health-portalen?**

När du tar bort en agent från en server tas servern inte automatiskt bort från Azure AD Connect Health-portalen. Om du manuellt tar bort en agent från en server eller tar bort själva servern måste du manuellt ta bort serverposten från Azure AD Connect Health-portalen.

Du kan använda en server på nytt eller skapa en ny server med samma information (till exempel datornamn). Om du inte tog bort den redan registrerade servern från Azure AD Connect Health-portalen och du har installerat agenten på den nya servern kan två poster med samma namn visas.

I det här fallet tar du bort posten som tillhör den äldre servern manuellt. Data för den här servern bör vara inaktuella.

## <a name="health-agent-registration-and-data-freshness"></a>Health Agent registrering och data färskhet

**F: Vilka är vanliga orsaker till fel i hälsoagentregistreringen och hur felsöker jag problem?**

Hälsoagenten kan inte registrera sig på grund av följande möjliga orsaker:

* Agenten kan inte kommunicera med de slutpunkter som krävs eftersom en brandvägg blockerar trafik. Detta är särskilt vanligt på proxyservrar för webbprogram. Kontrollera att du har tillåtit utgående kommunikation till de slutpunkter och portar som krävs. Mer information finns i [avsnittet krav.](how-to-connect-health-agent-install.md#requirements)
* Utgående kommunikation är föremål för en TLS-inspektion av nätverksskiktet. Detta medför att certifikatet som agenten använder ersätts av inspektionsservern/entiteten och stegen för att slutföra agentregistreringen misslyckas.
* Användaren har inte åtkomst till registreringen av agenten. Globala administratörer har åtkomst som standard. Du kan använda [rollbaserad åtkomstkontroll](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) för att delegera åtkomst till andra användare.

**F: Jag får en avisering om att "Hälsotjänstdata är inte uppdaterade". Hur felsöker jag problemet?**

Azure AD Connect Health genererar aviseringen när den inte tar emot alla datapunkter från servern under de senaste två timmarna. [Läs mer](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Verksamhetsfrågor
**F: Måste jag aktivera granskning på proxyservrar för webbprogrammet?**

Nej, granskning behöver inte aktiveras på proxyservrar för webbprogrammet.

**F: Hur löses hälsoaviseringar för Azure AD Connect?**

Azure AD Connect Health-aviseringar löses på ett lyckat villkor. Azure AD Connect Health Agents identifiera och rapportera framgångsvillkoren till tjänsten med jämna mellanrum. För några få aviseringar är dämpningen tidsbaserad. Med andra ord, om samma feltillstånd inte observeras inom 72 timmar från aviseringsgenereringen, löses aviseringen automatiskt.

**F: Jag får en avisering om att "Test Authentication Request (Synthetic Transaction) failed to obtain a token." Hur felsöker jag problemet?**

Azure AD Connect Health for AD FS genererar den här aviseringen när hälsoagenten som är installerad på en AD FS-server inte kan hämta en token som en del av en syntetisk transaktion som initierats av hälsoagenten. Hälsoagenten använder den lokala systemkontexten och försöker hämta en token för en självförlitar part. Detta är ett catch-all-test för att säkerställa att AD FS är i ett tillstånd av att utfärda token.

Oftast misslyckas det här testet eftersom hälsoagenten inte kan matcha AD FS-servergruppens namn. Detta kan inträffa om AD FS-servrarna ligger bakom en nätverksbelastningsutjämningshanterare och begäran initieras från en nod som ligger bakom belastningsutjämnaren (i motsats till en vanlig klient som ligger framför belastningsutjämnaren). Detta kan åtgärdas genom att uppdatera filen "hosts" som finns under "C:\Windows\System32\drivers\etc" för att inkludera IP-adressen för AD FS-servern eller en loopback IP-adress (127.0.0.1) för AD FS-servergruppens namn (t.ex. sts.contoso.com). Om du lägger till värdfilen kortsluts nätverksanropet, vilket gör att hälsoagenten kan hämta token.

**F: Jag fick ett e-postmeddelande som anger mina maskiner är inte lappat för den senaste ransomware attacker. Varför fick jag det här e-postmeddelandet?**

Azure AD Connect Health-tjänsten genomsökte alla datorer som övervakades för att säkerställa att de nödvändiga korrigeringarna installerades. E-postmeddelandet skickades till klientadministratörerna om minst en dator inte hade de kritiska korrigeringsfilerna. Följande logik användes för att göra denna bestämning.
1. Hitta alla snabbkorrigeringar som är installerade på datorn.
2. Kontrollera om det finns minst en av snabbkorrigeringarna från den definierade listan.
3. Om Ja är maskinen skyddad. Om inte, är maskinen i riskzonen för attacken.

Du kan använda följande PowerShell-skript för att utföra den här kontrollen manuellt. Den implementerar ovanstående logik.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**F: Varför visar PowerShell-cmdlet <i>Get-MsolDirSyncProvisioningError</i> mindre synkroniseringsfel i resultatet?**

<i>Get-MsolDirSyncProvisioningError</i> returnerar endast DirSync-etableringsfel. Dessutom visar Connect Health-portalen även andra synkroniseringsfeltyper, till exempel exportfel. Detta är förenligt med Azure AD Connect delta resultat. Läs mer om [Azure AD Connect Sync-fel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**F: Varför genereras inte mina ADFS-granskningar?**

Använd PowerShell cmdlet <i>Get-AdfsProperties -AuditLevel</i> för att säkerställa att granskningsloggar inte är inaktiverade. Läs mer om [ADFS-granskningsloggar](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Observera att om avancerade granskningsinställningar skickas till ADFS-servern skrivs eventuella ändringar med auditpol.exe över (händelse om programmet genereras inte är konfigurerat). I det här fallet anger du den lokala säkerhetsprincipen för att logga programgenererade fel och lyckade.

**F: När förnyas agentcertifikatet automatiskt innan utgångsdatum?**
Agentcertifieringen förnyas automatiskt **6 månader** före utgångsdatumet. Om den inte förnyas, se till att agentens nätverksanslutning är stabil. Starta om agenttjänsterna eller uppdatera till den senaste versionen kan också lösa problemet.


## <a name="related-links"></a>Relaterade länkar
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Installation av Azure AD Connect-hälsoagent](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health-åtgärder](how-to-connect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](how-to-connect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](how-to-connect-health-adds.md)
* [Versionshistorik för Azure AD Connect Health](reference-connect-health-version-history.md)
