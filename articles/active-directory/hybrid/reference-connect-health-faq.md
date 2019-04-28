---
title: Azure Active Directory Connect Health vanliga frågor och svar – Azure | Microsoft Docs
description: HÄR får du svar på frågor om Azure AD Connect Health. Avsnittet ger svar på frågor om hur du använder tjänsten, inklusive faktureringsmodellen, funktioner, begränsningar och support.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec88caafa9a6168860a8e9e2ff9e2abe0cfd0e77
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096135"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Vanliga frågor och svar om Azure AD Connect Health
Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om Azure Active Directory (Azure AD) Connect Health. Avsnittet täcker frågor om hur du använder tjänsten, inklusive fakturering modellen, funktioner, begränsningar och support.

## <a name="general-questions"></a>Allmänna frågor
**F: Jag hantera flera Azure AD-kataloger. Hur växlar jag till den som har Azure Active Directory Premium?**

Växla mellan olika Azure AD-klienter, Välj den för tillfället inloggade **användarnamn** i det övre högra hörnet och välj sedan lämpligt konto. Om kontot inte listas här, väljer **logga ut**, och sedan använda de autentiseringsuppgifter som global administratör för katalogen som har Azure Active Directory Premium aktiverat för att logga in.

**F: Vilken version av identitet roller stöds av Azure AD Connect Health?**

I följande tabell listar rollerna och operativsystemversioner som stöds.

