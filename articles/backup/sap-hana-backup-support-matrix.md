---
title: Stödmatris för SAP HANA-säkerhetskopiering
description: I den här artikeln kan du läsa om scenarier och begränsningar som stöds när du använder Azure-säkerhetskopiering för att säkerhetskopiera SAP HANA-databaser på virtuella Azure-datorer.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252446"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Supportmatris för säkerhetskopiering av SAP HANA-databaser på virtuella Azure-datorer

Azure Backup stöder säkerhetskopiering av SAP HANA-databaser till Azure. Den här artikeln sammanfattar de scenarier som stöds och begränsningar som finns när du använder Azure Backup för att säkerhetskopiera SAP HANA-databaser på virtuella Azure-datorer.

> [!NOTE]
> Frekvensen för logbackup kan nu ställas in på minst 15 minuter. Loggsäkerhetskopior börjar flöda först när en lyckad fullständig säkerhetskopiering för databasen har slutförts.

## <a name="scenario-support"></a>Scenariostöd

| **Scenario**               | **Konfigurationer som stöds**                                | **Konfigurationer som inte stöds**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologi**               | SAP HANA körs endast i virtuella Azure Linux-datorer                    | Stora HANA-instanser (HLI)                                   |
| **Geos**                   | **Ga:**<br> **Amerika** – centrala USA, östra USA 2, östra USA, norra centrala USA, södra centrala USA, västra USA 2, västra centrala USA, västra USA, Kanada Central, Östra Kanada, Södra Brasilien <br> **Asien och Stillahavsområdet** – Australien Central, Australien Central 2, Östra Australien, Sydöstra Australien, Östra Japan, Japan Väst, Korea Central, Korea Syd-, Östasien, Sydostasien, Centrala Indien, Södra Indien, Västra Indien, Östra Kina, Kina Nord, Kina East2, China North 2 <br> **Europa** – Västeuropa, Norra Europa, Frankrike Central, Storbritannien Syd, Storbritannien Väst, Tyskland Norr, Tyskland Västcentralen, Schweiz North, Schweiz West, Centrala Schweiz North <br> **Afrika / ME** - Sydafrika North, Sydafrika Väst, UAE North, UAE Central  <BR>  **Azure Government-regioner** | Frankrike Södra, Tyskland Central, Tyskland Nordost, US Gov IOWA |
| **OS-versioner**            | SLES 12 med SP2, SP3 eller SP4; SLES 15 med SP1                              | RHEL                                                |
| **HANA-versioner**          | SDC på HANA 1.x, MDC på HANA 2.x <= SPS04 Rev 46       | -                                                            |
| **HANA-distributioner**       | SAP HANA på en enda Azure VM - Skala upp bara. <br><br> För distributioner med hög tillgänglighet behandlas båda noderna på de två olika datorerna som enskilda noder med separata datakedjor.               | Utskalning <br><br> Vid distributioner med hög tillgänglighet redundans inte säkerhetskopiering till den sekundära noden automatiskt. Konfigurera säkerhetskopiering bör göras separat för varje nod.                                           |
| **HANA-instanser**         | En enda SAP HANA-instans på en enda Azure-virtuell dator – skala endast upp | Flera SAP HANA-instanser på en enda virtuell dator                  |
| **HANA-databastyper**    | Behållare för en enda databas (SDC) ON 1.x, BEHÅLLARE för flera databaser (MDC) på 2.x | MDC i HANA 1.x                                              |
| **HANA-databasstorlek**     | 2 TB full säkerhetskopieringsstorlek som rapporterats av HANA)                   |                                                              |
| **Typer av säkerhetskopiering**           | Fullständiga säkerhetskopieringar och loggsäkerhetskopior                          | Inkrementella, ögonblicksbilder                                       |
| **Återställa typer**          | Läs SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) om du vill veta mer om de återställningstyper som stöds |                                                              |
| **Begränsningar för säkerhetskopiering**          | Upp till 2 TB full säkerhetskopieringsstorlek per SAP HANA-instans         |                                                              |
| **Särskilda konfigurationer** |                                                              | SAP HANA + Dynamisk nivåindelning <br>  Kloning genom LaMa        |

------

> [!NOTE]
> Säkerhetskopierings- och återställningsåtgärder från SAP HANA-inbyggda klienter (SAP HANA Studio/ Cockpit/ DBA Cockpit) stöds för närvarande inte.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [säkerhetskopierar SAP HANA-databaser som körs på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Lär dig hur du [återställer SAP HANA-databaser som körs på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Lär dig hur du [hanterar SAP HANA-databaser som säkerhetskopieras med Azure Backup](sap-hana-db-manage.md)
* Lär dig hur du [felsöker vanliga problem när du säkerhetskopierar SAP HANA-databaser](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
