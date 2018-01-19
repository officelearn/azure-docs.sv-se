---
title: "Felsöka Azure Backup-fel: Gäst Agent Status otillgänglig | Microsoft Docs"
description: "Symptom, orsaker och lösningar Azure Backup-fel som rör agent, tillägg, diskar"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
keywords: "Azure-säkerhetskopiering; VM-agent. Nätverksanslutningen;"
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: genli;markgal;sogup;
ms.openlocfilehash: 5eb326dfd89d9cc64eb0e05286e64c87e090e0a1
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="troubleshoot-azure-backup-failure-issues-with-agent-andor-extension"></a>Felsöka Azure Backup-fel: problem med agenten och/eller tillägg

Den här artikeln innehåller åtgärder för att hjälpa dig att lösa säkerhetskopiering fel som rör problem vid kommunikation med VM-agent och tillägg.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>VM-agenten kunde inte kommunicera med Azure Backup

> [!NOTE]
> Om säkerhetskopiorna virtuella Azure Linux-datorn har startats med det här felet på eller efter 4 januari 2018, kör följande kommando i de virtuella datorerna som påverkas och försök säkerhetskopieringar

    sudo rm -f /var/lib/waagent/*.[0-9]*.xml

När du registrerar och schemalägga en virtuell dator för Azure Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med den Virtuella datoragenten att ta en ögonblicksbild i tidpunkt. Något av följande villkor kan förhindra att ögonblicksbilden från som utlöses, vilket i sin tur kan leda till säkerhetskopiering misslyckades. Följ nedan felsökningssteg i angiven ordning och försök igen.

##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Orsak 1: [den virtuella datorn har ingen Internet-åtkomst](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Orsak 2: [agenten är installerad på den virtuella datorn men inte svarar (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Orsak 3: [agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Orsak 4: [går inte att hämta status för ögonblicksbild eller en ögonblicksbild kan inte utföras](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Orsak 5: [tillägget säkerhetskopiering misslyckas med att uppdatera eller läsa in](#the-backup-extension-fails-to-update-or-load)
##### <a name="cause-6-azure-classic-vms-may-require-additional-step-to-complete-registrationazure-classic-vms-may-require-additional-step-to-complete-registration"></a>Orsak 6: [klassiska virtuella Azure-datorer kan kräva ytterligare steg för att slutföra registreringen](#azure-classic-vms-may-require-additional-step-to-complete-registration)

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Snapshot-åtgärden misslyckades på grund av ingen nätverksanslutning på den virtuella datorn
När du registrerar och schemalägga en virtuell dator för Azure Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med VM-tillägg att ta en ögonblicksbild i tidpunkt för säkerhetskopiering. Något av följande villkor kan förhindra att ögonblicksbilden från som utlöses, vilket i sin tur kan leda till säkerhetskopiering misslyckades. Följ nedan felsökningssteg i angiven ordning och försök igen.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Orsak 1: [den virtuella datorn har ingen Internet-åtkomst](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Orsak 2: [går inte att hämta status för ögonblicksbild eller en ögonblicksbild kan inte utföras](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-3-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Orsak 3: [tillägget säkerhetskopiering misslyckas med att uppdatera eller läsa in](#the-backup-extension-fails-to-update-or-load)

## <a name="vmsnapshot-extension-operation-failed"></a>VMSnapshot tillägget misslyckades

När du registrerar och schemalägga en virtuell dator för Azure Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med VM-tillägg att ta en ögonblicksbild i tidpunkt för säkerhetskopiering. Något av följande villkor kan förhindra att ögonblicksbilden från som utlöses, vilket i sin tur kan leda till säkerhetskopiering misslyckades. Följ nedan felsökningssteg i angiven ordning och försök igen.
##### <a name="cause-1-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Orsak 1: [går inte att hämta status för ögonblicksbild eller en ögonblicksbild kan inte utföras](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-2-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Orsak 2: [tillägget säkerhetskopiering misslyckas med att uppdatera eller läsa in](#the-backup-extension-fails-to-update-or-load)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Orsak 3: [den virtuella datorn har ingen Internet-åtkomst](#the-vm-has-no-internet-access)
##### <a name="cause-4-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Orsak 4: [agenten är installerad på den virtuella datorn men inte svarar (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-5-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Orsak 5: [agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)

## <a name="unable-to-perform-the-operation-as-the-vm-agent-is-not-responsive"></a>Det gick inte att utföra åtgärden eftersom den Virtuella Datoragenten inte svarar

När du registrerar och schemalägga en virtuell dator för Azure Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med VM-tillägg att ta en ögonblicksbild i tidpunkt för säkerhetskopiering. Något av följande villkor kan förhindra att ögonblicksbilden från som utlöses, vilket i sin tur kan leda till säkerhetskopiering misslyckades. Följ nedan felsökningssteg i angiven ordning och försök igen.
##### <a name="cause-1-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Orsak 1: [agenten är installerad på den virtuella datorn men inte svarar (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Orsak 2: [agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Orsak 3: [den virtuella datorn har ingen Internet-åtkomst](#the-vm-has-no-internet-access)

## <a name="backup-failed-with-an-internal-error---please-retry-the-operation-in-a-few-minutes"></a>Det gick inte att säkerhetskopiera med ett internt fel - försök igen om några minuter

När du registrerar och schemalägga en virtuell dator för Azure Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med VM-tillägg att ta en ögonblicksbild i tidpunkt för säkerhetskopiering. Något av följande villkor kan förhindra att ögonblicksbilden från som utlöses, vilket i sin tur kan leda till säkerhetskopiering misslyckades. Följ nedan felsökningssteg i angiven ordning och försök igen.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Orsak 1: [den virtuella datorn har ingen Internet-åtkomst](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-installed-in-the-vm-but-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Orsak 2: [agenten som är installerade på den virtuella datorn men inte svarar (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Orsak 3: [agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Orsak 4: [går inte att hämta status för ögonblicksbild eller en ögonblicksbild kan inte utföras](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Orsak 5: [tillägget säkerhetskopiering misslyckas med att uppdatera eller läsa in](#the-backup-extension-fails-to-update-or-load)
##### <a name="cause-6-backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lockbackup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>Orsak 6: [Backup-tjänsten har inte behörighet att ta bort gamla återställningspunkter på grund av resursgruppen Lås](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)

## <a name="the-specified-disk-configuration-is-not-supported"></a>Den angivna diskkonfigurationen stöds inte

> [!NOTE]
> Det finns en privat förhandsgranskning som stöder säkerhetskopiering av virtuella datorer med > 1 TB ohanterade diskar. Information finns i [privat förhandsgranskning för stora diskstöd för säkerhetskopiering av VM](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a)
>
>

För närvarande Azure Backup stöder inte diskstorlekar [större än 1 023 GB](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm). 
- Om du har diskar som är större än 1 TB [kopplar du nya diskar](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) som är mindre än 1 TB <br>
- Kopiera sedan data från disken som är större än 1 TB till nyligen skapade diskar som är mindre än 1 TB. <br>
- Se till att alla data har kopierats och ta bort de diskar som är större än 1 TB
- Starta säkerhetskopieringen

## <a name="causes-and-solutions"></a>Orsaker och lösningar

### <a name="the-vm-has-no-internet-access"></a>Den virtuella datorn har ingen Internet-åtkomst
Den virtuella datorn har ingen Internetanslutning per distributionskrav, eller så har begränsningar på plats som förhindrar åtkomst till Azure-infrastrukturen.

Sekundär anknytning kräver anslutning till Azure offentliga IP-adresser ska fungera. Tillägget skickar kommandon till en Azure Storage-slutpunkt (http-URL) för att hantera ögonblicksbilder av den virtuella datorn. Om filnamnstillägget inte har åtkomst till det offentliga Internet, misslyckas säkerhetskopieringen förr eller senare.

####  <a name="solution"></a>Lösning
Lös problemet, försök med något av de metoder som anges här.
##### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>Tillåt åtkomst till Azure-datacenter IP-adressintervall

1. Hämta den [lista över Azure-datacenter IP-adresser](https://www.microsoft.com/download/details.aspx?id=41653) som ger åtkomst till.
2. Avblockera IP-adresser genom att köra den **ny NetRoute** cmdlet i virtuella Azure-datorn i ett upphöjt PowerShell-fönster. Kör cmdlet som administratör.
3. Om du vill tillåta åtkomst till IP-adresser att lägga till regler till nätverkssäkerhetsgruppen, om du har en.

##### <a name="create-a-path-for-http-traffic-to-flow"></a>Skapa en sökväg för HTTP-trafik

1. Om du har nätverksbegränsningar på plats (t.ex, en nätverkssäkerhetsgrupp) kan du distribuera en HTTP-proxyserver för att dirigera trafiken.
2. Om du vill tillåta åtkomst till Internet från HTTP-proxyserver, att lägga till regler till nätverkssäkerhetsgruppen, om du har en.

Information om hur du ställer in en HTTP-proxy för VM-säkerhetskopieringar finns [förbereda din miljö för att säkerhetskopiera virtuella datorer i Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

Om du använder hanterade diskar måste kanske en ytterligare port (8443) öppnas på brandvägg.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agenten som är installerade på den virtuella datorn men inte svarar (för virtuella Windows-datorer)

#### <a name="solution"></a>Lösning
Den Virtuella Datoragenten kan vara skadad eller tjänsten kan ha stoppats. Installera om den Virtuella datoragenten kunna hämta den senaste versionen och starta om kommunikationen.

1. Kontrollera om Gästagenten för Windows-tjänst som körs i tjänster (services.msc) för den virtuella datorn. Försök starta om tjänsten Windows Gästagenten och starta säkerhetskopieringen<br>
2. Om den inte är synlig i services Kontrollera i program och funktioner om Windows gäst agent-tjänsten är installerad.
4. Om du ska kunna visa i program och funktioner avinstallera Windows-Gästagenten.
5. Hämta och installera den [senaste versionen av agenten MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen.
6. Sedan ska du kunna visa Gästagenten för Windows-tjänster i tjänster
7. Försök att köra en på-begäran/ad hoc-säkerhetskopiering genom att klicka på ”säkerhetskopiering” i portalen.

Kontrollera också att den virtuella datorn har  **[.NET 4.5 installerat i systemet](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)**. Det krävs för VM-agenten kan kommunicera med tjänsten

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)

#### <a name="solution"></a>Lösning
De flesta agent-relaterade tillägget-relaterade fel eller för Linux virtuella datorer orsakas av problem som påverkar en inaktuell VM-agent. Följ dessa allmänna riktlinjer för att felsöka problemet:

1. Följ instruktionerna för [uppdatering Linux VM-agenten](../virtual-machines/linux/update-agent.md).

 >[!NOTE]
 >Vi *rekommenderar* att du uppdaterar agenten endast via en lagringsplats för distribution. Vi rekommenderar inte hämta agent-koden direkt från GitHub och uppdatera den. Om den senaste agenten inte är tillgänglig för din distribution supporten distribution för anvisningar om hur du installerar den. Om du vill söka efter den senaste agenten, gå till den [Windows Azure Linux-agenten](https://github.com/Azure/WALinuxAgent/releases) sidan på GitHub-lagringsplatsen.

2. Kontrollera att Azure-agenten körs på den virtuella datorn genom att köra följande kommando:`ps -e`

 Om processen inte körs startar du om den med hjälp av följande kommandon:

 * För Ubuntu:`service walinuxagent start`
 * För andra distributioner:`service waagent start`

3. [Konfigurera automatisk omstart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Kör en ny test-säkerhetskopia. Om felet kvarstår, samla in följande loggar från kundens virtuella datorer:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Om vi behöver utförlig loggning för waagent gör du följande:

1. Leta upp följande rad i filen /etc/waagent.conf: **aktivera utförlig loggning (y | n)**
2. Ändra den **Logs.Verbose** värde från  *n*  till *y*.
3. Spara ändringarna och starta sedan om waagent genom att följa stegen i det här avsnittet.

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Går inte att hämta status för ögonblicksbild eller en ögonblicksbild kan inte utföras
VM-säkerhetskopiering är beroende av en ögonblicksbild kommandot utfärdas till det underliggande storage-kontot. Säkerhetskopieringen kan misslyckas eftersom den inte har åtkomst till lagringskontot eller körningen av uppgiften ögonblicksbild är fördröjd.

#### <a name="solution"></a>Lösning
Följande villkor kan orsaka ögonblicksbild aktivitet, fel:

| Orsak | Lösning |
| --- | --- |
| Den virtuella datorn har konfigurerats för SQL Server-säkerhetskopiering. | Standard körs VM-säkerhetskopiering ett VSS fullständig säkerhetskopiering på virtuella Windows-datorer. På virtuella datorer som kör SQL Server-baserade servrar och på vilka SQL Server är säkerhetskopiering konfigurerad, kan det uppstå fördröjningar för körning av ögonblicksbild.<br><br>Om det uppstår ett fel för säkerhetskopiering på grund av ett problem med ögonblicksbilder, anger du följande registernyckel:<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] ”USEVSSCOPYBACKUP” = ”TRUE”** |
| VM-statusen rapporteras felaktigt eftersom den virtuella datorn stängs av i RDP. | Om du stänger av den virtuella datorn i Remote Desktop Protocol (RDP), kontrollera portal för att avgöra om VM-statusen är korrekt. Om det inte är korrekt, stänger du den virtuella datorn i portalen med hjälp av den **avstängning** alternativet på VM-instrumentpanelen. |
| Många virtuella datorer från samma molntjänst är konfigurerad för att säkerhetskopiera på samma gång. | Det är en bra idé att sprida ut scheman för säkerhetskopiering för virtuella datorer från samma Molntjänsten. |
| Den virtuella datorn körs på hårt CPU eller minne. | Om den virtuella datorn körs på hög CPU-användning (mer än 90 procent) eller hög minnesanvändning uppgiften ögonblicksbild i kö och fördröjd och slutligen tidsgränsen uppnås. Försök i så fall kan en säkerhetskopiering på begäran. |
| Den virtuella datorn kan inte hämta värden/fabric-adress från DHCP. | DHCP måste vara aktiverat på gästen för IaaS VM-säkerhetskopiering för att fungera.  Om den virtuella datorn inte kan hämta värden/fabric-adress från DHCP-svar 245, kan inte den hämta eller köra alla tillägg. Om du behöver en statisk privat IP-adress kan konfigurera du det via plattformen. DHCP-alternativet inuti den virtuella datorn ska aktiveras vänster. Mer information finns i [ange en statisk IP för interna privata](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>Tillägget säkerhetskopiering misslyckas med att uppdatera eller läsa in
Om tillägg inte kan läsas in, misslyckas säkerhetskopieringen eftersom en ögonblicksbild inte kan utföras.

#### <a name="solution"></a>Lösning

**För Windows-gäster:** Kontrollera att tjänsten iaasvmprovider är aktiverat och har en starttyp *automatisk*. Om tjänsten inte har konfigurerats på detta sätt, aktivera den att avgöra om det lyckas under nästa säkerhetskopiering.

**För Linux-gäster:** Kontrollera den senaste versionen av VMSnapshot för Linux (tillägg som används av säkerhetskopiering) är 1.0.91.0.<br>


Om tillägget säkerhetskopiering fortfarande inte att uppdatera eller läsa in, kan du tvinga VMSnapshot-tillägget ska läsas in genom att avinstallera tillägget. Nästa säkerhetskopiering försöket läses in tillägget.

Om du vill avinstallera tillägget gör du följande:

1. Gå till [Azure-portalen](https://portal.azure.com/).
2. Leta upp den virtuella datorn som har problem med backup.
3. Klicka på **inställningar**.
4. Klicka på **tillägg**.
5. Klicka på **Vmsnapshot tillägget**.
6. Klicka på **avinstallera**.

Den här proceduren medför att tillägg installeras under nästa säkerhetskopiering.

### <a name="azure-classic-vms-may-require-additional-step-to-complete-registration"></a>Klassiska virtuella Azure-datorer kan kräva ytterligare steg för att slutföra registreringen
Agenten i klassiska virtuella Azure-datorer måste registreras för att upprätta en anslutning till tjänsten för säkerhetskopiering och starta säkerhetskopieringen

#### <a name="solution"></a>Lösning

När du har installerat VM-gästagent starta Azure PowerShell <br>
1. Logga in i Azure-konto med hjälp av <br>
       `Login-AzureAsAccount`<br>
2. Kontrollera om Virtuella datorns ProvisionGuestAgent egenskap är inställd på True, med följande kommandon <br>
        `$vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>`<br>
        `$vm.VM.ProvisionGuestAgent`<br>
3. Om egenskapen är inställd på FALSE, följ nedan kommandon ska anges till TRUE<br>
        `$vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>`<br>
        `$vm.VM.ProvisionGuestAgent = $true`<br>
4. Kör följande kommando för att uppdatera den virtuella datorn <br>
        `Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>` <br>
5. Försök att initiera säkerhetskopieringen. <br>

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>Backup-tjänsten har inte behörighet att ta bort gamla återställningspunkter på grund av resursgruppen Lås
Det här problemet är specifikt för hanterade virtuella datorer där användaren låser resursgruppen och Backup-tjänsten går inte att ta bort äldre återställningspunkter. På grund av detta starta nya säkerhetskopior misslyckas eftersom det finns en gräns på högsta 18 återställningspunkter införts från serverdelen.

#### <a name="solution"></a>Lösning

Lös problemet genom att använda följande steg att ta bort samlingen återställning punkt: <br>
 
1. Ta bort resursgruppen låsa där den virtuella datorn finns 
     
2. Installera ARMClient med Chocolatey <br>
   https://github.com/projectkudu/ARMClient
     
3. Logga in på ARMClient <br>
             `.\armclient.exe login`
         
4. Get återställningspunkt samlingen motsvarar den virtuella datorn <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    Exempel:`.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
             
5. Ta bort samlingen återställning punkt <br>
            `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
 
6. Nästa schemalagda säkerhetskopiering skapar automatiskt återställning punkt insamling och nya återställningspunkter 
 
7. Problemet visas igen om du låser resursgruppen igen som det finns endast en högst 18 återställningspunkter efter vilken starta säkerhetskopieringar misslyckas 

