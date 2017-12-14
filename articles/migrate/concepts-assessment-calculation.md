---
title: "Bedömning beräkningar i Azure migrera | Microsoft Docs"
description: "En översikt över assessment beräkningar i tjänsten Azure migrera."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 2b274244cc7b7fd0fc3eee22a57a51db77370370
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="assessment-calculations"></a>Utvärderingsberäkningar

[Azure migrera](migrate-overview.md) utvärderar lokala arbetsbelastningar för migrering till Azure. Den här artikeln innehåller information om hur bedömningar beräknas.



## <a name="overview"></a>Översikt

En Azure migrera bedömning har tre steg. Bedömning som börjar med en lämplighet analys, följt av prestanda-baserade sizing uppskattningar och slutligen ett månatligt cost uppskattning. En dator flyttar endast till ett senare tillfälle om föregående överförs. Till exempel om en dator misslyckas Azure lämplighet kontrollen markeras som lämpar sig inte för Azure, och ange storlek och kostnadskrävande inte beräknas. 


## <a name="azure-suitability-analysis"></a>Azure lämplighet analys

Datorer som du vill migrera till Azure måste uppfylla kraven för Azure och begränsningar. Till exempel om en lokal virtuell disk är mer än 4 TB, får den inte finnas på Azure. Azure kompatibilitetskontroller sammanfattas i följande tabell. 

**Kontrollera** | **Detaljer**
--- | ---
**Start-typ** | Start-typ av gäst-OS-disken måste vara BIOS och UEFI inte.
**Kärnor** | Antal kärnor på datorerna måste vara lika med (eller mindre än) det högsta antalet kärnor (32) som stöds för en Azure VM.<br/><br/> Om det finns prestandahistorik anser Azure migrera utnyttjade kärnor för jämförelse. Om en bekvämlighet faktor anges i inställningarna för assessment multipliceras antal utnyttjade kärnor med bekvämlighet faktorn.<br/><br/> Om det finns ingen prestandahistorik, använder Azure migrera allokerade kärnor, utan att använda bekvämlighet faktorn.
**Minne** | Storleken på datorn minne måste vara lika med (eller mindre än) den maximala mängd minnet (448 GB) tillåten för en Azure VM. <br/><br/> Om det finns prestandahistorik anser Azure migrera utnyttjade minne för jämförelse. Om du anger en bekvämlighet faktor multipliceras utnyttjade minne med bekvämlighet faktorn.<br/><br/> Om det finns ingen historik allokerat minne används, utan att använda bekvämlighet faktorn.<br/><br/> 
**Windows Server 2003 2008 R2** | stöd för 32-bitars och 64-bitars.<br/><br/> Azure tillhandahåller support efter bästa förmåga endast.
**Windows Server 2008 R2 med alla Service Pack** | stöd för 64-bitars.<br/><br/> Azure tillhandahåller fullt stöd.
**Windows Server 2012 & alla Service Pack** | stöd för 64-bitars.<br/><br/> Azure tillhandahåller fullt stöd.
**Windows Server 2012 R2 & alla Service Pack** | stöd för 64-bitars.<br/><br/> Azure tillhandahåller fullt stöd.
**Windows Server 2016 & alla Service Pack** | stöd för 64-bitars.<br/><br/> Azure tillhandahåller fullt stöd.
**Klienten för Windows 7 och senare** | stöd för 64-bitars.<br/><br/> Azure tillhandahåller support med Visual Studio-prenumeration.
**Linux** | stöd för 64-bitars.<br/><br/> Azure tillhandahåller fullt stöd för dessa [operativsystem](../virtual-machines/linux/endorsed-distros.md).
**Lagringsdisken** | Allokerade storleken på en disk måste vara 4 TB (4096 GB) eller mindre.<br/><br/> Antalet diskar som är anslutna till datorn måste vara 65 eller mindre, inklusive OS-disk. 
**Nätverk** | En dator måste ha 32 eller mindre nätverkskort anslutna.


