---
title: Stöd för fysisk server utvärdering med Azure Migrate
description: Läs mer om stöd för fysisk server-utvärdering med Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 057d384c14328deca2853e891f23250aa1d61702
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154796"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Support mat ris för fysisk server-utvärdering 

Du kan använda [tjänsten Azure Migrate](migrate-overview.md) för att utvärdera och migrera datorer till Microsoft Azure molnet. Den här artikeln sammanfattar support inställningar och begränsningar för att utvärdera och migrera lokala fysiska servrar.


## <a name="overview"></a>Översikt

Om du vill utvärdera lokala datorer för migrering till Azure med den här artikeln lägger du till verktyget Azure Migrate: Server utvärderings verktyg i ett Azure Migrate projekt. Du distribuerar [Azure Migrate-enheten](migrate-appliance.md). Enheten identifierar kontinuerligt lokala datorer och skickar konfigurations-och prestanda data till Azure. Efter dator identifiering samlar du in identifierade datorer i grupper och kör en utvärdering för en grupp

## <a name="limitations"></a>Begränsningar

**Support** | **Detaljer**
--- | ---
**Utvärderings gränser**| Identifiera och utvärdera upp till 35 000 fysiska servrar i ett enda [projekt](migrate-support-matrix.md#azure-migrate-projects).
**Projekt gränser** | Du kan skapa flera projekt i en Azure-prenumeration. Ett projekt kan omfatta virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar, upp till utvärderings gränserna.
**Identifiering** | Azure Migrates apparaten kan identifiera upp till 250 fysiska servrar.
**Utvärdering** | Du kan lägga till upp till 35 000 datorer i en enda grupp.<br/><br/> Du kan utvärdera upp till 35 000 datorer i en enda utvärdering.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.




## <a name="physical-server-requirements"></a>Krav för fysisk server

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Distribution av fysisk server**       | Den fysiska servern kan vara fristående eller distribuerad i ett kluster. |
| **Behörigheter**           | **Windows:** Konfigurera ett lokalt användar konto på alla Windows-servrar som du vill ska ingå i identifieringen. Användar kontot måste läggas till i dessa grupper – användare av fjärr skrivbord, prestanda övervaknings användare och användare av prestanda loggar. <br/> **Linux:** Du behöver ett rot konto på de Linux-servrar som du vill identifiera. |
| **Operativsystem** | Alla [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operativsystem stöds förutom följande:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Krav för Azure Migrate utrustning

Azure Migrate använder [Azure Migrates enheten](migrate-appliance.md) för identifiering och utvärdering. Installationen av fysiska servrar kan köras på en virtuell dator eller på en fysisk dator. Du ställer in det med ett PowerShell-skript som du hämtar från Azure Portal.

- Lär dig mer om installations [krav](migrate-appliance.md#appliance---physical) för fysiska servrar.
- Lär dig mer om [webb adresser](migrate-appliance.md#url-access) som behövs för att få åtkomst till enheten.

## <a name="port-access"></a>Port åtkomst

I följande tabell sammanfattas port kraven för utvärdering.

**Enhet** | **Anslutning**
--- | ---
**Enhet** | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med URL: en: ``` https://<appliance-ip-or-name>:44368 ```<br/> Utgående anslutningar på portarna 443, 5671 och 5672 för att skicka identifierings-och prestanda-metadata till Azure Migrate.
**Fysiska servrar** | **Windows:** Inkommande anslutningar på port 443, WinRM-portar 5985 (HTTP) och 5986 (HTTPS) för att hämta konfigurations-och prestanda metadata från Windows-servrar. <br/> **Linux:**  Inkommande anslutningar på port 22 (UDP) för hämtning av konfigurations-och prestanda-metadata från Linux-servrar. |

## <a name="agent-based-dependency-visualization"></a>Agent-baserad beroende visualisering

[Beroende visualisering](concepts-dependency-visualization.md) hjälper dig att visualisera beroenden mellan datorer som du vill utvärdera och migrera. För en agent-baserad visualisering sammanfattas krav och begränsningar i följande tabell.


**Krav** | **Detaljer**
--- | ---
**Distribution** | Innan du distribuerar beroende visualisering bör du ha ett Azure Migrate-projekt på plats, med verktyget Azure Migrate: Server bedömning som har lagts till i projektet. Du kan distribuera beroende visualisering när du har konfigurerat en Azure Migrate-apparat för att identifiera dina lokala datorer.<br/><br/> Beroende visualisering är inte tillgänglig i Azure Government.
**Tjänstkarta** | Agent-baserad beroende visualisering använder [tjänstkarta](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) lösning i [Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Om du vill distribuera associerar du en ny eller befintlig Log Analytics arbets yta med ett Azure Migrate projekt.
**Log Analytics-arbetsyta** | Arbets ytan måste vara i samma prenumeration som Azure Migrate-projektet.<br/><br/> Azure Migrate stöder arbets ytor som finns i regionerna östra USA, Sydostasien och Europa, västra.<br/><br/>  Arbets ytan måste vara i en region där [tjänstkarta stöds](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Det går inte att ändra arbets ytan för ett Azure Migrate projekt när den har lagts till.
**Avgifter** | Tjänstkarta-lösningen debiteras inga avgifter för de första 180 dagarna (från dagen då du kopplade arbets ytan Log Analytics med Azure Migrate-projektet).<br/><br/> Efter 180 dagar kommer standard Log Analytics avgifter att gälla.<br/><br/> Om du använder någon annan lösning än Tjänstkarta i den associerade Log Analytics arbets ytan debiteras standard Log Analytics avgifter.<br/><br/> Om du tar bort Azure Migrate-projektet raderas inte arbets ytan med den. När du har tagit bort projektet är Tjänstkarta inte kostnads fritt, och varje nod debiteras enligt den betalda nivån i Log Analytics arbets ytan.
**Aktörer** | Agent-baserad beroende visualisering kräver att två agenter installeras på varje dator som du vill analysera.<br/><br/> - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [beroende agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Internetanslutning** | Om datorerna inte är anslutna till Internet måste du installera Log Analytics gateway på dem.

## <a name="next-steps"></a>Nästa steg

[Förbered för utvärdering av fysisk server](tutorial-prepare-physical.md).
