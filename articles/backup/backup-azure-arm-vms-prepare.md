---
title: Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services-valv med Azure Backup
description: Beskriver hur du säkerhetskopierar virtuella Azure-datorer i ett Recovery Services-valv med Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: effb00a4ebde857e06e34e5f83ca01fc5d74017b
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594193"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services-valv

Den här artikeln beskrivs hur du säkerhetskopierar för virtuell Azure-dator med hjälp av en [Azure Backup](backup-overview.md) genom att distribuera och aktiverar säkerhetskopiering i Recovery Services-valvet. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Kontrollera vilka scenarier och förutsättningar.
> * Förbereda virtuella Azure-datorer. Installera Azure VM-agenten om det behövs och kontrollera utgående åtkomst för virtuella datorer.
> * Skapa ett valv.
> * Konfigurera lagring för valvet
> * Identifiera virtuella datorer, konfigurera inställningar för säkerhetskopiering och principen.
> * Aktivera säkerhetskopiering för virtuella Azure-datorer


> [!NOTE]
   > Den här artikeln beskriver hur du säkerhetskopierar virtuella Azure-datorer genom att skapa ett valv och välja virtuella datorer för att säkerhetskopiera. Det är användbart om du vill säkerhetskopiera flera virtuella datorer. Du kan också [säkerhetskopiera en virtuell Azure-dator](backup-azure-vms-first-look-arm.md) direkt från inställningarna för virtuella datorer.

## <a name="before-you-start"></a>Innan du börjar

Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn.

