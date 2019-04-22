---
title: Vad är Azure Backup?
description: Ger en översikt över tjänsten Azure Backup och hur du distribuerar den som en del av din strategi för affärskontinuitet och haveriberedskap (BCDR).
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 04/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5408f920a16860972dca6450d5e51152048bbf82
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361799"
---
# <a name="what-is-azure-backup"></a>Vad är Azure Backup?

Med Azure Backup-tjänsten kan du säkerhetskopiera data till Microsoft Azure-molnet. Du kan säkerhetskopiera lokala datorer och arbetsbelastningar, samt virtuella Azure-datorer.


## <a name="why-use-azure-backup"></a>Varför ska jag använda Azure Backup?

Azure Backup ger följande viktiga fördelar:

- **Avlasta lokal säkerhetskopiering**: Azure Backup är en enkel lösning för att säkerhetskopiera dina lokala resurser till molnet. Du får både kort- och långsiktig kvarhållning av säkerhetskopior utan att behöva distribuera komplexa lokala lösningar.
- **Säkerhetskopiera virtuella Azure IaaS-datorer**: Med Azure Backup får du oberoende och isolerade säkerhetskopior, vilket skyddar originaldata från att förstöras oavsiktligt. Säkerhetskopior lagras i ett Recovery Services-valv med inbyggd hantering av återställningspunkter. Konfiguration och skalbarhet är enkla, säkerhetskopior är optimerade och kan du enkelt återställa efter behov.
- **Enkel skalning** – Azure Backup använder Azure-molnets underliggande kraft och obegränsade storlek för att tillhandahålla hög tillgänglighet – utan underhåll och omkostnad för övervakning.
- **Få obegränsad dataöverföring**: Azure Backup begränsar inte hur mycket inkommande eller utgående data du överför eller att ta betalt för de data som överförs.
    - Utgående data syftar på data som överförs från ett Recovery Services-valv under en återställningsåtgärd.
    - Om du utför en initial säkerhetskopiering offline med Azure Import/Export-tjänsten för att importera stora mängder data, finns det dock en kostnad som är kopplad till inkommande data.  [Läs mer](backup-azure-backup-import-export.md).
- **Skydda data**:
    - Lokalt, information som överförs krypteras på den lokala datorn med hjälp av AES256. De data som överförs skyddas av HTTPS mellan lagring och säkerhetskopiering. ISCSI-protokollet skyddar de data som överförs mellan säkerhetskopiering och användare-dator. Säkra tunnlar används för att skydda iSCSI-kanalen.
    - Data i Azure är krypterade i vila med lösenfras som du anger när du konfigurerar säkerhetskopiering för lokalt till Azure backup. Lösenfras eller nyckel det aldrig överförs eller lagras i Azure. Om det är nödvändigt att återställa data kan du göra det om du har krypteringslösenfrasen eller nyckeln.
    - För virtuella Azure-datorer krypteras data vid återställning av med Storage Service Encryption (SSE). Backup krypterar automatiskt data innan de lagras. Azure-lagring dekrypterar data innan de hämtas.
    - Säkerhetskopiering har även stöd för Azure virtuella datorer som krypterats med Azure Disk Encryption (ADE). [Läs mer](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups).
