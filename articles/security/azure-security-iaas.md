---
title: Säkerhetsmetodtips för IaaS-arbetsbelastningar i Azure | Microsoft Docs
description: " Migrering av arbetsbelastningar till Azure IaaS ger dig möjligheter att omvärdera våra design "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2018
ms.author: barclayn
ms.openlocfilehash: 42958576a127fee5e0a275e53203edd4e4dee6f9
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540296"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Rekommenderade säkerhetsmetoder för IaaS-arbetsbelastningar i Azure

I de flesta infrastruktur som en tjänst (IaaS)-scenarier, [virtuella Azure-datorer (VM)](https://docs.microsoft.com/azure/virtual-machines/) är huvudsakliga arbetsbelastningen för organisationer som använder molnbaserad databehandling. Detta kan ses i [hybridscenarier](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) där organisationer vill långsamt migrera arbetsbelastningar till molnet. I sådana scenarier, följer du de [allmänna säkerhetsaspekter för IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx), och tillämpa rekommenderade säkerhetsmetoder för alla dina virtuella datorer.

Ditt ansvar för säkerhet baseras på vilken typ av tjänst i molnet. Följande diagram sammanfattar balans ansvar för både Microsoft och du:

![Ansvarsområden](./media/azure-security-iaas/sec-cloudstack-new.png)

Säkerhetskrav varierar beroende på ett antal faktorer, inklusive olika typer av arbetsbelastningar. Inte en av dessa metodtips skydda ensamt dina system. Som allt annat i security måste du välja lämpliga alternativ och se hur lösningarna kan kompletterar varandra genom att fylla luckor.

Den här artikeln beskriver rekommenderade säkerhetsmetoder för virtuella datorer och operativsystem.

De bästa metoderna är baserade på en enhälligt av åsikter och de fungerar med den aktuella Azure-plattformsfunktioner och egenskapsuppsättningar. Eftersom andras åsikter och tekniker kan ändras med tiden, kommer den här artikeln att uppdateras för att återspegla dessa ändringar.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Skydda virtuella datorer med hjälp av autentisering och åtkomstkontroll
Det första steget i att skydda dina virtuella datorer är att säkerställa att endast behöriga användare kan ställa in nya virtuella datorer och åtkomst till virtuella datorer.

**Bästa praxis**: Styra åtkomst till virtuell dator.   
**Information om**: Använd [Azure principer](../azure-policy/azure-policy-introduction.md) upprätta konventioner för resurser i din organisation och skapa anpassade principer. Använda dessa principer på resurser, till exempel [resursgrupper](../azure-resource-manager/resource-group-overview.md). Virtuella datorer som tillhör en resursgrupp ärver dess principer.

Om din organisation har många prenumerationer, kanske du behöver ett sätt att effektivt hantera åtkomst, principer och efterlevnad för dessa prenumerationer. [Azure-hanteringsgrupper](../azure-resource-manager/management-groups-overview.md) ger en nivå av omfång över prenumerationer. Du organiserar prenumerationer till hanteringsgrupper (behållare) och tillämpa dina styrning villkor för dessa grupper. Alla prenumerationer i en hanteringsgrupp ärver automatiskt villkor som används för gruppen. Hanteringsgrupper tillhandahåller hantering i företagsklass i stor skala oavsett vilken typ av prenumeration du har.

**Bästa praxis**: Minska variationer i din installation och distribution av virtuella datorer.   
**Information om**: Använd [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) mallar att stärka dina distributionsalternativ och gör det enklare att förstå och inventera de virtuella datorerna i din miljö.

**Bästa praxis**: Säker privilegierad åtkomst.   
**Information om**: Använd en [minsta privilegium metoden](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) och inbyggda Azure-roller så att användarna kan komma åt och konfiguration av virtuella datorer:

- [Virtuell Datordeltagare](../role-based-access-control/built-in-roles.md#virtual-machine-contributor): Hantera virtuella datorer, men inte virtuella nätverks- eller kontot som de är anslutna.
- [Klassisk virtuell Datordeltagare](../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): Hantera virtuella datorer som skapas med hjälp av den klassiska distributionsmodellen, men inte virtuella nätverks- eller kontot som de virtuella datorerna är anslutna.
- [Säkerhetsadministratör](../role-based-access-control/built-in-roles.md#security-admin): I Säkerhetscenter: Kan visa säkerhetsprinciper, security tillstånd, redigera säkerhetsprinciper, Visa aviseringar och rekommendationer, avvisa aviseringar och rekommendationer.
- [DevTest Labs-användare](../role-based-access-control/built-in-roles.md#devtest-labs-user): Kan visa allt och ansluta, starta, starta om och stänga av virtuella datorer.

Dina prenumerationsadministratörer och coadmins ska kan ändra den här inställningen, vilket gör dem administratörer av alla virtuella datorer i en prenumeration. Var noga med att du litar på alla prenumerationsadministratörer och coadmins ska logga in på någon av dina datorer.

> [!NOTE]
> Vi rekommenderar att du sammanställa virtuella datorer med samma livscykel i samma resursgrupp. Med hjälp av resursgrupper kan du distribuera, övervaka och ackumulera faktureringskostnader för dina resurser.
>
>

Organisationer som styr åtkomst till virtuell dator och installationen att förbättra sina övergripande VM-säkerhet.

## <a name="use-multiple-vms-for-better-availability"></a>Använda flera virtuella datorer för bättre tillgänglighet
Om den virtuella datorn kör kritiska program som måste ha hög tillgänglighet, rekommenderar vi starkt att du använder flera virtuella datorer. För bättre tillgänglighet använda en [tillgänglighetsuppsättning](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

En tillgänglighetsuppsättning är en logisk gruppering som du kan använda i Azure så att du placerar i VM-resurserna är isolerade från varandra när de har distribuerats i ett Azure-datacenter. Azure ser till att de virtuella datorerna som du placerar i en tillgänglighetsuppsättning in kör över flera fysiska servrar, beräkning rack, lagringsenheter och nätverksväxlar. Om ett maskinvarufel eller Azure programvarufel inträffar endast en delmängd av dina virtuella datorer som påverkas och ditt program fortsätter att vara tillgängliga för dina kunder. Tillgänglighetsuppsättningar är en viktig funktion när du vill skapa tillförlitliga molnlösningar.

## <a name="protect-against-malware"></a>Skydda mot skadlig kod
Du bör installera skydd mot skadlig kod för att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Du kan installera [Microsoft Antimalware](azure-security-antimalware.md) eller en Microsoft-partner endpoint protection-lösning ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/search/result.aspx?q=Windows+defender+endpoint+protection), och [System Center Endpoint Protection](https://www.microsoft.com/search/result.aspx?q=System+Center+endpoint+protection)).

Microsoft Antimalware innehåller funktioner som realtidsskydd, schemalagd genomsökning, korrigering av skadlig kod, Signaturuppdateringar, uppdateringar, exempel reporting och insamling av undantag. Du kan använda ett tillägg för program mot skadlig kod för att skydda dina virtuella datorer och molntjänster för miljöer som finns separat från din produktionsmiljö.

Du kan integrera lösningar från Microsoft Antimalware och partner med [Azure Security Center](https://docs.microsoft.com/azure/security-center/) för enkel distribution och inbyggda identifieringar (aviseringar och incidenter).

**Bästa praxis**: Installera ett program mot skadlig kod som skydd mot skadlig kod.   
**Information om**: [Installera en lösning för Microsoft-partner eller Microsoft Antimalware](../security-center/security-center-install-endpoint-protection.md)

**Bästa praxis**: Integrera din lösning för program mot skadlig kod med Security Center för att övervaka status för skyddet av.   
**Information om**: [Hantera problem med endpoint protection med Security Center](../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Hantera VM-uppdateringar
Azure virtuella datorer, som alla lokala virtuella datorer är avsedda att hanteras av användare. Azure Skicka inte Windows-uppdateringar till dem. Du behöver hantera dina VM-uppdateringar.

**Bästa praxis**: Håll dina virtuella datorer uppdaterade.   
**Information om**: Använd den [uppdateringshantering](../automation/automation-update-management.md) lösning i Azure Automation för att hantera operativsystem systemuppdateringar för dina Windows- och Linux-datorer som distribueras i Azure, i lokala miljöer eller i andra moln providers. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och hantera installationsprocessen för nödvändiga uppdateringar för servrar.

Datorer som hanteras av uppdateringshantering Använd följande konfigurationer för att utföra utvärdering och uppdatera distribueringar:

- Microsoft Monitoring Agent (MMA) för Windows eller Linux
- Önskad PowerShell-tillståndskonfiguration (DSC) för Linux
- Automation Hybrid Runbook Worker
- Microsoft Update eller Windows Server Update Services (WSUS) för Windows-datorer

Om du använder Windows Update, lämna inställningen för automatisk uppdatering för Windows.

**Bästa praxis**: Kontrollera att du har inbyggda avbildningar inkluderar den senaste runda av Windows-uppdateringar vid distributionen.   
**Information om**: Sök efter och installera alla Windows-uppdateringar som ett första steg för varje distribution. Det här måttet är särskilt viktigt att tillämpa när du distribuerar avbildningar som kommer från dig eller dina egna bibliotek. Även om avbildningar från Azure Marketplace uppdateras automatiskt som standard, kan det finnas en fördröjning (upp till några veckor) när du har en offentlig version.

**Bästa praxis**: Distribuera med jämna mellanrum om dina virtuella datorer om du vill framtvinga en ny version av Operativsystemet.   
**Information om**: Definiera den virtuella datorn med en [Azure Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md) så att du enkelt kan distribuera om den. En mall ger dig en korrigerad och säker virtuell dator när du behöver den.

**Bästa praxis**: Installera de senaste säkerhetsuppdateringarna.   
**Information om**: Vissa av de första arbetsbelastningarna som kunder flyttar till Azure är labs och externa system. Om virtuella datorer i Azure vara värd för program eller tjänster som måste vara tillgänglig på Internet, vara vaksam om uppdateringar. Korrigera sig utöver operativsystemet. Okorrigerade säkerhetsproblem på partnerprogram kan också leda till problem som kan undvikas om bra uppdateringshantering är på plats.

**Bästa praxis**: Distribuera och testa en lösning för säkerhetskopiering.   
**Information om**: En säkerhetskopia måste hanteras på samma sätt som du hanterar andra åtgärder. Det här gäller för system som ingår i din produktionsmiljö utöka till molnet.

Testning och utveckling system måste följa säkerhetskopieringsstrategier som gör det möjligt för återställning som liknar vad användare har blivit vana vid, baserat på deras upplevelse med lokala miljöer. Produktionsarbetsbelastningar flyttade till Azure ska integrera med befintliga säkerhetskopieringslösningar när det är möjligt. Du kan också använda [Azure Backup](../backup/backup-azure-vms-first-look-arm.md) att hjälpa med dina behov för säkerhetskopiering.

Organisationer som inte tillämpa principer för programuppdatering exponeras mer för hot som utnyttjar kända, fasta tidigare sårbarheter. För att uppfylla branschbestämmelser måste företag bevisa att de är flitig och använda rätt säkerhetskontroller för att säkerställa säkerheten för sina arbetsbelastningar finns i molnet.

Programuppdateringen Metodtips för ett traditionella datacenter och Azure IaaS har många likheter. Vi rekommenderar att du utvärderar din aktuella uppdateringsprinciper för programvara för att inkludera virtuella dator i Azure.

## <a name="manage-your-vm-security-posture"></a>Hantera din säkerhetsposition för virtuell dator
Cyberhot är under utveckling. Skydda dina virtuella datorer kräver en övervakningsfunktionen som kan snabbt identifiera hot, förhindra obehörig åtkomst till dina resurser, utlöser aviseringar och minska falska positiva identifieringar.

Övervaka säkerhetstillståndet för dina [Windows](../security-center/security-center-virtual-machine.md) och [virtuella Linux-datorer](../security-center/security-center-linux-virtual-machine.md), använda [Azure Security Center](../security-center/security-center-intro.md). Skydda dina virtuella datorer genom att utnyttja följande funktioner i Security Center:

- Tillämpa säkerhetsinställningar för OS med rekommenderade konfigurationsregler.
- Identifiera och ladda ned systemsäkerhet och viktiga uppdateringar som saknas.
- Distribuera rekommendationer för slutpunktsskydd för program mot skadlig kod.
- Verifiera diskkryptering.
- Utvärdera och åtgärda sårbarheter.
- Identifiera hot.

Security Center kan övervaka aktivt för hot och potentiella hot som exponeras i säkerhetsaviseringar. Korrelerade hot räknas samman i en enda vy som kallas en säkerhetsincident.

Security Center lagrar data i [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Log Analytics tillhandahåller en fråga frågespråk och en analytisk motor som ger dig insikter om hur dina program och resurser. Data samlas även från [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md), hanteringslösningar och agenter som installerats på virtuella datorer i molnet eller lokalt. Denna delade funktion hjälper dig att få en komplett bild av din miljö.

Organisationer som inte framtvingar stark säkerhet för sina virtuella datorer vara ovetande om eventuella försök från obehöriga användare kringgå de säkerhetskontroller.

## <a name="monitor-vm-performance"></a>Övervaka prestanda för virtuell dator
Resursen missbruk kan vara ett problem när VM-processer använder fler resurser än vad de ska. Prestandaproblem med en virtuell dator kan leda till avbrott i tjänsten, som bryter mot reglerna security tillgänglighet. Detta är särskilt viktigt för virtuella datorer som är värd för IIS eller andra webbservrar, eftersom hög CPU eller minne användning kan tyda på en attack denial of service (DoS). Det är viktigt att övervaka åtkomst till virtuell dator inte bara reaktivt när ett problem uppstår, utan även proaktivt mot baslinjeprestanda mätt under normal drift.

Vi rekommenderar att du använder [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) att få insyn i resursens hälsotillstånd. Azure Monitor-funktioner:

- [Diagnostiklogg resursfiler](../azure-monitor/platform/diagnostic-logs-overview.md): Övervakar dina VM-resurser och identifierar potentiella problem som kan påverka datorns prestanda och tillgänglighet.
- [Azure Diagnostics-tillägg](../azure-monitor/platform/diagnostics-extension-overview.md): Tillhandahåller funktioner för övervakning och diagnostik på virtuella Windows-datorer. Du kan aktivera dessa funktioner genom att inkludera tillägget som en del av den [Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md).

Organisationer som inte övervakar prestanda för virtuella datorer kan inte avgöra om vissa ändringar i prestandamönster är normal eller onormalt. En attack från en extern resurs eller en komprometterad process som körs på den virtuella datorn kan tyda på en virtuell dator som använder fler resurser än normalt.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Kryptera din virtuella hårddisk-filer
Vi rekommenderar att du krypterar dina virtuella hårddiskar (VHD) för att skydda din startvolymen och datavolymer i vila i lagring, tillsammans med dina krypteringsnycklar och hemligheter.

[Azure Disk Encryption](azure-security-disk-encryption-overview.md) hjälper dig att kryptera din Windows- och Linux IaaS VM-diskar. Azure Disk Encryption används vanliga [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funktion i Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux för att kryptera volymer för Operativsystemet och datadiskarna. Lösningen är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att styra och hantera diskkrypteringsnycklar och hemligheter i key vault-prenumeration. Lösningen innebär också att alla data på diskar i virtuella datorer krypteras i vila i Azure Storage.

Nedan följer bästa praxis för att använda Azure Disk Encryption:

**Bästa praxis**: Aktivera kryptering på virtuella datorer.   
**Information om**: Azure Disk Encryption genererar och skriver dem till ditt nyckelvalv. Hantera krypteringsnycklar i ditt nyckelvalv kräver Azure AD-autentisering. Skapa ett Azure AD-program för detta ändamål. Du kan använda antingen autentisering med klient-hemlighet för autentisering, eller [klientautentisering certifikatbaserad Azure AD](../active-directory/active-directory-certificate-based-authentication-get-started.md).

**Bästa praxis**: Använda en krypteringsnyckel nyckel (KEK) för ett extra lager av säkerhet för krypteringsnycklar. Lägg till en KEK till ditt nyckelvalv.   
**Information om**: Använd den [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) cmdlet för att skapa en nyckelkrypteringsnyckel i nyckelvalvet. Du kan också importera en KEK från din lokala maskinvarusäkerhetsmodul (HSM) för hantering av nycklar. Mer information finns i den [dokumentationen för Key Vault](../key-vault/key-vault-hsm-protected-keys.md). När du anger en nyckelkrypteringsnyckel använder Azure Disk Encryption nyckeln för att omsluta kryptering hemligheter innan du skriver till Key Vault. Att behålla en escrow kopia av den här nyckeln i en lokal ger nyckelhantering HSM ytterligare skydd mot oavsiktlig borttagning av nycklar.

**Bästa praxis**: Ta en [ögonblicksbild](../virtual-machines/windows/snapshot-copy-managed-disk.md) och/eller säkerhetskopiera innan diskar krypteras. Säkerhetskopieringar ger ett återställningsalternativ om ett oväntat fel inträffar under krypteringen.   
**Information om**: Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. När en säkerhetskopia görs, kan du använda den **Set-AzureRmVMDiskEncryptionExtension** cmdlet för att kryptera hanterade diskar genom att ange den *- skipVmBackup* parametern. Mer information om hur du säkerhetskopiera och återställa krypterade virtuella datorer finns i den [Azure Backup](../backup/backup-azure-vms-encryption.md) artikeln.

**Bästa praxis**: Om du vill se till att kryptering hemligheterna inte går över regionala gränser, måste Azure Disk Encryption nyckelvalvet och de virtuella datorerna ska finnas i samma region.   
**Information om**: Skapa och använda ett nyckelvalv som är i samma region som den virtuella datorn måste vara krypterade.

När du använder Azure Disk Encryption kan du uppfylla följande affärsbehov:

- Virtuella IaaS-datorer är skyddade i vila med branschstandard krypteringsteknik att uppfylla organisationens krav för säkerhet och efterlevnad.
- Virtuella IaaS-datorer startar under kund-kontrollerade nycklar och principer och du kan granska deras användning i ditt nyckelvalv.

## <a name="next-steps"></a>Nästa steg
Se [säkerhet i Azure-metodtips och mönster](security-best-practices-and-patterns.md) för flera beprövade metoder för att använda när du utforma, distribuera och hantera dina molnlösningar med hjälp av Azure.

Följande resurser är tillgängliga för att tillhandahålla mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Azure-Säkerhetsteamets blogg](https://blogs.msdn.microsoft.com/azuresecurity/) – uppdaterad information på senast inom Azure-säkerhet
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där kan du rapportera säkerhetsproblem i Microsoft, inklusive problem med Azure, eller mejla till secure@microsoft.com
