---
title: Metodtips för säkerhet för IaaS-arbetsbelastningar i Azure | Microsoft-dokument
description: " Migrering av arbetsbelastningar till Azure IaaS ger möjligheter att omvärdera våra konstruktioner "
services: security
documentationcenter: na
author: terrylanfear
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 49a40d78b4ba3bc1e90bb341cca90bece0b998a8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450045"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Säkerhetsmetodtips för IaaS-arbetsbelastningar i Azure
I den här artikeln beskrivs metodtips för säkerhet för virtuella datorer och operativsystem.

De bästa metoderna baseras på konsensus och de fungerar med aktuella Azure-plattformsfunktioner och funktionsuppsättningar. Eftersom åsikter och tekniker kan ändras med tiden uppdateras den här artikeln så att den återspeglar dessa ändringar.

I de flesta infrastruktur som en tjänst (IaaS) scenarier, [Azure virtuella datorer (VIRTUELLA datorer)](/azure/virtual-machines/) är den viktigaste arbetsbelastningen för organisationer som använder cloud computing. Detta faktum är tydligt i [hybridscenarier](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) där organisationer långsamt vill migrera arbetsbelastningar till molnet. I sådana fall följer du de [allmänna säkerhetsövervägandena för IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)och tillämpar bästa säkerhetspraxis på alla dina virtuella datorer.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Skydda virtuella datorer med hjälp av autentiserings- och åtkomstkontroll
Det första steget för att skydda dina virtuella datorer är att se till att endast behöriga användare kan konfigurera nya virtuella datorer och komma åt virtuella datorer.

> [!NOTE]
> För att förbättra säkerheten för virtuella Linux-datorer på Azure kan du integrera med Azure AD-autentisering. När du använder [Azure AD-autentisering för virtuella Linux-datorer](/azure/virtual-machines/linux/login-using-aad)styr och tillämpar du principer centralt som tillåter eller nekar åtkomst till de virtuella datorerna.
>
>

**Bästa praxis:** Kontrollera vm-åtkomst.   
**Detalj:** Använd [Azure-principer](/azure/azure-policy/azure-policy-introduction) för att upprätta konventioner för resurser i organisationen och skapa anpassade principer. Tillämpa dessa principer på resurser, till exempel [resursgrupper](/azure/azure-resource-manager/resource-group-overview). Virtuella datorer som tillhör en resursgrupp ärver dess principer.

Om din organisation har många prenumerationer kanske du behöver ett sätt att effektivt hantera åtkomst, principer och efterlevnad för dessa prenumerationer. [Azure-hanteringsgrupper](/azure/azure-resource-manager/management-groups-overview) ger en nivå av omfattning över prenumerationer. Du organiserar prenumerationer i hanteringsgrupper (behållare) och tillämpar dina styrningsvillkor på dessa grupper. Alla prenumerationer inom en hanteringsgrupp ärver automatiskt de villkor som tillämpas på gruppen. Hanteringsgrupper tillhandahåller hantering i företagsklass i stor skala oavsett vilken typ av prenumeration du har.

**Bästa praxis:** Minska variabiliteten i din installation och distribution av virtuella datorer.   
**Detalj:** Använd [Azure Resource Manager-mallar](/azure/azure-resource-manager/resource-group-authoring-templates) för att stärka dina distributionsalternativ och göra det enklare att förstå och inventera de virtuella datorerna i din miljö.

**Bästa praxis**: Säker privilegierad åtkomst.   
**Detalj:** Använd en [metod med lägsta behörighet](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) och inbyggda Azure-roller för att göra det möjligt för användare att komma åt och konfigurera virtuella datorer:

