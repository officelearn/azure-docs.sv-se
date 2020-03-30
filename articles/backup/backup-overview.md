---
title: Vad är Azure Backup?
description: Ger en översikt över Azure Backup-tjänsten och hur den bidrar till din strategi för affärskontinuitet och haveriberedskap (BCDR).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: cf48090b2c32f0c3a1c8170873cb8d6a771fe21f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240298"
---
# <a name="what-is-the-azure-backup-service"></a>Vad är Azure Backup-tjänsten?

Azure Backup tillhandahåller enkla, säkra och kostnadseffektiva lösningar för att säkerhetskopiera dina data och återställa dem från Microsoft Azure-molnet.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>Vad kan jag säkerhetskopiera?

- Lokalt – Säkerhetskopiera filer, mappar, systemtillstånd med hjälp av [MICROSOFT Azure Recovery Services (MARS)-agenten](backup-support-matrix-mars-agent.md). **On-premises** Eller använd MABS-agenten (DPM eller Azure Backup Server) för att skydda lokala virtuella datorer[(Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) och [VMWare)](backup-azure-backup-server-vmware.md)och andra [lokala arbetsbelastningar](backup-mabs-protection-matrix.md)
- **Virtuella Azure-datorer** - [Säkerhetskopiera hela Windows/Linux-virtuella datorer](backup-azure-vms-introduction.md) (med hjälp av säkerhetskopieringstillägg) eller säkerhetskopiera filer, mappar och systemtillstånd med [MARS-agenten](backup-azure-manage-mars.md).
- **Azure Files delar** - [Säkerhetskopiera Azure-filresurser till ett lagringskonto](backup-afs.md)
- **SQL Server i Virtuella Azure-datorer** -  [Säkerhetskopiera SQL Server-databaser som körs på virtuella Azure-datorer](backup-azure-sql-database.md)
- **SAP HANA-databaser i Azure VMs** - [Backup SAP HANA-databaser som körs på virtuella Azure-datorer](backup-azure-sap-hana-database.md)

![Översikt över Azure-säkerhetskopiering](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Varför ska jag använda Azure Backup?

Azure Backup ger följande viktiga fördelar:

- **Avlasta lokal säkerhetskopiering:** Azure Backup erbjuder en enkel lösning för säkerhetskopiering av lokala resurser till molnet. Du får både kort- och långsiktig kvarhållning av säkerhetskopior utan att behöva distribuera komplexa lokala lösningar.
- **Säkerhetskopiera virtuella Azure IaaS-datorer:** Azure Backup tillhandahåller oberoende och isolerade säkerhetskopior för att skydda mot oavsiktlig förstörelse av ursprungliga data. Säkerhetskopior lagras i ett Recovery Services-valv med inbyggd hantering av återställningspunkter. Konfiguration och skalbarhet är enkla, säkerhetskopior är optimerade och du kan enkelt återställa efter behov.
- **Enkel skalning** – Azure Backup använder Azure-molnets underliggande kraft och obegränsade storlek för att tillhandahålla hög tillgänglighet – utan underhåll och omkostnad för övervakning.
- **Få obegränsad dataöverföring:** Azure Backup begränsar inte mängden inkommande eller utgående data som du överför eller debiterar för de data som överförs.
  - Utgående data syftar på data som överförs från ett Recovery Services-valv under en återställningsåtgärd.
  - Om du utför en första offlinesäkerhetskopia med azure import/export-tjänsten för att importera stora mängder data, finns det en kostnad som är associerad med inkommande data.  [Läs mer](backup-azure-backup-import-export.md).
- **Skydda data:** Azure Backup tillhandahåller lösningar för att skydda data [under överföring](backup-azure-security-feature.md) och i [vila](backup-azure-security-feature-cloud.md).
- **Centraliserad övervakning och hantering**: Azure Backup tillhandahåller [inbyggda övervaknings- och aviseringsfunktioner](backup-azure-monitoring-built-in-monitor.md) i ett Recovery Services-valv. Dessa funktioner är tillgängliga utan ytterligare hanteringsinfrastruktur. Du kan också öka omfattningen av din övervakning och rapportering med hjälp av [Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).
- **Hämta appkonsekventa säkerhetskopior:** En programkonsekvent säkerhetskopiering innebär att en återställningspunkt har alla nödvändiga data för att återställa säkerhetskopian. Azure Backup tillhandahåller programkonsekventa säkerhetskopior, vilket säkerställer att ytterligare korrigeringar inte krävs för att återställa data. Återställning av konsekventa programdata minskar tiden för återställning, så att du snabbt kan återgå till körläge.
- **Behåll kort- och långsiktiga data:** Du kan använda [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) för kortvarig och långsiktig datalagring.
- **Automatisk lagringshantering** – hybridmiljöer kräver ofta heterogen lagring – vissa lokalt och vissa i molnet. Med Azure Backup kostar det ingen kostnad för att använda lokala lagringsenheter. Azure Backup allokerar och hanterar lagringen av säkerhetskopiorna automatiskt och tillämpar en modell där du betalar baserat på din användning. Så du betalar bara för det lagringsutrymme du förbrukar. [Läs mer](https://azure.microsoft.com/pricing/details/backup) om prissättning.
- **Flera lagringsalternativ** – Azure Backup erbjuder två typer av replikering för att din lagring och dina data ska ha hög tillgänglighet.
  - [Lokalt redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) replikerar dina data tre gånger (det skapas tre kopior av dina data) i en lagringsskalningsenhet i ett datacenter. Alla datakopior finns i samma region. LRS är ett billigt alternativ för att skydda dina data mot fel i den lokala maskinvaran.
  - [Geo-redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md) är standardalternativet och det som rekommenderas vid replikering. GRS replikerar dina data till en sekundär region (hundratals mil bort från den primära platsen för datakällan). GRS kostar mer än LRS, men GRS ger en högre nivå av hållbarhet för dina data, även om det finns ett regionalt avbrott.

## <a name="next-steps"></a>Nästa steg

- [Gå igenom](backup-architecture.md) arkitekturen och komponenterna i olika scenarier för säkerhetskopiering.
- [Verifiera](backup-support-matrix.md) supportkrav och begränsningar för säkerhetskopiering och [för säkerhetskopiering av Azure VM](backup-support-matrix-iaas.md).
