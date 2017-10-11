---
title: "Felsöka Azure backup i klassiska portal | Microsoft Docs"
description: "Felsöka Azure-säkerhetskopiering och återställning av virtuella Azure-datorer i den klassiska portalen."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 117201fb-c0cd-4be4-b47f-abd88fe914cf
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: trinadhk;markgal;
ms.openlocfilehash: 284a1b64fbb15d0aa800182c6671d447e191b76a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Felsöka säkerhetskopiering av virtuell Azure-dator
> [!div class="op_single_selector"]
> * [Recovery services-valvet](backup-azure-vms-troubleshoot.md)
> * [Säkerhetskopieringsvalvet](backup-azure-vms-troubleshoot-classic.md)
>
>

Du kan felsöka fel påträffades när med Azure Backup med information som visas i tabellen nedan.

## <a name="discovery"></a>Identifiering
| Säkerhetskopieringen | information om fel | Lösning |
| --- | --- | --- |
| Identifiering |Det gick inte att identifiera nya objekt – Microsoft Azure Backup påträffade och internt fel. Vänta några minuter och försök sedan igen. |Försök identifieringsprocessen efter 15 minuter. |
| Identifiering |Det gick inte att identifiera nya objekt – identifiering av en annan åtgärd pågår redan. Vänta tills den aktuella Discovery-åtgärden har slutförts. |Ingen |

