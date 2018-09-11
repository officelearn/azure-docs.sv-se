---
title: 'Azure Backup: Förbereda säkerhetskopiering av virtuella datorer'
description: Kontrollera att din miljö är förberedd för att säkerhetskopiera virtuella datorer i Azure.
services: backup
author: markgalioto
manager: carmonm
keywords: säkerhetskopior. Säkerhetskopiera;
ms.service: backup
ms.topic: conceptual
ms.date: 9/10/2018
ms.author: markgal
ms.openlocfilehash: dc9d7b924f51fb3a4e9662dfa7ad4b9557b481bc
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347009"
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Förbereda din miljö för att säkerhetskopiera Resource Manager-distribuerade virtuella datorer

Den här artikeln innehåller steg för att förbereda din miljö för att säkerhetskopiera en Azure Resource Manager-distribuerade virtuella datorn (VM). Stegen som visas i följande använder Azure-portalen. När du säkerhetskopierar en virtuell dator lagras säkerhetskopierade data eller återställningspunkter, i ett Recovery Services-valv. Recovery Services-valv lagra säkerhetskopierade data för klassiska och Resource Manager-distribuerade virtuella datorer.

> [!NOTE]
> Azure har två distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md).

Innan du skyddar (eller säkerhetskopiera) en Resource Manager-distribuerade virtuella datorn, se till att följande krav vara uppfyllda:

* Skapa eller identifiera ett Recovery Services-valv *i samma region som den virtuella datorn*.
* Välj ett scenario, definiera principen för säkerhetskopiering och definiera objekt som ska skyddas.
* Kontrollera installationen av en VM-agent (tillägg) på den virtuella datorn.
* Kontrollera nätverksanslutningen.
* För virtuella Linux-datorer om du vill anpassa miljön säkerhetskopiering för programkonsekventa säkerhetskopior, följer du de [steg för att konfigurera skript före och efter ögonblicksbilder](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Om dessa villkor finns redan i din miljö, fortsätter du till den [säkerhetskopiera dina virtuella datorer](backup-azure-arm-vms.md) artikeln. Om du vill konfigurera eller kontrollera några av dessa krav leder dig genom stegen i den här artikeln.

## <a name="supported-operating-systems-for-backup"></a>Operativsystem som stöds för säkerhetskopiering

 * **Linux**: Azure Backup stöder [en lista över distributioner som Azure godkänner](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), förutom CoreOS Linux. Lista över Linux-operativsystem som stöd för att återställa filer, finns i [återställa filer från säkerhetskopiering av virtuella datorer](backup-azure-restore-files-from-vm.md#for-linux-os).

    > [!NOTE] 
    > Andra bring-your-own-Linux-distributioner kan fungera, så länge som den Virtuella datoragenten är tillgänglig på den virtuella datorn och har stöd för Python finns. Dessa distributioner stöds dock inte.
    >
 * **Windows Server**, **Windows-klienten**: versioner som är äldre än Windows Server 2008 R2 eller Windows 7, stöds inte.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Begränsningar när du säkerhetskopierar och återställer en virtuell dator
Innan du förbereder din miljö måste du förstå följande begränsningar:

* Säkerhetskopiera virtuella datorer med Standard SSD stöds inte för närvarande.
* Säkerhetskopiering av virtuella datorer med fler än 16 datadiskar stöds inte.
* Säkerhetskopiering av virtuella datorer med en reserverad IP-adress och ingen definierad slutpunkt stöds inte.
* Säkerhetskopiera virtuella Linux-datorer krypteras med kryptering för Linux Unified nyckel installationsprogrammet (LUKS) stöds inte.
* Vi rekommenderar inte att du säkerhetskopierar virtuella datorer som innehåller klusterdelade volymer (CSV) eller skalbar filserver. Om gjort, förväntas fel i CSV-skrivare. De kräver som omfattar alla virtuella datorer som ingår i klusterkonfigurationen under en ögonblicksbild-aktivitet. Azure Backup stöder inte konsekvens. 
* Säkerhetskopierade data omfattar inte monterade nätverksenheter som är kopplade till en virtuell dator.
* Att ersätta en befintlig virtuell dator under återställningen stöds inte. Om du försöker återställa den virtuella datorn när den virtuella datorn finns, misslyckas återställningen.
* Interregionala säkerhetskopiera och Återställ stöds inte.
* När du konfigurerar tillbaka upp, se till att den **brandväggar och virtuella nätverk** inställningarna för lagringskontot tillåta åtkomst från alla nätverk.
* Valda nätverk när du har konfigurerat brandvägg och inställningar för virtuella nätverk för ditt lagringskonto väljer **Tillåt att betrodda Microsoft-tjänster för att komma åt det här lagringskontot** som ett undantag för att aktivera Azure Backup-tjänsten till åtkomst till lagringskontot nätverksbegränsade. Återställning på objektnivå stöds inte för nätverksbegränsade lagringskonton.
* Du kan säkerhetskopiera virtuella datorer i alla offentliga regioner för Azure. (Se den [checklista](https://azure.microsoft.com/regions/#services) över regioner som stöds.) Om den region som du letar efter inte stöds i dag, visas den inte i den nedrullningsbara listan under Skapa valv.
* Återställa en domänkontrollant stöds (DC) virtuell dator som är en del av en multi-DC-konfiguration endast via PowerShell. Mer information finns i [återställa en multi-DC domänkontrollant](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Ögonblicksbild av disken Write Accelerator-aktiverade stöds inte. Den här begränsningen blockerar Azure Backup-tjänsten möjligheten att utföra en programkonsekvent ögonblicksbild för alla diskar på den virtuella datorn.
* Återställning av virtuella datorer som har följande särskilda nätverkskonfigurationer stöds endast via PowerShell. Virtuella datorer som skapats via återställning arbetsflödet i Användargränssnittet inte dessa nätverkskonfigurationer när återställningen är klar. Mer information finns i [återställning av virtuella datorer med särskilda nätverkskonfigurationer](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Virtuella datorer under konfigurationen för belastningsutjämnaren (interna och externa)
  * Virtuella datorer med flera reserverade IP-adresser
  * Virtuella datorer med flera nätverkskort

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Skapa ett Recovery Services-valv för en virtuell dator
Ett Recovery Services-valv är en entitet som lagrar säkerhetskopior och återställningspunkter som har skapats med tiden. Recovery Services-valvet innehåller även säkerhetskopieringspolicyerna som är kopplade till de skyddade virtuella datorerna.

Så här skapar du ett Recovery Services-valv:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. På den **Hub** menyn och välj **Bläddra**, och skriv sedan **återställningstjänster**. När du börjar skriva, filtrerar listan över resurser i dina indata. Välj **Recovery Services-valv**.

    ![Att skriva i rutan och välja ”Recovery Services-valv” i resultaten](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Listan över Recovery Services-valv visas.
1. På den **Recovery Services-valv** menyn och välj **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Den **Recovery Services-valv** öppnas fönstret. Uppmanas du att ange information för **namn**, **prenumeration**, **resursgrupp**, och **plats**.

    ![Fönstret ”recovery Services-valv”](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
1. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Skriv ett namn som innehåller 2 och 50 tecken. Det måste börja med en bokstav och det får innehålla endast bokstäver, siffror och bindestreck.
1. Välj **prenumeration** att se listan över prenumerationer. Om du inte vet vilken prenumeration som ska användas, använder du standardvärdet (eller föreslås) prenumeration. Det finns flera alternativ endast om ditt arbete eller skola konto är kopplat till flera Azure-prenumerationer.
1. Välj **resursgrupp** att se listan över resursgrupper eller välja **New** att skapa en ny resursgrupp. Fullständig information om resursgrupper finns i [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
1. Välj **plats** att välja ett geografiskt område för valvet. Valvet *måste* finnas i samma region som de virtuella datorer som du vill skydda.

   > [!IMPORTANT]
   > Om du är osäker på den plats där din virtuella dator finns stänger dialogrutan valvet skapas och gå till listan över virtuella datorer i portalen. Om du har virtuella datorer i flera regioner kan behöva du skapa ett Recovery Services-valv i varje region. Skapa valvet på den första platsen innan du fortsätter till nästa plats. Det finns behöver inte ange lagringskonton för att lagra säkerhetskopierade data. Recovery Services-valvet och tjänsten Azure Backup hanterar du som automatiskt.
   >
   >

1. Välj **Skapa**. Det kan ta en stund innan Recovery Services-valvet har skapats. Övervaka Statusmeddelandena i området längst upp till höger i portalen. När valvet har skapats visas den i listan över Recovery Services-valv. Om du inte ser ditt valv, väljer **uppdatera**.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Nu när du har skapat valvet ska vi se hur du konfigurerar lagringsreplikeringen.

## <a name="set-storage-replication"></a>Ställ in storage-replikering
Alternativet för lagringsreplikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Valvet använder geo-redundant lagring som standard. Lämna alternativet inställningen som geo-redundant lager för din primära säkerhetskopia. Om du vill använda ett billigare alternativ som inte är lika beständigt, Välj lokalt redundant lagring.

Så här redigerar du inställningen för lagringsreplikering:

1. På den **Recovery Services-valv** fönstret väljer du ditt valv.
    När du väljer valvet, den **inställningar** fönstret (som har namnet på valvet överst) och öppna vault informationsfönstret.

   ![Välj ditt valv i listan över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

1. På den **inställningar** fönstret använder det lodräta skjutreglaget för att rulla ned till den **hantera** och välj **infrastruktur för säkerhetskopiering**. I den **Allmänt** väljer **Säkerhetskopieringskonfigurationen**. På den **Säkerhetskopieringskonfigurationen** fönstret väljer du alternativet för lagringsreplikering för ditt valv. Valvet använder geo-redundant lagring som standard.

   ![Lista över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Om du använder Azure som en slutpunkt för primär lagring av säkerhetskopior kan fortsätta använda geo-redundant lagring. Om du använder Azure som en slutpunkt för icke-primär lagring av säkerhetskopior väljer du lokalt redundant lagring. Läs mer om lagringsalternativ i den [översikt över Azure Storage-replikering](../storage/common/storage-redundancy.md).

1. Om du har ändrat lagringsreplikeringstyp Välj **spara**.
    
När du har valt lagringsalternativet för valvet är du redo att associera den virtuella datorn med valvet. För att börja kopplingen identifierar du och registrerar de virtuella Azure-datorerna.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Välj ett säkerhetskopieringsmål, ange en princip och definiera objekt som ska skyddas
Innan du registrerar en virtuell dator med ett Recovery Services-valv, kör du identifieringsprocessen för att identifiera nya virtuella datorer för prenumerationen. Identifieringsprocessen frågar Azure lista över virtuella datorer i prenumerationen. Om det finns nya virtuella datorer visar på portalen molntjänstens namn och associerade region. I Azure-portalen i *scenariot* är vad du anger i Recovery Services-valvet. *Principen* är schemat för hur ofta återställningspunkter skapas. Principen omfattar också kvarhållningsintervallet för återställningspunkterna.

1. Om du redan har ett öppet Recovery Services-valv går du vidare till steg 2. Om du inte har ett Recovery Services-valv som är öppna, öppna den [Azure-portalen](https://portal.azure.com/). På den **Hub** menyn och välj **fler tjänster**.

   a. I listan över resurser skriver du **Recovery Services**. När du börjar skriva filtreras dina indata i listan. När du ser **Recovery Services-valv**, markera den.

      ![Att skriva i rutan och välja ”Recovery Services-valv” i resultaten](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      Listan över Recovery Services-valv visas. Om det finns inga valv i prenumerationen, är den här listan tom.

      ![Vy över listan med Recovery Services-valv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. Välj ett valv i listan över Recovery Services-valv.

      Den **inställningar** fönstret och instrumentpanelen för valvet för valt valvet öppen.

      ![Inställningar för instrumentpanelen för fönstret och valv](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
1. Välj på valvets instrumentpanel **Backup**.

   ![Säkerhetskopiering knappen](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Den **Backup** och **säkerhetskopieringsmål** fönstren öppna.

1. På den **säkerhetskopieringsmål** rutan Ange **var körs din arbetsbelastning?** som **Azure** och **vad vill du säkerhetskopiera?** som  **Virtuell dator**. Välj sedan **OK**.

   ![Fönster för säkerhetskopiering och säkerhetskopieringsmål](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Det här steget registrerar VM-tillägget med valvet. Den **säkerhetskopieringsmål** fönstret stängs och **säkerhetskopieringspolicy** öppnas fönstret.

   ![”Säkerhetskopiering” och ”säkerhetskopieringspolicy” fönster](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
1. På den **säkerhetskopieringspolicy** fönstret, Välj den säkerhetskopieringspolicy som du vill använda för valvet.

   ![Välja säkerhetskopieringspolicy](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   Information om standardprincipen visas under den nedrullningsbara menyn. Om du vill skapa en ny policy väljer du **Skapa ny** i listrutan. Mer information om hur du definierar en säkerhetskopieringspolicy finns i [Definiera en säkerhetskopieringspolicy](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Välj **OK** att associera säkerhetskopieringspolicyn med valvet.

   Den **säkerhetskopieringspolicy** fönstret stängs och **Välj virtuella datorer** öppnas fönstret.
1. På den **Välj virtuella datorer** fönstret väljer du de virtuella datorerna om du vill associera med den angivna principen och välj **OK**.

   ![Fönstret ”Välj virtuella datorer”](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   Den valda virtuella datorn verifieras. Om du inte ser de förväntade virtuella datorerna, kontrollerar du att de virtuella datorerna finns i samma Azure-region som Recovery Services-valvet. Om du inte kan se de virtuella datorerna, kontrollerar du att de inte redan skyddas med ett annat valv. Instrumentpanelen för valvet kan se regionen där det finns Recovery Services-valvet.

1. Nu när du har definierat alla inställningar för valvet på den **säkerhetskopiering** väljer **Aktivera säkerhetskopiering**. Det här steget distribuerar principen till valvet och de virtuella datorerna. Det här steget skapar inte den första återställningspunkten för den virtuella datorn.

   ![Knappen ”Aktivera säkerhetskopiering”](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

När du har har aktiverat säkerhetskopieringen körs säkerhetskopieringspolicyn enligt schema. Om du vill generera en säkerhetskopiering på begäran att säkerhetskopiera virtuella datorer nu se [utlösa säkerhetskopieringsjobbet](./backup-azure-vms-first-look-arm.md#initial-backup).

Om du har problem med att registrera den virtuella datorn kan du se följande information om hur du installerar VM-agenten och på nätverksanslutning. Förmodligen behöver inte du följande information om du skyddar virtuella datorer som skapats i Azure. Men om du har migrerat dina virtuella datorer till Azure är det viktigt att du installerade VM-agenten och att den virtuella datorn kan kommunicera med det virtuella nätverket.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installera VM-agenten på den virtuella datorn
För att säkerhetskopieringstillägget ska fungera, Azure [VM-agenten](../virtual-machines/extensions/agent-windows.md) måste installeras på virtuella Azure-datorer. Om den virtuella datorn skapades från Azure Marketplace finns redan VM-agenten på den virtuella datorn. 

Den här informationen för situationer där du är *inte* med hjälp av en virtuell dator skapas från Azure Marketplace. **Exempelvis kan migrerat du en virtuell dator från ett lokalt datacenter. I sådana fall måste VM-agenten installeras för att skydda den virtuella datorn.**

**Obs**: när du har installerat VM-agenten måste du också använda Azure PowerShell för att uppdatera egenskapen ProvisionGuestAgent så att Azure vet att den virtuella datorn har agenten installerad. 

Om du har problem med säkerhetskopiering av Virtuella Azure kan du använda följande tabell för att kontrollera att Azure VM-agenten är korrekt installerad på den virtuella datorn. Tabellen innehåller ytterligare information om VM-agenten för Windows och Linux-datorer.

| **Åtgärd** | **Windows** | **Linux** |
| --- | --- | --- |
| Installera VM-agenten |Ladda ned och installera [agentens MSI-fil](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen. |<li> Installera senast [linuxagenten](../virtual-machines/extensions/agent-linux.md). Du måste ha administratörsbehörighet för att slutföra installationen. Vi rekommenderar att du installerar agenten från din lagringsplats för distribution. Vi **rekommenderar inte** installera Linux VM-agenten direkt från github.  |
| Uppdatera VM-agenten |Det är enkelt att uppdatera VM-agenten. Du installerar bara om [binärfilerna för VM-agenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |Följ anvisningarna för hur du [uppdaterar VM-agenten för Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vi rekommenderar att du uppdaterar agenten från din lagringsplats för distribution. Vi **rekommenderar inte** uppdatering Linux VM-agenten direkt från github.<br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |
| Bekräfta installationen av VM-agenten |<li>Gå till mappen *C:\WindowsAzure\Packages* på den virtuella datorn i Azure. <li>Du bör hitta filen WaAppAgent.exe.<li> Högerklicka på filen, gå till **Egenskaper** och välj fliken **Information**. Fältet Produktversion ska vara 2.6.1198.718 eller högre. |Gäller inte |

### <a name="backup-extension"></a>Säkerhetskopieringstillägg
När VM-agenten har installerats på den virtuella datorn, installerar Azure Backup-tjänsten säkerhetskopieringstillägget till VM-agenten. Backup-tjänsten uppgraderar och korrigerar säkerhetskopieringstillägget.

Backup-tjänsten installerar tillägget för säkerhetskopiering, oavsett om Virtuellt datorn körs. En virtuell dator som körs har bäst chans att tilldelas en programkonsekvent återställningspunkt. Backup-tjänsten fortsätter dock att säkerhetskopiera den virtuella datorn även om den är avstängd och det gick inte att installera tillägget. Detta kallas *offline VM*. I detta fall är återställningspunkten *kraschkonsekvent*.

## <a name="establish-network-connectivity"></a>Upprätta nätverksanslutning
Om du vill hantera ögonblicksbilder av Virtuella ha säkerhetskopieringstillägget anslutning till Azure offentliga IP-adresser. Tidsgränsen nåddes för den virtuella datorns HTTP-förfrågningar utan rätt internet-anslutningen och utförs inte säkerhetskopieringen. Om distributionen har åtkomstbegränsningar--via en nätverkssäkerhetsgrupp (NSG), till exempel – Välj något av dessa alternativ för att tillhandahålla ett tydligt sätt för säkerhetskopieringstrafik:

* [Lista över tillåtna Azure datacenter IP-intervall](http://www.microsoft.com/en-us/download/details.aspx?id=41653).
* Distribuera en HTTP-proxyserver dirigeras trafiken.

När du bestämmer vilket alternativ som är avvägningarna mellan hanterbarhet, detaljerad kontroll och kostnad.

| Alternativ | Fördelar | Nackdelar |
| --- | --- | --- |
| Whitelist IP-intervall |Ingen extra kostnad.<br><br>För att öppna åtkomst i en NSG, använda den **Set-AzureNetworkSecurityRule** cmdlet. |Komplext för att hantera som den berörda IP-intervall ändras med tiden.<br><br>Ger åtkomst till hela Azure och inte bara lagring. |
| Använda en HTTP-proxy |Detaljerad kontroll i proxyn över lagringen URL: er tillåts.<br><br>Enskild punkt för internet-åtkomst till virtuella datorer.<br><br>Inte kan komma att ändras för Azure-IP-adress. |Ytterligare kostnader för att köra en virtuell dator med proxy-programvara. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Listan över godkända Azure-datacenter IP-intervall
Godkänna Azure datacenter IP-intervall finns i den [Azure-webbplatsen](http://www.microsoft.com/en-us/download/details.aspx?id=41653) mer information om IP-intervall och anvisningar.

Du kan tillåta anslutningar till storage för den specifika regionen med hjälp av [tjänsttaggar](../virtual-network/security-overview.md#service-tags). Se till att den regel som tillåter åtkomst till storage-kontot har högre prioritet än den regel som blockerar Internetåtkomst. 

![NSG med storage-taggar för en region](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

Följande videoklipp går du igenom steg för steg-procedur för att konfigurera tjänsttaggar: 

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

> [!WARNING]
> Tjänsttaggar för lagring är bara tillgängliga i vissa regioner och är i förhandsversion. En lista över regioner finns i [Tjänsttaggar för lagring](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>Använda en HTTP-proxy för VM-säkerhetskopieringar
När du säkerhetskopierar en virtuell dator, skickar kommandon för hantering av ögonblicksbild tillägget på den virtuella datorn till Azure Storage med hjälp av en HTTPS-API. Dirigera säkerhetskopieringstillägget-trafik via HTTP-proxy, eftersom det är den enda komponenten som konfigurerats för åtkomst till det offentliga internet.

> [!NOTE]
> Vi rekommenderar inte specifik proxy-programvara som du bör använda. Se till att du väljer en proxy som är kompatibel med konfigurationsstegen fram.
>
>

Följande exempelbild visar de tre konfigurationssteg som krävs för att använda en HTTP-proxy:

* VM-vägar appen alla HTTP-trafik bunden till det offentliga internet via proxy VM.
* Proxy VM tillåter inkommande trafik från virtuella datorer i det virtuella nätverket.
* Nätverkssäkerhetsgruppen med namnet NSF låsning måste en säkerhetsregel som tillåter utgående internet-trafik från proxyn VM.

Utför följande steg för att använda en HTTP-proxy för att kommunicera med det offentliga internet.

> [!NOTE]
> De här stegen kan du använda specifika namn och värden för det här exemplet. När du anger (eller klistra in) information i din kod använder de namn och värden för din distribution.

#### <a name="step-1-configure-outgoing-network-connections"></a>Steg 1: Konfigurera utgående nätverksanslutningar
###### <a name="for-windows-machines"></a>För Windows-datorer
Den här proceduren ställer in proxyserverkonfiguration för det lokala systemkontot.

1. Ladda ned [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
1. Öppna Internet Explorer genom att köra följande kommando från en upphöjd kommandotolk:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

1. I Internet Explorer går du till **verktyg** > **Internetalternativ** > **anslutningar** > **LAN-inställningar**.
1. Kontrollera proxyinställningarna för system-kontot. Ange proxy IP och port.
1. Stäng Internet Explorer.

Följande skript ställer in en datoromfattande proxykonfiguration och använder den för alla utgående HTTP eller HTTPS-trafik. Om du har konfigurerat en proxyserver på ett aktuella användarkonto (inte ett lokalt systemkonto) kan du använda det här skriptet för att tillämpa dem på SYSTEMACCOUNT.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Om du ser ”(407) proxyautentisering krävs” i loggen för proxy-server kan du kontrollera att autentiseringen är korrekt konfigurerat.
>
>

###### <a name="for-linux-machines"></a>För Linux-datorer
Lägg till följande rad i den ```/etc/environment``` fil:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Lägg till följande rader till den ```/etc/waagent.conf``` fil:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Steg 2: Tillåta inkommande anslutningar på proxyservern
1. Öppna Windows-brandväggen på proxyservern. Det enklaste sättet att få åtkomst till brandväggen är att söka efter **Windows-brandväggen med avancerad säkerhet**.
1. I den **Windows-brandväggen med avancerad säkerhet** dialogrutan högerklickar du på **regler för inkommande trafik** och välj **ny regel**.
1. I guiden Ny inkommande regel, på den **regeltyp** väljer den **anpassade** och välja **nästa**.
1. På den **programmet** väljer **alla program** och välj **nästa**.
1. På den **protokoll och portar** , anger du följande information och välj **nästa**:
   * För **protokollet typ**väljer **TCP**.
   * För **lokal port**väljer **specifika portar**. I följande ruta anger du antalet Proxyport som har konfigurerats.
   * För **Fjärrport**väljer **alla portar**.

Acceptera standardinställningarna för resten av guiden tills du kommer till slutet. Sedan ge regeln ett namn. 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Steg 3: Lägga till en undantagsregel i NSG
Följande kommando lägger till ett undantag i NSG. Det här undantaget kan TCP-trafik från alla portar på 10.0.0.5 till en Internetadress på port 80 (HTTP) eller 443 (HTTPS). Om du behöver en viss port på internet, måste du lägga till den porten till ```-DestinationPortRange```.

Ange följande kommando i en Azure PowerShell-kommandotolk:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>Har du några frågor?
Om du har frågor eller om det finns en funktion som du vill Välkommen [Skicka oss feedback](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Nästa steg
Nu när du har förberett din miljö för att säkerhetskopiera den virtuella datorn, är nästa logiska steg att skapa en säkerhetskopia. Planering artikeln innehåller mer detaljerad information om hur du säkerhetskopierar virtuella datorer.

* [Säkerhetskopiera virtuella datorer](backup-azure-arm-vms.md)
* [Planera din infrastruktur för säkerhetskopiering av virtuell dator](backup-azure-vms-introduction.md)
* [Hantera säkerhetskopiering för virtuella datorer](backup-azure-manage-vms.md)
