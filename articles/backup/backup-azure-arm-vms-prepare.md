---
title: Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services valv med Azure Backup
description: Beskriver hur du säkerhetskopierar virtuella Azure-datorer i ett Recovery Services valv med hjälp av Azure Backup
service: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: dacurwin
ms.openlocfilehash: 9a6ea961f7433f511ef22a6ac9aaefa51b5df8aa
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663694"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services valv

Den här artikeln beskriver hur du säkerhetskopierar virtuella Azure-datorer i ett Recovery Services valv med hjälp av tjänsten [Azure Backup](backup-overview.md) .

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Förbered virtuella Azure-datorer.
> * Skapa ett valv.
> * Identifiera virtuella datorer och konfigurera en säkerhets kopierings princip.
> * Aktivera säkerhets kopiering för virtuella Azure-datorer.
> * Kör den första säkerhetskopieringen.


> [!NOTE]
> Den här artikeln beskriver hur du konfigurerar ett valv och väljer virtuella datorer som ska säkerhets kopie ras. Det är användbart om du vill säkerhetskopiera flera virtuella datorer. Alternativt kan du [säkerhetskopiera en enskild virtuell Azure-dator](backup-azure-vms-first-look-arm.md) direkt från VM-inställningarna.

## <a name="before-you-start"></a>Innan du börjar


