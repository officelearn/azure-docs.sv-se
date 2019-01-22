---
title: Vad är Azure Backup?
description: Ger en översikt över tjänsten Azure Backup och hur du distribuerar den som en del av din strategi för affärskontinuitet och haveriberedskap (BCDR).
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7156042243b1ba28cea712dc3722600b9fc46c42
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360647"
---
# <a name="what-is-azure-backup"></a>Vad är Azure Backup?

Med Azure Backup-tjänsten kan du säkerhetskopiera data till Microsoft Azure-molnet. Du kan säkerhetskopiera lokala datorer och arbetsbelastningar, samt virtuella Azure-datorer.


## <a name="why-use-azure-backup"></a>Varför ska jag använda Azure Backup?

Azure Backup ger följande viktiga fördelar:

- **Avlasta lokal säkerhetskopiering**: Azure Backup är en enkel lösning för att säkerhetskopiera dina lokala resurser till molnet. Du får både kort- och långsiktig kvarhållning av säkerhetskopior utan att behöva distribuera komplexa lokala lösningar. Eliminera behovet av band eller säkerhetskopiering på annan plats.
- **Säkerhetskopiera virtuella Azure IaaS-datorer**: Med Azure Backup får du oberoende och isolerade säkerhetskopior, vilket skyddar originaldata från att förstöras oavsiktligt. Säkerhetskopior lagras i ett Recovery Services-valv med inbyggd hantering av återställningspunkter. Det är enkelt att konfigurera och skala, säkerhetskopiorna optimeras och du kan återställa vid behov.
- **Enkel skalning** – Azure Backup använder Azure-molnets underliggande kraft och obegränsade storlek för att tillhandahålla hög tillgänglighet – utan underhåll och omkostnad för övervakning. Du kan ställa in aviseringar för att tillhandahålla information om händelser, men du behöver inte oroa dig för hög tillgänglighet för dina data i molnet.
- **Obegränsad dataöverföring** – Azure Backup begränsar inte hur mycket inkommande eller utgående data du överför. Azure Backup debiterar inte heller för de data som överförs. Om du använder Azure Import/Export-tjänsten för att importera stora mängder data finns det dock en kostnad som är kopplad till inkommande data. Mer information om kostnaden finns i [Offline-backup workflow in Azure Backup](backup-azure-backup-import-export.md) (Arbetsflöde för säkerhetskopiering offline i Azure Backup). Utgående data syftar på data som överförs från ett Recovery Services-valv under en återställningsåtgärd.
- **Skydda data**: Datakryptering möjliggör säker överföring och lagring av data i det offentliga molnet. Krypteringslösenfrasen lagras på lokalt och överförs eller lagras aldrig i Azure. Om det är nödvändigt att återställa data kan du göra det om du har krypteringslösenfrasen eller nyckeln.
- **Programkonsekvent säkerhetskopiering**: Programkonsekvent säkerhetskopiering innebär att en återställningspunkt har alla data som krävs för att återställa säkerhetskopian. Azure Backup innehåller programkonsekventa säkerhetskopior vilket garanterar att inga ytterligare korrigeringar behövs för att återställa data. Återställning av konsekventa programdata minskar tiden för återställning, så att du snabbt kan återgå till körläge.
- **Kort- och långsiktig kvarhållning**: **Långsiktig kvarhållning** – Du kan använda Recovery Services-valv för långsiktig och kortsiktig datakvarhållning. Azure begränsar inte hur lång tid data behålls i ett Recovery Services-valv. Du kan förvara data i ett valv hur länge du vill. Azure Backup har en gräns på 9999 återställningspunkter per skyddad instans. Läs avsnittet [Säkerhetskopiering och kvarhållning](backup-introduction-to-azure-backup.md#backup-and-retention) i den här artikeln för att få en förklaring av hur den här gränsen kan påverka dina säkerhetskopieringsbehov.
- **Automatisk lagringshantering** – hybridmiljöer kräver ofta heterogen lagring – vissa lokalt och vissa i molnet. Med Azure Backup är det kostnadsfritt att använda lokala lagringsenheter. Azure Backup allokerar och hanterar lagringen av säkerhetskopiorna automatiskt och tillämpar en modell där du betalar baserat på din användning. Att betala baserat på din användning innebär att du bara betalar för den lagring du konsumerar. Mer information finns i artikeln om [prissättning i Azure](https://azure.microsoft.com/pricing/details/backup).
- **Flera lagringsalternativ** – en aspekt av hög tillgänglighet är lagringsreplikering. Azure Backup erbjuder två typer av replikering: [lokalt redundant lagring](../storage/common/storage-redundancy-lrs.md) och [geo-redundant lagring](../storage/common/storage-redundancy-grs.md). Välj lagring för säkerhetskopiering baserat på behov:
    - Lokalt redundant lagring (LRS) replikerar data tre gånger (det skapas tre kopior av dina data) i en lagringsskalningsenhet i ett datacenter i samma region. Alla datakopior finns i samma region. LRS är ett billigt alternativ för att skydda dina data mot fel i den lokala maskinvaran.
    - Geo-redundant lagring (GRS) är standardalternativet och det som rekommenderas för replikering. GRS replikerar dina data till en sekundär region (hundratals mil bort från den primära platsen för datakällan). GRS kostar mer än LRS, men GRS ger högre hållbarhet för dina data, även i händelse av ett regionalt avbrott.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Vad är skillnaden mellan Azure Backup och Azure Site Recovery?

Både Azure Backup och Azure Site Recovery kan ingå i din strategi för affärskontinuitet och haveriberedskap (BCDR) i verksamheten. En sådan strategi har två breda mål:

- Se till att dina affärsdata skyddas och kan återställas vid avbrott.
- Se till att dina program och arbetsbelastningar fortsätter köras under såväl planerade som oplanerade stillestånd.

De här två tjänsterna innehåller kompletterande men också olika funktioner.

- **Azure Site Recovery**: Site Recovery tillhandahåller en lösning för haveriberedskap för lokala datorer och virtuella Azure-datorer. Du kan replikera datorer från en primär till en sekundär plats. I händelse av en katastrof redundansväxlas datorerna till den sekundära platsen, med åtkomst därifrån. När allt fungerar som vanligt igen kan de redundansväxlas tillbaka till den primära platsen för återställning.
- **Azure Backup**: Tjänsten Azure Backup säkerhetskopierar data från lokala datorer och virtuella Azure-datorer. Data kan säkerhetskopieras och återställas på detaljerad nivå, till exempel genom säkerhetskopiering av filer, mappar, systemtillstånd och programmedvetna data. Azure Backup hanterar data på en mer detaljerad nivå än Site Recovery. Om till exempel en presentation på en bärbar dator skadas, använder du Azure Backup för att återställa presentationen. Om du vill skydda och ha tillgång till en virtuell dators konfiguration och data använder du Site Recovery.  

Bestäm dina behov för affärskontinuitet och haveriberedskap med hjälp av tabellen nedan. 

**Mål** | **Detaljer** | **Jämförelse**
--- | --- | --- | --- |
**Säkerhetskopiera/bevara data** | Säkerhetskopierade data kan bevaras och lagras i flera dagar, månader eller år, om det behövs i efterlevnadssyfte. | Med säkerhetskopieringslösningar som Azure Backup kan du välja vilka data som ska säkerhetskopieras och skräddarsy principer för säkerhetskopiering och kvarhållning.<br/><br/> Site Recovery har inte lika detaljerad anpassning.
**Mål för återställningspunkt (RPO)** | Mängden godtagbar dataförlust om en återställning krävs. | Säkerhetskopieringar har större variation vad gäller mål för återställningspunkter.<br/><br/> Säkerhetskopieringar av virtuella datorer har vanligtvis ett återställningspunktmål på en dag, medan säkerhetskopieringar av databaser har återställningspunktmål på så lite som 15 minuter.<br/><br/> Site Recovery tillhandahåller låga återställningspunktmål eftersom replikeringen är kontinuerlig eller frekvent, vilket innebär att deltat mellan källa och replikering är litet.
**Mål för återställningstid (RTO)** |Hur lång tid det tar att slutföra en återställning. | På grund av det större återställningspunktmålet är mängden data som en säkerhetskopieringslösning behöver bearbeta normalt mycket högre, vilket leder till längre mål för återställningstid. Det kan till exempel ta dagar att återställa data från band, beroende på hur lång tid det tar att överföra bandet från den externa platsen. | Lösningar för haveriberedskap som Site Recovery har låga återställningspunktmål eftersom kontinuerlig/frekvent replikering vanligtvis innebär att målet är mycket mer synkroniserat med källan. |

## <a name="what-backup-scenarios-are-supported"></a>Vilka säkerhetskopieringsscenarier stöds?

Azure Backup kan säkerhetskopiera både lokala datorer och virtuella Azure-datorer.

**Dator** | **Säkerhetskopieringsscenario**
--- | ---
**Lokala datorer (fysiska/virtuella)** |  Du kan säkerhetskopiera enskilda lokala datorer.<br/><br/>Du kan säkerhetskopiera lokala datorer som skyddas av System Center Data Protection Manager (DPM).<br/><br/> Du kan säkerhetskopiera lokala datorer som skyddas av Microsoft Azure Backup Server (MABS).
**Virtuella Azure-datorer** | Du kan säkerhetskopiera enskilda virtuella Azure-datorer.<br/><br/> Du kan säkerhetskopiera virtuella Azure-datorer som skyddas av DPM eller MABS.

### <a name="back-up-servers"></a>Säkerhetskopiera servrar

Du kanske behöver säkerhetskopiera lokala servrar och arbetsbelastningar, eller virtuella Azure-datorer och deras arbetsbelastningar. Först till en sekundär server och sedan till ett Recovery Services-valv. 

**Sekundär server** | **Detaljer**
--- | ---
**System Center Data Protection Manager (DPM)** | Använd Azure Backup för att säkerhetskopiera data som skyddas av DPM:<br/><br/> – DPM kan köras lokalt (fysiskt eller virtuellt) eller i Azure.<br/><br/> – Du kan skydda olika typer av data som körs på lokala datorer och virtuella Azure-datorer genom att säkerhetskopiera dem till DPM-servern.<br/><br/> – DPM-servern kan i sin tur säkerhetskopieras till Recovery Services-valvet med Azure Backup-tjänsten.<br/><br/> – DPM-servern och datorerna den skyddar måste ingå i samma nätverk. Lokala datorer kan endast skyddas av en lokal DPM-server. På samma sätt kan virtuella Azure-datorer endast skyddas av DPM som körs i Azure.
**Microsoft Azure Backup Server (MABS)** | Använd Azure Backup för att säkerhetskopiera data som skyddas av MABS:<br/><br/> – MABS kan köras lokalt (fysiskt eller virtuellt) eller i Azure.<br/><br/> – Du kan skydda olika typer av data som körs på lokala datorer och virtuella Azure-datorer genom att säkerhetskopiera dem till MABS.<br/><br/> – MABS kan i sin tur säkerhetskopieras till Recovery Services-valvet med Azure Backup-tjänsten.<br/><br/> – MABS tillhandahåller liknande funktioner som DPM, förutom att det inte går att säkerhetskopiera till band med MABS. MABS kräver inte System Center-licens.<br/><br/> – Precis som med DPM kan lokala datorer endast skyddas av en lokal MABS. Virtuella Azure-datorer kan endast skyddas av en MABS i Azure.

Fördelarna med att säkerhetskopiera först till DPM/MABS och sedan till ett valv är följande:

- Vid säkerhetskopiering till DPM/MABS ingår programmedvetna säkerhetskopior som är optimerade för vanliga program som SQL Server, Exchange och SharePoint, i tillägg till fil-/mapp-/volymsäkerhetskopior och säkerhetskopiering av systemtillstånd för datorer (utan operativsystem).
- Du behöver inte installera Azure Backup-agenten på varje dator som ska säkerhetskopieras. DPM/MABS-skyddsagenten körs på varje dator, och Azure Backup Microsoft Azure Recovery Services-agenten körs endast på DPM-servern/MABS.
- Du får större flexibilitet och mer detaljerade schemaläggningsalternativ för säkerhetskopiering.
- Du kan hantera säkerhetskopior för flera datorer som du grupperar i skyddsgrupper i en enda konsol. Detta är särskilt användbart för program som är nivåindelade över flera datorer och som behöver säkerhetskopieras tillsammans.

## <a name="what-can-be-backed-up"></a>Vad kan säkerhetskopieras?

**Dator** | **Sekundär server** | **Säkerhetskopiera**
--- | --- | ---
Lokala virtuella Windows-datorer | Säkerhetskopieras inte till DPM eller MABS | Säkerhetskopiera filer, mappar och systemtillstånd.
Virtuella Azure-datorer (Windows och Linux) | Säkerhetskopieras inte till DPM eller MABS | Säkerhetskopiera filer, mappar och systemtillstånd.<br/><br/> Säkerhetskopior är programmedvetna för Windows-datorer och filmedvetna för Linux-datorer.
Lokala virtuella datorer/virtuella Azure-datorer | Skyddas av DPM | Säkerhetskopiera allt som skyddas av DPM, inklusive filer/mappar/delningar/volymer och programspecifika data. [Läs mer om](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) vad som kan säkerhetskopieras av DPM.
Lokala virtuella datorer/virtuella Azure-datorer | Skyddas av MABS | Säkerhetskopiera allt som skyddas av MABS, inklusive filer/mappar/delningar/volymer och programspecifika data. [Läs mer om](backup-mabs-protection-matrix.md) vad som kan säkerhetskopieras av MABS.

## <a name="what-backup-agents-do-i-need"></a>Vilka säkerhetskopieringsagenter behöver jag?
**Scenario** | **Agent** | **Detaljer**
--- | --- | ---
Lokala datorer (ingen säkerhetskopieringsserver) | Microsoft Azure Recovery Services-agent (MARS) som körs på en Windows-dator. | Du laddar ned och installerar MARS-agenten under distributionen av Azure Backup.<br/><br/> Endast stöd för Windows-datorer.
Virtuella Azure-datorer (ingen säkerhetskopieringsserver) | Kräver ingen särskild agent. Azure VM-tillägget för säkerhetskopiering som körs på den virtuella Azure-datorn. | Tillägget installeras vid den första säkerhetskopieringen av den virtuella Azure-datorn.<br/><br/> Stöd för Windows och Linux.
Lokala datorer och virtuella Azure-datorer som skyddas av DPM. | MARS-agenten som körs på DPM-servern. | MARS-agenten behövs inte på enskilda datorer.<br/><br/> När du distribuerar DPM installeras DPM-skyddsagenten på varje dator som du skyddar. 
Säkerhetskopiering av lokala datorer och virtuella Azure-datorer som skyddas av MABS | MARS-agenten som körs på MABS. | MARS-agenten behövs inte på enskilda datorer.<br/><br/> När du distribuerar MABS installeras MABS-skyddsagenten på varje dator som du skyddar. 


## <a name="which-backup-agent-should-i-use"></a>Vilken säkerhetskopieringsagent ska jag använda?

**Säkerhetskopiering** | **Lösning** | **Begränsning**
--- | --- | ---
Jag vill säkerhetskopiera lokala Windows-datorer. Datorerna skyddas inte av DPM eller MABS. | Installera MARS-agenten på datorn. | Du kan säkerhetskopiera filer, mappar och systemtillstånd till Azure. Säkerhetskopior är inte programmedvetna.
Jag vill säkerhetskopiera lokala Linux-datorer. Datorerna skyddas inte av DPM eller MABS. | Du behöver distribuera DPM eller MABS för att kunna säkerhetskopiera till Azure.
Jag vill säkerhetskopiera program som körs på lokala Windows-datorer. | Windows-datorer måste skyddas av DPM eller MABS för att kunna genomföra programmedvetna säkerhetskopieringar.
Jag vill säkerhetskopiera virtuella Azure-datorer | Kör en säkerhetskopiering med Azure Backup. Säkerhetskopieringstillägget konfigureras automatiskt på den virtuella Azure-datorn, både för Windows och Linux. | VM-diskarna säkerhetskopieras.<br/><br/> Säkerhetskopior är programkonsekventa för virtuella Windows-datorer. Säkerhetskopior är filkonsekventa för Linux-datorer. Du måste konfigurera med anpassade skript om dessa behöver vara programmedvetna.
Jag vill säkerhetskopiera virtuella Azure-datorer med detaljerad flexibilitet i inställningarna för säkerhetskopiering och återställning. | Skydda virtuella Azure-datorer med DPM eller MABS som körs i Azure för extra flexibilitet för schemaläggning av säkerhetskopiering, och fullständig flexibilitet för att skydda och återställa filer, mappar, volymer, program och systemtillstånd.


## <a name="next-steps"></a>Nästa steg

- [Gå igenom](backup-architecture.md) arkitekturen och komponenterna i olika scenarier för säkerhetskopiering.
- [Kontrollera](backup-support-matrix.md) vilka funktioner och inställningar som stöds för säkerhetskopiering.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

