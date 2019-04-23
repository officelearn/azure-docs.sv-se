---
title: Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services-valv med Azure Backup
description: Beskriver hur du säkerhetskopierar virtuella Azure-datorer i ett Recovery Services-valv med Azure Backup
service: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: raynew
ms.openlocfilehash: 98934216c0860c79575874df26603b1187e35978
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149101"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services-valv

Den här artikeln beskrivs hur du säkerhetskopierar virtuella Azure-datorer i ett Recovery Services-valv med hjälp av den [Azure Backup](backup-overview.md) service. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Förbereda virtuella Azure-datorer.
> * Skapa ett valv.
> * Identifiera virtuella datorer och konfigurera en princip för säkerhetskopiering.
> * Aktivera säkerhetskopiering för virtuella Azure-datorer.
> * Kör den första säkerhetskopieringen.


> [!NOTE]
> Den här artikeln beskriver hur du ställer in ett valv och välj virtuella datorer för att säkerhetskopiera. Det är användbart om du vill säkerhetskopiera flera virtuella datorer. Du kan också [säkerhetskopiera en virtuell Azure-dator](backup-azure-vms-first-look-arm.md) direkt från inställningarna för virtuella datorer.

## <a name="before-you-start"></a>Innan du börjar


- [Granska](backup-architecture.md#architecture-direct-backup-of-azure-vms) arkitektur för Azure VM-säkerhetskopiering.
- [Lär dig mer om](backup-azure-vms-introduction.md) virtuell Azure-säkerhetskopiering och säkerhetskopieringstillägget.
- [Granska av stödmatrisen](backup-support-matrix-iaas.md) innan du konfigurerar säkerhetskopiering.

Det finns dessutom några saker som du kan behöva göra i vissa fall:

- **Installera VM-agenten på den virtuella datorn**: Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om den virtuella datorn har skapats från en Azure marketplace-avbildning, är agenten installerad och körs. Om du skapar en anpassad virtuell dator eller om du migrerar en lokal virtuell dator, kan du behöva [installerar du agenten manuellt](#install-the-vm-agent).
- **Uttryckligen tillåta utgående åtkomst**: Normalt behöver du inte uttryckligen tillåta utgående nätverksåtkomst för en Azure-dator att kommunicera med Azure Backup. Men vissa virtuella datorer kan uppleva anslutningsproblem, som visar den **ExtensionSnapshotFailedNoNetwork** fel vid försök att ansluta. Om detta inträffar bör du [uttryckligen tillåta utgående åtkomst](#explicitly-allow-outbound-access), så att tillägget Azure Backup kan kommunicera med Azure offentliga IP-adresser för säkerhetskopieringen.


## <a name="create-a-vault"></a>Skapa ett valv

 Ett valv lagrar säkerhetskopior och återställningspunkter som skapats med tiden och lagrar principer för säkerhetskopiering som är associerade med säkerhetskopierade virtuella datorer. Skapa ett valv enligt följande:    

1. Logga in på [Azure Portal](https://portal.azure.com/).    
2. Skriv i Sök **återställningstjänster**. Under **Services**, klickar du på **Recovery Services-valv**.   

     ![Sök efter Recovery Services-valv](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/> 

3. I **Recovery Services-valv** -menyn klickar du på **+ Lägg till**.    

     ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)   

4. I **Recovery Services-valv**, Skriv ett eget namn som identifierar valvet.   
    - Namnet måste vara unikt för Azure-prenumerationen.   
    - Det kan innehålla 2 och 50 tecken.    
    - Det måste börja med en bokstav och det får innehålla endast bokstäver, siffror och bindestreck.   
5. Välj Azure-prenumeration, resursgrupp och geografiska region där valvet ska skapas. Klicka sedan på **Skapa**.    
    - Det kan ta en stund innan valvet har skapats.  
    - Övervaka Statusmeddelandena i området längst upp till höger i portalen.   


 När valvet har skapats visas den i listan över Recovery Services-valv. Om du inte ser ditt valv, väljer **uppdatera**.
 
![Lista över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)    

### <a name="modify-storage-replication"></a>Ändra storage-replikering

Som standard valv Använd [geo-redundant lagring (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

- Om valvet är din primära mekanism för säkerhetskopiering, rekommenderar vi att du använder GRS.
- Du kan använda [lokalt redundant lagring (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för ett billigare alternativ.

Ändra lagringsreplikeringstyp på följande sätt:

1. I det nya valvet klickar du på **egenskaper** i den **inställningar** avsnittet.
2. I **egenskaper**under **Säkerhetskopieringskonfigurationen**, klickar du på **uppdatering**.
3. Välj lagringstyp för replikering och klicka på **spara**.

      ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/full-blade.png)
> [!NOTE]
   > Du kan inte ändra lagringstypen för replikering när valvet har ställts in och innehåller säkerhetskopieringsobjekt. Om du vill göra detta måste du återskapa valvet. 

## <a name="apply-a-backup-policy"></a>Tillämpa en princip för säkerhetskopiering

Konfigurera en princip för säkerhetskopiering för valvet.

1. I valvet, klickar du på **+ säkerhetskopiera** i den **översikt** avsnittet.

   ![Säkerhetskopiering knappen](./media/backup-azure-arm-vms-prepare/backup-button.png)


2. I **säkerhetskopieringsmål** > **var körs din arbetsbelastning?** Välj **Azure**. I **vad vill du säkerhetskopiera?** Välj **VM** >  **OK**. Nu registreras VM-tillägget i valvet.

   ![Fönster för säkerhetskopiering och säkerhetskopieringsmål](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. I **säkerhetskopieringspolicy**, väljer du den princip som du vill associera med valvet. 
    - Standardprincipen säkerhetskopierar den virtuella datorn en gång om dagen. Dagliga säkerhetskopior bevaras i 30 dagar. Omedelbar återställning kvarhålls i två dagar.
    - Om du inte vill använda standardprincipen väljer **Skapa ny**, och skapa en anpassad princip som beskrivs i nästa procedur.

      ![Standardprincip för säkerhetskopiering](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. I **Välj virtuella datorer**, Välj de virtuella datorerna som du vill säkerhetskopiera använder principen. Klicka sedan på **OK**.

   - De valda virtuella datorerna verifieras.
   - Du kan bara välja virtuella datorer i samma region som valvet.
   - Virtuella datorer kan endast säkerhetskopieras i ett enda valv.

     ![Fönstret ”Välj virtuella datorer”](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. I **säkerhetskopiering**, klickar du på **Aktivera säkerhetskopiering**. Detta distribueras principen till valvet och till de virtuella datorerna och installerar tillägget för säkerhetskopiering på VM-agenten som körs på Azure VM.
     
     ![Knappen ”Aktivera säkerhetskopiering”](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

När du har aktiverat säkerhetskopiering:

- Backup-tjänsten installerar tillägget för säkerhetskopiering, oavsett om Virtuellt datorn körs.
- En första säkerhetskopieringen kommer att köras i enlighet med schemat för säkerhetskopiering.
- Observera att när säkerhetskopiering köras:
    - En virtuell dator som körs har bäst chans att tilldelas för att samla in en programkonsekvent återställningspunkt.
    - Men även om den virtuella datorn stängs av säkerhetskopieras den. Sådana en virtuell dator som kallas en offline-dator. I det här fallet kommer återställningspunkten vara kraschkonsekvent.
    

### <a name="create-a-custom-policy"></a>Skapa en anpassad princip

Om du valde för att skapa en ny säkerhetskopieringsprincip, fyller du i inställningarna för principen.

1. I **principnamn**, ange ett beskrivande namn.
2. I **schema för säkerhetskopiering** ange när säkerhetskopieringar ska vidtas. Du kan göra dagliga och veckovisa säkerhetskopior för virtuella Azure-datorer.
2. I **omedelbar återställning**, anger hur länge du vill behålla ögonblicksbilder lokalt för omedelbar återställning.
    - När du återställer säkerhetskopierade virtuell dator diskar kopieras från lagring, över nätverket till lagringsplatsen för återställning. Du kan utnyttja lokalt lagrade ögonblicksbilder som tas under säkerhetskopieringsjobbet, utan att behöva vänta säkerhetskopierade data överförs till valvet med omedelbar återställning.
    - Du kan behålla ögonblicksbilder för omedelbar återställning för mellan en till fem dagar. Två dagar är standardinställningen.
3. I **Kvarhållningsintervall**, ange hur länge du vill behålla dina dagliga och veckovisa säkerhetskopieringspunkter.
4. I **kvarhållning av månatlig säkerhetskopieringspunkt**, ange om du vill spara en månatlig säkerhetskopia av dina dagliga och veckovisa säkerhetskopior. 
5. Klicka på **OK** att spara principen.

    ![Ny säkerhetskopieringsprincip](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup stöder inte automatisk klockan justering för sommartid ändringar för Virtuella Azure-säkerhetskopieringar. Vid tidsändringar kan du ändra principer för säkerhetskopiering manuellt vid behov.

## <a name="trigger-the-initial-backup"></a>Utlösa den första säkerhetskopieringen

Den första säkerhetskopieringen kommer att köras i enlighet med schemat, men du kan köra den direkt enligt följande:

1. I menyn valvet klickar du på **Säkerhetskopiera objekt**.
2. I **Säkerhetskopieringsobjekt** klickar du på **Azure-dator**.
3. I den **Säkerhetskopieringsobjekt** klickar du på ellipserna (...).
4. Klicka på **Säkerhetskopiera nu**.
5. I **Säkerhetskopiera nu**, använder kalenderkontrollen för att välja den sista dagen som återställningspunkten ska behållas. Klicka sedan på **OK**.
6. Meddelandena som portalen. Du kan övervaka jobbförloppet i instrumentpanelen för valvet > **säkerhetskopieringsjobb** > **pågår**. Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.

## <a name="optional-steps-install-agentallow-outbound"></a>Valfria steg (installera agent/Tillåt utgående)
### <a name="install-the-vm-agent"></a>Installera VM-agenten

Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om den virtuella datorn har skapats från en Azure Marketplace-avbildning, är agenten installerad och körs. Om du skapar en anpassad virtuell dator eller om du migrerar en lokal virtuell dator, kan du behöva installera agenten manuellt, som sammanfattas i tabellen.

**VM** | **Detaljer**
--- | ---
**Windows** | 1. [Ladda ned och installera](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) agenten MSI-filen.<br/><br/> 2. Installera med administratörsbehörighet på datorn.<br/><br/> 3. Verifiera installationen. I *C:\WindowsAzure\Packages* på den virtuella datorn högerklickar du på **WaAppAgent.exe** > **egenskaper**. På den **information** fliken **produktversion** ska vara 2.6.1198.718 eller högre.<br/><br/> Om du uppdaterar agenten, se till att inga säkerhetskopieringsåtgärder körs, och [installera om agenten](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installera med hjälp av en RPM- eller DEB-paketet från paketdatabasen för din distribution. Det här är den bästa metoden för att installera och uppgradera Azure Linux-agent. Alla de [godkända distribution providers](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrera Azure Linux-agenten i sina avbildningar och databaser. Agenten är tillgängligt på [GitHub](https://github.com/Azure/WALinuxAgent), men vi rekommenderar inte att installera därifrån.<br/><br/> Om du uppdaterar agenten kan du kontrollera att inga säkerhetskopieringsåtgärder kör och uppdatera binärfilerna.

### <a name="explicitly-allow-outbound-access"></a>Uttryckligen tillåta utgående åtkomst

Säkerhetskopieringstillägget som körs på den virtuella datorn behöver utgående åtkomst till Azure offentliga IP-adresser.

- Vanligtvis behöver du inte uttryckligen tillåta utgående nätverksåtkomst för en Azure-dator att kommunicera med Azure Backup.
- Om du stöter på problem med virtuella datorer ansluter, eller om du ser felet **ExtensionSnapshotFailedNoNetwork** vid försök att ansluta, bör du uttryckligen tillåta åtkomst så att säkerhetskopieringstillägget kan kommunicera med Azure offentlig IP-adress adresser för säkerhetskopieringen. Åtkomstmetoder sammanfattas i tabellen nedan.


**Alternativ** | **Åtgärd** | **Detaljer** 
--- | --- | --- 
**Konfigurera NSG-regler** | Tillåt den [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/> I stället för att tillåta och hanterar varje adressintervall, kan du lägga till en regel som tillåter åtkomst till Azure Backup-tjänsten med en [servicetagg](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). | [Läs mer](../virtual-network/security-overview.md#service-tags) om tjänsttaggar.<br/><br/> Tjänster taggar förenkla hantering och innebära inte ytterligare kostnader.
**Distribuera en proxy** | Distribuera en HTTP-proxyserver dirigeras trafiken. | Ger åtkomst till hela Azure och inte bara lagring.<br/><br/> Detaljerad kontroll över storage-URL: er tillåts.<br/><br/> Enskild punkt för Internetåtkomst för virtuella datorer.<br/><br/> Ytterligare kostnader för proxy.
**Konfigurera Azure-brandväggen** | Tillåta trafik via Azure-brandväggen på den virtuella datorn med ett FQDN-taggen för Azure Backup-tjänsten | Enkelt att använda om du har Azure brandväggen ställts in i ett undernät för virtuellt nätverk.<br/><br/> Du kan inte skapa dina egna FQDN-taggar eller ändra FQDN: er i en tagg.<br/><br/> Om virtuella datorer i Azure hanterade diskar, kan du behöva öppna ytterligare port (8443) i brandväggar.

#### <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

Upprätta en anslutning med NSG, via proxyservern eller brandväggen

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
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

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

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>Tillåt brandväggsåtkomst med ett FQDN-tagg

Du kan ställa in Azure-brandväggen att tillåta utgående åtkomst för trafik till Azure Backup.

- [Lär dig mer om](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) distribuera Azure-brandvägg.
- [Läs mer om](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN-taggar.



## <a name="next-steps"></a>Nästa steg

- Felsök eventuella problem med [Azure VM-agenter](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) eller [virtuell Azure-säkerhetskopiering](backup-azure-vms-troubleshoot.md).
- [Återställa](backup-azure-arm-restore-vms.md) virtuella Azure-datorer.

