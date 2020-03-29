---
title: Versionshistorik för Azure AD Connect Health
description: Det här dokumentet beskriver versionerna för Azure AD Connect Health och vad som har inkluderats i dessa versioner.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ace8d170a5ba48c00775c3b376df8bb70a337d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897034"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Versionshistorik för Azure AD Connect Health
Azure Active Directory-teamet uppdaterar regelbundet Azure AD Connect Health med nya funktioner och funktioner. I den här artikeln visas de versioner och funktioner som har släppts.  

> [!NOTE]
> Connect Health-agenter uppdateras automatiskt när den nya versionen släpps. Kontrollera att inställningarna för automatisk uppgradering är aktiverade från Azure-portalen.
>

Azure AD Connect Health for Sync är integrerat med Azure AD Connect-installation. Läs mer om [Azure AD Connect-utgivningshistorik](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) För funktionsfeedback, rösta på [Connect Health User Voice-kanal](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="july-2019"></a>Juli 2019
**Uppdatering av agent**
* Azure AD Connect Hälsoagent för AD FS (version 3.1.59.0) 
   1. Textändring i TestWindowsTransport
   2. Ändringar för AD FS RP-uppladdning
   
* Azure AD Connect Hälsoagent för AD FS (version 3.1.56.0) 
   1. Lägg till TestWindowsTransport-test och ta bort WsTrust-slutpunkters kontroller i CheckOffice365Endpoints-testet
   2. Logga OS- och .NET-information
   3. Öka RP-konfigurationsmeddelandeöverföringsstorleken till 1 MB.
   4. Felkorrigeringar
   
* Azure AD Connect Hälsoagent för AD DS (version 3.1.56.0) 
   1. Logga OS- och .NET-information 
   2. Felkorrigeringar

## <a name="may-2019"></a>Maj 2019
**Agent Uppdatering:** 
* Azure AD Connect Hälsoagent för AD FS (version 3.1.51.0) 
   1. Buggfix för att skilja mellan flera inloggningar som delar samma klient-request-id.
   2. Buggfix för att tolka felaktiga användarnamn/lösenordsfel på språklokaliserade servrar.   

## <a name="april-2019"></a>April 2019
**Agent Uppdatering:** 
* Azure AD Connect Hälsoagent för AD FS (version 3.1.46.0) 
   1. Fix Check Duplicera SPN-varningsprocess för ADFS

## <a name="march-2019"></a>Mars 2019
**Agent Uppdatering:** 
* Azure AD Connect Hälsoagent för AD DS (version 3.1.41.0)  
   1. .NET-versionssamling
   2. Förbättring av prestandaräknadsinsamling när vissa kategorier saknas
   3. Buggfix för att förhindra lek av flera Övervakningsagentinstanser

* Azure AD Connect Hälsoagent för AD FS (version 3.1.41.0) 
   1. Integrera och uppgradera AD FS-testskript med ADFSToolBox
   2. Implementera .NET-versionssamling
   3. Förbättring av prestandaräknadsinsamling när vissa kategorier saknas
   4. Buggfix för att förhindra lek av flera Övervakningsagentinstanser


## <a name="november-2018"></a>November 2018
**Nya GA-funktioner:** 
* Azure AD Connect Health for Sync – Diagnostisera och åtgärda dubblettat attributsynkroniseringsfel från portalen

**Agent Uppdatering:** 
* Azure AD Connect Hälsoagent för AD DS (version 3.1.24.0) 
   1. TLS-protokoll version 1.2-regel (Transport Layer Security) version 1.2
   2. Minska varningsbrus för global katalog
   3. Buggfixar för hälsoagentregistrering

* Azure AD Connect Hälsoagent för AD FS (version 3.1.24.0)  
   1. TLS-protokoll version 1.2-regel (Transport Layer Security) version 1.2
   2. Stöd för Test-ADFSRequestToken för lokaliserat operativsystem
   3. Löst problem med låsning av diagnostikagent EventHandler
   4. Buggfixar för hälsoagentregistrering

## <a name="august-2018"></a>Augusti 2018 
*  Azure AD Connect Health agent for Sync (version 3.1.7.0) som släpptes med Azure AD Connect version 1.1.880.0    
   1. Snabbkorrigering för [hög CPU-problem med övervakningsagent med .NET Framework KB-versioner](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Juni 2018 
**Nya förhandsgranskningsfunktioner:** 
* Azure AD Connect Health for Sync – Diagnostisera och åtgärda dubblettat attributsynkroniseringsfel från portalen 

**Agent Uppdatering:** 
* Azure AD Connect Hälsoagent för AD DS (version 3.1.7.0)    
  1. Snabbkorrigering för [hög CPU-problem med övervakningsagent med .NET Framework KB-versioner](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Azure AD Connect Hälsoagent för AD FS (version 3.1.7.0)  
  1. Snabbkorrigering för [hög CPU-problem med övervakningsagent med .NET Framework KB-versioner](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Testresultatkorrigeringar på sekundär ADFS Server 2016-server
   
* Azure AD Connect Hälsoagent för AD FS (version 3.1.2.0)  
  1. Snabbkorrigering för hantering av agentminne och relaterade aviseringar specifikt för version 3.0.244.0


## <a name="may-2018"></a>Maj 2018
**Agent Uppdatering:**
* Azure AD Connect Hälsoagent för AD DS (version 3.0.244.0)
  1. Förbättrad sekretess för agent  
  2. Buggfixar och allmänna förbättringar

* Azure AD Connect Hälsoagent för AD FS (version 3.0.244.0)
  1. Agent Diagnostics Service och relaterade Förbättringar av PowerShell-modul
  2. Förbättrad sekretess för agent  
  3. Buggfixar och allmänna förbättringar

* Azure AD Connect Health agent for Sync (version 3.0.164.0) släppt med Azure AD Connect version 1.1.819.0 
  1. Förbättrad sekretess för agent  
  2. Buggfixar och allmänna förbättringar


## <a name="march-2018"></a>Mars 2018
**Nya förhandsgranskningsfunktioner:**
* Azure AD Connect Health för AD FS - Riskfylld IP-rapport och avisering.

**Agent Uppdatering:**

* Azure AD Connect Hälsoagent för AD DS (version 3.0.176.0)
  1. Förbättringar av agenttillgänglighet 
  2. Buggfixar och allmänna förbättringar
* Azure AD Connect Hälsoagent för AD FS (version 3.0.176.0)
  1. Förbättringar av agenttillgänglighet 
  2. Buggfixar och allmänna förbättringar
* Azure AD Connect Health agent for Sync (version 3.0.129.0) släppt med Azure AD Connect version 1.1.750.0  
  1. Förbättringar av agenttillgänglighet 
  2. Buggfixar och allmänna förbättringar

## <a name="december-2017"></a>December 2017
**Agent Uppdatering:**

* Azure AD Connect Hälsoagent för AD DS (version 3.0.145.0)
  1. Förbättringar av agenttillgänglighet 
  2. Lade till nya felsökningskommandon för agenter
  3. Buggfixar och allmänna förbättringar
* Azure AD Connect Hälsoagent för AD FS (version 3.0.145.0)
  1. Lade till nya felsökningskommandon för agenter
  2. Förbättringar av agenttillgänglighet 
  3. Buggfixar och allmänna förbättringar
  
## <a name="october-2017"></a>Oktober 2017
**Agent Uppdatering:**

 * Azure AD Connect Health agent for Sync (version 3.0.129.0) släppt med Azure AD Connect version 1.1.649.0
<br></br> Åtgärdade ett versionskompatibilitetsproblem mellan Azure AD Connect och Azure AD Connect Health Agent for Sync. Det här problemet påverkar kunder som utför Azure AD Connect på plats uppgradering till version 1.1.647.0, men har för närvarande Health Agent version 3.0.127.0. Efter uppgraderingen kan hälsoagenten inte längre skicka hälsodata om Azure AD Connect-synkroniseringstjänst till Azure AD Health Service. Med den här korrigeringen installeras Health Agent version 3.0.129.0 under Azure AD Connect-uppgradering på plats. Health Agent version 3.0.129.0 har inget kompatibilitetsproblem med Azure AD Connect version 1.1.649.0.

## <a name="july-2017"></a>Juli 2017
**Agent Uppdatering:**

* Azure AD Connect Hälsoagent för AD DS (version 3.0.68.0)
  1. Buggfixar och allmänna förbättringar
  2. Suverän molnsupport
* Azure AD Connect Hälsoagent för AD FS (version 3.0.68.0)
  1. Buggfixar och allmänna förbättringar
  2. Suverän molnsupport
* Azure AD Connect Health agent for Sync (version 3.0.68.0) släppt med Azure AD Connect version 1.1.614.0
  1. Stöd för Microsoft Azure Government Cloud och Microsoft Cloud Germany

## <a name="april-2017"></a>April 2017      
**Agent Uppdatering:**

* Azure AD Connect Hälsoagent för AD FS (version 3.0.12.0)
  1. Buggfixar och allmänna förbättringar
* Azure AD Connect Hälsoagent för AD DS (version 3.0.12.0)
  1. Förbättringar av prestandaräknare laddar upp
  2. Buggfixar och allmänna förbättringar

## <a name="october-2016"></a>Oktober 2016
**Agent Uppdatering:**

* Azure AD Connect Hälsoagent för AD FS (version 2.6.408.0)
* Förbättringar i identifieringen av klient-IP-adresser i autentiseringsbegäranden
* Buggfixar relaterade till aviseringar
* Azure AD Connect Hälsoagent för AD DS (version 2.6.408.0)
* Buggfixar relaterade till aviseringar.
* Azure AD Connect Health agent for Sync (version 2.6.353.0) släppt med Azure AD Connect version 1.1.281.0
* Ange nödvändiga data för synkroniseringsfelrapporterna
* Buggfixar relaterade till aviseringar

**Nya förhandsgranskningsfunktioner:**

* Synkroniseringsfelrapporter för Azure AD Connect

**Nya funktioner:**

* Azure AD Connect Health for AD FS – IP-adressfältet är tillgängligt i rapporten om de 50 bästa användarna med felaktigt användarnamn/lösenord.

## <a name="july-2016"></a>Juli 2016
**Nya förhandsgranskningsfunktioner:**

* [Azure AD Connect-hälsotillstånd för AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Januari 2016
**Agent Uppdatering:**

* Azure AD Connect Hälsoagent för AD FS (version 2.6.91.1512)

**Nya funktioner:**

* [Test anslutningsverktyg för Azure AD Connect hälsoagenter](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>November 2015
**Nya funktioner:**

* Stöd för [rollbaserad åtkomstkontroll](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**Nya förhandsgranskningsfunktioner:**

* [Azure AD Connect-hälsotillstånd för synkronisering](how-to-connect-health-sync.md).

**Fasta problem:**

* Buggfixar för fel som visas under agentregistreringar.

## <a name="september-2015"></a>September 2015
**Nya funktioner:**

* Fel lösenordsrapport för fel användarnamn för AD FS
* Stöd för att konfigurera Oautentiserade HTTP-proxy
* Stöd för att konfigurera agent på Server core
* Förbättringar av aviseringar för AD FS
* Förbättringar i Azure AD Connect Health Agent för AD FS för anslutning och dataöverföring.

**Fasta problem:**

* Buggfixar i användningsstatistik för AD FS-feltyper.

## <a name="june-2015"></a>Juni 2015
**Första versionen av Azure AD Connect Health för AD FS och AD FS Proxy.**

**Nya funktioner:**

* Varningar för övervakning av AD FS- och AD FS-proxyservrar med e-postmeddelanden.
* Enkel åtkomst till AD FS-topologi och mönster i AD FS-prestandaräknare.
* Trend i lyckade tokenbegäranden på AD FS-servrar grupperade efter program, autentiseringsmetoder, begäran om nätverksplats etc.
* Trender i misslyckade begäranden på AD FS-servrar grupperade efter program, feltyper etc.
* Enklare agentdistribution med Azure AD Global Admin-autentiseringsuppgifter.  

## <a name="next-steps"></a>Nästa steg
Läs mer om [Övervaka din lokala identitetsinfrastruktur och synkroniseringstjänster i molnet](whatis-hybrid-identity-health.md).

