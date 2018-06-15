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
ms.openlocfilehash: 8b007c4658d3ca168c4c1a86a72a737c75ca33db
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371345"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Plattform som stöds migrering av IaaS-resurser från klassiska till Azure Resource Manager
I den här artikeln beskriver vi hur vi är att aktivera migrering av infrastruktur som en tjänst (IaaS)-resurser från klassiskt till Resource Manager distributionsmodellerna. Du kan läsa mer om [Azure Resource Manager-funktioner och fördelar](../articles/azure-resource-manager/resource-group-overview.md). Vi i detalj hur du ansluter resurser från de två distributionsmodeller som finns tillgängliga i din prenumeration med hjälp av virtuellt nätverk plats-till-plats-gatewayer.

## <a name="goal-for-migration"></a>Målet för migrering
Hanteraren för filserverresurser kan distribuera komplexa program via mallar, konfigurerar virtuella datorer med hjälp av VM-tillägg och inkluderar åtkomsthantering och Taggning. Azure Resource Manager innehåller skalbar, parallell distribution för virtuella datorer till tillgänglighetsuppsättningar. Den nya distributionsmodellen ger också livscykelhantering beräkning, nätverk och lagring oberoende av varandra. Slutligen finns fokuserar på hur du aktiverar säkerhet som standard med verkställandet av virtuella datorer i ett virtuellt nätverk.

Nästan alla funktioner från den klassiska distributionsmodellen stöds för beräkning, nätverk och lagring under Azure Resource Manager. Om du vill utnyttja de nya funktionerna i Azure Resource Manager, kan du migrera befintliga distributioner från den klassiska distributionsmodellen.

## <a name="supported-resources-for-migration"></a>Resurser som stöds för migrering
Dessa klassiska IaaS-resurser som stöds under migreringen

* Virtuella datorer
* Tillgänglighetsuppsättningar
* Cloud Services
* Lagringskonton
* Virtuella nätverk
* VPN-gatewayer
* Express Route-gatewayer _(i samma prenumeration som virtuellt nätverk endast)_
* Nätverkssäkerhetsgrupper
* Routningstabeller
* Reserverade ip-adresser

## <a name="supported-scopes-of-migration"></a>Stöds scope för migrering
Det finns 4 olika sätt att slutföra migreringen av beräkning, nätverk och lagringsresurser. Dessa är

* Migrering av virtuella datorer (inte i ett virtuellt nätverk)
* Migrering av virtuella datorer (inom ett virtuellt nätverk)
* Lagringsmigrering för konton
* Fria resurser (Nätverkssäkerhetsgrupper, vägtabeller och reserverade IP-adresser)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migrering av virtuella datorer (inte i ett virtuellt nätverk)
I Resource Manager-distributionsmodellen upprätthålls säkerheten för dina program som standard. Alla virtuella datorer måste vara i ett virtuellt nätverk i Resource Manager-modellen. Azure-plattformen startas (`Stop`, `Deallocate`, och `Start`) virtuella datorer som en del av migreringen. Har du två alternativ för de virtuella nätverk som de virtuella datorerna kommer att migreras till:

* Du kan begära plattformen för att skapa ett nytt virtuellt nätverk och migrera den virtuella datorn till ett nytt virtuellt nätverk.
* Du kan migrera den virtuella datorn till ett befintligt virtuellt nätverk i Resource Manager.

> [!NOTE]
> I det här omfånget för migrering, kan både åtgärderna management-plan och åtgärderna data-plan inte tillåtas för en viss tidsperiod under migreringen.
>
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migrering av virtuella datorer (inom ett virtuellt nätverk)
Endast metadata migrering mellan klassisk och Resource Manager distributionsmodellerna för de flesta konfigurationer av virtuell dator. De underliggande virtuella datorerna körs på samma maskinvara, i samma nätverk och med samma lagringsutrymme. Åtgärderna management-plan kan inte tillåten för en viss tidsperiod under migreringen. Dataplan fortsätter dock att fungera. Dina program som körs på virtuella datorer (klassiskt) det vill säga inte till driftstopp under migreringen.

