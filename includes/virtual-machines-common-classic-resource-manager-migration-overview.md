---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jpconnock
ms.service: virtual-machines
ms.topic: include
ms.date: 05/18/2018
ms.author: jeconnoc
ms.custom: include file
ms.openlocfilehash: d1a6ff8dbd17d2792709a1ce065bcf793154e585
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37780680"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Plattformsunderstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager
Den här artikeln beskriver hur du migrerar infrastruktur som en tjänst (IaaS)-resurser från klassiskt till Resource Manager-distributionsmodeller och information om hur du ansluter resurser från de två distributionsmodeller som finnas i din prenumeration med hjälp av virtuellt nätverk plats-till-plats-gatewayer. Du kan läsa mer om [Azure Resource Manager-funktioner och fördelar](../articles/azure-resource-manager/resource-group-overview.md). 

## <a name="goal-for-migration"></a>Målet för migrering
Resource Manager gör det möjligt att distribuera komplexa program via mallar, konfigurera virtuella datorer med hjälp av VM-tillägg och inkludera åtkomsthantering och Taggning. Azure Resource Manager innehåller skalbara, parallell distribution för virtuella datorer i tillgänglighetsuppsättningar. Den nya modellen innehåller också livscykelhantering av beräkning, nätverk och lagring oberoende av varandra. Slutligen finns fokus på att aktivera säkerhet som standard med verkställandet av virtuella datorer i ett virtuellt nätverk.

Nästan alla funktioner från den klassiska distributionsmodellen stöds för beräkning, nätverk och lagring i Azure Resource Manager. Om du vill dra nytta av de nya funktionerna i Azure Resource Manager, kan du migrera befintliga distributioner från den klassiska distributionsmodellen.

## <a name="supported-resources-for-migration"></a>Resurser som stöds för migrering
Dessa klassiska IaaS-resurser som stöds under migreringen

* Virtuella datorer
* Tillgänglighetsuppsättningar
* Cloud Services med virtuella datorer
* Lagringskonton
* Virtuella nätverk
* VPN-gatewayer
* Express Route-gatewayer _(i samma prenumeration som det virtuella nätverket endast)_
* Nätverkssäkerhetsgrupper
* Routningstabeller
* Reserverade ip-adresser

## <a name="supported-scopes-of-migration"></a>Stöds scope för migrering
Det finns fyra olika sätt att slutföra migreringen av resurser för beräkning, nätverk och lagring:

