---
title: Azure Backup-supportmatris för SQL Server-säkerhetskopiering i virtuella Azure-datorer
description: Ger en sammanfattning av supportinställningar och begränsningar när du säkerhetskopierar SQL Server i virtuella Azure-datorer med Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 79a7e30ab9240c489a66b547ff85bea7887131b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79410004"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Stödmatris för SQL Server-säkerhetskopiering i virtuella Azure-datorer

Du kan använda Azure Backup för att säkerhetskopiera SQL Server-databaser i virtuella Azure-datorer som finns på Microsoft Azure-molnplattformen. Den här artikeln sammanfattar allmänna supportinställningar och begränsningar för scenarier och distributioner av SQL Server Backup i virtuella Azure-datorer.

## <a name="scenario-support"></a>Scenariostöd

**Support** | **Detaljer**
--- | ---
**Distributioner som stöds** | Virtuella SQL Marketplace Azure-datorer och virtuella icke-Marketplace-datorer (manuellt installerat SQL Server) stöds.
**Geografiska områden som stöds** | Australien South East (ASE), East Australia (AE), Australia Central (AC), Australia Central 2 (AC) <br> Brasilien, södra (BRS)<br> Kanada Central (CNC), Östra Kanada (CE)<br> Sydostasien, Östasien (EA) <br> Östra USA (EUS), Östra USA 2 (EUS2), Västra centrala USA (WCUS), Västra USA (WUS); Västra US 2 (WUS 2) Norra centrala USA (NCUS) Centrala USA (CUS) Södra centrala USA (SCUS) <br> Indien Central (INC), Södra Indien (INS), Västra Indien <br> Japan Öst (JPE), Västra Japan (JPW) <br> Korea Central (KRC), Sydkorea South (KRS) <br> Norra Europa (NE), Västeuropa <br> Storbritannien South (UKS), Västra Storbritannien (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD Central, US DoD East <br> Tyskland North, Tyskland Västra Central <br> Schweiz North, Schweiz Väst <br> Frankrike, centrala <br> Kina Öst, Kina Öst 2, Kina Nord, Kina Nord 2
**Operativsystem som stöds** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux stöds inte för närvarande.
**SQL Server-versioner som stöds** | SQL Server 2019, SQL Server 2017 som beskrivs på [sidan Sökproduktlivscykel,](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)SQL Server 2016 och SPs enligt beskrivningen på [sidan Sökproduktlivscykel](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.
**Stöds .NET-versioner** | .NET Framework 4.5.2 eller senare installerad på den virtuella datorn

## <a name="feature-consideration-and-limitations"></a>Funktionsövervägande och begränsningar

* SQL Server-säkerhetskopiering kan konfigureras i Azure-portalen eller **PowerShell**. Vi stöder inte CLI.
* Lösningen stöds på båda [typerna](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) av distributioner - virtuella Azure Resource Manager-datorer och klassiska virtuella datorer.
* VM som kör SQL Server kräver internetanslutning för att komma åt offentliga Azure-IP-adresser.
* SQL Server **Redundansklusterinstans (FCI)** stöds inte.
* Säkerhetskopiering och återställning av åtgärder för spegeldatabaser och ögonblicksbilder av databaser stöds inte.
* Om du använder mer än en säkerhetskopieringslösningar för att säkerhetskopiera din fristående SQL Server-instans eller SQL Always on availability group kan det leda till säkerhetskopieringsfel. avstå från att göra detta.
* Säkerhetskopierar två noder i en tillgänglighetsgrupp individuellt med samma eller olika lösningar, kan också leda till säkerhetskopieringsfel.
* Azure Backup stöder endast fullständiga och kopierar fullständiga säkerhetskopieringstyper för **skrivskyddade** databaser
* Databaser med ett stort antal filer kan inte skyddas. Det maximala antalet filer som stöds är **~1000**.  
* Du kan säkerhetskopiera upp till **~2000** SQL Server-databaser i ett valv. Du kan skapa flera valv om du har ett större antal databaser.
* Du kan konfigurera säkerhetskopiering för upp till **50** databaser på en gång. Den här begränsningen hjälper till att optimera säkerhetskopieringsbelastningar.
* Vi stöder databaser upp till **2 TB** i storlek; för storlekar som är större än att prestandaproblem kan uppstå.
* Om du vill ha en känsla för hur många databaser som kan skyddas per server bör du ta hänsyn till faktorer som bandbredd, VM-storlek, säkerhetskopieringsfrekvens, databasstorlek och så vidare. [Hämta](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) resursplaneraren för att beräkna det ungefärliga antalet databaser som du kan ha per server baserat på vm-resurserna och säkerhetskopieringsprincipen.
* När tillgänglighetsgrupper konfigureras hämtas säkerhetskopior från de olika noderna baserat på några faktorer. Säkerhetskopieringsbeteendet för en tillgänglighetsgrupp sammanfattas nedan.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Säkerhetskopiera beteende med Alltid på tillgänglighetsgrupper

Vi rekommenderar att säkerhetskopian är konfigurerad på endast en nod i en tillgänglighetsgrupp (AG). Konfigurera alltid säkerhetskopiering i samma region som den primära noden. Med andra ord behöver du alltid den primära noden för att finnas i den region där du konfigurerar säkerhetskopieringen. Om alla noder i AG finns i samma region där säkerhetskopian är konfigurerad finns det inget problem.

#### <a name="for-cross-region-ag"></a>För gränsöverskridande AG

* Oavsett inställningen för säkerhetskopiering körs säkerhetskopieringar endast från noderna som finns i samma region där säkerhetskopian är konfigurerad. Detta beror på att säkerhetskopieringar mellan regioner inte stöds. Om du bara har två noder och den sekundära noden finns i den andra regionen fortsätter säkerhetskopiorna att köras från den primära noden (såvida inte inställningen för säkerhetskopiering är "endast sekundär").
* Om en nod växlar över till en annan region än den där säkerhetskopian är konfigurerad, misslyckas säkerhetskopieringar på noderna i den misslyckade regionen.

Beroende på inställningen för säkerhetskopiering och typer av säkerhetskopior (full/differential/log/copy-only full) hämtas säkerhetskopior från en viss nod (primär/sekundär).

#### <a name="backup-preference-primary"></a>Inställningar för säkerhetskopiering: Primär

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logga |  Primär
Endast kopia full |  Primär

#### <a name="backup-preference-secondary-only"></a>Inställningar för säkerhetskopiering: Endast sekundärt

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logga |  Sekundär
Endast kopia full |  Sekundär

#### <a name="backup-preference-secondary"></a>Inställningar för säkerhetskopiering: Sekundär

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logga |  Sekundär
Endast kopia full |  Sekundär

#### <a name="no-backup-preference"></a>Ingen inställning för säkerhetskopiering

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logga |  Sekundär
Endast kopia full |  Sekundär

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar en SQL Server-databas](backup-azure-sql-database.md) som körs på en Virtuell Azure.Learn how to back up a SQL Server database that's running on an Azure VM.
