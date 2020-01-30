---
title: Stödmatris för SAP HANA-säkerhetskopiering
description: I den här artikeln lär du dig om de scenarier och begränsningar som stöds när du använder Azure Backup för att säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 82d844385290ab0dc2953537c1f9a3387dd7b2b2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842639"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Supportmatris för säkerhetskopiering av SAP HANA-databaser på virtuella Azure-datorer

Azure Backup stöder säkerhets kopiering av SAP HANA-databaser till Azure. Den här artikeln sammanfattar scenarier som stöds och begränsningar som finns när du använder Azure Backup för att säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer.

## <a name="onboard-to-the-public-preview"></a>Publicera till den offentliga för hands versionen

Publicera till den offentliga för hands versionen enligt följande:

* I portalen registrerar du ditt prenumerations-ID till Recovery Services tjänst leverantören genom att [följa den här artikeln](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* För PowerShell kör du denna cmdlet. Den bör slutföras som "registrerad".

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> Frekvensen av logg säkerhets kopieringen kan nu anges till minst 15 minuter. Logg säkerhets kopior börjar bara att flyta efter en lyckad fullständig säkerhets kopiering för databasen har slutförts.

## <a name="scenario-support"></a>Scenariostöd

| **Scenario**               | **Konfigurationer som stöds**                                | **Konfigurationer som inte stöds**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologi**               | Endast SAP HANA som körs i virtuella Azure Linux-datorer                    | HANA stora instanser (HLI)                                   |
| **Geografiska områden**                   | **GA**<br />**Europa** – Västeuropa, Nord Europa, Frankrike, centrala, Storbritannien, södra, Storbritannien, västra, Tyskland, norra, Tyskland, västra centrala, Schweiz, norra, Schweiz, västra<br />**Asien och Stillahavsområdet** – Australien, centrala Australien 2, östra Australien, sydöstra Australien, Östra Japan, västra Japan, centrala Korea, södra Korea<br /><br>**Förhandsgranskningsvyn**<br />**Amerika** – centrala USA, östra USA 2, östra USA, norra centrala USA, södra centrala USA, västra USA 2, västra centrala USA, västra USA, centrala Kanada, Östra Kanada <br />**Asien och Stillahavsområdet** – Asien, östra, Sydostasien, Central Indien, södra Indien | Kina, östra, Kina, norra, Kina östra, Kina, norra 2, västra Indien, Central Schweiz, norra, sydöstra Sydafrika, västra Sydafrika, västra Förenade Arabemiraten, norra Förenade Arabemiraten Central, Azure Government regioner, södra Brasilien, södra Brasilien |
| **OS-versioner**            | SLES 12 med SP2, SP3 eller SP4                                | SLES 15, RHEL                                                |
| **HANA-versioner**          | SDC på HANA 1. x, MDC på HANA 2. x < = SPS04 rev 44            | -                                                            |
| **HANA-distributioner**       | SAP HANA på en enda virtuell Azure-dator – skala upp               | Skalbarhet                                                    |
| **HANA-instanser**         | En enda SAP HANA instans på en enda virtuell Azure-dator – skala upp | Flera SAP HANA-instanser på en enskild virtuell dator                  |
| **HANA-databas typer**    | Enkel databas container (SDC) på 1. x, MDC (Multi-Database container) på 2. x | MDC i HANA 1. x                                              |
| **HANA-databasens storlek**     | 2 – TB fullständig säkerhets kopierings storlek enligt vad som rapporteras av HANA)                   |                                                              |
| **Säkerhets kopierings typer**           | Fullständiga, differentiella och logg säkerhets kopior                          | Stegvisa ögonblicks bilder                                       |
| **Återställnings typer**          | Läs SAP HANA anmärkning [1642148](https://launchpad.support.sap.com/#/notes/1642148) om du vill veta mer om vilka återställnings typer som stöds |                                                              |
| **Säkerhets kopierings gränser**          | Upp till 2 TB fullständig säkerhets kopierings storlek per SAP HANA instans         |                                                              |
| **Särskilda konfigurationer** |                                                              | SAP HANA + dynamisk nivå <br>  Kloning via LaMa        |

------

> [!NOTE]
> Säkerhets kopierings-och återställnings åtgärder från SAP HANA interna klienter (SAP HANA Studio/cockpit/DBA cockpit) stöds inte för närvarande.



## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [Säkerhetskopierar SAP HANA databaser som körs på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Lär dig hur du [återställer SAP HANA databaser som körs på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Lär dig hur du [hanterar SAP HANA databaser som säkerhets kopie ras med Azure Backup](sap-hana-db-manage.md)
* Lär dig hur du [felsöker vanliga problem när du säkerhetskopierar SAP HANA databaser](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