1. [Granska](backup-architecture.md#architecture-direct-backup-of-azure-vms) arkitektur för säkerhetskopiering av virtuella Azure-datorer.
[Lär dig mer om](backup-azure-vms-introduction.md) virtuell Azure-säkerhetskopiering och säkerhetskopieringstillägget.
2. [Granska av stödmatrisen](backup-support-matrix-iaas.md) för virtuell Azure-säkerhetskopiering.
3. Förbereda virtuella Azure-datorer. Installera VM-agenten om den inte är installerad och kontrollera utgående åtkomst för virtuella datorer du vill säkerhetskopiera.


## <a name="prepare-azure-vms"></a>Förbereda virtuella Azure-datorer

Installera VM-agenten om det behövs och kontrollera utgående åtkomst från virtuella datorer.

### <a name="install-the-vm-agent"></a>Installera VM-agenten 
Om det behövs installerar du agenten på följande sätt.

**VM** | **Detaljer**
--- | ---
**Virtuella Windows-datorer** | [Ladda ned och installera](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) agenten MSI-filen. Installera med administratörsbehörighet på datorn.<br/><br/> Verifiera installationen, i *C:\WindowsAzure\Packages* på den virtuella datorn högerklickar du på WaAppAgent.exe > **egenskaper**, > **information** fliken. **Produktversion** ska vara 2.6.1198.718 eller högre.<br/><br/> Om du uppdaterar agenten, kontrollera att inga säkerhetskopieringsåtgärder körs och [installera om agenten](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Virtuella Linux-datorer** | Installationen med hjälp av en RPM- eller DEB-paketet från paketdatabasen för din distribution är den bästa metoden för att installera och uppgradera Azure Linux Agent. Alla de [godkända distribution providers](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrera Azure Linux-agenten i sina avbildningar och databaser. Agenten är tillgängligt på [GitHub](https://github.com/Azure/WALinuxAgent), men vi rekommenderar inte att installera därifrån.<br/><br/> Om du uppdaterar agenten kan du kontrollera att ingen säkerhetskopieringsåtgärd körs och uppdatera binärfilerna. 


### <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

Säkerhetskopieringstillägget som körs på den virtuella datorn måste ha utgående åtkomst till Azure offentliga IP-adresser.

- Ingen åtkomst explicit utgående nätverkstrafik måste anges för virtuella Azure-datorn att kommunicera med Azure Backup-tjänsten.
- Men vissa äldre virtuella datorer kan få problem med att och misslyckas med fel **ExtensionSnapshotFailedNoNetwork** vid försök att ansluta. I så fall använda något av följande alternativ så att säkerhetskopieringstillägget kan kommunicera med Azure offentliga IP-adresser för säkerhetskopieringen.

   **Alternativ** | **Åtgärd** | **Fördelar** | **Nackdelar**
   --- | --- | --- | ---
   **Konfigurera NSG-regler** | Tillåt den [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/>  Du kan lägga till en regel som tillåter åtkomst till Azure Backup-tjänsten med en [servicetagg](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure), i stället för enskilt så att och hantera varje adressintervall. [Läs mer](../virtual-network/security-overview.md#service-tags) om tjänsttaggar. | Inga ytterligare kostnader. Enkelt att hantera med tjänsttaggar
   **Distribuera en proxy** | Distribuera en HTTP-proxyserver dirigeras trafiken. | Ger åtkomst till hela Azure och inte bara lagring. Detaljerad kontroll över storage-URL: er tillåts.<br/><br/> Enskild punkt för Internetåtkomst för virtuella datorer.<br/><br/> Ytterligare kostnader för proxy.<br/><br/> 
   **Konfigurera Azure-brandväggen** | Tillåta trafik via Azure-brandväggen på den virtuella datorn med ett FQDN-taggen för Azure Backup-tjänsten.|  Enkelt att använda om du har Azure brandväggen ställts in i ett undernät för virtuellt nätverk | Det går inte att skapa dina egna FQDN-taggar eller ändra FQDN: er i en tagg.<br/><br/> Om du använder Azure Managed Disks kan behöva du ett inledande ytterligare porten (port 8443) i brandväggar.

#### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Konfigurera en NSG-regel för att tillåta utgående åtkomst till Azure

Om din virtuella Azure-datorn har åtkomst som hanteras av en NSG, Tillåt utgående åtkomst för säkerhetskopieringslagring till nödvändiga intervall och portar.

1. I den virtuella datorn > **nätverk**, klickar du på **Lägg till regel för utgående port**.
2. I **Lägg till utgående säkerhetsregel**, klickar du på **Avancerat**.
3. I **källa**väljer **VirtualNetwork**.
4. I **Source portintervall**, en asterisk (*) anger att tillåta utgående åtkomst från alla portar.
5. I **mål**väljer **Tjänsttagg**. I listan, väljer **Storage.region**. Regionen är den region som valvet och de virtuella datorerna som du vill säkerhetskopiera finns.
6. I **målportsintervall**, markera porten.
    - Ohanterad virtuell dator med okrypterat lagringskonto: 80
    - Ohanterad virtuell dator med krypterade storage-konto: 443 (standardinställning)
    - Den hanterade virtuella datorn: 8443.
7. I **protokollet**väljer **TCP**.
8. I **prioritet**, ange ett prioritetsvärde mindre än högre neka regler. Om du har en regel som nekar åtkomst kan kan de nya regeln måste vara högre. Exempel: Om du har en **Deny_All** regeluppsättning med prioritet 1000, din nya regel måste anges till mindre än 1 000.
9. Ange ett namn och beskrivning för regeln och klickar på **OK**.

Du kan använda NSG-regel för att tillåta utgående åtkomst på flera virtuella datorer.

Den här videon går igenom processen.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


#### <a name="route-backup-traffic-through-a-proxy"></a>Backup-trafik via en proxy

Du kan dirigera säkerhetskopiering trafik via en proxyserver och sedan ge proxy-åtkomst till de nödvändiga Azure intervall.
Du bör konfigurera proxyn VM att tillåta följande:

- Azure VM ska vidarebefordra alla HTTP-trafik som är bunden till det offentliga internet via proxy.
- Proxyn ska tillåta inkommande trafik från virtuella datorer i det virtuella nätverket (VNet).
- NSG: N **NSF låsning** måste en regel som tillåter utgående internet-trafik från proxy VM.

##### <a name="set-up-the-proxy"></a>Konfigurera proxyn
Om du inte har en proxy för system-konto, konfigurera en enligt följande:

1. Ladda ned [PsExec](https://technet.microsoft.com/sysinternals/bb897553).

2. Kör **PsExec.exe -i -s cmd.exe** att köra kommandotolken under ett systemkonto.
3. Kör webbläsaren i systemkontexten. Till exempel: **%PROGRAMFILES%\Internet Explorer\iexplore.exe** för Internet Explorer.  
4. Definiera proxyinställningarna.
    - På Linux-datorer:
        - Lägg till följande rad till den **/etc/miljö** fil:
            - **http_proxy =http://proxy IP-adress: Proxyport**
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

##### <a name="allow-incoming-connections-on-the-proxy"></a>Tillåt inkommande anslutningar på proxyn

Tillåt inkommande anslutningar i proxyinställningarna.

- Till exempel öppna **Windows-brandväggen med avancerad säkerhet**.
    - Högerklicka på **regler för inkommande** > **ny regel**.
    - I **regeltyp** Välj **anpassade** > **nästa**.
    - I **programmet**väljer **alla program** > **nästa**.
    - I **protokoll och portar** anger objekttyp **TCP**, **lokala portar** till **specifika portar**, och **Fjärrport**till **alla portar**.
    - Slutför guiden och ange ett namn för regeln.

##### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Lägga till en undantagsregel för NSG: N för proxyn

På NSG: N **NSF låsning**, tillåta trafik från alla portar på 10.0.0.5 till en Internetadress på port 80 (HTTP) eller 443 (HTTPS).

- Följande PowerShell-skript innehåller ett exempel för att tillåta trafik.
- I stället för att tillåta utgående trafik till alla offentliga internet-adresser, kan du ange ett IP-adressintervall (-DestinationPortRange), eller Använd storage.region tjänsttagg.   

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>Tillåt brandväggsåtkomst med FQDN-tagg

Du kan ställa in Azure-brandväggen att tillåta utgående åtkomst för trafik till Azure Backup.

- [Lär dig mer om](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) distribuera Azure-brandvägg.
- [Läs mer om](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN-taggar.

## <a name="create-a-vault"></a>Skapa ett valv

Ett valv lagrar säkerhetskopior och återställningspunkter som skapats med tiden och lagrar principer för säkerhetskopiering som är associerade med säkerhetskopierade virtuella datorer. Skapa ett valv enligt följande:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. På den **Hub** menyn och välj **Bläddra**, och skriv **återställningstjänster**. Välj **Recovery Services-valv**.

    ![Att skriva i rutan och välja ”Recovery Services-valv” i resultaten](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. På den **Recovery Services-valv** menyn och välj **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    ![Fönstret ”recovery Services-valv”](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. I **Recovery Services-valv** >  **namn**, ange ett eget namn som identifierar valvet.
    - Namnet måste vara unikt för Azure-prenumerationen.
    - Det kan innehålla 2 och 50 tecken.
    - Det måste börja med en bokstav och det får innehålla endast bokstäver, siffror och bindestreck.
5. Välj **prenumeration** att se listan över prenumerationer. Om du inte vet vilken prenumeration som ska användas, använder du standardvärdet (eller föreslås) prenumeration. Det finns flera alternativ endast om ditt arbete eller skola konto är kopplat till flera Azure-prenumerationer.
6. Välj **resursgrupp** att se listan över resursgrupper eller välja **New** att skapa en ny resursgrupp. [Läs mer](../azure-resource-manager/resource-group-overview.md) om resursgrupper.
7. Välj **plats** att välja ett geografiskt område för valvet. Valvet *måste* vara i samma region som de virtuella datorerna som du vill säkerhetskopiera.
8. Välj **Skapa**.
    - Det kan ta en stund innan valvet har skapats.
    - Övervaka Statusmeddelandena i området längst upp till höger i portalen.
    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

När valvet har skapats visas den i listan över Recovery Services-valv. Om du inte ser ditt valv, väljer **uppdatera**.

## <a name="set-up-storage-replication"></a>Konfigurera lagringsreplikering

Valvet har som standard [geo-redundant lagring (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). Vi rekommenderar GRS för din primära säkerhetskopia, men du kan använda[lokalt redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för ett billigare alternativ.

Ändra storage-replikering på följande sätt:

1. I valvet > **infrastruktur för säkerhetskopiering**, klickar du på **konfiguration av säkerhetskopiering**

   ![Lista över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/full-blade.png)

2. I **Säkerhetskopieringskonfigurationen**, ändra vilken lagringsmetod redundans som krävs och välj **spara**.


## <a name="configure-a-backup-policy"></a>Konfigurera en princip för säkerhetskopiering

Identifiera virtuella datorer i prenumerationen och konfigurera säkerhetskopiering.

1. I valvet > **översikt**, klickar du på **+ säkerhetskopiering**

   ![Säkerhetskopiering knappen](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Den **Backup** och **säkerhetskopieringsmål** fönstren öppna.

2. I **säkerhetskopieringsmål**> **var körs din arbetsbelastning?** väljer **Azure**. I **vad vill du säkerhetskopiera?** väljer **VM** >  **OK**. Nu registreras VM-tillägget i valvet.

   ![Fönster för säkerhetskopiering och säkerhetskopieringsmål](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Det här steget registrerar VM-tillägget med valvet. Den **säkerhetskopieringsmål** fönstret stängs och **säkerhetskopieringspolicy** öppnas fönstret.

3. I **säkerhetskopieringspolicy**, väljer du den princip som du vill associera med valvet. Klicka sedan på **OK**.
    - Information om standardprincipen visas under den nedrullningsbara menyn.
    - Klicka på **Skapa ny** att skapa en princip. [Läs mer](backup-azure-arm-vms-prepare.md#configure-a-backup-policy) om hur du definierar en princip.
    

    ![”Säkerhetskopiering” och ”säkerhetskopieringspolicy” fönster](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. I **Välj virtuella datorer** fönstret väljer du de virtuella datorer som ska använda den angivna säkerhetskopieringspolicyn > **OK**.

    - Den valda virtuella datorn har verifierats.
    - Du kan bara välja virtuella datorer i samma region som valvet. Virtuella datorer kan endast säkerhetskopieras i ett enda valv.

   ![Fönstret ”Välj virtuella datorer”](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. I **säkerhetskopiering**väljer **Aktivera säkerhetskopiering**.

   - Detta distribueras principen till valvet och till de virtuella datorerna och installerar tillägget för säkerhetskopiering på VM-agenten som körs på Azure VM.
   - Det här steget skapar inte den första återställningspunkten för den virtuella datorn.

   ![Knappen ”Aktivera säkerhetskopiering”](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

När du har aktiverat säkerhetskopiering:

- En första säkerhetskopieringen körs i enlighet med schemat för säkerhetskopiering.
- Backup-tjänsten installerar tillägget för säkerhetskopiering, oavsett om Virtuellt datorn körs.
    - En virtuell dator som körs har bäst chans att tilldelas en programkonsekvent återställningspunkt.
    -  Den virtuella datorn säkerhetskopieras dock även om den är avstängd och det går inte att installera tillägget. Detta kallas *offline VM*. I detta fall är återställningspunkten *kraschkonsekvent*.
    Observera att Azure Backup inte stöder automatisk klockan justering för sommartid ändringar för Virtuella Azure-säkerhetskopieringar. Ändra principer för säkerhetskopiering manuellt vid behov.
  
 ## <a name="run-the-initial-backup"></a>Kör den första säkerhetskopieringen

Den första säkerhetskopieringen kommer att köras i enlighet med schemat, såvida inte du manuellt köra den direkt. Köra den manuellt på följande sätt:

1. I menyn valvet klickar du på **Säkerhetskopiera objekt**.
2. I **Säkerhetskopieringsobjekt** klickar du på **Azure-dator**.
3. I den **Säkerhetskopieringsobjekt** klickar du på ellipserna **...** .
4. Klicka på **Säkerhetskopiera nu**.
5. I **Säkerhetskopiera nu**, använder kalenderkontrollen för att välja den sista dagen som återställningspunkten ska behållas > **OK**.
6. Meddelandena som portalen. Du kan övervaka jobbförloppet i instrumentpanelen för valvet > **säkerhetskopieringsjobb** > **pågår**. Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.



## <a name="next-steps"></a>Nästa steg

- Felsök eventuella problem som uppstår vid den [Azure VM-agenter](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) eller [virtuell Azure-säkerhetskopiering](backup-azure-vms-troubleshoot.md).
- [Säkerhetskopiera virtuella Azure-datorer](backup-azure-vms-first-look-arm.md)
