---
title: "Azure Active Directory Connect Health vanliga frågor och svar – Azure | Microsoft Docs"
description: "Det här avsnittet får du svar frågor om Azure AD Connect Health. Avsnittet ger svar på frågor om hur du använder tjänsten, inklusive faktureringsmodellen, funktioner, begränsningar och support."
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 0c32ac27187a88dd13bb747f541968d2e81c5064
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Vanliga frågor och svar om Azure AD Connect Health
Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om Azure Active Directory (AD Azure) Connect Health. Dessa vanliga frågor och svar tar upp frågor om hur du använder tjänsten, som innehåller fakturering modellen, funktioner, begränsningar och support.

## <a name="general-questions"></a>Allmänna frågor
**F: jag hantera flera Azure AD-kataloger. Hur växla till en som har Azure Active Directory Premium?**

Växla mellan olika Azure AD-innehavare, Välj den för tillfället inloggade **användarnamn** på övre högra hörn och sedan välja lämpligt konto. Om kontot inte finns med här, väljer **logga ut**, och sedan använda autentiseringsuppgifter för global administratör för katalogen som har Azure Active Directory Premium aktiverat för att logga in.

**F: vilken version av identiteten roller som stöds av Azure AD Connect Health?**

Följande tabell listar rollerna och operativsystemversioner som stöds.

