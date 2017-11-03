---
title: "Förbereda din miljö för att säkerhetskopiera virtuella datorer i Azure | Microsoft Docs"
description: "Kontrollera att din miljö har förberetts för att säkerhetskopiera virtuella datorer i Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonn
editor: 
keywords: "säkerhetskopiering. Säkerhetskopiera;"
ms.assetid: 238ab93b-8acc-4262-81b7-ce930f76a662
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/10/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: 35b40f80c5a9ccc67830429a5a75d2974d0d138c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-your-environment-to-back-up-azure-virtual-machines"></a>Förbereda din miljö för att säkerhetskopiera virtuella Azure-datorer
> [!div class="op_single_selector"]
> * [Resource Manager-modellen](backup-azure-arm-vms-prepare.md)
> * [Klassiska modellen](backup-azure-vms-prepare.md)
>
>

Innan du kan säkerhetskopiera en Azure virtuell dator (VM) finns det tre villkor som måste finnas.

* Du måste skapa ett säkerhetskopieringsvalv eller identifiera ett befintligt säkerhetskopieringsvalvet *i samma region som din virtuella dator*.
* Upprätta nätverksanslutningen mellan Azure offentliga Internet-adresser och Azure storage-slutpunkter.
* Installera VM-agenten på den virtuella datorn.

Om du känner till dessa villkor redan finns i din miljö och sedan fortsätta till den [säkerhetskopiera virtuella datorer artikeln](backup-azure-vms.md). Annars kan leder läsas på den här artikeln dig igenom stegen för att förbereda din miljö för att säkerhetskopiera en Azure VM.

##<a name="supported-operating-system-for-backup"></a>Operativsystem som stöds för säkerhetskopiering
 * **Linux**: Azure Backup stöder [en lista över distributioner som godkänts av Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) med undantag för Core OS Linux. _Andra Bring-Your-äger-Linux-distributioner också fungera så länge som den Virtuella datoragenten är tillgänglig på den virtuella datorn och stöd för Python finns. Vi inte stödja dessa distributioner för säkerhetskopiering._
 * **Windows Server**: versioner som är äldre än Windows Server 2008 R2 stöds inte.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Begränsningar när du säkerhetskopierar och återställer en virtuell dator
> [!NOTE]
> Azure har två distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Följande lista innehåller begränsningar vid distribution i den klassiska modellen.
>
>

