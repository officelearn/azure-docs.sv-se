---
title: ta med fil
description: ta med fil
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: 4e07334e859f2c1401547cc3f88988830b71c5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77192767"
---
I den här artikeln beskrivs hur du migrerar infrastruktur som en tjänst (IaaS) resurser från distributionsmodellerna Classic till Resource Manager och information om hur du ansluter resurser från de två distributionsmodeller som samexisterar i din prenumeration med hjälp av virtuellt nätverk gateways från plats till plats. Du kan läsa mer om [Azure Resource Manager-funktioner och fördelar](../articles/azure-resource-manager/management/overview.md). 

## <a name="goal-for-migration"></a>Mål för migrering
Resource Manager gör det möjligt att distribuera komplexa program via mallar, konfigurerar virtuella datorer med hjälp av VM-tillägg och innehåller åtkomsthantering och taggning. Azure Resource Manager innehåller skalbar, parallell distribution för virtuella datorer i tillgänglighetsuppsättningar. Den nya distributionsmodellen ger också livscykelhantering av beräkning, nätverk och lagring oberoende av varandra. Slutligen finns det fokus på att aktivera säkerhet som standard med tillämpning av virtuella datorer i ett virtuellt nätverk.

Nästan alla funktioner från den klassiska distributionsmodellen stöds för beräkning, nätverk och lagring under Azure Resource Manager. Om du vill dra nytta av de nya funktionerna i Azure Resource Manager kan du migrera befintliga distributioner från den klassiska distributionsmodellen.

## <a name="supported-resources-for-migration"></a>Resurser som stöds för migrering
Dessa klassiska IaaS-resurser stöds under migrering

* Virtuella datorer
* Tillgänglighetsuppsättningar
* Lagringskonton
* Virtuella nätverk
* VPN-gatewayer
* Express Route Gateways _(i samma prenumeration som endast virtuellt nätverk)_
* Nätverkssäkerhetsgrupper
* Routningstabeller
* Reserverade ip-adresser

## <a name="supported-scopes-of-migration"></a>Tillämpningsområde för migrering som stöds
Det finns fyra olika sätt att slutföra migreringen av beräknings-, nätverks- och lagringsresurser:

* [Migrering av virtuella datorer (INTE i ett virtuellt nätverk)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migrering av virtuella datorer (i ett virtuellt nätverk)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migrering av lagringskonton](#migration-of-storage-accounts)
* [Flyttning av obundna resurser](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migrering av virtuella datorer (INTE i ett virtuellt nätverk)
I resurshanterarens distributionsmodell tillämpas säkerhet för dina program som standard. Alla virtuella datorer måste finnas i ett virtuellt nätverk i Resource Manager-modellen. Azure-plattformen startar`Stop`om `Deallocate`( `Start`, och ) de virtuella datorerna som en del av migreringen. Du har två alternativ för de virtuella nätverk som de virtuella datorerna ska migreras till:

* Du kan begära att plattformen skapar ett nytt virtuellt nätverk och migrerar den virtuella datorn till det nya virtuella nätverket.
* Du kan migrera den virtuella datorn till ett befintligt virtuellt nätverk i Resource Manager.

> [!NOTE]
> I det här migreringsområdet kan både hanteringsplan och dataplansåtgärder inte tillåtas under en tidsperiod under migreringen.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migrering av virtuella datorer (i ett virtuellt nätverk)
För de flesta VM-konfigurationer migreras endast metadata mellan distributionsmodellerna för Klassisk och Resource Manager. De underliggande virtuella datorerna körs på samma maskinvara, i samma nätverk och med samma lagring. Ledningens planverksamhet får inte tillåtas under en viss tidsperiod under migreringen. Dataplanet fortsätter dock att fungera. Det vill än om dina program som körs ovanpå virtuella datorer (klassisk) inte medför driftstopp under migreringen.

Följande konfigurationer stöds för närvarande inte. Om stöd läggs till i framtiden kan vissa virtuella datorer i den här konfigurationen medföra driftstopp (gå igenom stop, deallocate och starta om vm-åtgärder).

* Du har mer än en tillgänglighet inställd i en enda molntjänst.
* Du har en eller flera tillgänglighetsuppsättningar och virtuella datorer som inte finns i en tillgänglighetsuppsättning i en enda molntjänst.

> [!NOTE]
> I det här migreringsområdet kanske hanteringsplanet inte tillåts under en viss tid under migreringen. För vissa konfigurationer som beskrivits tidigare inträffar avbrott i dataplanet.
>

### <a name="migration-of-storage-accounts"></a>Migrering av lagringskonton
Om du vill tillåta sömlös migrering kan du distribuera virtuella resurshanterare i ett klassiskt lagringskonto. Med den här funktionen kan och bör beräknings- och nätverksresurser migreras oberoende av lagringskonton. När du har migrerat över virtuella datorer och virtuella nätverk måste du migrera över dina lagringskonton för att slutföra migreringen.

Om ditt lagringskonto inte har några associerade diskar eller virtuella datorer data och bara har blobbar, filer, tabeller och köer kan migreringen till Azure Resource Manager göras som en fristående migrering utan beroenden.

> [!NOTE]
> Resurshanterarens distributionsmodell har inte begreppet klassiska avbildningar och diskar. När lagringskontot migreras visas inte klassiska avbildningar och diskar i Resurshanterarens stack, men de virtuella hårddiskarna för säkerhetskopiering finns kvar i lagringskontot.

Följande skärmbilder visar hur du uppgraderar ett klassiskt lagringskonto till ett Azure Resource Manager-lagringskonto med Azure Portal:The following screenshots show how to upgrade a Classic storage account to an Azure Resource Manager storage account using Azure portal:
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till ditt lagringskonto.
3. Klicka på Migrera **till ARM**i avsnittet **Inställningar** .
4. Klicka på **Validera** för att fastställa migreringsförsämringsmöjligheten.
5. Om valideringen går klickar du på **Förbered** för att skapa ett migrerat lagringskonto.
6. Skriv **ja** för att bekräfta migreringen och klicka på **Slutför** för att slutföra migreringen.

    ![Validera lagringskonto](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Förbereda lagringskonto](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Slutföra migrering av lagringskonto](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Flyttning av obundna resurser
Lagringskonton utan associerade diskar eller data för virtuella datorer kan migreras oberoende av dem.

Nätverkssäkerhetsgrupper, vägtabeller & reserverade IP-adresser som inte är kopplade till virtuella datorer och virtuella nätverk kan också migreras oberoende av dem.

<br>

## <a name="unsupported-features-and-configurations"></a>Funktioner och konfigurationer som inte stöds
Vissa funktioner och konfigurationer stöds för närvarande inte. i följande avsnitt beskrivs våra rekommendationer kring dem.

### <a name="unsupported-features"></a>Funktioner som inte stöds
Följande funktioner stöds för närvarande inte. Du kan också ta bort dessa inställningar, migrera de virtuella datorerna och sedan återaktivera inställningarna i Resurshanterarens distributionsmodell.

| Resursprovider | Funktion | Rekommendation |
| --- | --- | --- |
| Compute | Oassocierade virtuella datordiskar. | VHD-blobbar bakom dessa diskar migreras när lagringskontot migreras |
| Compute | Virtuella datoravbildningar. | VHD-blobbar bakom dessa diskar migreras när lagringskontot migreras |
| Nätverk | Slutpunktens åtkomstkontrollistor. | Ta bort ACL:er för slutpunkt och försök igen. |
| Nätverk | Application Gateway | Ta bort programgatewayen innan du påbörjar migreringen och återskapa sedan programgatewayen när migreringen är klar. |
| Nätverk | Virtuella nätverk med VNet Peering. | Migrera virtuellt nätverk till Resource Manager och sedan peer. Läs mer om [VNet-peering](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Konfigurationer som inte stöds
Följande konfigurationer stöds för närvarande inte.

| Tjänst | Konfiguration | Rekommendation |
| --- | --- | --- |
| Resource Manager |Rollbaserad åtkomstkontroll (RBAC) för klassiska resurser |Eftersom URI för resurserna ändras efter migreringen, rekommenderar vi att du planerar RBAC-principuppdateringar som måste ske efter migreringen. |
| Compute |Flera undernät som är associerade med en virtuell dator |Uppdatera undernätskonfigurationen så att den bara refererar till ett undernät. Detta kan kräva att du tar bort ett sekundärt nätverkskort (som refererar till ett annat undernät) från den virtuella datorn och sätter tillbaka det när migreringen har slutförts. |
| Compute |Virtuella datorer som tillhör ett virtuellt nätverk men inte har ett explicit undernät tilldelat |Du kan också ta bort den virtuella datorn. |
| Compute |Virtuella datorer som har aviseringar, principer för automatisk skalning |Migreringen går igenom och dessa inställningar tas bort. Vi rekommenderar starkt att du utvärderar din miljö innan du gör migreringen. Du kan också konfigurera om aviseringsinställningarna när migreringen är klar. |
| Compute |XML-vm-tillägg (BGInfo 1.*, Visual Studio-felsökning, webbut distribuera och fjärrfelsökning) |Detta stöds inte. Vi rekommenderar att du tar bort dessa tillägg från den virtuella datorn för att fortsätta migreringen eller att de kommer att tas bort automatiskt under migreringsprocessen. |
| Compute |Starta diagnostik med Premium-lagring |Inaktivera funktionen Startdiagnostik för de virtuella datorerna innan du fortsätter med migreringen. Du kan återaktivera startdiagnostik i Resurshanteraren-stacken när migreringen har slutförts. Dessutom bör blobbar som används för skärmbild och seriella loggar tas bort så att du inte längre debiteras för dessa blobbar. |
| Compute | Molntjänster som innehåller webb-/arbetarroller | Detta stöds för närvarande inte. |
| Compute | Molntjänster som innehåller mer än en tillgänglighetsuppsättning eller flera tillgänglighetsuppsättningar. |Detta stöds för närvarande inte. Flytta de virtuella datorerna till samma tillgänglighetsuppsättning innan du migrerar. |
| Compute | VM med Azure Security Center-tillägg | Azure Security Center installerar automatiskt tillägg på dina virtuella datorer för att övervaka deras säkerhet och höja aviseringar. Dessa tillägg installeras vanligtvis automatiskt om Azure Security Center-principen är aktiverad på prenumerationen. Om du vill migrera de virtuella datorerna inaktiverar du säkerhetscentrumprincipen för prenumerationen, som tar bort övervakningstillägget för Security Center från de virtuella datorerna. |
| Compute | Virtuell dator med säkerhetskopierings- eller ögonblicksbildtillägg | Dessa tillägg installeras på en virtuell dator som konfigurerats med Azure Backup-tjänsten. Även om migreringen av dessa virtuella datorer inte stöds följer du vägledningen [här](/azure/virtual-machines/windows/migration-classic-resource-manager-faq#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) för att hålla säkerhetskopior som togs före migreringen.  |
| Compute | VM med Azure Site Recovery-tillägg | Dessa tillägg installeras på en virtuell dator som konfigurerats med Azure Site Recovery-tjänsten. Även om migreringen av lagring som används med Site Recovery kommer att fungera, kommer den aktuella replikeringen att påverkas. Du måste inaktivera och aktivera VM-replikering efter lagringsmigrering. |
| Nätverk |Virtuella nätverk som innehåller virtuella datorer och webb-/arbetsroller |Detta stöds för närvarande inte. Flytta webb-/arbetarrollerna till sitt eget virtuella nätverk innan du migrerar. När det klassiska virtuella nätverket har migrerats kan det migrerade virtuella Azure Resource Manager-nätverket peered med det klassiska virtuella nätverket för att uppnå liknande konfiguration som tidigare.|
| Nätverk | Klassiska Express Route-kretsar |Detta stöds för närvarande inte. Dessa kretsar måste migreras till Azure Resource Manager innan du påbörjar IaaS-migrering. Mer information finns i [Flytta ExpressRoute-kretsar från klassikern till Resurshanterarens distributionsmodell](../articles/expressroute/expressroute-move.md).|
| Azure Apptjänst |Virtuella nätverk som innehåller App Service-miljöer |Detta stöds för närvarande inte. |
| Azure HDInsight |Virtuella nätverk som innehåller HDInsight-tjänster |Detta stöds för närvarande inte. |
| Microsoft Dynamics livscykeltjänster |Virtuella nätverk som innehåller virtuella datorer som hanteras av Dynamics Lifecycle Services |Detta stöds för närvarande inte. |
| Azure AD Domain Services |Virtuella nätverk som innehåller Azure AD-domäntjänster |Detta stöds för närvarande inte. |
| Azure API Management |Virtuella nätverk som innehåller Azure API Management-distributioner |Detta stöds för närvarande inte. Om du vill migrera IaaS VNET ändrar du VNET för API Management-distributionen, vilket är en åtgärd utan driftstopp. |