|Roll| Operativsystem / Version|
|--|--|
|Active Directory Federation Services (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Version 1.0.9125 eller högre|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Observera att funktionerna som tillhandahålls av tjänsten kan variera beroende på rollen och operativsystemet. Med andra ord kanske alla funktioner inte tillgänglig för alla versioner av operativsystemet. Funktionsbeskrivning mer information finns i.

**F: hur många licenser behöver att övervaka min infrastrukturen?**

* Första Connect Health Agent kräver minst en Azure AD Premium-licens.
* Varje ytterligare registrerade agent kräver 25 ytterligare Azure AD Premium-licenser.
* Agentantal motsvarar det totala antalet agenter som registrerats över alla övervakade roller (AD FS, Azure AD Connect och AD DS).

Licensieringsinformation finns även på den [priser för Azure AD-sidan](https://aka.ms/aadpricing).

Exempel:

| Registrerade agenter | Licenser som behövs | Exempel övervakningskonfiguration |
| ------ | --------------- | --- |
| 1 | 1 | 1 azure AD Connect-server |
| 2 | 26| 1 azure AD Connect-servern och domänkontrollanten för 1 |
| 3 | 51 | 1 server för active Directory Federation Services (AD FS), 1 AD FS-proxy och 1-domänkontrollant |
| 4 | 76 | 1 AD FS-servern, 1 AD FS-proxy och 2 domänkontrollanter |
| 5 | 101 | 1 azure AD Connect-servern, 1 AD FS-servern, 1 AD FS-proxy och 2-domänkontrollanter |

**F: stöder Azure AD Connect Health Azure Tyskland molnet?**

Azure AD Connect Health har en [installation](active-directory-aadconnect-health-agent-install.md) för Azure Tyskland. Alla data för tyska molnet kunder behålls i Tyskland Azure-molnet.


## <a name="installation-questions"></a>Frågor om installation

**F: Vad är effekten av att installera Azure AD Connect Health Agent på enskilda servrar?**

Effekten av att installera Microsoft Azure AD Connect Health Agent, AD FS, webbprogramproxyservrarna, Azure AD Connect (synkronisering)-servrar, domänkontrollanter är minimal med avseende på CPU, minne används, nätverkets bandbredd och lagring.

Följande siffror är en uppskattning:

* CPU-förbrukning: ökning ~ 1-5%.
* Minnesförbrukning: upp till 10% av det totala systemminnet.

> [!NOTE]
> Om agenten inte kan kommunicera med Azure lagrar agenten data lokalt för en definierad högsta gräns. Agenten skriver över ”cachelagrade” data på grundval av ”minst senast underhålls”.
>
>

* Bufferten för lokal lagring för Azure AD Connect Health-agenter: ~ 20 MB.
* För AD FS-servrar rekommenderar vi att du etablerar ett utrymme på 1 024 MB (1 GB) för AD FS-granskning kanal för Azure AD Connect Health-agenter att bearbeta alla granskningsdata innan den skrivs över.

**F: Jag måste starta om min servrar under installationen av den Azure AD Connect Health-agenter?**

Nej. Installationen av agenterna kräver inte att starta om servern. Installationen av vissa nödvändiga steg kan dock kräva en omstart av servern.

Exempelvis kräver installation av .NET Framework för 4.5 på Windows Server 2008 R2 servern startas om.

**F: kan Azure AD Connect Health arbete via en direkt HTTP-proxy?**

Ja. Du kan konfigurera Hälsoagenten om du vill använda en HTTP-proxy för att vidarebefordra utgående HTTP-begäranden för pågående åtgärder.
Läs mer om [konfigurera HTTP-Proxy för Health-agenter](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Om du behöver konfigurera en proxyserver under agentregistreringen kan du behöva ändra proxyinställningarna för Internet Explorer i förväg.

1. Öppna Internet Explorer > **inställningar** > **Internetalternativ** > **anslutningar** > **LAN-inställningar** .
2. Välj **använder en proxyserver för nätverket**.
3. Välj **Avancerat** om du har en annan proxyserver portar för HTTP och HTTPS/Secure.

**F: kan Azure AD Connect Health stöder grundläggande autentisering när du ansluter till http-proxy?**

Nej. En mekanism för att ange en valfri användarnamn och lösenord för grundläggande autentisering stöds inte för närvarande.

**F: vad brandväggsportar behöver att öppna för Azure AD Connect Health Agent ska fungera?**

Finns det [avsnittet krav](active-directory-aadconnect-health-agent-install.md#requirements) lista över portar i brandväggen och andra anslutningskrav.

**F: Varför visas två servrar med samma namn i Azure AD Connect Health-portalen?**

När du tar bort en agent från en server är på servern inte bort automatiskt från Azure AD Connect Health-portalen. Om du manuellt ta bort en agent från en server eller ta bort själva servern måste du manuellt ta bort posten från Azure AD Connect Health-portalen.

Du kan återavbilda en server eller skapa en ny server med samma information (till exempel datornamnet). Om du inte ta bort servern redan är registrerad från Azure AD Connect Health-portalen, och du har installerat agenten på den nya servern, kan du se två poster med samma namn.

I det här fallet Ta manuellt bort posten som hör till äldre server. Data för den här servern ska vara inaktuell.

## <a name="health-agent-registration-and-data-freshness"></a>Health Agent registrerings- och dokumentens

**F: Vad är vanliga orsaker till registreringsfel Health Agent och hur felsöker problem?**

Health-agenten kan inte kan registreras på grund av följande anledningar:

* Agenten kan inte kommunicera med de nödvändiga slutpunkterna eftersom en brandvägg blockerar trafik. Detta är särskilt vanligt på webbprogramproxyservrarna. Kontrollera att du har tillåtit utgående kommunikation till de nödvändiga slutpunkter och portar. Finns det [avsnittet krav](active-directory-aadconnect-health-agent-install.md#requirements) mer information.
* Utgående kommunikation är föremål för SSL-kontroll av nätverksnivån. Detta gör att det certifikat som använder agenten som ska ersättas med inspektion server/entiteten och stegen för att slutföra agentregistreringen misslyckas.
* Användaren inte behörighet att utföra registreringen av agenten. Globala administratörer har åtkomst som standard. Du kan använda [rollbaserad åtkomstkontroll](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) att delegera åtkomst till andra användare.

**F: Jag är få ett meddelande om att ”Hälsotjänstens data inte är uppdaterad”. Hur felsöker problemet?**

Azure AD Connect Health genererar en avisering när den inte får alla datapunkter från servern under de senaste två timmarna. Det kan finnas flera orsaker till den här aviseringen.

* Agenten kan inte kommunicera med de nödvändiga slutpunkterna eftersom en brandvägg blockerar trafik. Detta är särskilt vanligt på webbprogramproxyservrarna. Kontrollera att du har tillåtit utgående kommunikation till de nödvändiga slutpunkterna och portar. Finns det [avsnittet krav](active-directory-aadconnect-health-agent-install.md#requirements) mer information.
* Utgående kommunikation är föremål för SSL-kontroll av nätverksnivån. Detta gör att det certifikat som använder agenten som ska ersättas med inspektion server/entiteten och processen inte kan överföra data till Azure AD Connect Health-tjänsten.
* Du kan använda kommandot anslutningen inbyggda i agenten. [Läs mer](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service).
* Agenterna stöder också utgående anslutning via en oautentiserad HTTP-Proxy. [Läs mer](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy).

## <a name="operations-questions"></a>Operations frågor
**F: behöver jag att aktivera granskning på web application proxy-servrar?**

Nej, granskning behöver inte aktiveras på web application proxy-servrar.

**F: hur Azure AD Connect Health-aviseringar löses?**

Azure AD Connect Health-aviseringar löses på ett lyckat tillstånd. Azure AD Connect Health-agenter identifiera och rapportera lyckade villkor till tjänsten med jämna mellanrum. För några aviseringar är dämpning tidsbaserade. Om samma fel inte observeras inom 72 timmar från varningsgenereringen, matchas med andra ord automatiskt aviseringen.

**F: Jag är hämtar ett meddelande om att ”Test autentiseringsbegäran (syntetisk transaktion) kunde inte hämta en token”. Hur felsöker problemet?**

Azure AD Connect Health för AD FS genererar aviseringen när Health-agenten installeras på en AD FS-servern inte kan hämta en token som en del av en syntetisk transaktion som initieras av Hälsoagenten. Health-agenten använder det lokala systemsammanhanget och försöker hämta en token för ett självsignerat förlitande part. Det här är en fångar in alla test så att AD FS är i ett tillstånd för att utfärda token.

Det här testet misslyckas oftast eftersom Hälsoagenten inte går att matcha namnet för AD FS-servergruppen. Detta kan inträffa om AD FS-servrar som finns bakom en belastningsutjämnare för nätverket och begäran hämtar initieras från en nod som är bakom belastningsutjämnaren (i stället för en vanlig klient som är framför belastningsutjämnaren). Detta kan åtgärdas genom att uppdatera ”” värdfilen som finns under ”C:\Windows\System32\drivers\etc” med IP-adressen för AD FS-servern eller en loopback-IP-adress (127.0.0.1) för AD FS-servergruppsnamnet (t.ex sts.contoso.com). Lägga till värd-fil kommer kortslutning anropet nätverket, vilket ger Hälsoagenten att hämta token.

**F: Jag har fått ett e-postmeddelande som anger mina datorer inte korrigeras för senaste ransomeware attacker. Varför fick e-postmeddelandet?**

Azure AD Connect Health service genomsöks alla datorer som övervakas för att säkerställa uppdateringar som krävs har installerats. E-postmeddelandet skickades till administratörer för klientorganisation om minst en dator inte har kritiska korrigeringar. Följande logik användes för att göra detta.
1. Hitta alla snabbkorrigeringar som är installerade på datorn.
2. Kontrollera om minst en av snabbkorrigeringar från listan över definierade finns.
3. Om Ja, är datorn skyddad. Om inte datorn är i fara för angrepp.

Du kan använda följande PowerShell-skript för att utföra den här kontrollen manuellt. Den implementerar logiken som ovan.

```
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



## <a name="related-links"></a>Relaterade länkar
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation av Azure AD Connect Health Agent](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health-åtgärder](active-directory-aadconnect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](active-directory-aadconnect-health-adds.md)
* [Versionshistorik för Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
