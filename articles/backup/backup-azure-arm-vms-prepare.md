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
ms.date: 9/3/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: 7ee2e42e05fb4866d32c24b0d4c788b0197970ad
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Förbereda din miljö för att säkerhetskopiera Resource Manager-distribuerade virtuella datorer
> [!div class="op_single_selector"]
> * [Resource Manager-modellen](backup-azure-arm-vms-prepare.md)
> * [Klassiska modellen](backup-azure-vms-prepare.md)
>
>

Den här artikeln innehåller steg för att förbereda din miljö för att säkerhetskopiera en Resource Manager-distribuerad virtuell dator (VM). Stegen visas i procedurer använder Azure-portalen.  

Azure Backup-tjänsten har två typer av valv (säkerhetskopiera valv och recovery services-valv) för att skydda dina virtuella datorer. Ett säkerhetskopieringsvalv skyddar virtuella datorer som distribueras med hjälp av den klassiska distributionsmodellen. Recovery services-ventilen skyddar **både distribuerade klassiska eller Resource Manager distribuerade virtuella datorer**. För att skydda en Resource Manager-distribuerad virtuell dator måste du använda en Recovery Services-valvet.

> [!NOTE]
> Azure har två distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md). Se [förbereda din miljö för att säkerhetskopiera virtuella datorer i Azure](backup-azure-vms-prepare.md) mer information om hur du arbetar med klassisk distribution modellen virtuella datorer.
>
>

Innan du kan skydda eller säkerhetskopiera en Resource Manager-distribuerad virtuell dator (VM), se till att dessa krav finns:

* Skapa ett recovery services-valv (eller identifiera befintliga recovery services-ventilen) *på samma plats som den virtuella datorn*.
* Välj ett scenario, definiera princip för säkerhetskopiering och definiera objekt som ska skyddas.
* Kontrollera installationen av VM-agenten på den virtuella datorn.
* Kontrollera nätverksanslutningen
* Virtuella Linux-datorer, om du vill anpassa säkerhetskopiering miljön för program som konsekvent säkerhetskopiering finns Följ den [steg för att konfigurera inför ögonblicksbilden och efter ögonblickbild skript](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)

Om du känner till dessa villkor redan finns i din miljö och sedan fortsätta till den [säkerhetskopiera virtuella datorer artikeln](backup-azure-vms.md). Om du behöver konfigurera eller kontrollera någon av dessa förutsättningar leder dig igenom stegen för att förbereda den nödvändiga i den här artikeln.

##<a name="supported-operating-system-for-backup"></a>Operativsystem som stöds för säkerhetskopiering
 * **Linux**: Azure Backup stöder [en lista över distributioner som godkänts av Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) med undantag för Core OS Linux. _Andra Bring-Your-äger-Linux-distributioner också fungera så länge som den Virtuella datoragenten är tillgänglig på den virtuella datorn och stöd för Python finns. Vi inte stödja dessa distributioner för säkerhetskopiering._
 * **Windows Server**: versioner som är äldre än Windows Server 2008 R2 stöds inte.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Begränsningar när du säkerhetskopierar och återställer en virtuell dator
Innan du förbereder din miljö kan du förstå begränsningar.