- **Programkonsekvent säkerhetskopiering**: Programkonsekvent säkerhetskopiering innebär att en återställningspunkt har alla data som krävs för att återställa säkerhetskopian. Azure Backup innehåller programkonsekventa säkerhetskopior vilket garanterar att inga ytterligare korrigeringar behövs för att återställa data. Återställning av konsekventa programdata minskar tiden för återställning, så att du snabbt kan återgå till körläge.
- **Behålla kort- och långsiktiga data**: Du kan använda Recovery Services-valv för kortsiktig och långsiktig datakvarhållning. Azure begränsar inte hur lång tid data behålls i ett Recovery Services-valv. Du kan förvara den så länge du vill. Azure Backup har en gräns på 9999 återställningspunkter per skyddad instans. [Läs mer](backup-introduction-to-azure-backup.md#backup-and-retention)om hur den här begränsningen påverkar dina säkerhetskopieringsbehov.
- **Automatisk lagringshantering** – hybridmiljöer kräver ofta heterogen lagring – vissa lokalt och vissa i molnet. Med Azure Backup är det kostnadsfritt att använda lokala lagringsenheter. Azure Backup allokerar och hanterar lagringen av säkerhetskopiorna automatiskt och tillämpar en modell där du betalar baserat på din användning. Du betalar alltså bara för den lagring som du använder. [Läs mer](https://azure.microsoft.com/pricing/details/backup) om prissättning.
- **Flera lagringsalternativ** – Azure Backup erbjuder två typer av replikering för att din lagring och dina data ska ha hög tillgänglighet.
    - [Lokalt redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) replikerar dina data tre gånger (det skapas tre kopior av dina data) i en lagringsskalningsenhet i ett datacenter. Alla datakopior finns i samma region. LRS är ett billigt alternativ för att skydda dina data mot fel i den lokala maskinvaran.
    - [Geo-redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md) är standardalternativet och det som rekommenderas vid replikering. GRS replikerar dina data till en sekundär region (hundratals mil bort från den primära platsen för datakällan). GRS kostar mer än LRS, men GRS ger högre hållbarhet för dina data, även i händelse av ett regionalt avbrott.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Vad är skillnaden mellan Azure Backup och Azure Site Recovery?

Både Azure Backup och Azure Site Recovery kan ingå i din strategi för affärskontinuitet och haveriberedskap (BCDR) i verksamheten. En sådan strategi har två breda mål:

- Se till att dina affärsdata skyddas och kan återställas vid avbrott.
- Se till att dina program och arbetsbelastningar fortsätter köras under såväl planerade som oplanerade stillestånd.

De här två tjänsterna innehåller kompletterande men också olika funktioner.

- **Azure Site Recovery**: Site Recovery tillhandahåller en lösning för haveriberedskap för lokala datorer och virtuella Azure-datorer. Du kan replikera datorer från en primär till en sekundär plats. I händelse av en katastrof redundansväxlar du datorerna till den sekundära platsen och får åtkomst till dem därifrån. När allt fungerar som vanligt igen redundansväxlar du tillbaka datorerna till den primära platsen för återställning.
- **Azure Backup**: Tjänsten Azure Backup säkerhetskopierar data från lokala datorer och virtuella Azure-datorer. Data kan säkerhetskopieras och återställas på detaljerad nivå, till exempel genom säkerhetskopiering av filer, mappar, datorsystemtillstånd och appmedvetna data. Azure Backup hanterar data på en mer detaljerad nivå än Site Recovery. Om till exempel en presentation på en bärbar dator skadas, använder du Azure Backup för att återställa presentationen. Om du vill skydda och ha tillgång till en virtuell dators konfiguration och data använder du Site Recovery.  

Bestäm dina behov för affärskontinuitet och haveriberedskap med hjälp av tabellen nedan.

**Mål** | **Detaljer** | **Jämförelse**
--- | --- | ---
**Säkerhetskopiera/bevara data** | Säkerhetskopierade data kan bevaras och lagras i flera dagar, månader eller år, om det behövs i efterlevnadssyfte. | Med säkerhetskopieringslösningar som Azure Backup kan du välja vilka data som ska säkerhetskopieras och skräddarsy principer för säkerhetskopiering och kvarhållning.<br/><br/> Site Recovery kan inte samma finjustera.
**Mål för återställningspunkt (RPO)** | Mängden godtagbar dataförlust om en återställning krävs. | Säkerhetskopieringar har större variation vad gäller mål för återställningspunkter.<br/><br/> Säkerhetskopieringar av virtuella datorer har vanligtvis ett återställningspunktmål på en dag, medan säkerhetskopieringar av databaser har återställningspunktmål på så lite som 15 minuter.<br/><br/> Site Recovery tillhandahåller låga återställningspunktmål eftersom replikeringen är kontinuerlig eller frekvent, vilket innebär att deltat mellan källa och replikering är litet.
**Mål för återställningstid (RTO)** |Hur lång tid det tar att slutföra en återställning. | På grund av det större återställningspunktmålet är mängden data som en säkerhetskopieringslösning behöver bearbeta normalt mycket högre, vilket leder till längre mål för återställningstid. Det kan till exempel ta dagar att återställa data från band, beroende på hur lång tid det tar att överföra bandet från den externa platsen.

## <a name="what-backup-scenarios-are-supported"></a>Vilka säkerhetskopieringsscenarier stöds?

Azure Backup kan säkerhetskopiera både lokala datorer och virtuella Azure-datorer.

**Dator** | **Säkerhetskopieringsscenario**
--- | ---
**Lokal säkerhetskopiering** |  1) Kör Azure Backup Microsoft Azure Recovery Services-agenten (MARS) på lokala Windows-datorer för att säkerhetskopiera enskilda filer och systemtillstånd. <br/><br/>2) säkerhetskopiering av lokala datorer till en sekundär server (System Center Data Protection Manager (DPM) eller Microsoft Azure Backup Server (MABS)) och sedan konfigurera backup-servern att säkerhetskopiera till ett Azure Backup Recovery Services-valv i Azure.
**Virtuella Azure-datorer** | 1) Aktivera säkerhetskopiering av enskilda virtuella Azure-datorer. När du aktiverar säkerhetskopiering installerar Azure Backup ett tillägg på Azure VM-agenten som körs på den virtuella datorn. Agenten säkerhetskopierar hela den virtuella datorn.<br/><br/> 2) Kör MARS-agenten på en virtuell Azure-dator. Detta är användbart om du vill säkerhetskopiera enskilda filer och mappar på den virtuella datorn.<br/><br/> 3) Säkerhetskopiera en virtuell Azure-dator till en DPM-server eller MABS som körs i Azure. Säkerhetskopiera sedan DPM-servern eller MABS till ett valv med hjälp av Azure Backup.