* [Migrering av virtuella datorer (inte i ett virtuellt nätverk)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migrering av virtuella datorer (inom ett virtuellt nätverk)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migrering av lagringskonton](#migration-of-storage-accounts)
* [Migrering av ej anslutna resurser](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migrering av virtuella datorer (inte i ett virtuellt nätverk)
I Resource Manager-distributionsmodellen tillämpas säkerhet för dina program som standard. Alla virtuella datorer måste vara i ett virtuellt nätverk i Resource Manager-modellen. Azure-plattformen omstarter (`Stop`, `Deallocate`, och `Start`) de virtuella datorerna som en del av migreringen. Har du två alternativ för de virtuella nätverk som de virtuella datorerna kommer att migreras till:

* Du kan begära plattform för att skapa ett nytt virtuellt nätverk och migrera den virtuella datorn till det nya virtuella nätverket.
* Du kan migrera den virtuella datorn till ett befintligt virtuellt nätverk i Resource Manager.

> [!NOTE]
> I den här migreringsomfång kan både hanteringsplanåtgärder och dataplanet-åtgärder inte tillåts för en viss tidsperiod under migreringen.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migrering av virtuella datorer (inom ett virtuellt nätverk)
För de flesta konfigurationer av virtuell dator migrerar endast metadata mellan klassiska och Resource Manager-distributionsmodellen. De underliggande virtuella datorerna körs på samma maskinvara, i samma nätverk, samt med samma lagringsutrymme. Hanteringsplanåtgärder kan inte tillåts för en viss tidsperiod under migreringen. Dataplanet fortsätter dock att fungera. Det vill säga medför dina program som körs på virtuella datorer (klassisk) inte några avbrott under migreringen.

Följande konfigurationer stöds inte för närvarande. Om stöd lagts till i framtiden kommer vissa virtuella datorer i den här konfigurationen orsaka avbrott (gå via stop, frigöra och starta om VM-åtgärder).

* Du har mer än en tillgänglighetsuppsättning i en enda molntjänst.
* Du har en eller flera tillgänglighetsuppsättningar och virtuella datorer som inte ingår i en tillgänglighetsuppsättning i en enda molntjänst.

> [!NOTE]
> I det här omfånget för migrering, kan du inte tillåts Hanteringsplanet för en viss tidsperiod under migreringen. För vissa konfigurationer enligt beskrivningen ovan, driftstopp för dataplanet inträffar.
>

### <a name="migration-of-storage-accounts"></a>Migrering av lagringskonton
Du kan distribuera virtuella Resource Manager-datorer i ett klassiskt lagringskonto för att tillåta sömlös migrering. Med den här funktionen kan resurser för beräkning och nätverk och du bör migrera oberoende av storage-konton. När du migrerar över dina virtuella datorer och virtuellt nätverk, måste du migrera över dina lagringskonton för att slutföra migreringen.

Om ditt lagringskonto inte har några associerade diskar eller virtuella datorer data och har bara blobar, filer, tabeller och köer kan migreringen till Azure Resource Manager göras som en fristående migrering utan beroenden.

> [!NOTE]
> Resource Manager-distributionsmodellen har inte begreppet klassiska avbildningar och diskar. När lagringskontot är migrerade, klassiska avbildningar och diskar visas inte i Resource Manager-stacken men säkerhetskopiering virtuella hårddiskar finns kvar i lagringskontot.
>

### <a name="migration-of-unattached-resources"></a>Migrering av ej anslutna resurser
Storage-konton utan tillhörande diskar eller data för virtuella datorer kan migreras oberoende av varandra.

Nätverkssäkerhetsgrupper, routningstabeller och reserverade IP-adresser som inte är kopplade till alla virtuella datorer och virtuella nätverk kan också migreras oberoende av varandra.

<br>

## <a name="unsupported-features-and-configurations"></a>Funktioner som inte stöds och konfigurationer
Vissa funktioner och konfigurationer stöds inte för närvarande. i följande avsnitt beskrivs våra rekommendationer för dem.

### <a name="unsupported-features"></a>Funktioner som inte stöds
Följande funktioner stöds inte för närvarande. Du kan eventuellt ta bort de här inställningarna, migrera de virtuella datorerna och sedan återaktivera inställningarna i distributionsmodellen för Resource Manager.

| Resursprovider | Funktion | Rekommendation |
| --- | --- | --- |
| Compute | Olänkade virtuella diskar. | VHD-blobbar bakom dessa diskar kan migreras när Lagringskontot har migrerats |
| Compute | Avbildningar av virtuella datorer. | VHD-blobbar bakom dessa diskar kan migreras när Lagringskontot har migrerats |
| Nätverk | Åtkomstkontrollistor för slutpunkter. | Ta bort Endpoint ACL: er och gör om migreringen. |
| Nätverk | Application Gateway | Ta bort Programgatewayen innan du påbörjar migreringen och sedan återskapa Application Gateway när migreringen är klar. |
| Nätverk | Virtuella nätverk med VNet-Peering. | Migrera virtuella nätverket till Resource Manager och sedan peer. Läs mer om [VNet-Peering](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Konfigurationer som inte stöds
Följande konfigurationer stöds inte för närvarande.

| Tjänst | Konfiguration | Rekommendation |
| --- | --- | --- |
| Resource Manager |Rollbaserad åtkomstkontroll (RBAC) för klassiska resurser |Eftersom URI: N för resurserna som modifierats efter migrering, rekommenderar vi att du planerar de RBAC-principuppdateringar som behövs efter migreringen. |
| Compute |Flera undernät som är associerade med en virtuell dator |Uppdatera undernätskonfiguration för att referera till endast ett undernät. Du kan behöva du ta bort ett sekundärt nätverkskort (som hänvisar till ett annat undernät) från den virtuella datorn och ansluta den igen när migreringen har slutförts. |
| Compute |Virtuella datorer som tillhör ett virtuellt nätverk men inte har ett explicit undernät som har tilldelats |Du kan också ta bort den virtuella datorn. |
| Compute |Virtuella datorer som har aviseringar, principer för automatisk skalning |Migreringen går igenom och de här inställningarna ignoreras. Vi rekommenderar starkt att du utvärderar din miljö innan du utför migreringen. Du kan också konfigurera om aviseringsinställningarna när migreringen är klar. |
| Compute |XML-VM-tillägg (BGInfo 1.*, Visual Studio-felsökaren, Web Deploy och fjärrfelsökning) |Detta stöds inte. Vi rekommenderar att du tar bort de här tilläggen från den virtuella datorn ska fortsätta att migrera eller de kommer att tas bort automatiskt under migreringsprocessen. |
| Compute |Startdiagnostik med Premium storage |Inaktivera startdiagnostik för de virtuella datorerna innan du fortsätter med migreringen. Du kan återaktivera startdiagnostiken i Resource Manager-stacken när migreringen är klar. BLOB-objekt som används för skärmbild och seriell loggar bör dessutom tas bort så att du inte längre debiteras för dessa blobar. |
| Compute | Molntjänster som innehåller web/worker-roller | Detta stöds för närvarande inte. |
| Compute | Ange molntjänster som innehåller fler än en tillgänglighet eller flera tillgänglighetsuppsättningar. |Detta stöds för närvarande inte. Flytta de virtuella datorerna till samma tillgänglighetsuppsättning innan du migrerar. |
| Compute | Virtuell dator med Azure Security Center-tillägget | Azure Security Center installeras automatiskt tillägg på dina virtuella datorer kan du övervaka deras säkerhet och generera aviseringar. De här tilläggen installeras vanligtvis automatiskt om Azure Security Center-principen är aktiverad för prenumerationen. Inaktivera säkerhetsprincipen i security center för prenumerationen som tas bort i Security Center monitoring-tillägget från de virtuella datorerna för att migrera de virtuella datorerna. |
| Compute | Virtuell dator med backup eller ögonblicksbild | De här tilläggen är installerade på en virtuell dator som har konfigurerats med Azure Backup-tjänsten. Medan migreringen av dessa virtuella datorer inte stöds, följer du anvisningarna i [här](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault) att behålla säkerhetskopior som gjorts före migreringen.  |
| Nätverk |Virtuella nätverk som innehåller virtuella datorer och web/worker-roller |Detta stöds för närvarande inte. Flytta Web/Worker-roller till sina egna virtuella nätverk innan du migrerar. När det klassiska virtuella nätverket migreras, kan migrerade Azure Resource Manager virtuellt nätverk peerkopplas med klassiska virtuella nätverket för att få liknande konfiguration som tidigare.|
| Nätverk | Klassiska Express Route-kretsar |Detta stöds för närvarande inte. Dessa kretsar måste migreras till Azure Resource Manager innan du påbörjar migreringen IaaS. Mer information finns i [flytta ExpressRoute-kretsar från klassiskt till Resource Manager-distributionsmodellen](../articles/expressroute/expressroute-move.md).|
| Azure App Service |Virtuella nätverk som innehåller App Service-miljöer |Detta stöds för närvarande inte. |
| Azure HDInsight |Virtuella nätverk som innehåller HDInsight-tjänster |Detta stöds för närvarande inte. |
| Microsoft Dynamics Lifecycle Services |Virtuella nätverk som innehåller virtuella datorer som hanteras av Dynamics Lifecycle Services |Detta stöds för närvarande inte. |
| Azure AD Domain Services |Virtuella nätverk som innehåller Azure AD Domain services |Detta stöds för närvarande inte. |
| Azure RemoteApp |Virtuella nätverk som innehåller Azure RemoteApp-distributioner |Detta stöds för närvarande inte. |
| Azure API Management |Virtuella nätverk som innehåller Azure API Management-distributioner |Detta stöds för närvarande inte. Ändra det virtuella nätverket för API Management-distribution, som en ingen åtgärd har stilleståndstid för att migrera IaaS VNET. |
