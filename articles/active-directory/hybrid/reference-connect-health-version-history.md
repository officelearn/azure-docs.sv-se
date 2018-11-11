---
title: Versionshistorik för Azure AD Connect Health
description: Det här dokumentet beskriver versionerna för Azure AD Connect Health och vad ingår i dessa versioner.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: mtillman
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 8ca04fef8a17c43c9d1f761e36d8da7f1e07d4d5
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283588"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Versionshistorik för Azure AD Connect Health
Azure Active Directory-teamet uppdaterar regelbundet Azure AD Connect Health med nya funktioner. Den här artikeln visas de versioner och funktioner som har frisläppts.
Azure AD Connect Health för synkronisering är integrerat med Azure AD Connect-installationen. Läs mer om [historik för Azure AD Connect-version](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)


## <a name="august-2018"></a>Augusti 2018 
*  Azure AD Connect Health-agenten för synkronisering (version 3.1.7.0) som lanseras med Azure AD Connect version 1.1.880.0    
   1. Snabbkorrigering för [hög CPU-problemet av övervakningsagent med .NET Framework KB versioner](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Juni 2018 
**Nya funktioner i förhandsversion:** 
* Azure AD Connect Health för synkronisering – diagnostisera och åtgärda Duplicerat attribut synkroniseringsfel från portalen 

**Agentuppdatering:** 
*  Azure AD Connect Health agent för AD DS (version 3.1.7.0)    
   1. Snabbkorrigering för [hög CPU-problemet av övervakningsagent med .NET Framework KB versioner](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
*   Azure AD Connect Health agent för AD FS (version 3.1.7.0)  
   1. Snabbkorrigering för [hög CPU-problemet av övervakningsagent med .NET Framework KB versioner](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   2. Testa resultaten korrigeringar på sekundär server för AD FS Server 2016
   
*   Azure AD Connect Health agent för AD FS (version 3.1.2.0)  
   1. Snabbkorrigering för minneshantering för agenten och relaterade aviseringar specifikt för version 3.0.244.0


## <a name="may-2018"></a>Maj 2018
**Agentuppdatering:**
*   Azure AD Connect Health agent för AD DS (version 3.0.244.0)
 1. Agenten sekretess förbättring  
 2. Felkorrigeringar och allmänna förbättringar

*   Azure AD Connect Health agent för AD FS (version 3.0.244.0)
 1. Diagnostik-agenttjänsten och relaterade PowerShell-modulen förbättringar
 2. Agenten sekretess förbättring  
 3. Felkorrigeringar och allmänna förbättringar

* Azure AD Connect Health-agenten för synkronisering (version 3.0.164.0) som lanseras med Azure AD Connect version 1.1.819.0 
 1. Agenten sekretess förbättring  
 2. Felkorrigeringar och allmänna förbättringar


## <a name="march-2018"></a>Mars 2018
**Nya funktioner i förhandsversion:**
* Azure AD Connect Health för AD FS - rapport för riskfyllda IP- och avisering.

**Agentuppdatering:**

*   Azure AD Connect Health agent för AD DS (version 3.0.176.0)
  1. Agenten tillgänglighetsförbättringar 
  2. Felkorrigeringar och allmänna förbättringar
*   Azure AD Connect Health agent för AD FS (version 3.0.176.0)
  1. Agenten tillgänglighetsförbättringar 
  2. Felkorrigeringar och allmänna förbättringar
* Azure AD Connect Health-agenten för synkronisering (version 3.0.129.0) som lanseras med Azure AD Connect version 1.1.750.0  
  1. Agenten tillgänglighetsförbättringar 
  2. Felkorrigeringar och allmänna förbättringar

## <a name="december-2017"></a>December 2017
**Agentuppdatering:**

*   Azure AD Connect Health agent för AD DS (version 3.0.145.0)
  1. Agenten tillgänglighetsförbättringar 
  2. Kommandon har lagts till nya agenten felsökning
  3. Felkorrigeringar och allmänna förbättringar
*   Azure AD Connect Health agent för AD FS (version 3.0.145.0)
  1. Kommandon har lagts till nya agenten felsökning
  2. Agenten tillgänglighetsförbättringar 
  3. Felkorrigeringar och allmänna förbättringar
  
## <a name="october-2017"></a>Oktober 2017
**Agentuppdatering:**

 * Azure AD Connect Health-agenten för synkronisering (version 3.0.129.0) som lanseras med Azure AD Connect version 1.1.649.0
<br></br> Fasta ett version kompatibilitetsproblem mellan Azure AD Connect och Azure AD Connect Health Agent för synkronisering. Det här problemet påverkar kunder som utför Azure AD Connect uppgradering till version 1.1.647.0, men för närvarande har Hälsoagenten version 3.0.127.0. Efter uppgraderingen måste skicka Hälsoagenten inte längre hälsotillståndsdata om Azure AD Connect-synkroniseringstjänsten till Azure AD Health-tjänsten. Med den här snabbkorrigeringen installeras Hälsoagenten version 3.0.129.0 under Azure AD Connect-uppgradering. Hälsoagenten version 3.0.129.0 saknar kompatibilitetsproblem med Azure AD Connect version 1.1.649.0.

## <a name="july-2017"></a>Juli 2017
**Agentuppdatering:**

*   Azure AD Connect Health agent för AD DS (version 3.0.68.0)
  1. Felkorrigeringar och allmänna förbättringar
  2. Molnstöd för nationella
*   Azure AD Connect Health agent för AD FS (version 3.0.68.0)
  1. Felkorrigeringar och allmänna förbättringar
  2. Molnstöd för nationella
* Azure AD Connect Health-agenten för synkronisering (version 3.0.68.0) som lanseras med Azure AD Connect version 1.1.614.0
  1. Stöd för Microsoft Azure Government-molnet och Microsoft Cloud Tyskland

## <a name="april-2017"></a>April 2017      
**Agentuppdatering:**

*   Azure AD Connect Health agent för AD FS (version 3.0.12.0)
  1. Felkorrigeringar och allmänna förbättringar
*   Azure AD Connect Health agent för AD DS (version 3.0.12.0)
  1. Prestandaräknare överför förbättringar
  2. Felkorrigeringar och allmänna förbättringar

## <a name="october-2016"></a>Oktober 2016
**Agentuppdatering:**

* Azure AD Connect Health agent för AD FS (version 2.6.408.0)
1. Förbättringar för identifiering av klienternas IP-adresser i autentiseringsbegäranden
2. Felkorrigeringar som rör aviseringar
* Azure AD Connect Health agent för AD DS (version 2.6.408.0)
1. Felkorrigeringar relateras till aviseringar.
* Azure AD Connect Health-agenten för synkronisering (version 2.6.353.0) som lanseras med Azure AD Connect version 1.1.281.0
1. Ange nödvändiga data för synkronisering av felrapporter
2. Felkorrigeringar som rör aviseringar

**Nya funktioner i förhandsversion:**

* Synkronisering felrapporter för Azure AD Connect

**Nya funktioner:**

* Azure AD Connect Health för AD FS - IP-adressfältet finns i rapporten över de 50 användarna med felaktigt användarnamn/lösenord.

## <a name="july-2016"></a>Juli 2016
**Nya funktioner i förhandsversion:**

* [Azure AD Connect Health för AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Januari 2016
**Agentuppdatering:**

* Azure AD Connect Health agent för AD FS (version 2.6.91.1512)

**Nya funktioner:**

* [Testa anslutningsverktyget för Azure AD Connect Health-agenter](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>November 2015
**Nya funktioner:**

* Stöd för [rollbaserad åtkomstkontroll](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**Nya funktioner i förhandsversion:**

* [Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md).

**Åtgärdade problem:**

* Felkorrigeringar för fel som visas under agenten registreringar.

## <a name="september-2015"></a>September 2015
**Nya funktioner:**

* Felaktigt användarnamn lösenord rapport för AD FS
* Stöd för att konfigurera oautentiserade HTTP-Proxy
* Stöd för att konfigurera agenten på Server core
* Förbättringar av aviseringar för AD FS
* Förbättringar i Azure AD Connect Health Agent för AD FS för anslutning och data överför.

**Åtgärdade problem:**

* Felkorrigeringar i Användningsinsikter för AD FS feltyper.

## <a name="june-2015"></a>Juni 2015
**Första versionen av Azure AD Connect Health för AD FS och AD FS-Proxy.**

**Nya funktioner:**

* Aviseringar för övervakning av AD FS och AD FS-Proxy-servrar med e-postmeddelanden.
* Enkel åtkomst till AD FS-topologi och mönster i AD FS-prestandaräknaren.
* Trend i lyckade tokenbegäranden på AD FS-servrar som är grupperade efter program, autentiseringsmetoder, begär nätverk plats osv.
* Trender i misslyckade begäranden på AD FS-servrar som är grupperade efter program, fel typer osv.
* Enklare Agentdistribution med hjälp av autentiseringsuppgifter som Global administratör för den Azure AD.  

## <a name="next-steps"></a>Nästa steg
Läs mer om [övervaka den lokala identitetsinfrastrukturen och synkroniseringstjänster i molnet](whatis-hybrid-identity-health.md).