Följande konfigurationer stöds inte för närvarande. Om stöd lagts till i framtiden, vissa virtuella datorer i den här konfigurationen kan innebära avbrottstid (gå via stopp, frigöra och starta om VM operations).

* Du har mer än en tillgänglighetsuppsättning i en enda molntjänst.
* Du har en eller flera tillgänglighetsuppsättningar och virtuella datorer som inte ingår i en tillgänglighetsuppsättning i en enda molntjänst.

> [!NOTE]
> I den här migreringen omfång tillåtas management plan inte för en viss tidsperiod under migreringen. För vissa konfigurationer enligt beskrivningen tidigare, data-plan avbrottstiden inträffar.
>
>

### <a name="storage-accounts-migration"></a>Lagringsmigrering för konton
Du kan distribuera Resource Manager virtuella datorer i ett klassiskt storage-konto för att tillåta sömlös migrering. Med den här funktionen kan beräknings- och nätverksresurser och de ska migreras oberoende av storage-konton. När du migrerar över dina virtuella datorer och virtuella nätverk måste du migrera över storage-konton för att slutföra migreringen.

> [!NOTE]
> Resource Manager-distributionsmodellen har inte begreppet klassisk avbildningar och diskar. När lagringskontot är migrerade, klassiska avbildningar och diskar visas inte i Resource Manager-stacken men säkerhetskopiera virtuella hårddiskar finns kvar i lagringskontot.
>
>

### <a name="unattached-resources-network-security-groups-route-tables--reserved-ips"></a>Fria resurser (Nätverkssäkerhetsgrupper, vägtabeller och reserverade IP-adresser)
Nätverkssäkerhetsgrupper, vägtabeller och reserverade IP-adresser som inte är kopplade till virtuella datorer och virtuella nätverk som kan migreras oberoende av varandra.

<br>

## <a name="unsupported-features-and-configurations"></a>Funktioner som inte stöds och konfigurationer
Vi stöder för närvarande inte vissa funktioner och konfigurationer. I följande avsnitt beskrivs våra rekommendationer runtom.

### <a name="unsupported-features"></a>Funktioner som inte stöds
Följande funktioner stöds inte för närvarande. Du kan eventuellt ta bort de här inställningarna, migrera de virtuella datorerna och sedan återaktivera inställningarna i Resource Manager-distributionsmodellen.

