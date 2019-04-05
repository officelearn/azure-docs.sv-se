---
title: Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services-valv med hjälp av Azure Backup
description: Beskriver hur du säkerhetskopierar virtuella Azure-datorer i ett Recovery Services-valv med hjälp av Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: 3342b15511305ab337d9b5032080e205e36150d3
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049821"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services-valv

Den här artikeln beskrivs hur du säkerhetskopierar virtuella Azure-datorer i ett Recovery Services-valv med hjälp av den [Azure Backup](backup-overview.md) service. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Kontrollera support och krav för en säkerhetskopia.
> * Förbereda virtuella Azure-datorer. Installera Azure VM-agenten om det behövs och kontrollera utgående åtkomst för virtuella datorer.
> * Skapa ett valv.
> * Identifiera virtuella datorer och konfigurera en princip för säkerhetskopiering.
> * Aktivera säkerhetskopiering för virtuella Azure-datorer.


> [!NOTE]
> Den här artikeln beskriver hur du ställer in ett valv och välj virtuella datorer för att säkerhetskopiera. Det är användbart om du vill säkerhetskopiera flera virtuella datorer. Du kan också [säkerhetskopiera en virtuell Azure-dator](backup-azure-vms-first-look-arm.md) direkt från inställningarna för virtuella datorer.

## <a name="before-you-start"></a>Innan du börjar