- [Deltagare för virtuell dator:](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)Kan hantera virtuella datorer, men inte det virtuella nätverks- eller lagringskonto som de är anslutna till.
- [Klassisk deltagare i virtuella](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor)datorer: Kan hantera virtuella datorer som skapats med hjälp av den klassiska distributionsmodellen, men inte det virtuella nätverks- eller lagringskontot som de virtuella datorerna är anslutna till.
- [Säkerhetsadministratör](../../role-based-access-control/built-in-roles.md#security-admin): Endast i Säkerhetscenter: Kan visa säkerhetsprinciper, visa säkerhetstillstånd, redigera säkerhetsprinciper, visa aviseringar och rekommendationer, avvisa aviseringar och rekommendationer.
- [DevTest Labs Användare:](../../role-based-access-control/built-in-roles.md#devtest-labs-user)Kan visa allt och ansluta, starta, starta om och stänga av virtuella datorer.

Dina prenumerationsadministratörer och coadmins kan ändra den här inställningen, vilket gör dem till administratörer för alla virtuella datorer i en prenumeration. Se till att du litar på alla dina prenumerationsadministratörer och coadmins att logga in på någon av dina datorer.

> [!NOTE]
> Vi rekommenderar att du konsoliderar virtuella datorer med samma livscykel i samma resursgrupp. Med hjälp av resursgrupper kan du distribuera, övervaka och summera faktureringskostnader för dina resurser.
>
>

Organisationer som styr vm-åtkomst och inställningar förbättrar deras övergripande vm-säkerhet.

## <a name="use-multiple-vms-for-better-availability"></a>Använda flera virtuella datorer för bättre tillgänglighet
Om den virtuella datorn kör kritiska program som behöver ha hög tillgänglighet rekommenderar vi starkt att du använder flera virtuella datorer. För bättre tillgänglighet, använd en [tillgänglighetsuppsättning](../../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) eller [tillgänglighetszoner](../../availability-zones/az-overview.md).

En tillgänglighetsuppsättning är en logisk gruppering som du kan använda i Azure för att säkerställa att de vm-resurser du placerar i den är isolerade från varandra när de distribueras i ett Azure-datacenter. Azure säkerställer att de virtuella datorerna du placerar i en tillgänglighetsuppsättning som körs över flera fysiska servrar, beräkningsställ, lagringsenheter och nätverksväxlar. Om ett maskinvaru- eller Azure-programvarufel inträffar påverkas bara en delmängd av dina virtuella datorer och ditt övergripande program fortsätter att vara tillgängligt för dina kunder. Tillgänglighetsuppsättningar är en viktig funktion när du vill skapa tillförlitliga molnlösningar.

## <a name="protect-against-malware"></a>Skydd mot skadlig kod
Du bör installera skydd mot skadlig kod för att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Du kan installera [Microsoft Antimalware](antimalware.md) eller en Microsoft-partners slutpunktsskyddslösning ([Trend Micro,](https://help.deepsecurity.trendmicro.com/Welcome.html) [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)och System [Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

Microsoft Antimalware innehåller funktioner som realtidsskydd, schemalagd skanning, reparation av skadlig kod, signaturuppdateringar, motoruppdateringar, exempelrapportering och händelseinsamling för uteslutning. För miljöer som är värd separat från din produktionsmiljö kan du använda ett antimalware-tillägg för att skydda dina virtuella datorer och molntjänster.

Du kan integrera Microsoft Antimalware och partnerlösningar med [Azure Security Center](../../security-center/index.yml) för enkel distribution och inbyggda identifieringar (aviseringar och incidenter).

**Bästa praxis:** Installera en lösning mot skadlig kod för att skydda mot skadlig kod.   
**Detalj:** [Installera en Microsoft-partnerlösning eller Microsoft Antimalware](../../security-center/security-center-install-endpoint-protection.md)

**Bästa praxis:** Integrera din lösning mot skadlig kod med Security Center för att övervaka status för ditt skydd.   
**Detalj:** [Hantera problem med slutpunktsskydd med Security Center](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Hantera vm-uppdateringar
Virtuella Azure-datorer, liksom alla lokala virtuella datorer, är avsedda att vara användarhanterade. Azure skickar inte Windows-uppdateringar till dem. Du måste hantera dina vm-uppdateringar.

**Bästa praxis:** Håll dina virtuella datorer aktuella.   
**Detalj:** Använd [lösning för uppdateringshantering](../../automation/automation-update-management.md) i Azure Automation för att hantera operativsystemuppdateringar för dina Windows- och Linux-datorer som distribueras i Azure, i lokala miljöer eller i andra molnleverantörer. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och hantera installationsprocessen för nödvändiga uppdateringar för servrar.

Datorer som hanteras med Uppdateringshantering använder följande konfigurationer för att utföra utvärdering och uppdateringsdistributioner:

- Microsoft Monitoring Agent (MMA) för Windows eller Linux
- Önskad PowerShell-tillståndskonfiguration (DSC) för Linux
- Automation Hybrid Runbook Worker
- Microsoft Update eller Windows Server Update Services (WSUS) för Windows-datorer

Om du använder Windows Update lämnar du den automatiska Windows Update-inställningen aktiverad.

**Bästa praxis:** Se till att avbildningar som du har skapat innehåller den senaste omgången av Windows-uppdateringar.   
**Detalj:** Sök efter och installera alla Windows-uppdateringar som ett första steg i varje distribution. Den här åtgärden är särskilt viktig att använda när du distribuerar bilder som kommer från antingen dig eller ditt eget bibliotek. Även om avbildningar från Azure Marketplace uppdateras automatiskt som standard kan det finnas en fördröjning (upp till några veckor) efter en offentlig version.

**Bästa praxis:** Distribuera regelbundet om dina virtuella datorer för att tvinga fram en ny version av operativsystemet.   
**Detalj:** Definiera din virtuella dator med en [Azure Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) så att du enkelt kan distribuera om den. Med hjälp av en mall får du en korrigerad och säker virtuell dator när du behöver den.

**Bästa praxis:** Snabbt tillämpa säkerhetsuppdateringar på virtuella datorer.   
**Detalj:** Aktivera Azure Security Center (kostnadsfri nivå eller standardnivå) för att [identifiera saknade säkerhetsuppdateringar och tillämpa dem](../../security-center/security-center-apply-system-updates.md).

**Bästa praxis:** Installera de senaste säkerhetsuppdateringarna.   
**Detalj:** Några av de första arbetsbelastningarna som kunder flyttar till Azure är labb och externa system. Om dina virtuella Azure-datorer är värdar för program eller tjänster som måste vara tillgängliga för internet bör du vara vaksam på korrigeringen. Korrigering utanför operativsystemet. Ohämmade sårbarheter för partnerprogram kan också leda till problem som kan undvikas om bra korrigeringshantering finns på plats.

**Bästa praxis:** Distribuera och testa en lösning för säkerhetskopiering.   
**Detalj**: En säkerhetskopia måste hanteras på samma sätt som du hanterar alla andra åtgärder. Detta gäller för system som är en del av din produktionsmiljö som sträcker sig till molnet.

Test- och utvecklingssystem måste följa säkerhetskopieringsstrategier som ger återställningsfunktioner som liknar vad användarna har vant sig vid, baserat på deras erfarenhet av lokala miljöer. Produktionsarbetsbelastningar som flyttas till Azure bör integreras med befintliga säkerhetskopieringslösningar när det är möjligt. Du kan också använda [Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) för att uppfylla dina säkerhetskopieringskrav.

Organisationer som inte tillämpar principer för programuppdatering är mer utsatta för hot som utnyttjar kända, tidigare fasta sårbarheter. För att följa branschbestämmelser måste företag bevisa att de är flitiga och använder korrekta säkerhetskontroller för att säkerställa säkerheten för deras arbetsbelastningar i molnet.

Metodtips för programvaruuppdatering för ett traditionellt datacenter och Azure IaaS har många likheter. Vi rekommenderar att du utvärderar dina aktuella principer för programuppdatering för att inkludera virtuella datorer som finns i Azure.

## <a name="manage-your-vm-security-posture"></a>Hantera säkerhetspositionen för den virtuella datorn
Cyberhot utvecklas. För att skydda dina virtuella datorer krävs en övervakningsfunktion som snabbt kan identifiera hot, förhindra obehörig åtkomst till dina resurser, utlösa aviseringar och minska falska positiva identifieringar.

Om du vill övervaka säkerhetspositionen för virtuella datorer med [Windows](../../security-center/security-center-virtual-machine.md) och [Linux](../../security-center/security-center-linux-virtual-machine.md)använder du Azure [Security Center](../../security-center/security-center-intro.md). Skydda dina virtuella datorer i Security Center genom att dra nytta av följande funktioner:

- Använd säkerhetsinställningar för operativsystem med rekommenderade konfigurationsregler.
- Identifiera och hämta systemsäkerhet och viktiga uppdateringar som kan saknas.
- Distribuera rekommendationer för skydd mot skadlig slutpunkt.
- Validera diskkryptering.
- Bedöma och åtgärda sårbarheter.
- Upptäck hot.

Security Center kan aktivt övervaka för hot och potentiella hot exponeras i säkerhetsaviseringar. Korrelerade hot sammanställs i en enda vy som kallas en säkerhetsincident.

Security Center lagrar data i [Azure Monitor-loggar](/azure/log-analytics/log-analytics-overview). Azure Monitor-loggarna tillhandahåller ett frågespråk och en analysmotor som ger dig insikter om hur dina program och resurser fungerar. Data samlas också in från [Azure Monitor](../../batch/monitoring-overview.md), hanteringslösningar och agenter installerade på virtuella datorer i molnet eller lokalt. Denna delade funktion hjälper dig att få en komplett bild av din miljö.

Organisationer som inte tillämpar stark säkerhet för sina virtuella datorer är fortfarande omedvetna om obehöriga användares potentiella försök att kringgå säkerhetskontroller.

## <a name="monitor-vm-performance"></a>Övervaka vm-prestanda
Resursmissbruk kan vara ett problem när VM-processer förbrukar mer resurser än de borde. Prestandaproblem med en virtuell dator kan leda till avbrott i tjänsten, vilket bryter mot säkerhetsprincipen för tillgänglighet. Detta är särskilt viktigt för virtuella datorer som är värdar för IIS eller andra webbservrar, eftersom hög CPU- eller minnesanvändning kan tyda på en DoS-attack (Denial of Service). Det är absolut nödvändigt att övervaka åtkomst till virtuella datorer inte bara reaktivt medan ett problem uppstår, men också proaktivt mot baslinjeprestanda mätt under normal drift.

Vi rekommenderar att du använder [Azure Monitor](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) för att få insyn i resursens hälsa. Azure Monitor-funktioner:

- [Resursdiagnostikloggfiler:](../../azure-monitor/platform/platform-logs-overview.md)Övervakar dina vm-resurser och identifierar potentiella problem som kan äventyra prestanda och tillgänglighet.
- [Azure Diagnostics-tillägg](/azure/azure-monitor/platform/diagnostics-extension-overview): Tillhandahåller funktioner för övervakning och diagnostik på virtuella datorer i Windows. Du kan aktivera dessa funktioner genom att inkludera tillägget som en del av [Azure Resource Manager-mallen](/azure/virtual-machines/windows/extensions-diagnostics-template).

Organisationer som inte övervakar vm-prestanda kan inte avgöra om vissa ändringar i prestandamönster är normala eller onormala. En virtuell dator som förbrukar mer resurser än normalt kan tyda på en attack från en extern resurs eller en komprometterade process som körs i den virtuella datorn.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Kryptera dina virtuella hårddiskfiler
Vi rekommenderar att du krypterar dina virtuella hårddiskar (VHD) för att skydda startvolymen och datavolymerna i vila i lagring, tillsammans med dina krypteringsnycklar och hemligheter.

[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) hjälper dig att kryptera dina virtuella datorer med Windows och Linux IaaS. Azure Disk Encryption använder [bitlockerfunktionen i](https://technet.microsoft.com/library/cc732774.aspx) branschstandard i Windows och [DM-Crypt-funktionen](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla volymkryptering för operativsystemet och datadiskarna. Lösningen är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att hjälpa dig att styra och hantera diskkrypteringsnycklar och hemligheter i din nyckelvalvsprenumeration. Lösningen säkerställer också att alla data på diskarna på den virtuella datorn krypteras i vila i Azure Storage.

Här följer metodtips för att använda Azure Disk Encryption:

**Bästa praxis:** Aktivera kryptering på virtuella datorer.   
**Detalj:** Azure Disk Encryption genererar och skriver krypteringsnycklarna till ditt nyckelvalv. För att hantera krypteringsnycklar i nyckelvalvet krävs Azure AD-autentisering. Skapa ett Azure AD-program för detta ändamål. I autentiseringssyfte kan du använda antingen klienthemlig autentisering eller [klientcertifikatbaserad Azure AD-autentisering](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

**Bästa praxis:** Använd en nyckelkrypteringsnyckel (KEK) för ett extra säkerhetslager för krypteringsnycklar. Lägg till en KEK i ditt nyckelvalv.   
**Detalj:** Använd cmdleten [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) för att skapa en nyckelkrypteringsnyckel i nyckelvalvet. Du kan också importera en KEK från din lokala maskinvarusäkerhetsmodul (HSM) för nyckelhantering. Mer information finns i [dokumentationen för Key Vault](../../key-vault/keys/hsm-protected-keys.md). När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption den nyckeln för att radbrytas krypteringshemligheterna innan du skriver till Key Vault. Om du behåller en depositionskopia av den här nyckeln i en lokal nyckelhantering hsm ger ytterligare skydd mot oavsiktlig borttagning av nycklar.

**Bästa praxis:** Ta en [ögonblicksbild](../../virtual-machines/windows/snapshot-copy-managed-disk.md) och/eller säkerhetskopia innan diskarna krypteras. Säkerhetskopior ger ett återställningsalternativ om ett oväntat fel inträffar under krypteringen.   
**Detalj:** Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. När en säkerhetskopia har gjorts kan du använda cmdleten **Set-AzVMDiskEncryptionExtension** för att kryptera hanterade diskar genom att ange parametern *-skipVmBackup.* Mer information om hur du säkerhetskopierar och återställer krypterade virtuella datorer finns i artikeln [Kring Azure Backup.](../../backup/backup-azure-vms-encryption.md)

**Bästa praxis:** För att se till att krypteringshemligheterna inte korsar regionala gränser behöver Azure Disk Encryption nyckelvalvet och de virtuella datorerna som ska finnas i samma region.   
**Detalj:** Skapa och använd ett nyckelvalv som finns i samma region som den virtuella datorn som ska krypteras.

När du använder Azure Disk Encryption kan du uppfylla följande affärsbehov:

- Virtuella IaaS-datorer skyddas i vila genom krypteringsteknik av branschstandard för att uppfylla organisatoriska säkerhets- och efterlevnadskrav.
- Virtuella IaaS-datorer börjar under kundstyrda nycklar och principer och du kan granska deras användning i nyckelvalvet.

## <a name="restrict-direct-internet-connectivity"></a>Begränsa direkt internetanslutning
Övervaka och begränsa den virtuella datorns direkta internetanslutning. Angripare söker ständigt igenom offentliga moln-IP-intervall för öppna hanteringsportar och försöker "enkla" attacker som vanliga lösenord och kända opatrullerade sårbarheter. I följande tabell visas metodtips för att skydda mot dessa attacker:

**Bästa praxis:** Förhindra oavsiktlig exponering för nätverksroutning och säkerhet.   
**Detalj**: Använd RBAC för att se till att endast den centrala nätverksgruppen har behörighet till nätverksresurser.

**Bästa praxis:** Identifiera och åtgärda exponerade virtuella datorer som tillåter åtkomst från "alla" käll-IP-adress.   
**Detalj**: Använd Azure Security Center. Security Center rekommenderar att du begränsar åtkomsten via internetvända slutpunkter om någon av dina nätverkssäkerhetsgrupper har en eller flera inkommande regler som tillåter åtkomst från "valfri" käll-IP-adress. Security Center rekommenderar att du redigerar dessa inkommande regler för att [begränsa åtkomsten](../../security-center/security-center-network-recommendations.md) till käll-IP-adresser som faktiskt behöver åtkomst.

**Bästa praxis**: Begränsa hanteringsportar (RDP, SSH).   
**Detalj:** [NYVD-åtkomst (JIT)](../../security-center/security-center-just-in-time.md) kan användas för att låsa inkommande trafik till dina virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som du enkelt kan ansluta till virtuella datorer när det behövs. När JIT är aktiverat låser Security Center inkommande trafik till dina virtuella Azure-datorer genom att skapa en regel för nätverkssäkerhetsgrupp. Du väljer de portar på den virtuella datorn som inkommande trafik ska vara låst. Dessa portar styrs av JIT-lösningen.

## <a name="next-steps"></a>Nästa steg
Se metodtips och mönster för [Azure-säkerhet](best-practices-and-patterns.md) för fler metodtips för säkerhet som ska användas när du utformar, distribuerar och hanterar dina molnlösningar med hjälp av Azure.

Följande resurser är tillgängliga för att ge mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Blogg för Azure Security Team](https://blogs.msdn.microsoft.com/azuresecurity/) – för aktuell information om det senaste inom Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där Microsofts säkerhetsproblem, inklusive problem med Azure, kan rapporteras eller via e-post tillsecure@microsoft.com