* Säkerhetskopiera virtuella datorer med fler än 16 datadiskar stöds inte.
* Säkerhetskopiering av virtuella datorer med data storlekar för diskar som är större än 1 023 GB stöds inte.
* Säkerhetskopiering av virtuella datorer med en reserverad IP-adress och ingen definierad slutpunkt stöds inte.
* Säkerhetskopiering av virtuella datorer som har krypterats med bara BEK stöds inte. Säkerhetskopiering av virtuella Linux-datorer krypteras med LUKS kryptering stöds inte.
* Säkerhetskopiering av virtuella datorer som innehåller klustret delade Volumes(CSV) eller skala ut filservern konfigurationen rekommenderas inte eftersom de kräver som omfattar alla virtuella datorer som ingår i klusterkonfigurationen under ögonblicksbilden. Azure-säkerhetskopiering har inte stöd för flera Virtuella datorer. 
* Säkerhetskopierade data innehåller monterade nätverksenheter kopplat till VM.
* Att ersätta en befintlig virtuell dator under återställningen stöds inte. Återställningen misslyckas om du försöker återställa den virtuella datorn när den virtuella datorn finns.
* Region mellan säkerhetskopiering och återställning stöds inte.
* Du kan säkerhetskopiera virtuella datorer i alla offentliga områden i Azure (se den [checklista](https://azure.microsoft.com/regions/#services) av regioner som stöds). Om den region som du letar efter stöds idag visas det inte i den nedrullningsbara listan under skapande av valvet.
* Återställa en domänkontrollant stöds (DC) virtuell dator som är en del av en multi-DC-konfiguration bara via PowerShell. Läs mer om [återställa en multi-DC-domänkontrollant](backup-azure-restore-vms.md#restoring-domain-controller-vms).
* Återställning av virtuella datorer som har följande särskilda nätverkskonfigurationer stöds bara via PowerShell. Virtuella datorer som skapades med arbetsflödet för återställning i Användargränssnittet inte dessa nätverkskonfigurationer när återställningen är klar. Läs mer i [återställa virtuella datorer med särskilda nätverkskonfigurationer](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations).
  * Virtuella datorer under konfigurationen av belastningsutjämnaren (interna och externa)
  * Virtuella datorer med flera reserverade IP-adresser
  * Virtuella datorer med flera nätverkskort

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Skapa ett Recovery Services-valv för en virtuell dator
Recovery services-ventilen är en entitet som lagrar säkerhetskopior och återställningspunkter som har skapats med tiden. Recovery services-ventilen innehåller även principer för säkerhetskopiering som är associerade med de skyddade virtuella datorerna.

Så här skapar du ett Recovery Services-valv:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På navmenyn klickar du på **Bläddra** och i listan över resurser skriver du **Recovery Services**. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Recovery Services-valv**.

    ![Klicka på Bläddra och Skriv Recovery Services. När alternativet Recovery Services-valvet, klickar du på den för att öppna bladet Recovery Services-valvet.](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Listan över Recovery Services-valv visas.
3. På menyn **Recovery Services-valv** klickar du på **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Bladet Recovery Services-valv öppnas och du uppmanas att ange **namn**, **prenumeration**, **resursgrupp** och **plats**.

    ![Skapa ett Recovery Services-valv (steg 5)](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Skriv ett namn som innehåller mellan 2 och 50 tecken. Det måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck.
5. Klicka på **Prenumeration** för att visa listan över prenumerationer. Om du inte är säker på vilken prenumeration du ska använda använder du standardprenumerationen (eller den föreslagna). Du kan bara välja mellan flera alternativ om ditt organisationskonto är associerat med flera Azure-prenumerationer.
6. Klicka på **Resursgrupp** för att visa listan över resursgrupper eller klicka på **Nytt** för att skapa en ny resursgrupp. För komplett information om resursgrupper, se [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Klicka på **Plats** för att välja en geografisk region för valvet. Valvet **måste** finnas i samma region som de virtuella datorer som du vill skydda.

   > [!IMPORTANT]
   > Om du är osäker på var din virtuella dator finns stänger du dialogrutan för valvgenerering och går till listan med virtuella datorer på portalen. Om du har virtuella datorer i flera regioner måste du skapa ett Recovery Services-valv i varje region. Skapa valvet på den första platsen innan du fortsätter till nästa plats. Du behöver inte ange lagringskonton för att lagra säkerhetskopierade data: Recovery Services-valvet och tjänsten Azure Backup hanterar detta automatiskt.
   >
   >

8. Klicka på **Skapa**. Det kan ta en stund innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena uppe till höger på portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser ditt valv, klickar du på **uppdatera** till

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    Nu när du har skapat valvet ska vi se hur du konfigurerar lagringsreplikeringen.

## <a name="set-storage-replication"></a>Konfigurera lagringsreplikering
Med alternativet för lagringsreplikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Valvet använder geo-redundant lagring som standard. Lämna alternativet inställt på geo-redundant lagring om det här är din primära säkerhetskopia. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som inte är lika beständigt.

Så här redigerar du inställningen för lagringsreplikering:

1. På den **Recovery Services-valv** bladet välj ditt valv.
    När du klickar på ditt valv inställningsbladet (*som har namnet på valvet överst*) och valvet detaljer blad öppnas.

    ![Välj ditt valv i listan över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. På den **inställningar** bladet använder lodräta skjutreglaget för att rulla ned till den **hantera** avsnitt. Klicka på **säkerhetskopiering infrastruktur** att öppna dess bladet. I den **allmänna** Klicka på avsnittet **konfigurering av säkerhetskopiering** att öppna dess bladet. På bladet **Konfiguration av säkerhetskopiering** väljer du alternativet för lagringsreplikering för ditt valv. Valvet använder geo-redundant lagring som standard. Om du ändrar lagringstyp för replikering, klickar du på **spara**.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/full-blade.png)

     Om du använder Azure som en primär slutpunkt för lagring av säkerhetskopior fortsätter du att använda geo-redundant lagring. Om du använder Azure som en icke-primär säkerhetskopieringslagring slutpunkt Välj lokalt redundant lagring. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy.md#geo-redundant-storage) och [lokalt redundant](../storage/common/storage-redundancy.md#locally-redundant-storage) lagring i [Översikt över Azure Storage-replikering](../storage/common/storage-redundancy.md).
    När du har valt lagringsalternativet för valvet är det dags att associera den virtuella datorn med valvet. För att börja kopplingen identifierar du och registrerar de virtuella Azure-datorerna.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Välj ett säkerhetskopieringsmål, ange en princip och definiera objekt som ska skyddas
Innan du registrerar en virtuell dator med ett valv kör du identifieringsprocessen för att säkerställa att nya virtuella datorer som har lagts till i prenumerationen identifieras. Under processen uppmanas Azure att returnera listan med virtuella datorer i prenumerationen, tillsammans med ytterligare information som molntjänstens namn och regionen. På Azure-portalen refererar scenariot till vad du ska lägga till i Recovery Services-valvet. Principen är schemat för hur ofta återställningspunkter skapas. Principen omfattar också kvarhållningsintervallet för återställningspunkterna.

1. Om du redan har ett öppet Recovery Services-valv går du vidare till steg 2. Om du inte har ett Recovery Services-valv öppna öppnar du den [Azure-portalen](https://portal.azure.com/) och klickar på navmenyn **fler tjänster**.

   * I listan över resurser skriver du **Recovery Services**.
   * När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv** klickar du på det.

     ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

     Listan över Recovery Services-valv visas. Om det finns några valv i din prenumeration kan är den här listan tom.

    ![Vy över listan med Recovery Services-valv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   * Välj en valvet för att öppna dess instrumentpanelen från listan över Recovery Services-valv.

     Bladet inställningar och valvet instrumentpanelen för valda valvet, öppnas.

     ![Öppna bladet för valvet](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. På menyn på instrumentpanelen för valvet öppnar du bladet Säkerhetskopiering genom att klicka på **Säkerhetskopiering**.

    ![Öppna bladet Säkerhetskopiering](./media/backup-azure-arm-vms-prepare/backup-button.png)

    Bladen Säkerhetskopiering och Säkerhetskopieringsmål öppnas.

    ![Öppna bladet Scenario](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Ange på bladet säkerhetskopiering målet **var körs din arbetsbelastning** till Azure och **vad vill du säkerhetskopiera** till den virtuella datorn, klicka sedan på **OK**.

    Nu registreras VM-tillägget med valvet. Bladet Säkerhetskopieringsmål stängs och bladet **Säkerhetskopieringspolicy** öppnas.

    ![Öppna bladet Scenario](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. På bladet Säkerhetskopieringspolicy väljer du den säkerhetskopieringspolicy som du vill använda för valvet.

    ![Välja säkerhetskopieringspolicy](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Information om standardprincipen visas under den nedrullningsbara menyn. Om du vill skapa en ny policy väljer du **Skapa ny** i listrutan. Mer information om hur du definierar en säkerhetskopieringspolicy finns i [Definiera en säkerhetskopieringspolicy](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Klicka på **OK** för att associera säkerhetskopieringspolicyn med valvet.

    Bladet Säkerhetskopieringspolicy stängs och bladet **Välj virtuella datorer** öppnas.
5. På bladet **Välj virtuella datorer** väljer du de virtuella datorer som du vill associera med den angivna principen och klickar på **OK**.

    ![Välja arbetsbelastning](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    Den valda virtuella datorn verifieras. Om du inte ser de virtuella datorerna som du förväntade dig att se, kontrollera att de finns i samma Azure-plats som Recovery Services-valvet och inte redan skyddas i ett annat valv. Platsen för Recovery Services-valvet visas på instrumentpanelen för valvet.

6. Nu när du har definierat alla inställningar för valvet klickar du på **Aktivera säkerhetskopiering** på bladet Säkerhetskopiering. När du gör det distribueras policyn till valvet och de virtuella datorerna. Dock skapas inte den första återställningspunkten för den virtuella datorn med den här åtgärden.

    ![Aktivera säkerhetskopiering](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

När du har aktiverat säkerhetskopieringen körs säkerhetskopieringspolicyn enligt schemat. Om du vill generera en på-begäran jobbet att säkerhetskopiera virtuella datorer nu se [utlösa jobbet som](./backup-azure-arm-vms.md#triggering-the-backup-job).

Om du har problem med att registrera den virtuella datorn finns i följande information på VM-agenten installeras och nätverksanslutningen. Förmodligen behöver inte du följande information om du skyddar virtuella datorer som skapats i Azure. Men om du har migrerat virtuella datorer till Azure sedan vara säker på att VM-agenten har installerats och att den virtuella datorn kan kommunicera med det virtuella nätverket.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installera VM-agenten på den virtuella datorn
VM-agenten i Azure måste installeras på den virtuella Azure-datorn för att säkerhetskopieringstillägget ska fungera. VM-agenten är redan finns på den virtuella datorn om den virtuella datorn har skapats från Azure-galleriet. Den här informationen anges i situationer där du har *inte* använda en virtuell dator som skapats från Azure-galleriet – till exempel du migrerat en virtuell dator från ett lokalt datacenter. I sådana fall måste VM-agenten installeras för att skydda den virtuella datorn. Lär dig mer om den [VM-agenten](../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux).

Om du har problem med att säkerhetskopiera den virtuella datorn i Azure kontrollerar du att VM-agenten i Azure är korrekt installerad på den virtuella datorn (se tabellen nedan). Följande tabell innehåller ytterligare information om VM-agenten för virtuella Windows- och Linux-datorer.

| **Åtgärd** | **Windows** | **Linux** |
| --- | --- | --- |
| Installera VM-agenten |Ladda ned och installera [agentens MSI-fil](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen. |<li> Installera senaste [Linux-agenten](../virtual-machines/linux/agent-user-guide.md). Du måste ha administratörsbehörighet för att slutföra installationen. Vi rekommenderar att du installerar agenten från databasen för din distribution. Vi **rekommenderar inte** Linux VM-agent som installeras direkt från github.  |
| Uppdatera VM-agenten |Det är enkelt att uppdatera VM-agenten. Du installerar bara om [binärfilerna för VM-agenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |Följ anvisningarna för hur du [uppdaterar VM-agenten för Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vi rekommenderar att du uppdaterar agenten från databasen för din distribution. Vi **rekommenderar inte** uppdatering Linux VM-agenten direkt från github.<br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |
| Bekräfta installationen av VM-agenten |<li>Gå till mappen *C:\WindowsAzure\Packages* på den virtuella datorn i Azure. <li>Du bör hitta filen WaAppAgent.exe.<li> Högerklicka på filen, gå till **Egenskaper** och välj fliken **Information**. Fältet Produktversion ska vara 2.6.1198.718 eller högre. |Saknas |

### <a name="backup-extension"></a>Säkerhetskopieringstillägg
När VM-agenten har installerats på den virtuella datorn installerar Azure Backup-tjänsten säkerhetskopieringstillägget till VM-agenten. Tjänsten Azure Backup sömlöst uppgraderar och korrigeringsfiler sekundär anknytning.

Säkerhetskopieringstillägget installeras av Backup-tjänsten oavsett om den virtuella datorn körs eller inte. En virtuell dator som körs har bäst chans att tilldelas en programkonsekvent återställningspunkt. Azure Backup-tjänsten fortsätter dock att säkerhetskopiera den virtuella datorn även om den är avstängd och det inte gick att installera tillägget. Detta kallas för en virtuell offlinedator. I detta fall är återställningspunkten *kraschkonsekvent*.

## <a name="network-connectivity"></a>Nätverksanslutning
För att kunna hantera VM-ögonblicksbilder måste sekundär anknytning anslutningen till Azure offentliga IP-adresser. Den virtuella datorns HTTP-begäranden timeout utan rätt Internet-anslutning och säkerhetskopieringen misslyckas. Om distributionen har åtkomstbegränsningar (via en nätverkssäkerhetsgrupp (NSG), till exempel), väljer du något av följande alternativ för att tillhandahålla en tydlig sökväg för säkerhetskopiering trafik:

* [Godkända Azure datacenter-IP-adressintervall](http://www.microsoft.com/en-us/download/details.aspx?id=41653) -finns i artikeln anvisningar om hur till listan över godkända IP-adresser.
* Distribuera en HTTP-proxyserver för dirigera trafiken.

När du bestämmer vilket alternativ som är avvägningarna mellan hanterbarhet, granulär kontroll och kostnader.

| Alternativ | Fördelar | Nackdelar |
| --- | --- | --- |
| Whitelist IP-adressintervall |Inga ytterligare kostnader.<br><br>För att öppna åtkomst i en NSG, Använd den <i>Set AzureNetworkSecurityRule</i> cmdlet. |Komplext för att hantera som den berörda IP-adressintervall ändras med tiden.<br><br>Tillhandahåller åtkomst till Azure och inte bara lagring som helhet. |
| HTTP-proxy |Granulär kontroll i proxyn över lagringen webbadresser tillåts.<br>Enskild plats Internet-åtkomst till virtuella datorer.<br>Inte ändras Azure IP-adress. |Ytterligare kostnader för att köra en virtuell dator med proxy-programvara. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Godkända Azure-datacentret IP-intervall
* Att godkända Azure-datacenter IP-adressintervall, finns det [Azure-webbplatsen](http://www.microsoft.com/en-us/download/details.aspx?id=41653) för information om IP-adressintervall och instruktioner.
* Du kan använda tjänsten taggar för att tillåta anslutningar till lagring av specifik region med [Service taggar](../virtual-network/security-overview.md#service-tags). Kontrollera att den regel som tillåter åtkomst till lagringskontot har högre prioritet än regeln blockera åtkomst till internet. 

  ![NSG med lagring taggar för en region](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> Storage-taggar är bara tillgängliga i vissa regioner och finns i förhandsgranskningen. Lista över regioner finns [tjänsten taggar för lagring](../virtual-network/security-overview.md#service-tags)

### <a name="using-an-http-proxy-for-vm-backups"></a>Med hjälp av en HTTP-proxy för VM-säkerhetskopieringar
När du säkerhetskopierar en virtuell dator skickas säkerhetskopiering tillägget på den virtuella datorn ögonblicksbild management till Azure Storage med hjälp av en HTTPS-API. Vidarebefordra trafik reservanknytning via HTTP-proxy eftersom det är den enda komponenten som konfigurerats för åtkomst till Internet.

> [!NOTE]
> Det finns ingen rekommendation för proxyprogram som ska användas. Se till att du väljer en proxy som är kompatibel med konfigurationssteg som nedan.
>
>

Exempel bilden nedan visar de tre konfigurationssteg som krävs för att använda en HTTP-proxy:

* Appen VM dirigerar alla HTTP-trafik för Internet via Proxy VM.
* Proxy VM tillåter inkommande trafik från virtuella datorer i det virtuella nätverket.
* Den Nätverkssäkerhetsgrupp (NSG) med namnet NSF låsning måste en säkerhet regeln möjliggör utgående Internet-trafik från VM-Proxy.

![NSG: N med HTTP-proxy deployment diagram](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

Följ dessa steg om du vill använda en HTTP-proxy kommunikation till det offentliga Internet:

#### <a name="step-1-configure-outgoing-network-connections"></a>Steg 1. Konfigurera utgående nätverksanslutningar
###### <a name="for-windows-machines"></a>För Windows-datorer
Detta konfigurerar proxyserverkonfiguration för det lokala systemkontot.

1. Hämta [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. Kör följande kommando från en upphöjd kommandotolk

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Fönster i internet explorer öppnas.
3. Gå till Verktyg -> Internet-alternativ -> anslutningar LAN-inställningar ->.
4. Kontrollera proxy-inställningar för System-kontot. Ange Proxy-IP och port.
5. Stäng Internet Explorer.

Detta ställer in en datoromfattande proxy-konfiguration och kommer att användas för all utgående HTTP/HTTPS-trafik.

Om du har konfigurerat en proxyserver för ett aktuella användarkonto (inte ett lokalt systemkonto) kan du använda följande skript för att använda dem på SYSTEMACCOUNT:

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Om du upptäcker ”(407) krävs proxyautentisering” i serverloggen proxy, kontrollera autentiseringen har konfigurerats korrekt.
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

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Steg 2. Tillåt inkommande anslutningar på proxyservern:
1. Öppna Windows-brandväggen på proxyservern. Det enklaste sättet att komma åt brandväggen är att söka efter Windows-brandväggen med avancerad säkerhet.

    ![Öppna brandväggen](./media/backup-azure-vms-prepare/firewall-01.png)
2. I dialogrutan Windows-brandväggen högerklickar du på **regler för inkommande trafik** och på **ny regel...** .

    ![Skapa en ny regel](./media/backup-azure-vms-prepare/firewall-02.png)
3. I den **ny inkommande regel för**, Välj den **anpassad** för den **regeltyp** och klicka på **nästa**.
4. På sidan för att välja den **programmet**, Välj **alla program** och på **nästa**.
5. På den **protokoll och portar** , anger du följande information och klickar på **nästa**:

    ![Skapa en ny regel](./media/backup-azure-vms-prepare/firewall-03.png)

   * för *protokolltyp* Välj *TCP*
   * för *lokal port* Välj *specifika portar*, i fältet nedan anger du den ```<Proxy Port>``` som har konfigurerats.
   * för *Fjärrport* Välj *alla portar*

     Klicka på ända till slutet och ge regeln ett namn för resten av guiden.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Steg 3. Lägg till en undantagsregel NSG: N:
Ange följande kommando i en Azure PowerShell-kommandotolk:

Följande kommando lägger till ett undantag NSG: N. Det här undantaget kan TCP-trafik från alla portar på 10.0.0.5 till alla Internet-adress på port 80 (HTTP) eller 443 (HTTPS). Om du behöver en specifik port i det offentliga Internet bör du lägga till porten till den ```-DestinationPortRange``` samt.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```


*De här stegen kan du använda specifika namn och värden för det här exemplet. Använd namn och värden för din distribution när du anger, eller kopiera och klistra in information i koden.*

Nu när du vet att du har nätverksanslutning, är du redo att säkerhetskopiera den virtuella datorn. Se [säkerhetskopiera Resource Manager distribuerade virtuella datorer](backup-azure-arm-vms.md).

## <a name="questions"></a>Frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Nästa steg
Nu när du har förberett din miljö för att säkerhetskopiera den virtuella datorn, är nästa logiska steg att skapa en säkerhetskopia. Planering artikeln innehåller mer detaljerad information om hur du säkerhetskopierar virtuella datorer.

* [Säkerhetskopiera virtuella datorer](backup-azure-vms.md)
* [Planera infrastrukturen för säkerhetskopiering VM](backup-azure-vms-introduction.md)
* [Hantera säkerhetskopiering för virtuella datorer](backup-azure-manage-vms.md)