* Säkerhetskopiera virtuella datorer med fler än 16 datadiskar stöds inte.
* Säkerhetskopiering av virtuella datorer med en reserverad IP-adress och ingen definierad slutpunkt stöds inte.
* Säkerhetskopierade data innehåller monterade nätverksenheter kopplat till VM.
* Att ersätta en befintlig virtuell dator under återställningen stöds inte. Först tar bort den befintliga virtuella datorn och alla associerade diskar och återställa data från en säkerhetskopia.
* Region mellan säkerhetskopiering och återställning stöds inte.
* Säkerhetskopiering av virtuella datorer med hjälp av Azure Backup-tjänsten stöds i alla offentliga områden i Azure (se den [checklista](https://azure.microsoft.com/regions/#services) av regioner som stöds). Om den region som du letar efter stöds idag visas det inte i den nedrullningsbara listan under skapande av valvet.
* Säkerhetskopiering av virtuella datorer med hjälp av Azure Backup-tjänsten stöds bara för väljer operativsystemversioner:
* Återställa en domänkontrollant stöds (DC) virtuell dator som är en del av en multi-DC-konfiguration bara via PowerShell. Läs mer om [återställa en multi-DC-domänkontrollant](backup-azure-restore-vms.md#restoring-domain-controller-vms).
* Återställning av virtuella datorer som har följande särskilda nätverkskonfigurationer stöds bara via PowerShell. Virtuella datorer som du skapar med hjälp av restore-arbetsflöde i Användargränssnittet inte dessa nätverkskonfigurationer när återställningen är klar. Läs mer i [återställa virtuella datorer med särskilda nätverkskonfigurationer](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations).
  * Virtuella datorer under konfigurationen av belastningsutjämnaren (interna och externa)
  * Virtuella datorer med flera reserverade IP-adresser
  * Virtuella datorer med flera nätverkskort

## <a name="create-a-backup-vault-for-a-vm"></a>Skapa ett säkerhetskopieringsvalv för en virtuell dator
Ett säkerhetskopieringsvalv är en entitet som lagrar alla säkerhetskopior och återställningspunkter som har skapats med tiden. Säkerhetskopieringsvalvet som innehåller också säkerhetskopiering principerna som tillämpas på de virtuella datorerna som säkerhetskopieras.

> [!IMPORTANT]
> Från och med mars 2017 kan du inte längre använda den klassiska portalen för att skapa säkerhetskopieringsvalv. Befintliga säkerhetskopieringsvalv stöds fortfarande och går att [använda Azure PowerShell för att skapa säkerhetskopieringsvalv](./backup-client-automation-classic.md#create-a-backup-vault). Microsoft rekommenderar dock starkt att du skapar Recovery Services-valv för alla distributioner eftersom framtida förbättringar endast kommer att gälla för Recovery Services-valv.


Den här bilden visar relationerna mellan olika Azure Backup-entiteter: ![Azure Backup-enheter och relationer](./media/backup-azure-vms-prepare/vault-policy-vm.png)



## <a name="network-connectivity"></a>Nätverksanslutning
För att kunna hantera VM-ögonblicksbilder måste sekundär anknytning anslutningen till Azure offentliga IP-adresser. Den virtuella datorns HTTP-begäranden timeout utan rätt Internet-anslutning och säkerhetskopieringen misslyckas. Om distributionen har åtkomstbegränsningar (via en nätverkssäkerhetsgrupp (NSG), till exempel), väljer du något av följande alternativ för att tillhandahålla en tydlig sökväg för säkerhetskopiering trafik:

* [Godkända Azure datacenter-IP-adressintervall](http://www.microsoft.com/en-us/download/details.aspx?id=41653) -finns i artikeln anvisningar om hur till listan över godkända IP-adresser.
* Distribuera en HTTP-proxyserver för dirigera trafiken.

När du bestämmer vilket alternativ som är avvägningarna mellan hanterbarhet, granulär kontroll och kostnader.

| Alternativ | Fördelar | Nackdelar |
| --- | --- | --- |
| Whitelist IP-adressintervall |Inga ytterligare kostnader.<br><br>För att öppna åtkomst i en NSG, Använd den <i>Set AzureNetworkSecurityRule</i> cmdlet. |Komplext för att hantera som den berörda IP-adressintervall ändras med tiden.<br><br>Tillhandahåller åtkomst till Azure och inte bara lagring som helhet. |
| HTTP-proxy |Granulär kontroll i proxyn över lagringen webbadresser tillåts. Att installationsprogrammet granulär kontroll i proxy, https://\*.blob.core.windows.net/\* URL mönstret måste vara godkända. Att godkända endast lagringskontot används av den virtuella datorn https://\<storageAccount\>.blob.core.windows.net/\* URL mönstret måste vara godkända. <br>Enskild plats Internet-åtkomst till virtuella datorer.<br>Inte ändras Azure IP-adress. |Ytterligare kostnader för att köra en virtuell dator med proxy-programvara. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Godkända Azure-datacentret IP-intervall
Att godkända Azure-datacenter IP-adressintervall, finns det [Azure-webbplatsen](http://www.microsoft.com/en-us/download/details.aspx?id=41653) för information om IP-adressintervall och instruktioner.

### <a name="using-an-http-proxy-for-vm-backups"></a>Med hjälp av en HTTP-proxy för VM-säkerhetskopieringar
När du säkerhetskopierar en virtuell dator skickas säkerhetskopiering tillägget på den virtuella datorn ögonblicksbild management till Azure Storage med hjälp av en HTTPS-API. Vidarebefordra trafik reservanknytning via HTTP-proxy eftersom det är den enda komponenten som konfigurerats för åtkomst till Internet.

> [!NOTE]
> Det finns ingen rekommendation för proxyprogram som ska användas. Se till att du väljer en proxy som har utgående varaktighet och som är kompatibel med konfigurationssteg som nedan. Kontrollera att programvara från tredje part inte kan ändra proxyinställningarna
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

*Se till att ersätta namnen i exemplet med informationen som är lämpliga för din distribution.*

## <a name="vm-agent"></a>VM-agent
Innan du kan säkerhetskopiera den virtuella Azure-datorn, bör du kontrollera att den Virtuella Azure-agenten är korrekt installerat på den virtuella datorn. Eftersom den Virtuella datoragenten är en valfri komponent vid den tidpunkt då den virtuella datorn har skapats kan du kontrollera att kryssrutan för den Virtuella datoragenten är markerad innan den virtuella datorn har etablerats.

### <a name="manual-installation-and-update"></a>Manuell installation och uppdatera
Den Virtuella datoragenten finns redan i virtuella datorer som har skapats från Azure-galleriet. Virtuella datorer som har migrerats från lokala Datacenter skulle inte ha VM-agenten installerad. För sådana virtuella datorer måste den Virtuella datoragenten installeras explicit. 

| **Åtgärd** | **Windows** | **Linux** |
| --- | --- | --- |
| Installera VM-agenten |Ladda ned och installera [agentens MSI-fil](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen.<li>[Uppdatera VM-egenskapen](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att ange att agenten är installerad. |<li> Installera senaste [Linux-agenten](../virtual-machines/linux/agent-user-guide.md). Du måste ha administratörsbehörighet för att slutföra installationen. Vi rekommenderar att du installerar agenten från databasen för din distribution. Vi **rekommenderar inte** Linux VM-agent som installeras direkt från github.  |
| Uppdatera VM-agenten |Det är enkelt att uppdatera VM-agenten. Du installerar bara om [binärfilerna för VM-agenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |Följ anvisningarna för hur du [uppdaterar VM-agenten för Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vi rekommenderar att du uppdaterar agenten från databasen för din distribution. Vi **rekommenderar inte** uppdatering Linux VM-agenten direkt från github.<br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |
| Bekräfta installationen av VM-agenten |<li>Gå till mappen *C:\WindowsAzure\Packages* på den virtuella datorn i Azure. <li>Du bör hitta filen WaAppAgent.exe.<li> Högerklicka på filen, gå till **Egenskaper** och välj fliken **Information**. Fältet Produktversion ska vara 2.6.1198.718 eller högre. |Saknas |

Lär dig mer om den [Virtuella datoragenten](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) och [hur du installerar det](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### <a name="backup-extension"></a>Säkerhetskopieringstillägg
Om du vill säkerhetskopiera den virtuella datorn installerar Azure Backup-tjänsten ett tillägg till den Virtuella datoragenten. Azure Backup-tjänsten uppgraderar och korrigerar säkerhetskopieringstillägget utan ytterligare användarinteraktion.

Sekundär anknytning installeras om den virtuella datorn körs. En aktiv virtuell dator innehåller också största möjlighet att få en programkonsekvent återställningspunkt. Däremot installeras på Azure Backup-tjänsten fortsätter att säkerhetskopiera den virtuella datorn – även om den är avstängd, och filnamnstillägget inte kunde (aka Offline VM). I det här fallet återställningspunkten kommer att *krasch konsekvent* som beskrivs ovan.

## <a name="questions"></a>Frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Nästa steg
Nu när du har förberett din miljö för att säkerhetskopiera den virtuella datorn, är nästa logiska steg att skapa en säkerhetskopia. Planering artikeln innehåller mer detaljerad information om hur du säkerhetskopierar virtuella datorer.

* [Säkerhetskopiera virtuella datorer](backup-azure-vms.md)
* [Planera infrastrukturen för säkerhetskopiering VM](backup-azure-vms-introduction.md)
* [Hantera säkerhetskopiering för virtuella datorer](backup-azure-manage-vms.md)