- [Granska](backup-architecture.md#architecture-direct-backup-of-azure-vms) arkitektur för Azure VM-säkerhetskopiering.
- [Lär dig mer om](backup-azure-vms-introduction.md) virtuell Azure-säkerhetskopiering och säkerhetskopieringstillägget.
- [Granska av stödmatrisen](backup-support-matrix-iaas.md) för virtuell Azure-säkerhetskopiering.


## <a name="prepare-azure-vms"></a>Förbereda virtuella Azure-datorer

I vissa fall kan du behöva ställa in Azure VM-agenten på Azure Virtual Machines eller uttryckligen tillåta utgående åtkomst på en virtuell dator.

### <a name="install-the-vm-agent"></a>Installera VM-agenten 

Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om den virtuella datorn har skapats från en Azure Marketplace-avbildning, är agenten installerad och körs. Om du skapar en anpassad virtuell dator eller om du migrerar en lokal virtuell dator, kan du behöva installera agenten manuellt, som sammanfattas i tabellen.

**Virtuell dator** | **Information**
--- | ---
**Windows** | 1. [Ladda ned och installera](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) agenten MSI-filen.<br/><br/> 2. Installera med administratörsbehörighet på datorn.<br/><br/> 3. Verifiera installationen. I *C:\WindowsAzure\Packages* på den virtuella datorn högerklickar du på **WaAppAgent.exe** > **egenskaper**. På den **information** fliken **produktversion** ska vara 2.6.1198.718 eller högre.<br/><br/> Om du uppdaterar agenten, se till att inga säkerhetskopieringsåtgärder körs, och [installera om agenten](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installera med hjälp av en RPM- eller DEB-paketet från paketdatabasen för din distribution. Det här är den bästa metoden för att installera och uppgradera Azure Linux-agent. Alla de [godkända distribution providers](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrera Azure Linux-agenten i sina avbildningar och databaser. Agenten är tillgängligt på [GitHub](https://github.com/Azure/WALinuxAgent), men vi rekommenderar inte att installera därifrån.<br/><br/> Om du uppdaterar agenten kan du kontrollera att inga säkerhetskopieringsåtgärder kör och uppdatera binärfilerna.


### <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

Säkerhetskopieringstillägget som körs på den virtuella datorn behöver utgående åtkomst till Azure offentliga IP-adresser.

Normalt behöver du inte uttryckligen tillåta utgående nätverksåtkomst för en Azure virtuell dator så att den kan kommunicera med Azure Backup.
Om dina virtuella datorer inte kan ansluta och om du ser felet **ExtensionSnapshotFailedNoNetwork**, bör du uttryckligen tillåta åtkomst. Säkerhetskopieringstillägget kan sedan kommunicera med Azure offentliga IP-adresser för säkerhetskopieringen.


#### <a name="explicitly-allow-outbound-access"></a>Uttryckligen tillåta utgående åtkomst

Om en virtuell dator inte kan ansluta till säkerhetskopieringstjänsten, måste du uttryckligen tillåta utgående åtkomst med hjälp av en av de metoder som sammanfattas i tabellen.

**Alternativ** | **Åtgärd** | **Information** 
--- | --- | --- 
**Konfigurera NSG-regler** | Tillåt den [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653). | I stället för att tillåta och hanterar varje adressintervall, kan du lägga till en regel för security nätverkssäkerhetsgrupper (NSG) som ger åtkomst till Azure Backup-tjänsten med hjälp av en [servicetagg](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). [Läs mer](../virtual-network/security-overview.md#service-tags).<br/><br/> Det finns inga ytterligare kostnader.<br/><br/> Regler är enkla att hantera med tjänsttaggar.
**Distribuera en proxy** | Distribuera en HTTP-proxyserver dirigeras trafiken. | Den här metoden ger åtkomst till hela Azure och inte bara lagring.<br/><br/> Detaljerad kontroll över storage-URL: er tillåts.<br/><br/> Det finns en enda punkt för Internetåtkomst för virtuella datorer.<br/><br/> Det finns ytterligare kostnader för en proxy.
**Konfigurera Azure-brandväggen** | Tillåta trafik via Azure-brandväggen på den virtuella datorn med hjälp av en FQDN-tagg för Azure Backup-tjänsten. |  Den här metoden är enkel att använda om du har Azure brandvägg ställa in i undernät för ett virtuellt nätverk.<br/><br/> Du kan inte skapa dina egna FQDN-taggar eller ändra FQDN: er i en tagg.<br/><br/> Om du använder Azure Managed Disks kan behöva du ett inledande ytterligare porten (port 8443) i brandväggar.

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Konfigurera en NSG-regel för att tillåta utgående åtkomst till Azure

Om en NSG hanterar åtkomst till virtuella datorer, Tillåt utgående åtkomst för säkerhetskopieringslagring till nödvändiga intervall och portar.

1. I virtuella datorns egenskaper > **nätverk**väljer **Lägg till regel för utgående port**.
2. I **Lägg till utgående säkerhetsregel**väljer **Avancerat**.
3. I **källa**väljer **VirtualNetwork**.
4. I **Source portintervall**, anger du en asterisk (*) för att tillåta utgående åtkomst från alla portar.
5. I **mål**väljer **Tjänsttagg**. I listan, väljer **Storage.region**. Regionen är där valvet och de virtuella datorerna som du vill säkerhetskopiera finns.
6. I **målportsintervall**, markera porten.
    - Ohanterad virtuell dator med okrypterat lagringskonto: 80
    - Ohanterad virtuell dator med krypterade storage-konto: 443 (standardinställning)
    - Den hanterade virtuella datorn: 8443.
7. I **protokollet**väljer **TCP**.
8. I **prioritet**, ange ett prioritetsvärde mindre än högre neka regler.
   
   Om du har en regel som nekar åtkomst kan kan de nya regeln måste vara högre. Exempel: Om du har en **Deny_All** regeluppsättning med prioritet 1000, din nya regel måste anges till mindre än 1 000.
9. Ange ett namn och beskrivning för regeln och välj **OK**.

Du kan använda NSG-regel för att tillåta utgående åtkomst på flera virtuella datorer. Den här videon går igenom processen.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>Backup-trafik via en proxy

Du kan dirigera säkerhetskopiering trafik via en proxyserver och sedan ge proxy-åtkomst till de nödvändiga Azure intervall. Konfigurera proxyn VM att tillåta följande:

- Azure VM ska vidarebefordra alla HTTP-trafik som är bunden till det offentliga internet via proxy.
- Proxyn ska tillåta inkommande trafik från virtuella datorer i det virtuella nätverket som är tillämpligt.
- NSG: N **NSF låsning** måste en regel som tillåter utgående internet-trafik från proxy VM.

###### <a name="set-up-the-proxy"></a>Konfigurera proxyn

Om du inte har en proxy för system-konto, konfigurera en enligt följande:

1. Ladda ned [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Kör **PsExec.exe -i -s cmd.exe** att köra kommandotolken under ett systemkonto.
3. Kör webbläsaren i systemkontexten. Till exempel använda **%PROGRAMFILES%\Internet Explorer\iexplore.exe** för Internet Explorer.  
4. Definiera proxyinställningarna.
   - På Linux-datorer:
     - Lägg till följande rad till den **/etc/miljö** fil:
       - **http_proxy = http:\//proxy IP-adress: Proxyport**
     - Lägg till följande rader till den **/etc/waagent.conf** fil:
         - **HttpProxy.Host=proxy IP-adress**
         - **HttpProxy.Port=proxy port**
   - Ange att en proxyserver ska användas på Windows-datorer i inställningarna för webbläsaren. Om du använder en proxyserver för ett användarkonto, kan du använda det här skriptet för att använda inställningen på kontonivå system.
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Tillåt inkommande anslutningar på proxyn

Tillåt inkommande anslutningar i proxyinställningarna.

1. I Windows-brandväggen, öppna **Windows-brandväggen med avancerad säkerhet**.
2. Högerklicka på **regler för inkommande** > **ny regel**.
3. I **regeltyp**väljer **anpassade** > **nästa**.
4. I **programmet**väljer **alla program** > **nästa**.
5. I **protokoll och portar**:
   - Ange vilket **TCP**.
   - Ange **lokala portar** till **specifika portar**.
   - Ange **Fjärrport** till **alla portar**.
  
6. Slutför guiden och ange ett namn för regeln.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Lägga till en undantagsregel för NSG: N för proxyn

På NSG: N **NSF låsning**, tillåta trafik från alla portar på 10.0.0.5 till en Internetadress på port 80 (HTTP) eller 443 (HTTPS).

Följande PowerShell-skript innehåller ett exempel för att tillåta trafik.
I stället för att tillåta utgående trafik till alla offentliga internet-adresser, kan du ange ett IP-adressintervall (`-DestinationPortRange`), eller Använd storage.region tjänsttagg.   

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

### <a name="allow-firewall-access-by-using-an-fqdn-tag"></a>Tillåt brandväggsåtkomst med hjälp av en FQDN-tagg

Du kan ställa in Azure-brandväggen att tillåta utgående åtkomst för trafik till Azure Backup.

- [Lär dig mer om](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) distribuera Azure-brandvägg.
- [Läs mer om](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN-taggar.

## <a name="modify-storage-replication-settings"></a>Ändra inställningarna för lagringsreplikering

Valvet har som standard [geo-redundant lagring (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). Vi rekommenderar GRS för din primära säkerhetskopia. Du kan använda [lokalt redundant lagring (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för ett billigare alternativ.

Ändra lagringsreplikeringstyp på följande sätt:

1. Välj det nya valvet i portalen. Under **inställningar**väljer **egenskaper**.
2. I **egenskaper**under **Säkerhetskopieringskonfigurationen**väljer **uppdatering**.
3. Välj lagringsreplikeringstyp och **spara**.

![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

Identifiera virtuella datorer i prenumerationen och konfigurera säkerhetskopiering.

1. I valvet > **översikt**väljer **+ säkerhetskopiera**.

   ![Säkerhetskopiering knappen](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Den **Backup** och **säkerhetskopieringsmål** fönstren öppna.

2. I **säkerhetskopieringsmål** > **var körs din arbetsbelastning?** väljer **Azure**. I **vad vill du säkerhetskopiera?** väljer **VM** >  **OK**.

   ![Fönster för säkerhetskopiering och säkerhetskopieringsmål](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Det här steget registrerar VM-tillägget med valvet. Den **säkerhetskopieringsmål** fönstret stängs och **säkerhetskopieringspolicy** öppnas fönstret.

3. I **säkerhetskopieringspolicy**, väljer du den princip som du vill associera med valvet. Välj sedan **OK**.
    - Information om standardprincipen visas under den nedrullningsbara menyn.
    - Välj **Skapa ny** att skapa en princip. [Läs mer](backup-azure-arm-vms-prepare.md#configure-a-backup-policy) om hur du definierar en princip.

    ![”Säkerhetskopiering” och ”säkerhetskopieringspolicy” fönster](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. I den **Välj virtuella datorer** fönstret väljer du de virtuella datorer som ska använda den angivna säkerhetskopieringspolicyn > **OK**.

   Den valda virtuella datorn har verifierats. Du kan bara välja virtuella datorer i samma region som valvet. Virtuella datorer kan endast säkerhetskopieras i ett enda valv.

   ![Fönstret ”Välj virtuella datorer”](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. I **säkerhetskopiering**väljer **Aktivera säkerhetskopiering**.

   Det här steget distribuerar principen till valvet och till de virtuella datorerna. Säkerhetskopieringstillägget installeras även på VM-agenten som körs på Azure VM.
   
   Det här steget skapar inte den första återställningspunkten för den virtuella datorn.

   ![Knappen ”Aktivera säkerhetskopiering”](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

När du har aktiverat säkerhetskopiering:

- En första säkerhetskopieringen körs i enlighet med schemat för säkerhetskopiering.
- Backup-tjänsten installerar tillägget för säkerhetskopiering, oavsett om Virtuellt datorn körs.

En virtuell dator som körs har bäst chans att tilldelas en programkonsekvent återställningspunkt. Den virtuella datorn säkerhetskopieras dock även om den är avstängd och det går inte att installera tillägget. Det är känt som en offline virtuell dator. I det här fallet kommer återställningspunkten vara kraschkonsekvent.
    
> [!NOTE]
> Azure Backup stöder inte automatisk klockan justering för sommartid ändringar för Virtuella Azure-säkerhetskopieringar. Ändra principer för säkerhetskopiering manuellt vid behov.

## <a name="run-the-initial-backup"></a>Kör den första säkerhetskopieringen

Den första säkerhetskopieringen kommer att köras i enlighet med schemat, såvida inte du manuellt köra den direkt. Köra den manuellt på följande sätt:

1. I menyn valv väljer **Säkerhetskopiera objekt**.
2. I **Säkerhetskopieringsobjekt**väljer **Azure-dator**.
3. I den **Säkerhetskopieringsobjekt** väljer du de tre punkterna (**...** ).
4. Välj **Säkerhetskopiera nu**.
5. I **Säkerhetskopiera nu**, använder kalenderkontrollen för att välja den sista dagen som återställningspunkten ska behållas > **OK**.
6. Meddelandena som portalen. Du kan övervaka jobbförloppet i instrumentpanelen för valvet > **säkerhetskopieringsjobb** > **pågår**. Beroende på storleken på den virtuella datorn, kan det ta en stund att skapa den första säkerhetskopieringen.



## <a name="next-steps"></a>Nästa steg

- Felsök eventuella problem med [Azure VM-agenter](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) eller [virtuell Azure-säkerhetskopiering](backup-azure-vms-troubleshoot.md).
- [Återställa](backup-azure-arm-restore-vms.md) virtuella Azure-datorer.

