---
title: Förbereda säkerhetskopiering av virtuella Azure-datorer med Azure Backup
description: Beskriver hur du förbereder Azure virtuella datorer för säkerhetskopiering med Azure Backup-tjänsten
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: raynew
ms.openlocfilehash: a7a2d8729e1abdafa89eff912faf84d8f247b442
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215447"
---
# <a name="prepare-to-back-up-azure-vms"></a>Förbereda säkerhetskopiering av virtuella Azure-datorer

Den här artikeln beskriver hur du förbereder för att säkerhetskopiera en virtuell Azure-dator med hjälp av en [Azure Backup](backup-introduction-to-azure-backup.md) Recovery Services-valv. Förbereda för säkerhetskopiering inkluderar:


> [!div class="checklist"]
> * Innan du börjar bör du granska scenarier som stöds och begränsningar.
> * Kontrollera förutsättningarna, inklusive Azure VM-krav och nätverksanslutning.
> * Skapa ett valv.
> * Välj hur lagring replikerar.
> * Identifiera virtuella datorer, konfigurera inställningar för säkerhetskopiering och principen.
> * Aktivera säkerhetskopiering för valda virtuella datorerna


> [!NOTE]
   > Den här artikeln beskriver hur du säkerhetskopierar virtuella Azure-datorer genom att skapa ett valv och välja virtuella datorer för att säkerhetskopiera. Det är användbart om du vill säkerhetskopiera flera virtuella datorer. Du kan också säkerhetskopiera en virtuell Azure-dator direkt från dess inställningar för virtuell dator. [Läs mer](backup-azure-vms-first-look-arm.md)

## <a name="before-you-start"></a>Innan du börjar

