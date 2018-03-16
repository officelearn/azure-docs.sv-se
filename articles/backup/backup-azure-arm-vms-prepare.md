---
title: "Azure-säkerhetskopiering: Förbereda för att säkerhetskopiera virtuella datorer | Microsoft Docs"
description: "Kontrollera att din miljö har förberetts för att säkerhetskopiera virtuella datorer i Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "säkerhetskopiering. Säkerhetskopiera;"
ms.assetid: e87e8db2-b4d9-40e1-a481-1aa560c03395
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/1/2018
ms.author: markgal;trinadhk;sogup;
ms.openlocfilehash: cd8274ab6b50eee83bc3e41ea543930aa309e790
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Förbereda din miljö för att säkerhetskopiera Resource Manager-distribuerade virtuella datorer

Den här artikeln innehåller steg för att förbereda din miljö för att säkerhetskopiera en Azure Resource Manager-distribuerad virtuell dator (VM). Stegen visas i procedurer använder Azure-portalen. Lagra säkerhetskopierade data för virtuell dator i en Recovery Services-valvet. Valvet innehåller säkerhetskopierade data för klassiska och Resource Manager-distribuerade virtuella datorer.

> [!NOTE]
> Azure har två distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md).

Innan du skyddar (eller säkerhetskopiera) en Resource Manager-distribuerad virtuell dator, kontrollera att dessa krav finns:

* Skapa ett Recovery Services-valv (eller identifiera ett befintligt Recovery Services-valv) *i samma region som din virtuella dator*.
* Välj ett scenario, definiera princip för säkerhetskopiering och definiera objekt som ska skyddas.
* Kontrollera installationen av en VM-agenten på den virtuella datorn.
* Kontrollera nätverksanslutningen.
* För Linux virtuella datorer kan om du vill anpassa säkerhetskopiering programkonsekventa säkerhetskopior i miljön gör den [steg för att konfigurera inför ögonblicksbilden och efter ögonblickbild skript](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Om dessa villkor finns redan i din miljö, fortsätter du till den [säkerhetskopiera din virtuella dator](backup-azure-arm-vms.md) artikel. Om du behöver konfigurera eller kontrollera någon av dessa förutsättningar leder dig genom stegen i den här artikeln.

## <a name="supported-operating-systems-for-backup"></a>Operativsystem som stöds för säkerhetskopiering
 * **Linux**: Azure Backup stöder [en lista över distributioner rekommenderar Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), förutom CoreOS Linux. 
 
    > [!NOTE] 
    > Andra bring-your-äger-Linux-distributioner fungera så länge som den Virtuella datoragenten är tillgänglig på den virtuella datorn, och stöd för Python finns. Dessa distributioner stöds dock inte.
 * **Windows Server**: versioner som är äldre än Windows Server 2008 R2 stöds inte.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Begränsningar när du säkerhetskopierar och återställer en virtuell dator
Innan du förbereder din miljö måste du förstå följande begränsningar:

* Säkerhetskopiera virtuella datorer med fler än 16 datadiskar stöds inte.
* Säkerhetskopiering av virtuella datorer med en reserverad IP-adress och ingen definierad slutpunkt stöds inte.
* Säkerhetskopiera virtuella Linux-datorer krypterade via Linux Unified nyckeln installationsprogrammet (LUKS)-kryptering stöds inte.
* Vi rekommenderar inte att du säkerhetskopierar virtuella datorer som innehåller konfiguration för klusterdelade volymer (CSV) eller en skalbar filserver. De kräver som omfattar alla virtuella datorer som ingår i klusterkonfigurationen under en ögonblicksbilden. Azure-säkerhetskopiering har inte stöd för flera Virtuella datorer. 
* Säkerhetskopierade data innehåller monterade nätverksenheter kopplad till en virtuell dator.
* Att ersätta en befintlig virtuell dator under återställningen stöds inte. Återställningen misslyckas om du försöker återställa den virtuella datorn när den virtuella datorn finns.
* Cross-region säkerhetskopiera och Återställ stöds inte.
* Säkerhetskopiera och återställa virtuella datorer med hjälp av ohanterade diskar i storage-konton med reglerna för nätverk, stöds inte. 
* När du konfigurerar tillbaka in, kontrollera att den **brandväggar och virtuella nätverk** storage-konto tillåter åtkomst från alla nätverk.
* Du kan säkerhetskopiera virtuella datorer i alla offentliga områden av Azure. (Se den [checklista](https://azure.microsoft.com/regions/#services) av regioner som stöds.) Om den region som du letar efter stöds idag visas det inte i den nedrullningsbara listan under skapande av valvet.
* Återställa en domänkontrollant stöds (DC) virtuell dator som är en del av en multi-DC-konfiguration bara via PowerShell. Läs mer i [återställa en multi-DC-domänkontrollant](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Återställning av virtuella datorer som har följande särskilda nätverkskonfigurationer stöds bara via PowerShell. Virtuella datorer som skapats via återställning arbetsflödet i Användargränssnittet inte dessa nätverkskonfigurationer när återställningen är klar. Läs mer i [återställa virtuella datorer med särskilda nätverkskonfigurationer](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Virtuella datorer under konfigurationen av belastningsutjämnaren (interna och externa)
  * Virtuella datorer med flera reserverade IP-adresser
  * Virtuella datorer med flera nätverkskort

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Skapa ett Recovery Services-valv för en virtuell dator
Recovery Services-valvet är en entitet som lagrar säkerhetskopior och återställningspunkter som har skapats med tiden. Recovery Services-valvet innehåller även principer för säkerhetskopiering som är associerade med de skyddade virtuella datorerna.

Så här skapar du ett Recovery Services-valv:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På den **hubb** väljer du **Bläddra**, och skriv sedan **återställningstjänster**. När du börjar skriva filtrerar listan över resurser i dina indata. Välj **Recovery Services-valv**.

    ![Att skriva i rutan och välja ”Recovery Services-valv” i resultaten](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Listan över Recovery Services-valv visas.
3. På den **Recovery Services-valv** väljer du **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Den **Recovery Services-valv** fönstret öppnas. Uppmanas du att ange information för **namn**, **prenumeration**, **resursgruppen**, och **plats**.

    ![”Recovery Services-valv” fönstret](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Ange ett namn som innehåller 2 och 50 tecken. Det måste börja med en bokstav och kan innehålla endast bokstäver, siffror och bindestreck.
5. Välj **prenumeration** att se listan över prenumerationer tillgängliga. Om du inte är säker på vilken prenumeration för att använda använda standardvärdet (eller förslag) prenumerationen. Det finns flera alternativ bara om ditt arbete eller skola konto är kopplat till flera Azure-prenumerationer.
6. Välj **resursgruppen** finns i listan över resursgrupper tillgängliga, eller välj **ny** att skapa en ny resursgrupp. Mer information om resursgrupper finns i [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Välj **plats** att välja den geografiska regionen för valvet. Valvet *måste* finnas i samma region som de virtuella datorer som du vill skydda.

   > [!IMPORTANT]
   > Om du är osäker på den plats där den virtuella datorn finns Stäng dialogrutan valvet skapas och gå till listan över virtuella datorer i portalen. Om du har virtuella datorer i flera områden, måste du skapa ett Recovery Services-valv i varje region. Skapa valvet på den första platsen innan du fortsätter till nästa plats. Det finns inget behov av att ange storage-konton för att lagra säkerhetskopierade data. Recovery Services-valvet och Azure Backup-tjänsten ska du hantera som automatiskt.
   >
   >

8. Välj **Skapa**. Det kan ta en stund innan Recovery Services-valvet har skapats. Meddelandena status längst upp till höger i portalen. När din valvet har skapats visas den i listan över Recovery Services-valv. Om du inte ser ditt valv, Välj **uppdatera**.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Nu när du har skapat valvet ska vi se hur du konfigurerar lagringsreplikeringen.

## <a name="set-storage-replication"></a>Ange storage-replikering
Lagringsalternativ för replikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Valvet använder geo-redundant lagring som standard. Lämna alternativet inställningen som geo-redundant lagring för din primära säkerhetskopiering. Om du vill att ett billigare alternativ som inte är beständig som du Välj lokalt redundant lagring.

Så här redigerar du inställningen för lagringsreplikering:

1. På den **Recovery Services-valv** rutan, Välj ditt valv.
    När du väljer ditt valv den **inställningar** fönstret (som har namnet på valvet överst) och öppna valvet informationsfönstret.

   ![Välj ditt valv i listan över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. På den **inställningar** rutan Använd lodräta skjutreglaget för att rulla ned till den **hantera** och markerar **säkerhetskopiering infrastruktur**. I den **allmänna** väljer **konfigurering av säkerhetskopiering**. På den **konfigurering av säkerhetskopiering** fönstret Välj replikeringsalternativ lagring för ditt valv. Valvet använder geo-redundant lagring som standard.

   ![Lista över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Om du använder Azure som en primär säkerhetskopieringslagring slutpunkt kan fortsätta att använda geo-redundant lagring. Om du använder Azure som en icke-primär säkerhetskopieringslagring slutpunkt, välja lokalt redundant lagring. Läs mer om alternativ för lagring i den [Azure Storage-replikering: översikt](../storage/common/storage-redundancy.md).

3. Om du har ändrat replikeringstyp lagring väljer **spara**.
    
När du väljer lagringsalternativet för ditt valv, är du redo att associera den virtuella datorn med valvet. För att börja kopplingen identifierar du och registrerar de virtuella Azure-datorerna.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Välj ett mål för säkerhetskopiering, ange principen och definiera objekt som ska skyddas
Innan du registrerar en virtuell dator med ett Recovery Services-valv, köra identifieringsprocessen för att identifiera eventuella nya virtuella datorer som har lagts till i prenumerationen. Identifieringsprocessen frågar Azure för en lista över virtuella datorer i prenumerationen. Om det nya virtuella datorer finns visar portalen molntjänstnamnet och associerade region. I Azure-portalen på *scenariot* är vad du anger i Recovery Services-valvet. *Princip för* är schemat för hur ofta och när återställningspunkter tas. Principen omfattar också kvarhållningsintervallet för återställningspunkterna.

1. Om du redan har ett öppet Recovery Services-valv går du vidare till steg 2. Om du inte har ett Recovery Services-valv öppna, öppna den [Azure-portalen](https://portal.azure.com/). På den **hubb** väljer du **fler tjänster**.

   a. I listan över resurser skriver du **Recovery Services**. När du börjar skriva filtrerar indata i listan. När du ser **Recovery Services-valv**, markerar du den.

      ![Att skriva i rutan och välja ”Recovery Services-valv” i resultaten](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      Listan över Recovery Services-valv visas. Om det finns några valv i din prenumeration kan är den här listan tom.

      ![Vy över listan med Recovery Services-valv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. Välj ett valv i listan över Recovery Services-valv.

      Den **inställningar** rutan och valvet instrumentpanelen för valt valvet öppna.

      ![Inställningar för fönstret och valvet instrumentpanelen](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. Välj på menyn valvet instrumentpanelen **säkerhetskopiering**.

   ![Säkerhetskopiering knappen](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Den **säkerhetskopiering** och **säkerhetskopiering målet** fönstren öppna.

3. På den **säkerhetskopiering målet** ställer du in **var körs din arbetsbelastning?** som **Azure** och **vad vill du säkerhetskopiera?** som  **Virtuella**. Välj sedan **OK**.

   ![Fönster för säkerhetskopiering och mål för säkerhetskopian](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Det här steget registrerar VM-tillägget med valvet. Den **säkerhetskopiering målet** fönstret stängs och **säkerhetskopiera princip** fönstret öppnas.

   ![”Säkerhetskopiering” och ”säkerhetskopiera principen” fönster](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. På den **säkerhetskopiera princip** fönstret Välj principen för säkerhetskopiering som du vill koppla till valvet.

   ![Välja säkerhetskopieringspolicy](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   Information om standardprincipen visas under den nedrullningsbara menyn. Om du vill skapa en ny policy väljer du **Skapa ny** i listrutan. Mer information om hur du definierar en säkerhetskopieringspolicy finns i [Definiera en säkerhetskopieringspolicy](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Välj **OK** att koppla principen för säkerhetskopiering till valvet.

   Den **säkerhetskopiera princip** fönstret stängs och **Välj virtuella datorer** fönstret öppnas.
5. På den **Välj virtuella datorer** fönstret väljer du de virtuella datorerna som associeras med den angivna principen och välj **OK**.

   ![”Välj virtuella datorer” fönstret](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   Den valda virtuella datorn verifieras. Om du inte ser de förväntade virtuella datorerna, kontrollera att de virtuella datorerna i samma Azure-region som Recovery Services-valvet. Om de virtuella datorerna fortfarande inte visas kontrollerar du att de inte redan skyddas med ett annat valv. Valvet instrumentpanelen visar regionen där Recovery Services-valvet finns.

6. Nu när du har definierat alla inställningar för valvet, på den **säkerhetskopiering** väljer **Aktivera säkerhetskopiering**. Det här steget distribuerar principen till valvet och de virtuella datorerna. Det här steget kan inte skapa den första återställningspunkten för den virtuella datorn.

   ![Knappen ”Aktivera säkerhetskopiering”](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

När du har aktiverat säkerhetskopieringen, körs din princip för säkerhetskopiering enligt schema. Om du vill generera en på-begäran jobbet att säkerhetskopiera virtuella datorer nu se [utlösa säkerhetskopieringsjobbet](./backup-azure-arm-vms.md#triggering-the-backup-job).

Om du har problem med att registrera den virtuella datorn finns i följande information på VM-agenten installeras och nätverksanslutningen. Förmodligen behöver inte du följande information om du skyddar virtuella datorer som skapats i Azure. Men om du har migrerat virtuella datorer till Azure, se till att du installerade VM-agenten och att den virtuella datorn kan kommunicera med det virtuella nätverket.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installera VM-agenten på den virtuella datorn
För säkerhetskopiering tillägg fungerar, Azure [VM-agenten](../virtual-machines/windows/agent-user-guide.md) måste vara installerad på den virtuella Azure-datorn. Om den virtuella datorn har skapats från Azure Marketplace, är VM-agenten redan finns på den virtuella datorn. 

Den här informationen för situationer där du har *inte* använda en virtuell dator som skapats från Azure Marketplace. Till exempel migrerat du en virtuell dator från ett lokalt datacenter. I sådana fall måste VM-agenten installeras för att skydda den virtuella datorn.

Om du har problem med säkerhetskopiering av virtuella Azure-datorn använder du följande tabell för att kontrollera att den Virtuella Azure-agenten är korrekt installerat på den virtuella datorn. Tabellen innehåller ytterligare information om VM-agenten för Windows och Linux virtuella datorer.

| **Åtgärd** | **Windows** | **Linux** |
| --- | --- | --- |
| Installera den Virtuella datoragenten |Ladda ned och installera [agentens MSI-fil](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen. |Installera senaste [Linux-agenten](../virtual-machines/linux/agent-user-guide.md). Du måste ha administratörsbehörighet för att slutföra installationen. Vi rekommenderar att du installerar agenten från databasen för din distribution. Vi *rekommenderar inte* Linux VM-agenten installeras direkt från GitHub.  |
| Uppdatera den Virtuella datoragenten |Uppdatera den Virtuella datoragenten är så enkelt som att installera om den [binärfilerna för VM-agenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |Följ instruktionerna för [uppdatering Linux VM-agenten](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vi rekommenderar att du uppdaterar agenten från databasen för din distribution. Vi *rekommenderar inte* uppdaterar Linux VM-agenten direkt från GitHub.<br><br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |
| Verifiera installationen av VM |1. Bläddra till mappen C:\WindowsAzure\Packages i Azure-VM. <br><br>2. Hitta WaAppAgent.exe-filen. <br><br>3. Högerklicka på filen, gå till **Egenskaper** och välj fliken **Information**. Den **produktversionen** fältet måste innehålla 2.6.1198.718 eller högre. |Gäller inte |

### <a name="backup-extension"></a>Säkerhetskopieringstillägg
När den Virtuella datoragenten är installerad på den virtuella datorn installerar tjänsten Azure Backup tillägget säkerhetskopiering av VM-agenten. Backup-tjänsten sömlöst uppgraderar och korrigeringsfiler sekundär anknytning.

Backup-tjänsten installerar tillägget säkerhetskopiering oavsett om den virtuella datorn körs. En virtuell dator som körs har bäst chans att tilldelas en programkonsekvent återställningspunkt. Backup-tjänsten fortsätter dock att säkerhetskopiera den virtuella datorn även om den är avstängd och tillägget kunde inte installeras. Detta kallas *offline VM*. I detta fall är återställningspunkten *kraschkonsekvent*.

## <a name="establish-network-connectivity"></a>Upprätta nätverksanslutning
Om du vill hantera VM-ögonblicksbilder måste sekundär anknytning anslutningen till Azure offentliga IP-adresser. Den virtuella datorns HTTP-begäranden timeout utan rätt internet-anslutning och säkerhetskopieringen misslyckas. Om distributionen har åtkomstbegränsningar--via en nätverkssäkerhetsgrupp (NSG), till exempel--Välj något av dessa alternativ för att göra det lätt för säkerhetskopiering trafik:

* [Godkända Azure datacenter-IP-adressintervall](http://www.microsoft.com/en-us/download/details.aspx?id=41653).
* Distribuera en HTTP-proxyserver för dirigera trafiken.

När du bestämmer vilket alternativ som är avvägningarna mellan hanterbarhet, granulär kontroll och kostnader.

| Alternativ | Fördelar | Nackdelar |
| --- | --- | --- |
| Whitelist IP-adressintervall |Inga ytterligare kostnader.<br><br>För att öppna åtkomst i en NSG, Använd den **Set AzureNetworkSecurityRule** cmdlet. |Komplext för att hantera som den berörda IP-adressintervall ändras med tiden.<br><br>Tillhandahåller åtkomst till Azure och inte bara lagring som helhet. |
| Använda en HTTP-proxy |Granulär kontroll i proxyn över lagringen webbadresser tillåts.<br><br>Enskild plats internet-åtkomst till virtuella datorer.<br><br>Inte ändras Azure IP-adress. |Ytterligare kostnader för att köra en virtuell dator med proxy-programvara. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Godkända Azure-datacentret IP-intervall
Godkända Azure-datacenter IP-adressintervall, finns det [Azure-webbplatsen](http://www.microsoft.com/en-us/download/details.aspx?id=41653) för information om IP-adressintervall och instruktioner.

Du kan tillåta anslutningar till lagring av specifik region med hjälp av [tjänsten taggar](../virtual-network/security-overview.md#service-tags). Se till att den regel som tillåter åtkomst till lagringskontot har högre prioritet än den regel som blockerar Internetåtkomst. 

![NSG med lagring taggar för en region](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> Storage service-taggar är bara tillgängliga i vissa regioner och finns i förhandsgranskningen. En lista över regioner finns [tjänsten taggar för lagring](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>Använda en HTTP-proxy för VM-säkerhetskopieringar
När du säkerhetskopierar en virtuell dator, skickas säkerhetskopiering tillägget på den virtuella datorn ögonblicksbild management till Azure Storage med hjälp av en HTTPS-API. Vidarebefordra trafik reservanknytning via HTTP-proxy eftersom det är den enda komponenten som konfigurerats för åtkomst till internet.

> [!NOTE]
> Vi rekommenderar inte att specifika proxy-programvara som du ska använda. Se till att du väljer en proxy som är kompatibel med konfigurationssteg som följer.
>
>

Följande exempelbild visar tre konfigurationssteg som krävs för att använda en HTTP-proxy:

* VM-vägar app trafiken bunden till det offentliga internet via proxy VM.
* Proxy VM tillåter inkommande trafik från virtuella datorer i det virtuella nätverket.
* Nätverkssäkerhetsgruppen med namnet NSF låsning måste en säkerhetsregel som tillåter utgående internet-trafik från VM-proxyservern.

Utför följande steg om du vill använda en HTTP-proxy ska kunna kommunicera med det offentliga internet.

> [!NOTE]
> De här stegen kan du använda specifika namn och värden för det här exemplet. När du anger (eller klistra in) information i koden, använda namn och värden för din distribution.

#### <a name="step-1-configure-outgoing-network-connections"></a>Steg 1: Konfigurera utgående nätverksanslutningar
###### <a name="for-windows-machines"></a>För Windows-datorer
Den här proceduren ställer in proxyserverkonfiguration för det lokala systemkontot.

1. Hämta [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Öppna Internet Explorer genom att köra följande kommando från en upphöjd kommandotolk:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

3. I Internet Explorer går du till **verktyg** > **Internetalternativ** > **anslutningar** > **LAN-inställningar**.
4. Kontrollera proxyinställningarna för system-kontot. Ange proxy-IP och port.
5. Stäng Internet Explorer.

Följande skript ställer in en datoromfattande proxy-konfiguration och använder den för utgående HTTP eller HTTPS-trafik. Om du har ställt in en proxyserver för ett aktuella användarkonto (inte ett lokalt systemkonto), kan du använda skriptet för att använda dem på SYSTEMACCOUNT.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Om du upptäcker ”(407) krävs proxyautentisering” i serverloggen proxy, kontrollera att autentiseringen har ställts in korrekt.
>
>

###### <a name="for-linux-machines"></a>För Linux-datorer
Lägg till följande rad i den ```/etc/environment``` filen:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Lägg till följande rader till den ```/etc/waagent.conf``` filen:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Steg 2: Tillåta inkommande anslutningar på proxyservern
1. Öppna Windows-brandväggen på proxyservern. Det enklaste sättet att komma åt brandväggen är att söka efter **Windows-brandväggen med avancerad säkerhet**.
2. I den **Windows-brandväggen med avancerad säkerhet** dialogrutan, högerklicka på **regler för inkommande trafik** och välj **ny regel**.
3. I guiden Ny inkommande regel, på den **regeltyp** väljer den **anpassad** alternativet och välj **nästa**.
4. På den **programmet** väljer **alla program** och välj **nästa**.
5. På den **protokoll och portar** anger du följande information och välj **nästa**:
   * För **protokolltyp**väljer **TCP**.
   * För **lokal port**väljer **specifika portar**. Ange numret på den Proxyport som har konfigurerats i följande ruta.
   * För **Fjärrport**väljer **alla portar**.

Acceptera standardinställningarna för resten av guiden tills du kommer till slutet. Sedan ge regeln ett namn. 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Steg 3: Lägga till en undantagsregel NSG: N
Följande kommando lägger till ett undantag NSG: N. Det här undantaget kan TCP-trafik från alla portar på 10.0.0.5 till alla internet-adress på port 80 (HTTP) eller 443 (HTTPS). Om du behöver en viss port på internet, måste du lägga till porten till ```-DestinationPortRange```.

Ange följande kommando i en Azure PowerShell-kommandotolk:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>Har du några frågor?
Om du har frågor eller om det finns en funktion som du vill se ingår, [skicka feedback](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Nästa steg
Nu när du har förberett din miljö för att säkerhetskopiera den virtuella datorn, är nästa logiska steg att skapa en säkerhetskopia. Planering artikeln innehåller mer detaljerad information om hur du säkerhetskopierar virtuella datorer.

* [Säkerhetskopiera virtuella datorer](backup-azure-arm-vms.md)
* [Planera infrastrukturen för säkerhetskopiering VM](backup-azure-vms-introduction.md)
* [Hantera säkerhetskopiering för virtuella datorer](backup-azure-manage-vms.md)