| Resursprovider | Funktion | Rekommendation |
| --- | --- | --- |
| Compute | Olänkade virtuella diskar. | VHD-blobbar bakom diskarna som ska migreras när Lagringskontot migreras |
| Compute | Avbildningar av virtuella datorer. | VHD-blobbar bakom diskarna som ska migreras när Lagringskontot migreras |
| Nätverk | Slutpunkts-ACL:. | Ta bort slutpunkts-ACL: och gör om migreringen. |
| Nätverk | Application Gateway | Ta bort Programgatewayen innan du påbörjar migreringen och sedan återskapa Programgatewayen när migreringen är klar. |
| Nätverk | Virtuella nätverk med hjälp av VNet-Peering. | Migrera det virtuella nätverket till Resource Manager och sedan peer. Lär dig mer om [VNet-Peering](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Konfigurationer som inte stöds
Följande konfigurationer stöds inte för närvarande.

| Tjänst | Konfiguration | Rekommendation |
| --- | --- | --- |
| Resource Manager |Rollen åtkomstkontroll (RBAC) för klassiska resurser |Eftersom URI för resurserna som har ändrats efter migreringen bör du planera RBAC principuppdateringar som måste utföras efter migreringen. |
| Compute |Flera undernät som är kopplad till en virtuell dator |Uppdatera konfigurationen av undernät för att referera till en enda undernät. Du kan behöva du till ett sekundärt nätverkskort (som refererar till ett annat undernät) från den virtuella datorn och Återanslut den när migreringen har slutförts. |
| Compute |Virtuella datorer som hör till ett virtuellt nätverk men har inte ett explicit undernät som tilldelats |Alternativt kan du ta bort den virtuella datorn. |
| Compute |Virtuella datorer som har aviseringar, Autoskala principer |Migreringen går igenom och inställningarna tas bort. Vi rekommenderar starkt att du utvärderar din miljö innan du utför migreringen. Du kan också konfigurera aviseringar när migreringen är klar. |
| Compute |XML-VM-tillägg (BGInfo-1.*, Visual Studio-felsökaren, Web Deploy och fjärrfelsökning) |Detta stöds inte. Vi rekommenderar att du tar bort de här tilläggen från den virtuella datorn ska fortsätta att migrera eller de kommer att tas bort automatiskt under migreringsprocessen. |
| Compute |Startdiagnostikinställningar med Premium-lagring |Inaktivera Start-diagnostik för virtuella datorer innan du fortsätter med migreringen. Du kan återaktivera startdiagnostikinställningar i Resource Manager-stacken när migreringen är klar. Blobbar som används för skärmbild och seriella loggar bör dessutom tas bort så att du inte längre debiteras för de blobbar. |
| Compute | Molntjänster som innehåller web/worker-roller | Detta stöds för närvarande inte. |
| Compute | Ange molntjänster som innehåller mer än en tillgänglighet eller flera tillgänglighetsuppsättningar. |Detta stöds för närvarande inte. Flytta de virtuella datorerna till samma tillgänglighetsuppsättning innan du migrerar. |
| Compute | Virtuell dator med Azure Security Center-tillägg | Azure Security Center installeras automatiskt tillägg på dina virtuella datorer för att övervaka deras säkerhet och generera aviseringar. Dessa tillägg installeras vanligtvis automatiskt om Azure Security Center-principen är aktiverad för prenumerationen. Om du vill migrera de virtuella datorerna, inaktivera center säkerhetsprincipen på den prenumeration som tar bort Säkerhetscenter övervakning tillägg från de virtuella datorerna. |
| Compute | Virtuell dator med säkerhetskopieringen eller ögonblicksbilden tillägg | Dessa tillägg är installerade på en virtuell dator konfigureras med Azure Backup-tjänsten. När migreringen av dessa virtuella datorer inte stöds, följer du anvisningarna [här](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault) att hålla säkerhetskopior som gjorts före migreringen.  |
| Nätverk |Virtuella nätverk som innehåller virtuella datorer och web/worker-roller |Detta stöds för närvarande inte. Flytta Web/Worker-roller till sina egna virtuella nätverk innan du migrerar. När det klassiska virtuella nätverk migreras, kan den migrerade Azure Resource Manager-nätverk att peerkoppla med klassiska virtuella nätverket för att få liknande konfiguration som tidigare.|
| Nätverk | Klassiska Express Route-kretsar |Detta stöds för närvarande inte. Dessa kretsar måste migreras till Azure Resource Manager innan du påbörjar migreringen IaaS. Mer information om detta finns [flytta ExpressRoute-kretsar från klassiskt till Resource Manager-distributionsmodellen](../articles/expressroute/expressroute-move.md).|
| Azure App Service |Virtuella nätverk som innehåller apptjänstmiljöer |Detta stöds för närvarande inte. |
| Azure HDInsight |Virtuella nätverk som innehåller HDInsight-tjänster |Detta stöds för närvarande inte. |
| Microsoft Dynamics livscykel Services |Virtuella nätverk som innehåller virtuella datorer som hanteras av Dynamics livscykel Services |Detta stöds för närvarande inte. |
| Azure AD Domain Services |Virtuella nätverk som innehåller Azure AD Domain services |Detta stöds för närvarande inte. |
| Azure RemoteApp |Virtuella nätverk som innehåller Azure RemoteApp-distributioner |Detta stöds för närvarande inte. |
| Azure API Management |Virtuella nätverk som innehåller Azure API Management-distributioner |Detta stöds för närvarande inte. Om du vill migrera IaaS VNET, ändra VNET för API Management-distribution som en ingen åtgärd har driftstopp. |