1. [Få en översikt](backup-azure-vms-introduction.md) Azure Backup för virtuella Azure-datorer.
2. Granska information om support och begränsningar som nedan.

   **Support/begränsning** | **Detaljer**
   --- | ---
   **Windows OS** | Windows Server 2008 R2 64-bitars eller senare.<br/><br/> Windows Client 7 64-bitars eller senare.
   **Linux OS** | Du kan säkerhetskopiera en Linux-distributioner för 64-bitars [stöds av Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), med undantag för CoreOS Linux.<br/><br/> Granska Linux-operativsystem som [stöd för filåterställning](backup-azure-restore-files-from-vm.md#for-linux-os).<br/><br/> Andra Linux-distributioner kan fungera, så länge som den Virtuella datoragenten är tillgänglig på den virtuella datorn och har stöd för Python finns. Men stöds dessa distributioner inte.
   **Region** | Du kan säkerhetskopiera virtuella Azure-datorer i alla [regioner som stöds](https://azure.microsoft.com/regions/#services). Om en region inte stöds, kan du inte välja det när du skapar valvet.<br/><br/> Du kan inte säkerhetskopiera och återställa i Azure-regioner. Endast inom en enda region.
   **Disk datagräns** | Du kan inte säkerhetskopiera virtuella datorer med fler än 16 datadiskar.
   **Delad lagring** | Vi rekommenderar inte att säkerhetskopiera virtuella datorer med hjälp av CSV- eller Scale-Out File Server. CSV-skrivarna är sannolikt att misslyckas.
   **Linux-kryptering** | Säkerhetskopiering av virtuella Linux-datorer krypteras med Linux Unified nyckel installationsprogrammet (LUKS) stöds inte.
   **VM-konsekvens** | Azure Backup stöder inte konsekvens.
   **Nätverk** | Säkerhetskopierade data omfattar inte monterade nätverksenheter som är kopplade till en virtuell dator.<br/><br/>
   **Ögonblicksbilder** | Att ta ögonblicksbilder på en write accelerator-aktiverade disk stöds inte. Azure Backup blockeras från att ta en programkonsekvent ögonblicksbild av alla VM-diskar.
   **PowerShell** | Det finns ett antal åtgärder som endast är tillgängliga med PowerShell:<br/><br/> – Återställning av virtuella datorer hanteras av intern/extern belastningsutjämnare eller med flera reserverade IP-adresser eller kort. [Läs mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)<br/><br/> – Återställa en domänkontrollant virtuell dator i en multi konfigurationen av domänkontrollanten. [Läs mer](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
   **Systemtiden** | Azure Backup stöder inte automatisk klockan justering för sommartid ändringar för Virtuella Azure-säkerhetskopieringar. Ändra principer för säkerhetskopiering manuellt vid behov.
   **Lagringskonton** | Om du använder ett nätverksbegränsade storage-konto, se till att du aktiverar **Tillåt att betrodda Microsoft-tjänster för att komma åt det här lagringskontot** så att Azure Backup-tjänsten kan komma åt kontot. Återställning på objektnivå stöds inte för nätverksbegränsade lagringskonton.<br/><br/> I ett lagringskonto, se till att **brandväggar och virtuella nätverk** tillåter åtkomst från **alla nätverk**.


## <a name="prerequisites"></a>Förutsättningar

- Du måste skapa valvet i samma region som virtuella Azure-datorer du vill säkerhetskopiera.
- Kontrollera de Virtuella Azure-regionerna innan du börjar.
    - Om du har virtuella datorer i flera regioner kan du skapa ett valv i varje region.
    - Du behöver inte ange lagringskonton för att lagra säkerhetskopierade data. Valvet och tjänsten Azure Backup hanterar du som automatiskt.
- Kontrollera att den Virtuella datoragenten är installerad på Azure virtuella datorer som du vill säkerhetskopiera.



### <a name="install-the-vm-agent"></a>Installera VM-agenten

Om du vill aktivera säkerhetskopiering av installerar Azure Backup säkerhetskopieringstillägget (ögonblicksbild för virtuell dator eller virtuell dator ögonblicksbild Linux) till VM-agenten som körs på Azure VM.
    -  Azure VM-agenten installeras som standard på virtuella Windows-datorer som distribueras från Azure Marketplace-avbildning. När du distribuerar en Azure Marketplace-avbildning från portalen, PowerShell, CLI eller en Azure Resource Manager-mall, installeras även Azure VM-agenten.
    - Om du har migrerat en virtuell dator från den lokala agenten är inte installerad och du måste installera den innan du kan aktivera säkerhetskopiering för den virtuella datorn.

Om det behövs installerar du agenten på följande sätt.

**VIRTUELL DATOR** | **Detaljer**
--- | ---
**Virtuella Windows-datorer** | [Ladda ned och installera](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) agenten med administratörsbehörighet på datorn.<br/><br/> Verifiera installationen, i *C:\WindowsAzure\Packages* på den virtuella datorn högerklickar du på WaAppAgent.exe > **egenskaper**, > **information** fliken. **Produktversion** ska vara 2.6.1198.718 eller högre.
**Virtuella Linux-datorer** | Installationen med hjälp av en RPM- eller DEB-paketet från paketdatabasen för din distribution är den bästa metoden för att installera och uppgradera Azure Linux Agent. Alla de [godkända distribution providers](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrera Azure Linux-agenten i sina avbildningar och databaser. Agenten är tillgängligt på [GitHub](https://github.com/Azure/WALinuxAgent), men vi rekommenderar inte att installera därifrån.
Om du har problem med säkerhetskopiering av Virtuella Azure kan du använda följande tabell för att kontrollera att Azure VM-agenten är korrekt installerad på den virtuella datorn. Tabellen innehåller ytterligare information om VM-agenten för Windows och Linux-datorer.

### <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

Säkerhetskopieringstillägget som körs på den virtuella datorn måste ha utgående åtkomst till Azure offentliga IP-adresser. För att tillåta åtkomst kan du:


- **NSG-regler**: Tillåt den [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653). Du kan lägga till en regel som tillåter åtkomst till Azure Backup-tjänsten med en [servicetagg](../virtual-network/security-overview.md#service-tags), i stället för enskilt så att varje adressintervall och hantera dem över tid.
- **Proxy**: Distribuera en HTTP-proxyserver dirigeras trafiken.
- **Azure-brandväggen**: Tillåta trafik via Azure-brandväggen på den virtuella datorn med ett FQDN-taggen för Azure Backup-tjänsten.

Tänk på kompromisser när du bestämmer mellan alternativen.

**Alternativ** | **Fördelar** | **Nackdelar**
--- | --- | ---
**NSG** | Inga ytterligare kostnader. Enkelt att hantera med tjänsttaggar | Ger åtkomst till hela Azure och inte bara lagring. |
**HTTP-proxy** | Detaljerad kontroll över storage-URL: er tillåts.<br/><br/> Enskild punkt för Internetåtkomst för virtuella datorer.<br/><br/> Ytterligare kostnader för proxy.
**FQDN-taggar** | Enkelt att använda om du har Azure brandväggen ställts in i ett undernät för virtuellt nätverk | Det går inte att skapa dina egna FQDN-taggar eller ändra FQDN: er i en tagg.



Om du använder Azure Managed Disks kan behöva du ett inledande ytterligare porten (port 8443) i brandväggar.



### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Konfigurera en NSG-regel för att tillåta utgående åtkomst till Azure

Om din virtuella Azure-datorn har åtkomst som hanteras av en NSG, Tillåt utgående åtkomst för säkerhetskopieringslagring till nödvändiga intervall och portar.



1. I den virtuella datorn > **nätverk**, klickar du på **Lägg till regel för utgående port**.
- Om du har en regel som nekar åtkomst kan kan de nya regeln måste vara högre. Exempel: Om du har en **Deny_All** regeluppsättning med prioritet 1000, din nya regel måste anges till mindre än 1 000.
2. I **Lägg till utgående säkerhetsregel**, klickar du på **Avancerat**.
3. I källan, Välj **VirtualNetwork**.
4. I **Source portintervall**, en asterisk (*) anger att tillåta utgående åtkomst från alla portar.
5. I **mål**väljer **Tjänsttagg**. Välj lagring i listan. <region>. Regionen är den region som valvet och de virtuella datorerna som du vill säkerhetskopiera finns.
6. I **målportsintervall**, markera porten.

    - Virtuell dator med ohanterade diskar och okrypterat lagringskonto: 80
    - Virtuell dator med ohanterade diskar och krypterade storage-konto: 443 (standardinställning)
    - Den hanterade virtuella datorn: 8443.
1. I **protokollet**väljer **TCP**.
2. I **prioritet**, ge den ett prioritetsvärde mindre än högre neka regler.
3. Ange ett namn och beskrivning för regeln och klickar på **OK**.

Du kan använda NSG-regel för att tillåta utgående åtkomst till Azure för Azure Backup på flera virtuella datorer.

Den här videon går igenom processen.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]



### <a name="route-backup-traffic-through-a-proxy"></a>Backup-trafik via en proxy

Du kan dirigera säkerhetskopiering trafik via en proxyserver och sedan ge proxy-åtkomst till de nödvändiga Azure intervall.

Du bör konfigurera proxyn VM att tillåta följande:

- Azure VM ska vidarebefordra alla HTTP-trafik som är bunden till det offentliga internet via proxy.
- Proxyn ska tillåta inkommande trafik från virtuella datorer i det virtuella nätverket (VNet).
- NSG: N **NSF låsning** måste en regel som tillåter utgående internet-trafik från proxy VM.

Här är hur du behöver konfigurera proxyn. Vi använder exempelvärden. Du bör ersätta dem med dina egna.

#### <a name="set-up-a-system-account-proxy"></a>Konfigurera en proxyserver för system-konto
Om du inte har en proxy för system-konto, konfigurera en enligt följande:

1. Ladda ned [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Kör **PsExec.exe -i -s cmd.exe** att köra kommandotolken under ett systemkonto.
3. Kör webbläsaren i systemkontexten. Exempel: **ProgramFiles%\Internet Explorer\iexplore.exe** för Internet Explorer.  
4. Definiera proxyinställningarna.
    - På Linux-datorer:
        - Lägg till följande rad till den **/etc/miljö** fil:
            - **http_proxy =http://proxy IP-adress: Proxyport**
        - Lägg till följande rader till den **/etc/waagent.conf** fil:
            - **HttpProxy.Host=proxy IP-adress**
            - **HttpProxy.Port=proxy port**
    - Ange att en proxyserver ska användas på Windows-datorer i inställningarna för webbläsaren. Om du använder en proxyserver för ett användarkonto, kan du använda det här skriptet för att använda inställningen på systemnivå-konto.
        ```
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

        ```

#### <a name="allow-incoming-connections-on-the-proxy"></a>Tillåt inkommande anslutningar på proxyn

1. Tillåt inkommande anslutningar i proxyinställningarna.
2. Till exempel öppna **Windows-brandväggen med avancerad säkerhet**.
    - Högerklicka på **regler för inkommande** > **ny regel**.
    - I **regeltyp** Välj **anpassade** > **nästa**.
    - I **programmet**väljer **alla program** > **nästa**.
    - I **protokoll och portar** anger objekttyp **TCP**, **lokala portar** till **specifika portar**, och **Fjärrport**till **alla portar**.
    - Slutför guiden och ange ett namn för regeln.

#### <a name="add-an-exception-rule-to-the-nsg"></a>Lägg till en undantagsregel i NSG

På NSG: N **NSF låsning**, tillåta trafik från alla portar på 10.0.0.5 till en Internetadress på port 80 (HTTP) eller 443 (HTTPS).

- Följande PowerShell-skript innehåller ett exempel för att tillåta trafik.
- I stället för att tillåta utgående trafik till alla offentliga internet-adresser, kan du ange ett IP-adressintervall (-DestinationPortRange), eller Använd storage.region tjänsttagg.   

    ```
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```
### <a name="allow-firewall-access-with-fqdn-tag"></a>Tillåt brandväggsåtkomst med FQDN-tagg

Du kan ställa in Azure-brandväggen att tillåta utgående åtkomst för trafik till Azure Backup.

- [Lär dig mer om](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) distribuera Azure-brandvägg.
- [Läs mer om](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN-taggar.


## <a name="create-a-vault"></a>Skapa ett valv

Ett Recovery Services-valv för säkerhetskopiering lagrar säkerhetskopior och återställningspunkter som skapats med tiden och lagrar principer för säkerhetskopiering som är associerade med säkerhetskopierade virtuella datorer. Skapa ett valv enligt följande:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. På den **Hub** menyn och välj **Bläddra**, och skriv **återställningstjänster**. När du börjar skriva, filtrerar listan över resurser i dina indata. Välj **Recovery Services-valv**.

    ![Att skriva i rutan och välja ”Recovery Services-valv” i resultaten](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Listan över Recovery Services-valv visas.
3. På den **Recovery Services-valv** menyn och välj **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Den **Recovery Services-valv** öppnas fönstret. Uppmanas du att ange information för **namn**, **prenumeration**, **resursgrupp**, och **plats**.

    ![Fönstret ”recovery Services-valv”](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. I **Namn** anger du ett eget namn som identifierar valvet.
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


## <a name="configure-backup"></a>Konfigurera säkerhetskopiering

Identifiera virtuella datorer i prenumerationen och konfigurera säkerhetskopiering.

1. I valvet > **översikt**, klickar du på **+ säkerhetskopiering**

   ![Säkerhetskopiering knappen](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Den **Backup** och **säkerhetskopieringsmål** fönstren öppna.

2. I **säkerhetskopieringsmål**> **var körs din arbetsbelastning?** väljer **Azure**. I **vad vill du säkerhetskopiera?** väljer **VM** >  **OK**. Nu registreras VM-tillägget i valvet.

   ![Fönster för säkerhetskopiering och säkerhetskopieringsmål](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Det här steget registrerar VM-tillägget med valvet. Den **säkerhetskopieringsmål** fönstret stängs och **säkerhetskopieringspolicy** öppnas fönstret.

3. I **säkerhetskopieringspolicy**, väljer du den princip som du vill associera med valvet. Klicka sedan på **OK**.
    - Information om standardprincipen visas under den nedrullningsbara menyn.
    - Klicka på **Skapa ny** att skapa en princip. [Läs mer](backup-azure-vms-first-look-arm.md#defining-a-backup-policy) om hur du definierar en princip.

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

- Principen för säkerhetskopiering körs i enlighet med schemat för säkerhetskopiering.
- Backup-tjänsten installerar tillägget för säkerhetskopiering, oavsett om Virtuellt datorn körs.
    - En virtuell dator som körs har bäst chans att tilldelas en programkonsekvent återställningspunkt.
    -  Den virtuella datorn säkerhetskopieras dock även om den är avstängd och det går inte att installera tillägget. Detta kallas *offline VM*. I detta fall är återställningspunkten *kraschkonsekvent*.
- Om du vill generera en säkerhetskopiering på begäran för den virtuella datorn direkt i **Säkerhetskopieringsobjekt**, klicka på ellipsen (...) bredvid den virtuella datorn > **Säkerhetskopiera nu**.


## <a name="next-steps"></a>Nästa steg

- Felsök eventuella problem som uppstår vid den [Azure VM-agenter](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) eller [virtuell Azure-säkerhetskopiering](backup-azure-vms-troubleshoot.md).
- [Säkerhetskopiera virtuella Azure-datorer](backup-azure-vms-first-look-arm.md)