|Roll| Operativsystem / Version|
|--|--|
|Active Directory Federation Services (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Version 1.0.9125 eller högre|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Observera att de funktioner som tillhandahålls av tjänsten kan variera beroende på rollen och operativsystemet. Med andra ord kanske alla funktioner inte tillgänglig för alla versioner av operativsystemet. Se funktionsbeskrivningar för information.

**F: Hur många licenser behöver jag att övervaka min infrastruktur?**

* Den första Connect Health-agenten kräver minst en Azure AD Premium-licens.
* Alla registrerade ytterligare agenter krävs 25 ytterligare Azure AD Premium-licenser.
* Agentantal motsvarar det totala antalet agenter som är registrerade i alla övervakade roller (AD FS, Azure AD Connect och/eller AD DS).
* Licensiering av AAD Connect Health kräver inte att tilldela licensen till specifika användare. Du behöver bara har det nödvändiga antalet giltiga licenser.

Mer information om licenser finns även på den [sidan med priser för Azure AD](https://aka.ms/aadpricing).

Exempel:

| Registrerade agenter | Licenser som behövs | Exempel övervakningskonfigurationen |
| ------ | --------------- | --- |
| 1 | 1 | 1 azure AD Connect-servern |
| 2 | 26| 1 azure AD Connect-servern och 1 domänkontrollant |
| 3 | 51 | 1 server för active Directory Federation Services (AD FS), 1 AD FS-proxy och 1-domänkontrollant |
| 4 | 76 | 1 AD FS-servern, 1 AD FS-proxy och 2 domänkontrollanter |
| 5 | 101 | 1 azure AD Connect-servern, 1 AD FS-servern, 1 AD FS-proxy och 2-domänkontrollanter |

**F: Azure AD Connect Health har stöd för Azure-molnet för Tyskland?**

Azure AD Connect Health stöds inte i molnet för Tyskland undantag för den [synkroniseringsfunktionen fel rapporten](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Roller | Funktioner | Stöds i tyska molnet |
| ------ | --------------- | --- |
| Connect Health för synkronisering | Övervaka / Insight / aviseringar / analys | Nej |
|  | Felrapport för synkronisering | Ja |
| Connect Health för AD FS | Övervaka / Insight / aviseringar / analys | Nej |
| Connect Health för ADDS | Övervaka / Insight / aviseringar / analys | Nej |

För att säkerställa agentanslutning över Connect Health för synkronisering, konfigurera den [installationskrav](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) därefter.

## <a name="installation-questions"></a>Installationsfrågor

**F: Vad är effekten av att installera Azure AD Connect Health-agenten på enskilda servrar?**

Effekten av att installera Microsoft Azure AD Connect Health Agent, AD FS, web application proxy-servrar, Azure AD Connect (sync)-servrar, domänkontrollanter är minimal med avseende på processor, minnesförbrukning, nätverkets bandbredd och lagring.

Följande talen är ett approximativt värde:

* CPU-förbrukning: ~ 1-5% ökning.
* Minnesförbrukning: Upp till 10% av det totala systemminnet.

> [!NOTE]
> Om agenten inte kan kommunicera med Azure lagrar agenten lokalt för en definierad högsta gräns. Agenten skriver över dessa ”cachelagrade” data på basis av ”minst senast servas”.
>
>

* Bufferten för lokal lagring för Azure AD Connect Health-agenterna: ~ 20 MB.
* För AD FS-servrar rekommenderar vi att du etablerar ett utrymme på 1 024 MB (1 GB) för AD FS-granskning kanalen för Azure AD Connect Health-agenter att bearbeta alla granskningsdata innan den skrivs över.

**F: Måste jag att starta om Mina servrar under installationen av Azure AD Connect Health-agenterna?**

Nej. Installationen av agenterna kräver inte omstart av servern. Installationen av vissa nödvändiga steg kan dock kräva en omstart av servern.

I Windows Server 2008 R2 måste till exempel installationen av .NET 4.5 Framework server startas om.

**F: Fungerar Azure AD Connect Health via en direkt HTTP-proxy?**

Ja. Du kan konfigurera Hälsoagenten om du vill använda en HTTP-proxy för att vidarebefordra utgående HTTP-begäranden för pågående åtgärder.
Läs mer om [konfigurera HTTP-Proxy för Health-agenterna](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Om du vill konfigurera en proxyserver under agentregistreringen kan behöva du ändra proxyinställningarna för Internet Explorer i förväg.

1. Öppna Internet Explorer > **inställningar** > **Internetalternativ** > **anslutningar** > **LAN-inställningar** .
2. Välj **använder en proxyserver för ditt lokala nätverk**.
3. Välj **Avancerat** om du har olika aktiveringsproxyportarna för HTTP och HTTPS/Secure.

**F: Azure AD Connect Health stöder grundläggande autentisering när du ansluter till HTTP-proxyservrar?**

Nej. En mekanism för att ange en godtycklig användarnamn och lösenord för grundläggande autentisering stöds inte för närvarande.

**F: Vilka portar i brandväggen behöver jag öppna för Azure AD Connect Health-agenten ska fungera?**

Se den [kravavsnitt](how-to-connect-health-agent-install.md#requirements) lista över portar i brandväggen och andra krav för anslutning.

**F: Varför ser jag två servrar med samma namn i Azure AD Connect Health-portalen?**

När du tar bort en agent från en server tas servern inte automatiskt bort från Azure AD Connect Health-portalen. Om du manuellt ta bort en agent från en server eller ta bort själva servern, måste du manuellt ta bort posten från Azure AD Connect Health-portalen.

Du kan återställa avbildningen av en server eller skapa en ny server med samma information (till exempel datornamnet). Om du inte ta bort redan registrerad server från Azure AD Connect Health-portalen, och du har installerat agenten på den nya servern, visas två poster med samma namn.

I det här fallet manuellt tar du bort posten som hör till den äldre servern. Data för den här servern ska vara inaktuell.

## <a name="health-agent-registration-and-data-freshness"></a>Health-agenten registrering och data färskhet

**F: Vad är vanliga orsaker till registreringsfel Health-agenten och hur felsöker jag problem?**

Health-agenten kan inte registrera på grund av följande anledningar:

* Agenten kan inte kommunicera med de nödvändiga slutpunkterna eftersom en brandvägg som blockerar trafiken. Detta är särskilt vanligt på proxyservrarna för webbappen. Kontrollera att du har tillåtit utgående kommunikation till de nödvändiga slutpunkterna och portar. Se den [kravavsnitt](how-to-connect-health-agent-install.md#requirements) information.
* Utgående kommunikation är föremål för en SSL-kontroll av nätverket. Detta gör att det certifikat som använder agenten som ska ersättas med server/entiteten inspektion och stegen för att slutföra agentregistreringen misslyckas.
* Användaren har inte åtkomst för att genomföra registreringen av agenten. Globala administratörer har åtkomst som standard. Du kan använda [rollbaserad åtkomstkontroll](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) att delegera åtkomst till andra användare.

**F: Jag är komma aviserad om att ”Hälsotjänstens data inte är uppdaterad”. Hur gör jag för att felsöka problemet?**

Azure AD Connect Health genererar en avisering när den inte tar emot alla datapunkter från servern under de senaste två timmarna. [Läs mer](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Operations-frågor
**F: Måste jag aktivera granskning på web application proxy-servrar?**

Nej, granskning behöver inte aktiveras på web application proxy-servrar.

**F: Hur Azure AD Connect Health-aviseringar supportärendena?**

Azure AD Connect Health-aviseringar supportärendena går att på ett lyckat tillstånd. Azure AD Connect Health-agenterna identifiera och rapportera lyckades villkor till tjänsten med jämna mellanrum. För några aviseringar är Undertryckning tidsbaserade. Om samma fel inte observeras inom 72 timmar från genereringen, matchas med andra ord automatiskt aviseringen.

**F: Jag är komma aviserad om att ”Test-autentiseringsbegäran (syntetisk transaktion) kunde inte hämta en token”. Hur gör jag för att felsöka problemet?**

Azure AD Connect Health för AD FS genererar den här aviseringen när Health-agenten installeras på AD FS-servern inte kan hämta en token som en del av en syntetisk transaktion som initieras av Hälsoagenten. Health-agenten använder det lokala systemsammanhanget och försöker hämta en token för ett självsignerat förlitande part. Det här är ett övergripande test för att kontrollera att AD FS är i tillståndet utfärda token.

Det här testet misslyckas oftast eftersom Health-agenten inte kan matcha namnet på AD FS-servergruppen. Detta kan inträffa om AD FS-servrar som finns bakom en belastningsutjämnare för nätverk och begäran hämtar initieras från en nod som är bakom belastningsutjämnare (i stället för en vanlig klient som är framför belastningsutjämnaren). Detta kan åtgärdas genom att uppdatera filen ”värdar” ”C:\Windows\System32\drivers\etc” inkludera IP-adressen för AD FS-servern eller en loopback-IP-adress (127.0.0.1) för namnet på AD FS-servergruppen (t.ex sts.contoso.com). Att lägga till värdfilen kommer kortslutning nätverksanrop, vilket medför att Health-agenten att hämta token.

**F: Jag får ett e-postmeddelande som anger mina datorer inte är korrigeras för de senaste utpressningstrojan-attackerna. Varför får jag detta e-postmeddelande?**

Azure AD Connect Health-tjänsten genomsöks alla datorer som övervakas för att säkerställa att uppdateringarna som krävs har installerats. E-postmeddelandet har skickats till innehavaradministratörer Om minst en dator inte har de viktiga uppdateringarna. Följande logik används för att göra den bedömningen.
1. Hitta alla snabbkorrigeringar som är installerade på datorn.
2. Kontrollera om minst en av snabbkorrigeringar från listan över definierade finns.
3. Om Ja, är datorn skyddad. Om inte datorn är risk för angrepp.

Du kan använda följande PowerShell-skript för att utföra den här kontrollen i manuellt. Den implementerar logiken som ovan.

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

**F: Varför har PowerShell-cmdleten <i>Get-MsolDirSyncProvisioningError</i> visa mindre synkroniseringsfel i resultatet?**

<i>Get-MsolDirSyncProvisioningError</i> returnerar endast DirSync etablering fel. Förutom att visar Connect Health-portalen även andra feltyper, till exempel exportfel. Det här är konsekvent med Azure AD Connect delta resultatet. Läs mer om [Azure AD Connect Sync-fel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**F: Varför är min ADFS granskningar skapas?**

Använd PowerShell-cmdleten <i>Get-AdfsProperties - AuditLevel</i> att kontrollera granskningsloggar inte är i inaktiverat tillstånd. Läs mer om [AD FS-granskningsloggar](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Meddelande om det är avancerade granskningsinställningar som skickas till AD FS-servern, eventuella ändringar med auditpol.exe kommer att skrivas över (händelse om programmet genereras inte har konfigurerats). I det här fallet, ange den lokala säkerhetsprincipen logga programmet genererade fel och lyckades.

**F: När kommer agentcertifikatet vara automatisk förnyade innan upphör att gälla?**
Agent-certifieringen kommer att automatisk förnyas **6 månader** innan dess förfallodatum. Om den inte förnyas, kontrollera att nätverksanslutningen för agenten är stabil. Starta om agenten tjänsterna eller uppdatera till den senaste versionen kan också lösa problemet.


## <a name="related-links"></a>Relaterade länkar
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Installation av Azure AD Connect Health Agent](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health-åtgärder](how-to-connect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](how-to-connect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](how-to-connect-health-adds.md)
* [Versionshistorik för Azure AD Connect Health](reference-connect-health-version-history.md)