## <a name="register"></a>Registrera dig
| Säkerhetskopieringen | information om fel | Lösning |
| --- | --- | --- |
| Registrera dig |Antalet datadiskar som är kopplade till den virtuella datorn överskrider gränsen som stöds – ange koppla vissa datadiskar på den virtuella datorn och försök igen. Azure backup stöder upp till 16 datadiskar som är kopplad till en virtuell Azure-dator för säkerhetskopiering |Ingen |
| Registrera dig |Microsoft Azure Backup påträffade ett internt fel - Vänta några minuter och försök sedan igen. Kontakta Microsoft-supporten om problemet kvarstår. |Du kan få detta fel på grund av följande stöds inte konfigurationen för den virtuella datorn på Premium LRS. <br> Premium-lagring virtuella datorer kan säkerhetskopieras med recovery services-valvet. [Läs mer](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup) |
| Registrera dig |Det gick inte att registrera med installera Agent tidsgräns för åtgärd |Kontrollera om det finns stöd för OS-versionen av den virtuella datorn. |
| Registrera dig |Kommandot körningen misslyckades - en annan åtgärd pågår på det här objektet. Vänta tills den föregående åtgärden har slutförts |Ingen |
| Registrera dig |Virtuella datorer med virtuella hårddiskar lagrade på Premium-lagring stöds inte för säkerhetskopiering |Ingen |
| Registrera dig |Virtuella datorns agent är inte installerat på den virtuella datorn – installera den nödvändiga krav VM-agenten och gör om åtgärden. |[Läs mer](#vm-agent) om VM agentinstallation och hur du verifierar installationen av virtuell dator. |

## <a name="backup"></a>Säkerhetskopiering
| Säkerhetskopieringen | information om fel | Lösning |
| --- | --- | --- |
| Säkerhetskopiering |Det gick inte att kommunicera med den virtuella datoragenten för ögonblicksbildsstatus. Ögonblicksbild tidsgränsen VM nåddes. -Finns i felsökningsguiden för hur du löser problemet. |Det här felet returneras om det uppstår ett problem med den Virtuella Datoragenten eller nätverksåtkomst till Azure-infrastrukturen är blockerad på något sätt. Lär dig mer om [felsökning in VM ögonblicksbild problem](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Om den Virtuella datoragenten inte orsakar problem, och starta sedan om den virtuella datorn. Ett felaktigt tillstånd för virtuell dator kan ibland orsaka problem och starta om den virtuella datorn återgår den här ”dåligt tillstånd” |
| Säkerhetskopiering |Det gick inte att säkerhetskopiera med ett internt fel - försök igen om några minuter. Kontakta Microsoft Support om problemet kvarstår |Kontrollera om det finns ett övergående problem med att komma åt VM-lagring. Kontrollera [Azure Status](https://azure.microsoft.com/en-us/status/) att se om det finns några pågående problem relaterade till beräkning / / lagringsnätverk i region. Gör säkerhetskopiering efter problemet minskas. |
| Säkerhetskopiering |Det gick inte att utföra åtgärden eftersom den virtuella datorn inte finns längre. |Säkerhetskopieringen kan inte utföras eftersom den virtuella datorn som konfigurerats för säkerhetskopiering har tagits bort. Stoppa ytterligare säkerhetskopieringar genom att gå till vyn över skyddade objekt, Välj skyddade objektet och klicka på Stoppa skydd. Du kan behålla data genom att välja alternativet för säkerhetskopiering behålla data. Du kan senare Återuppta skydd för den här virtuella datorn genom att klicka på Konfigurera skydd från vyn registrerade objekt |
| Säkerhetskopiering |Det gick inte att installera Azure Recovery Services är tillägget för det markerade objektet - VM-agenten ett krav för Azure Recovery Services-tillägget. Installera Azure VM-agenten och gör om åtgärden registrering |<ol> <li>Kontrollera om VM-agenten har installerats korrekt. <li>Se till att flaggan på VM-konfigurationen är korrekt.</ol> [Läs mer](#validating-vm-agent-installation) om VM agentinstallation och hur du verifierar installationen av virtuell dator. |
| Säkerhetskopiering |Kommandot körningen misslyckades - en annan åtgärd pågår på det här objektet. Vänta tills den föregående åtgärden har slutförts och försök sedan igen |En befintlig säkerhetskopia eller återställningsjobbet för den virtuella datorn körs och går inte att starta ett nytt jobb medan befintliga jobbet körs. |
| Säkerhetskopiering |Installation av webbprogramtillägg misslyckades med felet ”COM + kunde inte kommunicera med Microsoft Distributed Transaction Coordinator |Detta innebär vanligen att COM +-tjänsten inte körs. Kontakta Microsoft support för hjälp med att åtgärda problemet. |
| Säkerhetskopiering |Snapshot-åtgärden misslyckades med fel i VSS ”den här enheten är låst av BitLocker-diskkryptering. Du måste låsa upp enheten från Kontrollpanelen. |Inaktivera BitLocker för alla enheter på den virtuella datorn och notera om VSS problemet är löst |
| Säkerhetskopiering |Virtuella datorer med virtuella hårddiskar lagrade på Premium-lagring stöds inte för säkerhetskopiering |Ingen |
| Säkerhetskopiering |Virtuell Azure-dator inte att hitta. |Detta händer när den primära virtuella datorn tas bort men säkerhetskopieringsprincipen fortsätter att söka efter en virtuell dator att utföra säkerhetskopiering. Åtgärda det här felet: <ol><li>Skapa den virtuella datorn med samma namn och samma resursgruppens namn [cloud service name] <br>(ELLER) <li> Inaktivera skyddet för den här virtuella datorn så att efterföljande säkerhetskopior kommer hämta inte aktiveras. </ol> |
| Säkerhetskopiering |Virtuella datorns agent är inte installerat på den virtuella datorn – installera den nödvändiga krav VM-agenten och gör om åtgärden. |[Läs mer](#vm-agent) om VM agentinstallation och hur du verifierar installationen av virtuell dator. |

## <a name="jobs"></a>Jobb
| Åtgärd | information om fel | Lösning |
| --- | --- | --- |
| Avbryta jobb |Annullering stöds inte för den här jobbtypen - vänta tills jobbet har slutförts. |Ingen |
| Avbryta jobb |Jobbet är inte i tillståndet cancelable - vänta tills jobbet har slutförts. <br>ELLER<br> Det valda jobbet är inte i tillståndet cancelable - vänta tills jobbet ska slutföras. |Med största sannolikhet slutförs nästan jobbet. Vänta tills jobbet har slutförts |
| Avbryta jobb |Det går inte att avbryta jobbet eftersom det pågår inte – annullering stöds bara för jobb som pågår. Försök avbryter på ett pågående jobb. |Detta inträffar på grund av ett övergående tillstånd. Vänta en stund och försök på Avbryt |
| Avbryta jobb |Det gick inte att avbryta jobbet - vänta tills jobbet har slutförts. |Ingen |

## <a name="restore"></a>Återställ
| Åtgärd | information om fel | Lösning |
| --- | --- | --- |
| Återställ |Det gick inte att återställa med molnet internt fel |<ol><li>Molntjänst som du försöker återställa har konfigurerats med DNS-inställningarna. Du kan kontrollera <br>$deployment = get-AzureDeployment - ServiceName ”ServiceName”-fack ”produktion” Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Om adressen konfigurerad, innebär det att DNS-inställningarna har konfigurerats.<br> <li>Molntjänst som du försöker återställa har konfigurerats med ReservedIP och befintliga virtuella datorer i Molntjänsten har stoppats.<br>Du kan kontrollera en molnbaserad tjänst har reserverade IP-Adressen med hjälp av följande powershell-cmdlets:<br>$deployment = get-AzureDeployment - ServiceName ”servicename”-fack ”produktion” $dep. ReservedIPName <br><li>Du försöker återställa en virtuell dator med följande särskilda nätverkskonfigurationer i samma molntjänst. <br>-Virtuella datorer under konfigurationen av belastningsutjämnaren (interna och externa)<br>-Virtuella datorer med flera reserverade IP:<br>-Virtuella datorer med flera nätverkskort<br>Välj en ny molntjänst i Användargränssnittet eller se [återställa överväganden](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations) för virtuella datorer med särskilda nätverkskonfigurationer</ol> |
| Återställ |Det valda DNS-namnet är upptaget – ange ett annat DNS-namn och försök igen. |DNS-namnet här refererar till molntjänstnamnet (vanligtvis slutar med. cloudapp.net). Detta måste vara unika. Om du får det här felet måste du välja ett annat VM-namn under återställningen. <br><br> Det här felet visas endast på användare i Azure-portalen. Återställningsåtgärden via PowerShell lyckas eftersom det endast återställer diskarna och inte skapa den virtuella datorn. Felet kommer riktas när den virtuella datorn skapas av du uttryckligen när disken återställningsåtgärden. |
| Återställ |Den angivna virtuella nätverkskonfigurationen är inte korrekt – ange ett annat virtuellt nätverk-konfigurationen och försök igen. |Ingen |
| Återställ |Den angivna Molntjänsten använder en reserverad IP-adress, som inte överensstämmer med konfigurationen av den virtuella datorn återställs – ange en annan molntjänst som inte använder reserverade IP-adress eller välj en annan återställningspunkt att återställa från. |Ingen |
| Återställ |Molntjänsten har nått gränsen för antalet inkommande slutpunkter, försök igen genom att ange en annan molntjänst eller genom att använda en befintlig slutpunkt. |Ingen |
| Återställ |Backup-valvet och mål för storage-konto finns i två olika regioner och kontrollera att lagringskontot som angavs i restore-åtgärden är i samma Azure-region som säkerhetskopieringsvalvet. |Ingen |
| Återställ |Storage-konto som angetts för återställningen inte är stöds - endast grundläggande/Standard storage-konton med lokalt redundant eller inställningarna för geo-redundant replikering stöds. Välj ett lagringskonto som stöds |Ingen |
| Återställ |Typ av Lagringskonto som angetts för återställningen är inte online – se till att lagringskontot som angavs i restore-åtgärden är online |Detta kan inträffa på grund av ett tillfälligt fel i Azure Storage eller på grund av ett avbrott. Välj ett annat lagringskonto. |
| Återställ |Resursgruppen kvoten har nåtts – ta bort vissa resursgrupper från Azure-portalen eller kontakta Azure-supporten om du vill öka gränserna. |Ingen |
| Återställ |Valda undernätet finns inte - Välj ett undernät som finns |Ingen |

## <a name="policy"></a>Princip
| Åtgärd | information om fel | Lösning |
| --- | --- | --- |
| Skapa princip |Det gick inte att skapa principen - minska kvarhållningsalternativen att fortsätta med principkonfiguration. |Ingen |

## <a name="vm-agent"></a>VM-Agent
### <a name="setting-up-the-vm-agent"></a>Konfigurera VM-Agent
Normalt finns VM-agenten redan i virtuella datorer som har skapats från Azure-galleriet. Virtuella datorer som har migrerats från lokala Datacenter skulle inte ha VM-agenten installerad. För sådana virtuella datorer måste den Virtuella Datoragenten installeras explicit. Läs mer om [VM-agenten installeras på en befintlig virtuell dator](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

För virtuella Windows-datorer:

* Ladda ned och installera [agentens MSI-fil](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen.
* [Uppdatera VM-egenskapen](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att ange att agenten är installerad.

För Linux virtuella datorer:

* Installera senaste [Linux-agenten](https://github.com/Azure/WALinuxAgent) från github.
* [Uppdatera VM-egenskapen](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att ange att agenten är installerad.

### <a name="updating-the-vm-agent"></a>Uppdatera VM-agenten
För virtuella Windows-datorer:

* Det är enkelt att uppdatera VM-agenten. Du installerar bara om [binärfilerna för VM-agenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste dock se till att inga Säkerhetskopieringsåtgärden körs medan den Virtuella Datoragenten uppdateras.

För Linux virtuella datorer:

* Följ instruktionerna på [uppdatering Linux VM-agenten](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="validating-vm-agent-installation"></a>Verifiera installation av VM-Agent
Hur du kontrollerar agentversionen VM på virtuella Windows-datorer:

1. Logga in på den virtuella Azure-datorn och navigera till mappen *C:\WindowsAzure\Packages*. Du bör hitta filen WaAppAgent.exe.
2. Högerklicka på filen, gå till **Egenskaper** och välj fliken **Information**. Fältet produktversionen får vara 2.6.1198.718 eller högre
