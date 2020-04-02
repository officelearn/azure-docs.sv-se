---
title: Utöka stödet för SQL Server 2008 & 2008 R2
description: Utöka stödet för SQL Server 2008 och SQL Server 2008 R2 genom att migrera SQL Server-instansen till Azure eller köpa utökat stöd för att hålla instanser lokalt.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: faaff9e9ad5982efac6409f2284158a3d0711331
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548412"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Utöka stödet för SQL Server 2008 och SQL Server 2008 R2 med Azure

SQL Server 2008 och SQL Server 2008 R2 har båda nått [slutet av sin support (EOS) livscykel](https://www.microsoft.com/sql-server/sql-server-2008). Eftersom många kunder fortfarande använder båda versionerna erbjuder vi flera alternativ för att fortsätta få support. Du kan migrera dina lokala SQL Server-instanser till virtuella Azure-datorer (virtuella datorer), migrera till Azure SQL Database eller hålla dig lokal och köpa utökade säkerhetsuppdateringar.

Till skillnad från med en hanterad instans kräver migrering till en Azure VM inte att certifiera om dina program. Och till skillnad från att vara lokalt får du kostnadsfria utökade säkerhetskorrigeringar genom att migrera till en Virtuell Azure-dator.

Resten av den här artikeln innehåller överväganden för att migrera DIN SQL Server-instans till en Azure VM.

Mer information om när supportalternativen har avslutats finns i [Slut på supporten](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Etablering

Det finns en SQL Server **2008 R2-avbildning som gäller för windows server 2008 r2 på** Azure Marketplace.

Kunder som är på SQL Server 2008 måste antingen självinstallera eller uppgradera till SQL Server 2008 R2. På samma sätt måste kunder på Windows Server 2008 antingen distribuera sin virtuella dator från en anpassad virtuell hårddisk eller uppgradera till Windows Server 2008 R2.

Avbildningar som distribueras via Azure Marketplace levereras med SQL IaaS-tillägget förinstallerat. SQL IaaS-tillägget är ett krav för flexibel licensiering och automatiserad korrigering. Kunder som distribuerar självinstallerade virtuella datorer måste installera SQL IaaS-tillägget manuellt. SQL IaaS-tillägget stöds inte på Windows Server 2008.

> [!NOTE]
> Även om sql **server-funktionerna skapa** och **hantera** fungerar med SQL Server 2008 R2-avbildningen i Azure-portalen, _stöds inte_följande funktioner: Automatiska säkerhetskopior, Azure Key Vault-integrering, R-tjänster och lagringskonfiguration.

## <a name="licensing"></a>Licensiering
Användningsbaserad SQL Server 2008 R2-distributioner kan konverteras till [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

Om du vill konvertera en SA-baserad licens (Software Assurance) till användningsbaserad betalning bör kunderna registrera sig hos SQL [VM-resursleverantören](virtual-machines-windows-sql-register-with-resource-provider.md). Efter registreringen är SQL-licenstypen utbytbar mellan Azure Hybrid Benefit och användningsbaserad betalning.

Självinstallerade SQL Server 2008- eller SQL Server 2008 R2-instanser på en Azure VM-resurs kan registrera sig hos SQL VM-resursprovidern och konvertera sin licenstyp till användningsbaserad betalning.

## <a name="migration"></a>Migrering
Du kan migrera EOS SQL Server-instanser till en Virtuell Azure-dator med manuella säkerhetskopierings-/återställningsmetoder. Det här är den vanligaste migreringsmetoden från lokalt till en Azure VM.This is the most common migration method from on-premises to an Azure VM.

### <a name="azure-site-recovery"></a>Azure Site Recovery

För massmigreringar rekommenderar vi [azure site recovery-tjänsten.](/azure/site-recovery/site-recovery-overview) Med Azure Site Recovery kan kunder replikera hela den virtuella datorn, inklusive SQL Server från lokalt till Azure VM.

SQL Server kräver appkonsekventa ögonblicksbilder av Azure Site Recovery för att garantera återställning. Azure Site Recovery stöder appkonsekventa ögonblicksbilder med ett intervall på minst 1 timme. Det minsta återställningspunktmål (RPO) som är möjligt för SQL Server med Azure Site Recovery-migreringar är 1 timme. Återställningstidens mål (RTO) är 2 timmar plus SQL Server återställningstid.

### <a name="database-migration-service"></a>Database Migration Service

[Tjänsten Databasmigrering](/azure/dms/dms-overview) är ett alternativ för kunder om de migrerar från lokalt till en virtuell Azure-dator genom att uppgradera SQL Server till 2012-versionen eller senare.

## <a name="disaster-recovery"></a>Haveriberedskap

Lösningar för haveriberedskap för EOS SQL Server på en virtuell Azure-dator är följande:

- **Säkerhetskopiering av SQL Server:** Använd Azure Backup för att skydda din EOS SQL Server 2008 och 2008 R2 mot ransomware, oavsiktlig borttagning och skada med 15-min RPO och point-in-time-återställning. Mer information finns i [den här artikeln](https://docs.microsoft.com/azure/backup/sql-support-matrix#scenario-support).
- **Loggleverans:** Du kan skapa en loggfraktreplik i en annan zon eller Azure-region med kontinuerliga återställningar för att minska RTO.Log shipping : Du kan skapa en loggfraktreplik i en annan zon eller Azure-region med kontinuerliga återställningar för att minska RTO. Du måste konfigurera loggfrakten manuellt.
- **Azure Site Recovery:** Du kan replikera din virtuella dator mellan zoner och regioner via Azure Site Recovery replication. SQL Server kräver appkonsekventa ögonblicksbilder för att garantera återställning i händelse av en katastrof. Azure Site Recovery erbjuder en minst 1-timmars RPO och en 2-timmars (plus SQL Server återställningstid) RTO för EOS SQL Server haveriberedskap.

## <a name="security-patching"></a>Säkerhetskorrigering
Utökade säkerhetsuppdateringar för virtuella SQL Server-datorer levereras via Microsoft Update-kanalerna efter att SQL Server-VM har registrerats hos SQL [VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md). Patchar kan laddas ner manuellt eller automatiskt.

*Automatisk uppdatering* är aktiverat som standard. Med inställningen Automatisk uppdatering kan Azure korrigera SQL Server och operativsystemet automatiskt. Du kan ange en veckodag, tid och varaktighet för ett underhållsfönster om SQL Server IaaS-tillägget är installerat. Azure utför uppdateringar under den här underhållsperioden. Den virtuella datorns lokala tid används för underhållsperiodens schema.  Mer information finns i [Automatisk korrigering för SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Nästa steg

Migrera din virtuella SQL Server-dator till Azure:

* [Migrera en SQL Server-databas till SQL Server på en virtuell Azure-dator](virtual-machines-windows-migrate-sql.md)

Komma igång med SQL Server på virtuella Azure-datorer:

* [Skapa en virtuell SQL Server-dator på Azure Portal](quickstart-sql-vm-create-portal.md)

Få svar på vanliga frågor om virtuella SQL Server-datorer:

* [Vanliga frågor och svar om SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-faq.md)

Läs mer om att avsluta supportalternativ och utökade säkerhetsuppdateringar:

* [på supporten](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [utökade säkerhetsuppdateringar](/sql/sql-server/end-of-support/sql-server-extended-security-updates)