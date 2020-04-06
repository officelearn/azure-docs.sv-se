---
title: Felsöka replikering av virtuella Azure-datorer med Azure Site Recovery
description: Felsöka replikering i Azure VM-haveriberedskap med Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: 8cba02d3c7d1e649853570b199b646b1c4dcce2d
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667405"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Felsöka replikering i Azure VM-haveriberedskap

I den här artikeln beskrivs vanliga problem i Azure Site Recovery när du replikerar och återställer virtuella Azure-datorer (VM) från en region till en annan region. Det förklarar också hur du felsöker de vanliga problemen. Mer information om konfigurationer som stöds finns i [supportmatrisen för replikering av virtuella Azure-datorer](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery replikerar konsekvent data från källregionen till katastrofåterställningsregionen. Det skapar också en krasch-konsekvent återställningspunkt var 5 minuter. Om site recovery inte kan skapa återställningspunkter i 60 minuter, varnar den dig med den här informationen:

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

I följande avsnitt beskrivs orsaker och lösningar.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>Hög dataändringstakt på den virtuella källdatorn

Azure Site Recovery skapar en händelse om dataändringshastigheten på källvurdatorn är högre än gränserna som stöds. Om du vill se om problemet beror på hög omsättning går du till > **VM-händelser** >  **för replikerade objekt****- senaste 72 timmarna**.
Du bör se **händelsedataändringshastigheten utöver de gränser som stöds:**

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="Sidan Azure Site Recovery som visar en hög dataförändringshastighet som är för hög.":::

Om du väljer händelsen bör du se den exakta diskinformationen:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="Sida som visar händelseinformationen för dataändringshastighet.":::

### <a name="azure-site-recovery-limits"></a>Gränser för Azure Site Recovery

Följande tabell innehåller gränserna för Azure Site Recovery. Dessa gränser baseras på våra tester, men de kan inte täcka alla möjliga i/o-kombinationer (Application Input-Output). De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program.

Det finns två gränser att tänka på: dataomsättning per disk och dataomsättning per virtuell dator. Låt oss titta på Premium P20-disken i följande tabell för ett exempel. För en enda virtuell dator kan Site Recovery hantera 5 MB/s omsättning per disk med högst fem sådana diskar. Site Recovery har en gräns på 25 MB /s av den totala omsättningen per virtuell dator.

**Replikeringslagringsmål** | **Genomsnittlig I/O-storlek för källdisk** |**Genomsnittlig dataomsättning för källdisken** | **Total dataomsättning per dag för källdatadisk**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |    336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB |20 MB/s | 1684 GB per disk

### <a name="solution"></a>Lösning

Azure Site Recovery har begränsningar för dataändringshastigheter, beroende på vilken typ av disk. Om du vill se om problemet är återkommande eller tillfälligt hittar du dataändringshastigheten för den berörda virtuella datorn. Gå till den virtuella källdatorn, leta reda på måtten under **Övervakning**och lägg till måtten som visas i den här skärmbilden:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="Sida som visar trestegsprocessen för att hitta dataändringshastigheten.":::

1. Välj **Lägg till mått**och lägg till **OS-diskskrivningsbyten/sek** och **datadiskskrivningsbyten/sek**.
1. Övervaka spiken som visas i skärmdumpen.
1. Visa de totala skrivåtgärderna som sker på OS-diskar och alla datadiskar tillsammans. Dessa mått kanske inte ger dig information på disknivå, men de anger det totala mönstret för dataomsättning.

En ökning av dataändringshastigheten kan komma från en tillfällig datasprängning. Om dataändringshastigheten är större än 10 MB/s (för Premium) eller 2 MB/s (för standard) och kommer ner, kommer replikeringen att komma ikapp. Om omsättningen är konsekvent långt över den gräns som stöds bör du överväga något av följande alternativ:

- Uteslut disken som orsakar en hög dataändringshastighet: Inaktivera först replikeringen. Sedan kan du utesluta disken med [powershell](azure-to-azure-exclude-disks.md).
- Ändra nivån på lagringsdisken för haveriberedskap: Det här alternativet är endast möjligt om diskdataomsättningen är mindre än 20 MB/s. En virtuell dator med en P10-disk har till exempel en dataomsättning på mer än 8 MB/s men mindre än 10 MB/s. Om kunden kan använda en P30-disk för mållagring under skydd kan problemet lösas. Den här lösningen är endast möjlig för datorer som använder Premium-hanterade diskar. Följ de här stegen:

  1. Gå till **Diskar** för den berörda replikerade datorn och kopiera replikdisknamnet.
  1. Gå till den här repliken av den hanterade disken.
  1. Du kan se en banderoll i **Översikt som** säger att en SAS-URL har genererats. Välj den här banderollen och avbryt exporten. Ignorera det här steget om du inte ser banderollen.
  1. Så snart SAS-URL:en har återkallats går du till **Konfiguration** för den hanterade disken. Öka storleken så att Site Recovery stöder den observerade omsättningshastigheten på källdisken.

## <a name="network-connectivity-problems"></a>Problem med nätverksanslutningen

### <a name="network-latency-to-a-cache-storage-account"></a>Nätverksfördröjning till ett cachelagringskonto

Site Recovery skickar replikerade data till cachelagringskontot. Nätverksfördröjningen kan uppstå om det är långsammare att överföra data från en virtuell dator till cachelagringskontot än 4 MB på 3 sekunder.

Om du vill kontrollera om det finns ett problem relaterat till svarstid använder du [AzCopy](/azure/storage/common/storage-use-azcopy). Du kan använda det här kommandoradsverktyget för att överföra data från den virtuella datorn till cachelagringskontot. Om svarstiden är hög kontrollerar du om du använder en virtuell nätverksinstallation (NVA) för att styra utgående nätverkstrafik från virtuella datorer. Apparaten kan få strypt om all replikeringstrafik passerar genom NVA.

Vi rekommenderar att du skapar en slutpunkt för nätverkstjänsten i det virtuella nätverket för "Lagring" så att replikeringstrafiken inte går till NVA. Mer information finns i [Konfigurationen för virtuell nätverksinstallation](azure-to-azure-about-networking.md#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Nätverksanslutningar

För att replikering av platsåterställning ska fungera måste den virtuella datorn tillhandahålla utgående anslutning till specifika webbadresser eller IP-intervall. Du kan ha den virtuella datorn bakom en brandvägg eller använda NSG-regler (Network Security Group) för att styra utgående anslutning. Om så är fallet kan du få problem. Se [Utgående anslutning för webbadresser](azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>Fel-ID 153006 - Ingen appkonsekvent återställningspunkt tillgänglig för den virtuella datorn under de senaste "X"-minuterna

Följande är några av de vanligaste problemen.

### <a name="known-issue-in-sql-server-20082008-r2"></a>Känt problem i SQL server 2008/2008 R2

**Så här fixar du:** Det finns ett känt problem med SQL Server 2008/2008 R2. I artikeln [Azure Site Recovery Agent eller annan VSS-säkerhetskopiering som inte ärkomponent misslyckas det för en server som är värd för SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Azure Site Recovery-jobb misslyckas på servrar som är värdar för alla versioner av SQL Server-instanser med AUTO_CLOSE DBs

**Så här fixar du:** Se artikeln [VSS-säkerhetskopior som inte är komponenter, till exempel Azure Site Recovery-jobb, misslyckas på servrar som är värdar för SQL Server-instanser med AUTO_CLOSE DBs](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).

### <a name="known-issue-in-sql-server-2016-and-2017"></a>Kända problem i SQL Server 2016 och 2017

**Åtgärda:** Se artikeln [Kumulativ uppdatering 16 för SQL Server 2017](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017).

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Du använder Azure Storage Spaces Direct-konfiguration

**Åtgärda:** Azure Site Recovery kan inte skapa programkonsekvent återställningspunkt för Direct-konfiguration för lagringsutrymmen. [Konfigurera replikeringsprincipen](azure-to-azure-how-to-enable-replication-s2d-vms.md).

### <a name="more-causes-because-of-vss-related-issues"></a>Fler orsaker på grund av VSS-relaterade problem:

Om du vill felsöka ytterligare kontrollerar du filerna på källmaskinen för att få den exakta felkoden för fel:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

Om du vill hitta felen öppnar du _filen vacp.log_ i en textredigerare-sökning efter strängen **vacpError**.

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

I föregående exempel är **2147754994** felkoden som berättar om felet efter den här meningen.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS-brännare är inte installerad - Fel 2147221164

**Åtgärda:** För att generera programkonsekvenstagg använder Azure Site Recovery VSS (Volume Shadow Copy Service). Site Recovery installerar en VSS-provider för dess funktion för att ta ögonblicksbilder av appkonsekvenser. Azure Site Recovery installerar den här VSS-providern som en tjänst. Om VSS-providern inte är installerad misslyckas skapandet av ögonblicksbilder av programkonsekvens. Den visar **fel-ID 0x80040154-klassen har inte registrerats**. Se artikeln för felsökning av [VSS-brännareinstallation](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS-brännare är inaktiverad - Fel 2147943458

**Så här åtgärdar du**: För att generera programkonsekvenstaggen använder Azure Site Recovery VSS. Site Recovery installerar en VSS-provider för dess funktion för att ta ögonblicksbilder av appkonsekvenser. Denna VSS-provider installeras som en tjänst. Om du inte har vss-providertjänsten aktiverad misslyckas skapandet av ögonblicksbilder av programsekvenser. Den visar felet: **Den angivna tjänsten är inaktiverad och kan inte startas (0x80070422)**.

Om VSS är inaktiverat:

- Kontrollera att starttypen för VSS Provider-tjänsten är inställd **på Automatisk**.
- Starta om följande tjänster:
  - VSS-tjänst.
  - Azure Site Recovery VSS-provider.
  - VDS-tjänst.

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS-LEVERANTÖR NOT_REGISTERED - Fel 2147754756

**Så här åtgärdar du**: För att generera programkonsekvenstaggen använder Azure Site Recovery VSS. Kontrollera om tjänsten Azure Site Recovery VSS Provider är installerad.

Använd följande kommandon för att installera om VSS Provider:

1. Avinstallera befintlig provider:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. Installera om VSS-provider:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Kontrollera att starttypen för VSS Provider-tjänsten är inställd **på Automatisk**.

Starta om följande tjänster:

- VSS-tjänst.
- Azure Site Recovery VSS-provider.
- VDS-tjänst.
