---
title: Azure Backup support mat ris för SQL Server säkerhets kopiering på virtuella Azure-datorer
description: Innehåller en översikt över support inställningar och begränsningar när du säkerhetskopierar SQL Server i virtuella Azure-datorer med tjänsten Azure Backup.
ms.topic: conceptual
ms.date: 03/05/2020
ms.custom: references_regions
ms.openlocfilehash: b189eceb6b5a7f2e508387c0b91b238ff5fcb088
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174063"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Support mat ris för SQL Server säkerhets kopiering på virtuella Azure-datorer

Du kan använda Azure Backup för att säkerhetskopiera SQL Server databaser i virtuella Azure-datorer som finns på Microsoft Azure moln plattformen. Den här artikeln sammanfattar allmänna support inställningar och begränsningar för scenarier och distributioner av SQL Server säkerhets kopiering på virtuella Azure-datorer.

## <a name="scenario-support"></a>Scenariostöd

**Support** | **Detaljer**
--- | ---
**Distributioner som stöds** | Virtuella SQL Marketplace Azure-datorer och virtuella icke-Marketplace-datorer (manuellt installerat SQL Server) stöds.
**Regioner som stöds** | Sydöstra Australien (ASE), östra Australien (AE), Australien, centrala (AC), Australien, centrala 2 (AC) <br> Brasilien, södra (BRS)<br> Kanada, centrala (CNC), Kanada, östra (CE)<br> Asien, sydöstra (SEA), Asien, östra (EA) <br> Östra USA (EUS), östra USA 2 (EUS2), västra centrala USA (WCUS), västra USA (WUS); Västra USA 2 (WUS 2) norra centrala USA (NCUS) centrala USA (CUS) södra centrala USA (SCUS) <br> Indien, centrala (INC), Indien, södra (moduler), västra Indien <br> Japan, östra (JPE), Japan, väst (JPW) <br> Korea, centrala (KRC), Korea, södra (KRS) <br> Nord Europa (NE), Västeuropa <br> Storbritannien, södra (UKS), Storbritannien, västra (UKW) <br> US Gov, Arizona, US Gov, Virginia, US Gov, Texas, US DoD, centrala, US DoD, östra <br> Tyskland, norra Tyskland, västra centrala <br> Schweiz, norra Schweiz, västra <br> Frankrike, centrala <br> Kina, östra, Kina, östra 2, Kina, norra, Kina, norra 2
**Operativ system som stöds** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux stöds inte för närvarande.
**SQL Server-versioner som stöds** | SQL Server 2019, SQL Server 2017 som beskrivs på [sidan Sök produktens livs cykel](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 och SPS enligt beskrivningen på [sidan Sök produkt livs cykel](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.<br><br>Express lokala DB-versioner stöds inte.
**.NET-versioner som stöds** | .NET Framework 4.5.2 eller senare installerat på den virtuella datorn

## <a name="feature-considerations-and-limitations"></a>Funktions överväganden och begränsningar

|Inställning  |Övre gräns |
|---------|---------|
|Antal databaser som kan skyddas på en server (och i ett valv)    |   2000      |
|Databas storlek som stöds (utöver detta kan prestanda problem uppstå)   |   2 TB      |
|Antal filer som stöds i en databas    |   1000      |

>[!NOTE]
> [Hämta den detaljerade resurs planeraren](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) för att beräkna det ungefärliga antalet skyddade databaser som rekommenderas per server baserat på de virtuella dator resurserna, bandbredden och säkerhets kopierings principen.

* SQL Server säkerhets kopiering kan konfigureras i Azure Portal eller **PowerShell**. CLI stöds inte.
* Lösningen stöds på båda typerna av [distributioner](../azure-resource-manager/management/deployment-models.md) – Azure Resource Manager virtuella datorer och klassiska virtuella datorer.
* Alla säkerhets kopierings typer (fullständig/differentiell/log) och återställnings modeller (enkel/fullständig/Mass utloggning) stöds.
* Fullständiga och kopiera endast fullständiga säkerhets kopierings typer stöds för **skrivskyddade** databaser.
* Inbyggd SQL-komprimering stöds om användaren uttryckligen aktive ras av användaren i säkerhets kopierings principen. Azure Backup åsidosätter standardvärden på instans nivå med KOMPRIMERINGs-/NO_COMPRESSION-satsen, beroende på värdet för den här kontrollen som anges av användaren.
* TDE-aktiverad databas säkerhets kopiering stöds. Om du vill återställa en TDE-krypterad databas till en annan SQL Server måste du först [återställa certifikatet till mål servern](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server). Komprimering av säkerhets kopiering för TDE-aktiverade databaser för SQL Server 2016 och nyare versioner är tillgänglig, men vid lägre överförings storlek enligt beskrivningen [här](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593).
* Säkerhets kopierings-och återställnings åtgärder för speglings databaser och ögonblicks bilder av databasen stöds inte.
* SQL Server **-redundanskluster (FCI)** stöds inte.
* Om du använder fler än en säkerhets kopierings lösning för att säkerhetskopiera din fristående SQL Server instans eller SQL Always on-tillgänglighetsgruppen kan det leda till säkerhets kopierings problem. Avstå från att göra det. Att säkerhetskopiera två noder i en tillgänglighets grupp individuellt med samma eller olika lösningar, kan också leda till att säkerhets kopieringen Miss lyckas.
* När tillgänglighets grupper har kon figurer ATS tas säkerhets kopiorna från de olika noderna utifrån några faktorer. Säkerhets kopierings beteendet för en tillgänglighets grupp sammanfattas nedan.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Så fungerar säkerhetskopiering med AlwaysOn-tillgänglighetsgrupper

Vi rekommenderar att säkerhets kopian bara konfigureras på en nod i en tillgänglighets grupp (AG). Konfigurera alltid säkerhets kopiering i samma region som den primära noden. Med andra ord behöver du alltid den primära noden för att finnas i den region där du konfigurerar säkerhets kopian. Om alla noder i AG finns i samma region där säkerhets kopian har kon figurer ATS, finns det inga problem.

#### <a name="for-cross-region-ag"></a>För en AG i flera regioner

* Oavsett säkerhets kopierings inställningen körs säkerhets kopieringarna bara från noderna i samma region där säkerhets kopian har kon figurer ATS. Detta beror på att säkerhets kopiering mellan regioner inte stöds. Om du bara har två noder och den sekundära noden finns i den andra regionen fortsätter säkerhets kopieringarna att köras från den primära noden (om inte säkerhets kopierings inställningen är sekundär).
* Om en nod växlar över till en annan region än den där säkerhets kopian har kon figurer ATS, Miss lyckas säkerhets kopieringarna på noderna i den felande regionen.

Beroende på vilka säkerhets kopierings-och säkerhets kopierings typer (fullständig/differentiell/Logga/kopiera endast fullständig), tas säkerhets kopiorna från en viss nod (primär/sekundär).

#### <a name="backup-preference-primary"></a>Säkerhets kopierings inställning: primär

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logga |  Primär
Copy-Only fullständig |  Primär

#### <a name="backup-preference-secondary-only"></a>Inställningar för säkerhets kopiering: endast sekundär

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logga |  Sekundär
Copy-Only fullständig |  Sekundär

#### <a name="backup-preference-secondary"></a>Säkerhets kopierings inställning: sekundär

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logga |  Sekundär
Copy-Only fullständig |  Sekundär

#### <a name="no-backup-preference"></a>Ingen säkerhets kopierings inställning

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logga |  Sekundär
Copy-Only fullständig |  Sekundär

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar en SQL Server databas](backup-azure-sql-database.md) som körs på en virtuell Azure-dator.