- [Granska](backup-architecture.md#architecture-direct-backup-of-azure-vms) arkitekturen för säkerhets kopiering av virtuella Azure-datorer.
- [Läs mer om](backup-azure-vms-introduction.md) Säkerhets kopiering av virtuella Azure-datorer och tillägget för säkerhets kopiering.
- [Granska support mat ris](backup-support-matrix-iaas.md) innan du konfigurerar säkerhets kopiering.

Dessutom finns det några saker som du kan behöva göra i vissa fall:

- **Installera VM-agenten på den virtuella datorn**: Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om den virtuella datorn skapades från en Azure Marketplace-avbildning installeras och körs agenten. Om du skapar en anpassad virtuell dator, eller om du migrerar en lokal dator, kan du behöva [Installera agenten manuellt](#install-the-vm-agent).
- **Tillåt uttryckligen utgående åtkomst**: Normalt behöver du inte uttryckligen tillåta utgående nätverks åtkomst för en virtuell Azure-dator för att det ska kunna kommunicera med Azure Backup. Vissa virtuella datorer kan dock drabbas av anslutnings problem, vilket visar **ExtensionSnapshotFailedNoNetwork** -fel vid försök att ansluta. Om detta händer bör du [uttryckligen tillåta utgående åtkomst](#explicitly-allow-outbound-access), så Azure Backup tillägget kan kommunicera med offentliga Azure-IP-adresser för säkerhets kopierings trafik.


## <a name="create-a-vault"></a>Skapa ett valv

 Ett valv lagrar säkerhets kopior och återställnings punkter som skapats med tiden och lagrar säkerhets kopierings principer som är associerade med säkerhetskopierade datorer. Skapa ett valv på följande sätt:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. I Sök skriver du **Recovery Services**. Under **tjänster**klickar du på **Recovery Services valv**.

     ![Sök efter Recovery Services valv](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. I menyn **Recovery Services valv** klickar du på **+ Lägg till**.

     ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. I **Recovery Services valv**skriver du ett eget namn för att identifiera valvet.
    - Namnet måste vara unikt för Azure-prenumerationen.
    - Det kan innehålla 2 till 50 tecken.
    - Det måste börja med en bokstav och får bara innehålla bokstäver, siffror och bindestreck.
5. Välj den Azure-prenumeration, resurs grupp och geografiska region där valvet ska skapas. Klicka sedan på **Skapa**.
    - Det kan ta en stund innan valvet har skapats.
    - Övervaka status meddelanden i det övre högra avsnittet i portalen.


 När valvet har skapats visas det i listan Recovery Services valv. Om du inte ser ditt valv väljer du **Uppdatera**.

![Lista över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

> [!NOTE]
> Azure Backup tjänst skapar en separat resurs grupp (förutom VM-gruppgruppen) för att lagra ögonblicks bilder med namngivnings formatet **AzureBackupRG_geography_number** (exempel: AzureBackupRG_northeurope_1). Data i den här resurs gruppen kommer att behållas under den tid i dagar som anges i avsnittet *Behåll ögonblicks bild av ögonblicks bilder* i säkerhets kopierings principen för den virtuella Azure-datorn.  Att använda ett lås till den här resurs gruppen kan orsaka säkerhets kopierings fel.<br>
Den här resurs gruppen ska också undantas från eventuella namn-och märkes begränsningar som en begränsnings princip skulle blockera skapandet av resurs plats samlingar i den igen och orsaka säkerhets kopierings problem.


### <a name="modify-storage-replication"></a>Ändra Storage Replication

Som standard använder valven [Geo-redundant lagring (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

- Om valvet är din primära mekanism för säkerhets kopiering rekommenderar vi att du använder GRS.
- Du kan använda [LRS (lokalt redundant lagring)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för ett billigare alternativ.

Ändra typ av lagringsreplik-replikering enligt följande:

1. Klicka på **Egenskaper** i avsnittet **Inställningar** i det nya valvet.
2. I **Egenskaper**, under **säkerhets kopierings konfiguration**, klickar du på **Uppdatera**.
3. Välj typ av lagrings replikering och klicka på **Spara**.

      ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/full-blade.png)
> [!NOTE]
   > Du kan inte ändra typen av lagringsprovider när valvet har kon figurer ATS och innehåller säkerhets kopierings objekt. Om du vill göra det måste du återskapa valvet.

## <a name="apply-a-backup-policy"></a>Tillämpa en princip för säkerhets kopiering

Konfigurera en säkerhets kopierings policy för valvet.

1. Klicka på **+ säkerhetskopiera** i avsnittet **Översikt** i valvet.

   ![Säkerhets kopierings knapp](./media/backup-azure-arm-vms-prepare/backup-button.png)


2. I **säkerhets kopierings mål** > **var din arbets belastning körs? väljer du** **Azure**. I **vad vill du säkerhetskopiera?** Välj **virtuell dator** >  **OK**. Detta registrerar VM-tillägget i valvet.

   ![Säkerhetskopiera och säkerhetskopiera mål fönster](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. I **säkerhets kopierings princip**väljer du den princip som du vill associera med valvet.
    - Standard principen säkerhetskopierar den virtuella datorn en gång om dagen. De dagliga säkerhets kopiorna behålls i 30 dagar. Ögonblicks bilder av snabb återställning sparas i två dagar.
    - Om du inte vill använda standard principen väljer du **Skapa ny**och skapar en anpassad princip enligt beskrivningen i nästa procedur.

      ![Standard princip för säkerhets kopiering](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. I **Välj virtuella datorer**väljer du de virtuella datorer som du vill säkerhetskopiera med hjälp av principen. Klicka sedan på **OK**.

   - De valda virtuella datorerna verifieras.
   - Du kan bara välja virtuella datorer i samma region som valvet.
   - Virtuella datorer kan bara säkerhets kopie ras i ett enda valv.

     ![Fönstret Välj virtuella datorer](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. I **säkerhets kopiering**klickar du på **Aktivera säkerhets kopiering**. Detta distribuerar principen till valvet och till de virtuella datorerna och installerar säkerhets kopierings tillägget på VM-agenten som körs på den virtuella Azure-datorn.

     ![Knappen Aktivera säkerhets kopiering](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

När du har aktiverat säkerhets kopiering:

- Säkerhets kopierings tjänsten installerar säkerhets kopierings tillägget oavsett om den virtuella datorn körs eller inte.
- En första säkerhets kopiering kommer att köras enligt ditt schema för säkerhets kopiering.
- Tänk på följande när säkerhets kopieringarna körs:
    - En virtuell dator som kör har störst chans att fånga en programkonsekvent återställnings punkt.
    - Men även om den virtuella datorn är avstängd, säkerhets kopie ras. En sådan virtuell dator kallas för en virtuell dator som är offline. I det här fallet är återställnings punkten krasch-konsekvent.


### <a name="create-a-custom-policy"></a>Skapa en anpassad princip

Om du har valt att skapa en ny säkerhets kopierings princip, fyller du i princip inställningarna.

1. I **princip namn**anger du ett beskrivande namn.
2. I **schema för säkerhets kopiering** anger du när säkerhets kopior ska vidtas. Du kan utföra dagliga eller veckovis säkerhets kopieringar för virtuella Azure-datorer.
2. I **omedelbar återställning**anger du hur länge du vill behålla ögonblicks bilder lokalt för omedelbar återställning.
    - När du återställer kopieras de säkerhetskopierade virtuella dator diskarna från Storage, över nätverket till återställnings lagrings platsen. Med omedelbar återställning kan du utnyttja lokalt lagrade ögonblicks bilder som tas under ett säkerhets kopierings jobb utan att vänta på att säkerhets kopierings data överförs till valvet.
    - Du kan behålla ögonblicks bilder för omedelbar återställning mellan en och fem dagar. Standardvärdet är två dagar.
3. I **kvarhållningsintervall**anger du hur länge du vill behålla dina dagliga eller vecko Visa säkerhets kopierings punkter.
4. I **kvarhållning av månatlig säkerhets kopierings punkt**anger du om du vill behålla en månatlig säkerhets kopia av dina dagliga eller vecko Visa säkerhets kopior.
5. Spara principen genom att klicka på **OK** .

    ![Ny säkerhets kopierings princip](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup stöder inte automatisk klock justering för sommar tids ändringar för virtuella Azure-säkerhetskopieringar. När tids ändringarna inträffar ändrar du säkerhets kopierings principerna manuellt vid behov.

## <a name="trigger-the-initial-backup"></a>Utlös den första säkerhets kopieringen

Den första säkerhets kopieringen kommer att köras enligt schemat, men du kan köra den direkt på följande sätt:

1. I menyn valv klickar du på **säkerhets kopierings objekt**.
2. I **säkerhets kopierings objekt** klickar du på **virtuell Azure-dator**.
3. I listan **säkerhets kopierings objekt** klickar du på ellipserna (...).
4. Klicka på **Säkerhetskopiera nu**.
5. I **Säkerhetskopiera nu**använder du kalender kontrollen för att välja den sista dagen som återställnings punkten ska behållas. Klicka sedan på **OK**.
6. Övervaka Portal meddelanden. Du kan övervaka jobb förloppet i valv instrument panelen > **säkerhets kopierings jobb** > **pågår**. Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.

## <a name="verify-backup-job-status"></a>Verifiera status för säkerhets kopierings jobb

Informationen om säkerhets kopierings jobbet för varje VM-säkerhetskopiering består av två faser, ögonblicks bilds fasen följt av **överförings data till valv** fasen.<br/>
Ögonblicks bild fasen garanterar tillgängligheten för en återställnings punkt som lagras tillsammans med diskarna för **omedelbara** återställningar och är tillgängliga i högst 5 dagar beroende på den kvarhållna ögonblicks bilder som kon figurer ATS av användaren. Överföring av data till valv skapar en återställnings punkt i valvet för långsiktig kvarhållning. Överföring av data till valv startar bara när ögonblicks bild fasen har slutförts.

  ![Status för säkerhets kopierings jobb](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Det finns två **under aktiviteter** som körs på Server delen, en för säkerhets kopierings jobb på klient sidan som kan kontrol leras från bladet **säkerhets kopierings jobb** enligt vad som anges nedan:

  ![Status för säkerhets kopierings jobb](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Det kan ta flera dagar innan **överförings data till valv** fasen slutförs beroende på storleken på diskarna, omsättningen per disk och flera andra faktorer.

Jobbets status kan variera beroende på följande scenarier:

**Ögonblicks bild** | **Överför data till valv** | **Jobb status**
--- | --- | ---
Slutfört | Pågår | Pågår
Slutfört | Hoppades över | Slutfört
Slutfört | Slutfört | Slutfört
Slutfört | Misslyckad | Slutfört med varning
Misslyckad | Misslyckad | Misslyckad


Med den här funktionen kan två säkerhets kopior för samma virtuella dator köras parallellt, men i båda skedet (ögonblicks bilder, överför data till valv) kan endast en under aktivitet köras. I situationer då en pågående säkerhets kopiering resulterade i att säkerhets kopieringen till nästa dag inte kunde köras, undviks den här kopplings funktionen. Efterföljande dagars säkerhets kopieringar kan ha en ögonblicks bild som slutförts medan **överföring av data till valvet** hoppades över om en tidigare säkerhets kopierings jobb pågår.
Den stegvisa återställnings punkten som skapades i valvet fångar upp omsättningen från den senaste återställnings punkten som skapades i valvet. Det kostar inget att påverka användaren.


## <a name="optional-steps-install-agentallow-outbound"></a>Valfria steg (installera agent/Tillåt utgående)
### <a name="install-the-vm-agent"></a>Installera VM-agenten

Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om den virtuella datorn skapades från en Azure Marketplace-avbildning installeras och körs agenten. Om du skapar en anpassad virtuell dator, eller om du migrerar en lokal dator, kan du behöva installera agenten manuellt, som sammanfattas i tabellen.

**DATORN** | **Detaljer**
--- | ---
**Windows** | 1. [Ladda ned och installera](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) agent-MSI-filen.<br/><br/> 2. Installera med administratörs behörighet på datorn.<br/><br/> 3. Verifiera installationen. I *C:\WindowsAzure\Packages* på den virtuella datorn högerklickar du på**Egenskaper**för **WaAppAgent. exe** > . **Produkt versionen** bör vara 2.6.1198.718 eller högre på fliken **information** .<br/><br/> Om du uppdaterar agenten ser du till att inga säkerhets kopierings åtgärder körs och [installerar om agenten](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installera med hjälp av ett RPM-eller DEB-paket från distributionens paket lagrings plats. Detta är den bästa metoden för att installera och uppgradera Azure Linux-agenten. Alla godkända [distributions leverantörer](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrerar Azure Linux Agent-paketet i sina avbildningar och databaser. Agenten är tillgänglig på [GitHub](https://github.com/Azure/WALinuxAgent), men vi rekommenderar inte att du installerar därifrån.<br/><br/> Om du uppdaterar agenten ska du kontrol lera att inga säkerhets kopierings åtgärder körs och uppdatera binärfilerna.

### <a name="explicitly-allow-outbound-access"></a>Tillåt uttryckligen utgående åtkomst

Säkerhets kopierings tillägget som körs på den virtuella datorn behöver utgående åtkomst till offentliga Azure-IP-adresser.

- Normalt behöver du inte uttryckligen tillåta utgående nätverks åtkomst för en virtuell Azure-dator för att kunna kommunicera med Azure Backup.
- Om du stöter på problem med att ansluta virtuella datorer, eller om du ser felet **ExtensionSnapshotFailedNoNetwork** vid försök att ansluta, bör du uttryckligen tillåta åtkomst så att säkerhets kopierings tillägget kan kommunicera med offentliga Azure-IP-adresser för säkerhets kopiering aktiviteten. Åtkomst metoder sammanfattas i följande tabell.


**Alternativ** | **Åtgärd** | **Detaljer**
--- | --- | ---
**Konfigurera NSG-regler** | Tillåt [IP-intervall för Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/> I stället för att tillåta och hantera varje adress intervall kan du lägga till en regel som tillåter åtkomst till den Azure Backup tjänsten med hjälp av en [service tag](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). | [Lär dig mer](../virtual-network/security-overview.md#service-tags) om service märken.<br/><br/> Services-Taggar fören klar åtkomst hanteringen och debiteras inte ytterligare kostnader.
**Distribuera en proxy** | Distribuera en HTTP-proxyserver för routning av trafik. | Ger åtkomst till hela Azure och inte bara lagring.<br/><br/> Detaljerad kontroll över lagrings-URL: er tillåts.<br/><br/> Enskild punkt för Internet åtkomst för virtuella datorer.<br/><br/> Ytterligare kostnader för proxy.
**Konfigurera Azure-brandvägg** | Tillåt trafik via Azure-brandväggen på den virtuella datorn med hjälp av en FQDN-tagg för tjänsten Azure Backup | Enkelt att använda om du har konfigurerat Azure-brandväggen i ett VNet-undernät.<br/><br/> Du kan inte skapa egna FQDN-taggar eller ändra FQDN i en-tagg.<br/><br/> Om dina virtuella Azure-datorer har hanterade diskar kan du behöva öppna ytterligare en port (8443) i brand väggarna.

#### <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

Upprätta anslutningar med NSG, via proxy eller brand väggen

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Konfigurera en NSG-regel för att tillåta utgående åtkomst till Azure

Om en NSG hanterar VM-åtkomsten tillåter du utgående åtkomst för lagring av säkerhets kopior till de obligatoriska intervallen och portarna.

1. I egenskaperna för den virtuella datorn > **nätverk**väljer du **Lägg till regel för utgående port**.
2. I **Lägg till utgående säkerhets regel**väljer du **Avancerat**.
3. I **källa**väljer du **VirtualNetwork**.
4. I **käll ports intervall**anger du en asterisk (*) för att tillåta utgående åtkomst från vilken port som helst.
5. I **mål**väljer du **service tag**. I listan väljer du **Storage. region**. Regionen är den plats där valvet och de virtuella datorer som du vill säkerhetskopiera finns.
6. I **mål ports intervall**väljer du porten.
    - Virtuell dator med ohanterade diskar med okrypterade lagrings konton: 80
    - Virtuell dator med ohanterade diskar med krypterat lagrings konto: 443 (standardinställning)
    - Virtuell dator med hanterade diskar: 8443.
7. I **protokoll**väljer du **TCP**.
8. I **prioritet**anger du ett prioritets värde som är mindre än reglerna för högre neka.

   Om du har en regel som nekar åtkomst måste den nya regeln för Tillåt vara högre. Om du till exempel har en **Deny_All** regel uppsättning på prioritet 1000 måste den nya regeln vara lägre än 1000.
9. Ange ett namn och en beskrivning för regeln och välj **OK**.

Du kan tillämpa NSG-regeln på flera virtuella datorer för att tillåta utgående åtkomst. Den här videon vägleder dig genom processen.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>Dirigera säkerhets kopierings trafik via en proxy

Du kan dirigera säkerhets kopierings trafik via en proxy och ge proxy-åtkomst till de nödvändiga Azure-intervallen. Konfigurera den virtuella proxy-datorn så att den tillåter följande:

- Den virtuella Azure-datorn ska dirigera all HTTP-trafik som är kopplad till det offentliga Internet via proxyservern.
- Proxyservern ska tillåta inkommande trafik från virtuella datorer i det aktuella virtuella nätverket.
- NSG **NSF-Lockdown** behöver en regel som tillåter utgående Internet trafik från den virtuella proxy-datorn.

###### <a name="set-up-the-proxy"></a>Konfigurera proxyn

Om du inte har en system konto-proxy anger du ett på följande sätt:

1. Ladda ned [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Kör **PsExec. exe-i-s cmd. exe** för att köra kommando tolken under ett system konto.
3. Kör webbläsaren i system kontexten. Använd till exempel **%ProgramFiles%\Internet Explorer\iexplore.exe** för Internet Explorer.  
4. Definiera proxyinställningarna.
   - På Linux-datorer:
     - Lägg till den här raden i **/etc/Environment** -filen:
       - **http_proxy = http:\//proxy IP-adress: proxy-port**
     - Lägg till dessa rader i **/etc/waagent.conf** -filen:
         - **HttpProxy. Host = proxy-IP-adress**
         - **HttpProxy. port = proxy-port**
   - I webb läsar inställningarna på Windows-datorer anger du att en proxy ska användas. Om du för närvarande använder en proxyserver för ett användar konto kan du använda det här skriptet för att tillämpa inställningen på system konto nivå.
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

1. Öppna **Windows-brandväggen med avancerad säkerhet**i Windows-brandväggen.
2. Högerklicka på **regler** > för inkommande**trafik ny regel**.
3. I **regel typ**väljer du **anpassad** > **Nästa**.
4. I **program**väljer du **alla program** > **Nästa**.
5. I **protokoll och portar**:
   - Ange **TCP**som typ.
   - Ange **lokala portar** för **vissa portar**.
   - Ange fjärrporten till **alla portar**.

6. Slutför guiden och ange ett namn för regeln.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Lägg till en undantags regel i NSG för proxyn

På NSG **NSF-Lockdown**tillåter du trafik från alla portar på 10.0.0.5 till valfri Internet adress på port 80 (http) eller 443 (https).

Följande PowerShell-skript ger ett exempel på hur trafik tillåts.
I stället för att tillåta utgående till alla offentliga Internet adresser, kan du ange ett IP-`-DestinationPortRange`adressintervall () eller använda tjänst tag gen Storage. region.   

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>Tillåt brand Väggs åtkomst med en FQDN-tagg

Du kan konfigurera Azure-brandväggen så att den tillåter utgående åtkomst för nätverks trafik till Azure Backup.

- [Lär dig mer om](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) att distribuera Azure-brandväggen.
- [Läs om](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN-taggar.



## <a name="next-steps"></a>Nästa steg

- Felsök eventuella problem med [Azure VM-agenter](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) eller [Azure VM-säkerhetskopiering](backup-azure-vms-troubleshoot.md).
- [Återställ](backup-azure-arm-restore-vms.md) Virtuella Azure-datorer.