## <a name="why-use-a-backup-server"></a>Varför ska jag använda en säkerhetskopieringsserver?
Fördelar med säkerhetskopiering av datorer och appar till MABS/DPM-lagring och sedan säkerhetskopiera DPM/MABS-lagring till ett valv är följande:

- Vid säkerhetskopiering till MABS/DPM ingår programmedvetna säkerhetskopior som är optimerade för vanliga program som SQL Server, Exchange och SharePoint. Dessutom ingår fil-/mapp-/volymsäkerhetskopior och säkerhetskopiering av systemtillstånd för datorer (utan operativsystem).
- På lokala datorer behöver du inte installera MARS-agenten på varje dator som du vill säkerhetskopiera. Varje dator som kör DPM/MABS-skyddsagenten och MARS-agenten körs på MABS/DPM endast.
- Du får större flexibilitet och mer detaljerade schemaläggningsalternativ för säkerhetskopiering.
- Du kan hantera säkerhetskopior för flera datorer som du grupperar i skyddsgrupper i en enda konsol. Detta är särskilt användbart för program som är nivåindelade över flera datorer och som behöver säkerhetskopieras tillsammans.

Läs mer om [hur säkerhetskopiering fungerar](backup-architecture.md#architecture-back-up-to-dpmmabs) när du använder en säkerhetskopieringsserver och [supportkraven](backup-support-matrix-mabs-dpm.md) för säkerhetskopieringsservrar.

## <a name="what-can-i-back-up"></a>Vad kan jag säkerhetskopiera?

**Dator** | **Säkerhetskopieringsmetod** | **Säkerhetskopiera**
--- | --- | ---
**Lokala virtuella Windows-datorer** | Köra MARS-agenten | Säkerhetskopiera filer, mappar och systemtillstånd.<br/><br/> Linux-datorer stöds inte.
**Lokala datorer** | Säkerhetskopiera till DPM/MABS | Säkerhetskopiera allt som skyddas av [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) eller [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs), inklusive filer/mappar/filresurser/volymer och programspecifika data.
**Virtuella Azure-datorer** | Köra Azure VM-agentens säkerhetskopieringstillägg | Säkerhetskopiera en hel virtuell dator
**Virtuella Azure-datorer** | Köra MARS-agenten | Säkerhetskopiera filer, mappar och systemtillstånd.<br/><br/> Linux-datorer stöds inte.
**Virtuella Azure-datorer** | Säkerhetskopiera till MABS/DPM som körs i Azure | Säkerhetskopiera allt som skyddas av [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) eller [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807), inklusive filer/mappar/filresurser/volymer och programspecifika data.

## <a name="what-backup-agents-do-i-need"></a>Vilka säkerhetskopieringsagenter behöver jag?

**Scenario** | **Agent**
--- | ---
**Säkerhetskopiera virtuella Azure-datorer** | Ingen agent behövs. Azure VM-tillägget för säkerhetskopiering installeras på Azure VM när du kör den första virtuella Azure-säkerhetskopieringen.<br/><br/> Stöd för Windows och Linux.
**Säkerhetskopiera lokala Windows-datorer** | Hämta, installera och kör MARS-agenten direkt på datorn.
**Säkerhetskopiera virtuella Azure-datorer med MARS-agenten** | Hämta, installera och kör MARS-agenten direkt på datorn. MARS-agenten kan köras tillsammans med säkerhetskopieringstillägget.
**Säkerhetskopiering av lokala datorer och virtuella Azure-datorer till DPM/MABS** | DPM- eller MABS-skyddsagenten körs på de datorer som du vill skydda. MARS-agenten körs på DPM-servern/MABS för att säkerhetskopiera till Azure.

## <a name="which-backup-agent-should-i-use"></a>Vilken säkerhetskopieringsagent ska jag använda?

**Säkerhetskopiering** | **Lösning** | **Begränsning**
--- | --- | ---
**Jag vill säkerhetskopiera en hel virtuell Azure-dator** | Aktivera säkerhetskopiering för den virtuella datorn. Säkerhetskopieringstillägget konfigureras automatiskt på den virtuella Azure-datorn, både för Windows och Linux. | Hela den virtuella datorn säkerhetskopieras <br/><br/> Säkerhetskopior är programkonsekventa för virtuella Windows-datorer. Säkerhetskopior är filkonsekventa för Linux-datorer. Du måste konfigurera med anpassade skript om dessa ska vara programmedvetna.
**Jag vill säkerhetskopiera specifika filer/mappar på Azure VM** | Distribuera MARS-agenten på den virtuella datorn.
**Jag vill säkerhetskopiera direkt till lokala Windows-datorer** | Installera MARS-agenten på datorn. | Du kan säkerhetskopiera filer, mappar och systemtillstånd till Azure. Säkerhetskopior är inte programmedvetna.
**Jag vill säkerhetskopiera direkt till lokala Linux-datorer** | Du behöver distribuera DPM eller MABS för att kunna säkerhetskopiera till Azure. | Säkerhetskopiering av Linux-värden stöds inte, kan du endast säkerhetskopiering Linux gästdatorn finns på Hyper-V eller VMWare.
**Jag vill säkerhetskopiera program som körs lokalt** | Datorer måste skyddas av DPM eller MABS för att kunna genomföra programmedvetna säkerhetskopieringar.
**Jag vill ha detaljerade och flexibla säkerhetskopierings- och återställningsinställningar för virtuella Azure-datorer** | Skydda virtuella Azure-datorer med MABS/DPM som körs i Azure för extra flexibilitet vid schemaläggning av säkerhetskopiering, och fullständig flexibilitet för att skydda och återställa filer, mappar, volymer, program och systemtillstånd.


## <a name="next-steps"></a>Nästa steg

- [Gå igenom](backup-architecture.md) arkitekturen och komponenterna i olika scenarier för säkerhetskopiering.
- [Kontrollera](backup-support-matrix.md) vilka funktioner och inställningar som stöds för säkerhetskopiering.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