## <a name="performance-based-sizing"></a>Prestandabaserad storlek

När en dator har markerats som lämpar sig för Azure, mappar Azure migrera den till en VM-storlek i Azure, med följande villkor:

- **Kontroll av lagring**: Azure migrera försöker mappa alla diskar som är anslutna till datorn till en disk i Azure:-Azure migrera multiplicerar i/o per sekund (IOPS) med bekvämlighet faktorn. Den också multiplar dataflöde (i Mbit/s) för varje disk med bekvämlighet faktor. Detta ger faktiska IOPS och genomflöde. Baserat på den här mappar Azure migrera på disken till en standard eller premium i Azure.
    - Om tjänsten inte kan hitta en disk med nödvändiga IOPS & genomströmning, markerar datorn som lämpar sig inte för Azure.
    - Om den hittar en uppsättning lämpliga diskar väljer Azure migrera de som stöd för redundans lagringsmetod och den plats som anges i inställningarna för utvärdering.
    - Om det finns flera tillgängliga diskar, väljs med lägst kostnad.
- **Diskgenomflödet**: [mer](../azure-subscription-service-limits.md#storage-limits) om Azure begränsar per disk- och VM.
- **Disktyp**: Azure migrera har stöd för hanterade diskar.
- **Nätverk Kontrollera**: Azure migrera försöker hitta en virtuell Azure-dator som har stöd för antalet nätverkskort på den lokala datorn.
    - För att göra detta, den sammanställer de data som överförs per sekund (MBps) utanför datorn (nätverk ut) på alla nätverkskort och gäller bekvämlighet faktor för aggregerade numret. Det här numret om används för att hitta en virtuell Azure-dator som har stöd för nödvändiga nätverkets prestanda.
    - Azure migrera tar nätverksinställningar från den virtuella datorn och förutsätter att det ska vara ett nätverk utanför datacentret.
    - Om det finns inga nätverk prestandadata anses endast antalet nätverkskort för VM-storlek.
- **Beräkna Kontrollera**: när krav för lagring och nätverk beräknas Azure migrera anser behov:
    - Om prestandadata är tillgängliga för den virtuella datorn, söker i utnyttjade kärnor och minne och gäller bekvämlighet faktorn. Baserat på det antalet försök att hitta en lämplig VM-storlek i Azure.
    - Om det finns ingen lämplig storlek, markeras datorn som lämpar sig inte för Azure.
    - Om det finns en lämplig storlek, gäller Azure migrera beräkningar för lagring och nätverk. Det gäller plats och prissättning nivån inställningar för slutlig VM storlek rekommendationen.


## <a name="monthly-cost-estimation"></a>Månatliga uppskattning

När sizing rekommendationer är klart, beräknar Azure migrera kostnader för beräkning och lagring av efter migreringen.

- **Beräkna kostnaden**: med den rekommenderade Azure VM-storleken Azure migrera använder fakturerings-API för att beräkna månadskostnaden för den virtuella datorn. Operativsystemet, software assurance, plats och valutainställningar hänsyn tas med i beräkningen. Den sammanställer kostnaden över alla datorer att beräkna den totala månatliga beräkna kostnaden.
- **Lagringskostnaden**: månatliga storage kostnaden för en dator beräknas genom insamling av månadskostnaden för alla diskar som är anslutna till datorn. Azure migrera beräknar de totala månatliga lagringskostnaderna genom att sammanställa kostnader för lagring av alla datorer. Beräkningen tar för närvarande inte erbjudanden som anges i inställningarna för utvärdering i beräkningen.

Kostnaderna visas i valutan som anges i inställningarna för utvärdering. 


## <a name="next-steps"></a>Nästa steg

[Konfigurera en bedömning för lokala virtuella VMware-datorer](tutorial-assessment-vmware.md)
