---
title: Fel sökning av VM-certifiering för Azure Marketplace
description: Den här artikeln beskriver fel söknings ämnen som är vanliga för att testa och certifiera VM-avbildningar för Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: f065b1bc98eab86542ecff73e1471e4d90cd4182
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339542"
---
# <a name="vm-certification-troubleshooting"></a>Felsökning av VM-certifiering

När du publicerar avbildningen av den virtuella datorn (VM) till Azure Marketplace, verifierar Azure-teamet om det för att säkerställa dess start säkerhet, säkerhet och Azure-kompatibilitet. Om något av testerna med hög kvalitet Miss klar Miss klar, kommer publiceringen att Miss läge och du får ett fel meddelande som beskriver problemet.

I den här artikeln beskrivs vanliga fel meddelanden under publicering av VM-avbildningar, tillsammans med relaterade lösningar.

> [!NOTE]
> Om du har frågor eller feedback om förbättringar kan du kontakta [support](https://aka.ms/marketplacepublishersupport)för partner Center.

## <a name="approved-base-image"></a>Godkänd bas avbildning

När du skickar en begäran om att publicera om avbildningen med uppdateringar kan det hända att test fallet för del nummer verifiering Miss lyckas. Om det Miss lyckas godkänns inte avbildningen.

Felet uppstår när du använder en bas avbildning som tillhör en annan utgivare och du har uppdaterat avbildningen. I det här fallet kommer du inte att kunna publicera din avbildning.

Åtgärda problemet genom att hämta avbildningen från Azure Marketplace och göra ändringar i den. Mer information finns i följande artiklar:

- [Linux-avbildningar](../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows-avbildningar](azure-vm-create-using-approved-base.md)

> [!Note]
> Om du använder en Linux-avbildning som inte tas från Azure Marketplace kan du förskjuta den första partitionen med 2048 KB. På så sätt kan du använda det oformaterade utrymmet för att lägga till ny fakturerings information och låta Azure fortsätta att publicera den virtuella datorn på Azure Marketplace.  

> [!Note]
> Om du använder en Linux-avbildningsfil som inte tas från Marketplace kan du förskjuta den första partitionen med 2048 KB. På så sätt kan du använda det oformaterade utrymmet för att lägga till ny fakturerings information och låta Azure fortsätta att publicera den virtuella datorn på Marketplace.  

## <a name="vm-extension-failure"></a>Problem med VM-tillägg

Kontrol lera om avbildningen har stöd för VM-tillägg.

Gör så här för att aktivera VM-tillägg:

1. Välj din virtuella Linux-dator.
1. Gå till **diagnostikinställningar**.
1. Aktivera bas-matriser genom att uppdatera **lagrings kontot**.
1. Välj **Spara**.

   ![Aktivera övervakning på gästnivå](./media/create-vm/vm-certification-issues-solutions-1.png)

Kontrol lera att de virtuella dator tilläggen är korrekt aktiverade genom att göra följande:

1. I den virtuella datorn väljer du fliken **VM-tillägg** och kontrollerar sedan statusen för **Linux Diagnostics-tillägget**.
1. 
    * Om statusen är *etableringen slutförd* är testerna för tilläggen klara.  
    * Om statusen är *etableringen* misslyckades test väskan för tillägg och du måste ange den strikta flaggan.

      ![Skärm bild som visar att etableringen har slutförts](./media/create-vm/vm-certification-issues-solutions-2.png)

      Om det inte går att använda tillägget för virtuell dator kan du läsa mer i [använda Linux-tillägg för att övervaka mått och loggar](../virtual-machines/extensions/diagnostics-linux.md) . Om du inte vill att VM-tillägget ska aktive ras kontaktar du support teamet och ber dem att inaktivera det.

## <a name="vm-provisioning-issue"></a>Problem med VM-etablering

Kontrol lera att du har följt den virtuella dator etablerings processen noggrant innan du skickar in erbjudandet. Information om hur du visar JSON-formatet för etablering av den virtuella datorn finns i [testa en avbildning av en virtuell dator](azure-vm-image-test.md).

Etablerings problem kan omfatta följande fel scenarier:

|Scenario|Fel|Orsak|Lösning|
|---|---|---|---|
|1|Ogiltig virtuell hård disk (VHD)|Om det angivna cookie-värdet i VHD-foten är felaktigt anses den virtuella hård disken vara ogiltig.|Återskapa avbildningen och skicka begäran.|
|2|Ogiltig Blob-typ|VM-etableringen misslyckades eftersom det använda blocket är en Blob-typ i stället för en typ av sida.|Återskapa avbildningen och skicka begäran.|
|3|Tids gräns för etablering eller inte generaliseras korrekt|Det finns ett problem med VM-generalisering.|Återskapa avbildningen med generalisering och skicka begäran.|
|

> [!NOTE]
> Mer information om VM-generalisering finns i:
> - [Linux-dokumentation](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Windows-dokumentation](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)


## <a name="vhd-specifications"></a>VHD-specifikationer

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix cookie och andra VHD-specifikationer
Strängen "conectix" är en del av VHD-specifikationen och definieras som cookie för 8 byte i VHD-foten nedan som identifierar filens skapare. Alla VHD-filer som skapats av Microsoft har denna cookie. 

En VHD-formaterad BLOB måste ha en sidfot på 512 byte; Detta är formatet för VHD-foten:

|Fält för hård disk foten|Storlek (byte)|
|---|---|
Cookie|8
Funktioner|4
Fil format version|4
Data förskjutning|8
Tidsstämpel|4
Skapare program|4
Creator-version|4
Skapare värd operativ system|4
Ursprunglig storlek|8
Aktuell storlek|8
Disk geometri|4
Disktyp|4
Kontrollsumma|4
Unikt ID|16
Sparat tillstånd|1
Reserverat|427


### <a name="vhd-specifications"></a>VHD-specifikationer
Säkerställ att **VHD uppfyller följande kriterier** för att säkerställa en sömlös publicerings upplevelse:
* Cookien måste innehålla strängen "conectix"
* Disk typen måste vara fast
* Den virtuella hård diskens storlek är minst 20 MB
* Den virtuella hård disken är justerad (dvs. den virtuella storleken måste vara en multipel av 1 MB)
* VHD-blobens längd = virtuell storlek + VHD-fotens längd (512)

Du kan ladda ned VHD-specifikationen [här.](https://www.microsoft.com/download/details.aspx?id=23850)


## <a name="software-compliance-for-windows"></a>Program varans kompatibilitet för Windows

Om din begäran om Windows-avbildning avvisas på grund av ett problem med program varu kompatibilitet kan du ha skapat en Windows-avbildning med den installerade SQL Server-instansen i stället för att ta den relevanta SQL-grundavbildningen från Azure Marketplace.

Skapa inte en egen Windows-avbildning med SQL Server installerad. Använd i stället de godkända SQL-bas avbildningarna (Enterprise/standard/Web) från Azure Marketplace.

Om du försöker installera Visual Studio eller någon Office-licensierad produkt kontaktar du support teamet för tidigare godkännande.

Mer information om hur du väljer en godkänd bas finns i [skapa en virtuell dator från en godkänd bas](azure-vm-create-using-approved-base.md).

## <a name="tool-kit-test-case-execution-failed"></a>Det gick inte att köra test väska för verktygs paket

Microsoft Certificate Toolkit kan hjälpa dig att köra test ärenden och kontrol lera att den virtuella hård disken eller avbildningen är kompatibel med Azure-miljön.

Hämta [Microsoft Certificate Toolkit](azure-vm-image-test.md).

## <a name="linux-test-cases"></a>Scenarier för Linux-test

I följande tabell visas de Linux-testfall där Toolkit körs. Test verifiering anges i beskrivningen.

|Scenario|Testfall|Beskrivning|
|---|---|---|
|1|Bash-historik|Bash historik-filer bör rensas innan du skapar den virtuella dator avbildningen.|
|2|Linux-agentens version|Azure Linux-agentens 2.2.41 eller senare bör installeras.|
|3|Nödvändiga kernel-parametrar|Verifierar att följande kernel-parametrar har angetts: <br>konsol = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|Byt partition på OS-disk|Kontrollerar att växlings partitionerna inte skapas på OS-disken.|
|5|Rot partition på OS-disk|Skapa en enda rotnod för OS-disken.|
|6|OpenSSL-version|OpenSSL-versionen ska vara v 0.9.8 eller senare.|
|7|Python-version|Python version 2,6 eller senare rekommenderas starkt.|
|8|Klientens Alive-intervall|Ange ClientAliveInterval till 180. Det kan ställas in från 30 till 235 på programmets behov. Om du aktiverar SSH för dina slutanvändare måste det här värdet anges som förklarat.|
|9|Operativsystemarkitektur|Endast 64-bitars operativsystem stöds.|
|10|Automatisk uppdatering|Anger om automatisk uppdatering av Linux-Agent är aktiverat.|

### <a name="common-errors-found-while-executing-previous-test-cases"></a>Vanliga fel som påträffades vid körning av tidigare test fall

I följande tabell visas vanliga fel som påträffas vid körning av föregående test fall:
 
|Scenario|Testfall|Fel|Lösning|
|---|---|---|---|
|1|Test fall för Linux-agentens version|Den lägsta Linux-agentens version är 2.2.41 eller senare. Detta krav har varit obligatoriskt sedan den 1 maj 2020.|Uppdatera Linux-agentens version och bör vara 2,241 eller senare. Mer information finns på sidan med [versions uppdateringar för Linux-agenten](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).|
|2|Test väska för bash-historik|Du får ett fel meddelande om storleken på bash-historiken i den skickade avbildningen är större än 1 KB. Storleken är begränsad till 1 KB för att säkerställa att all potentiellt känslig information inte samlas in i din bash-historik fil.|Lös problemet genom att montera den virtuella hård disken till någon annan fungerande virtuell dator och göra eventuella ändringar (till exempel ta bort historikfilerna *. bash* ) för att minska storleken till mindre än eller lika med 1 KB.|
|3|Nödvändigt test fall för kernel-parameter|Du får det här felet när värdet för- **konsolen** inte är inställt på **ttyS0**. Kontrol lera genom att köra följande kommando:<br>`cat /proc/cmdline`|Ange värdet för- **konsolen** till **ttyS0** och skicka begäran på nytt.|
|4|Test fall för ClientAlive-intervall|Om verktygs resultatet ger dig ett misslyckat resultat för det här test fallet finns det ett olämpligt värde för **ClientAliveInterval**.|Ange värdet för **ClientAliveInterval** till mindre än eller lika med 235 och skicka sedan begäran igen.|

### <a name="windows-test-cases"></a>Windows-testfall

I följande tabell visas de Windows-testfall där verktygs uppsättningen ska köras, tillsammans med en beskrivning av test verifieringen:

|Scenario |Testfall|Beskrivning|
|---|---|---|---|
|1|Operativsystemarkitektur|Azure stöder endast 64-bitars operativ system.|
|2|Användar konto beroende|Program körningen bör inte vara beroende av administratörs kontot.|
|3|Redundanskluster|Funktionen kluster för växling vid fel i Windows Server stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|4|IPV6|IPv6 stöds ännu inte i Azure-miljön. Programmet bör inte vara beroende av den här funktionen.|
|5|DHCP|Dynamic Host Configuration Protocol server rollen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|6|Hyper-V|Rollen Hyper-V-Server stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|7|Fjärråtkomst|Fjärråtkomst (Direct Access) Server rollen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|8|Rights Management tjänster|Rights Management tjänster. Server rollen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|9|Windows Deployment Services|Windows Deployment Services. Server rollen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|10|BitLocker-diskkryptering|BitLocker-diskkryptering stöds inte på operativ systemets hård disk, men det kan användas på data diskar.|
|11|Namn server för Internet Storage|Funktionen Internet Storage Name Server stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|12|Multipath I/O|Multipath I/O. Den här server funktionen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|13|Utjämning av nätverksbelastning|Utjämning av nätverks belastning. Den här server funktionen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|14|Peer Name Resolution Protocol|Peer Name Resolution Protocol. Den här server funktionen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|15|SNMP-tjänster|Tjänst funktionen Simple Network Management Protocol (SNMP) stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|16|Windows Internet Name Service|Windows Internet Name Service. Den här server funktionen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|17|Trådlös LAN-tjänst|Tjänsten Wireless LAN. Den här server funktionen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|

Om du kommer över eventuella problem med föregående test fall, se kolumnen **Beskrivning** i tabellen för lösningen. Kontakta support teamet om du behöver mer information. 

## <a name="data-disk-size-verification"></a>Verifiering av data disk storlek

Om storleken på en begäran som skickas med data disken är större än 1023 GB, godkänns inte begäran. Den här regeln gäller för både Linux och Windows.

Skicka begäran igen med en storlek som är mindre än eller lika med 1023 GB.

## <a name="os-disk-size-validation"></a>Verifiering av OS-diskens storlek

Se följande regler för begränsningar av storleken på OS-disken. När du skickar en begäran kontrollerar du att storleken på operativ system disken är inom begränsningen för Linux eller Windows.

|Operativsystem|Rekommenderad VHD-storlek|
|---|---|
|Linux|30 GB till 1023 GB|
|Windows|30 GB till 250 GB|
|

Eftersom de virtuella datorerna tillåter åtkomst till det underliggande operativ systemet kontrollerar du att VHD-storleken är tillräckligt stor för den virtuella hård disken. Eftersom diskarna inte kan utökas utan nedtid använder du en disk storlek från 30 GB till 50 GB.

|VHD-storlek|Faktisk naturlig storlek|Lösning|
|---|---|---|
|>500 tebibyte (TiB)|saknas|Kontakta support teamet om du vill ha ett undantags godkännande.|
|250-500 TiB|>200-gibibyte (GiB) skillnad från BLOB-storlek|Kontakta support teamet om du vill ha ett undantags godkännande.|
|

> [!NOTE]
> Större disk storlekar medför högre kostnader och medför en fördröjning under installations-och Replikerings processen. På grund av denna fördröjning och kostnad kan support teamet söka efter motivering för undantags godkännande.

## <a name="wannacry-patch-verification-test-for-windows"></a>Verifierings test för WannaCry-korrigering för Windows

För att förhindra en potentiell attack som är relaterad till WannaCry virus, se till att alla Windows-avbildningar har uppdaterats med den senaste korrigeringen.

Om du vill kontrol lera den Windows Server-version som har korrigerats för OS-informationen och den lägsta version som den kommer att stödja, se följande tabell: 

Avbildnings fil versionen kan verifieras från `C:\windows\system32\drivers\srv.sys` eller `srv2.sys` .

> [!NOTE]
> Windows Server 2019 saknar nödvändiga versions krav.

|Operativsystem|Version|
|---|---|
|Windows servar 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|
|

## <a name="sack-vulnerability-patch-verification"></a>Bekräftelse av sårbarhets sårbarhets korrigering

När du skickar en Linux-avbildning kan din begäran avvisas på grund av problem med kernel-versionen.

Uppdatera kärnan med en godkänd version och skicka begäran på nytt. Du hittar den godkända kernel-versionen i följande tabell. Versions numret måste vara lika med eller större än det tal som anges här.

Om avbildningen inte är installerad med någon av följande kernel-versioner uppdaterar du den med rätt korrigeringar. Begär det godkännande som krävs från support teamet efter att avbildningen har uppdaterats med de här korrigeringarna:

- CVE – 2019-11477 
- CVE – 2019-11478 
- CVE – 2019-11479

|OS-familj|Version|Kernel|
|---|---|---|
|Ubuntu|14,04 LTS|4.4.0 – 151| 
||14,04 LTS|4.15.0-1049-*-Azure|
||16,04 LTS|4.15.0 – 1049|
||18,04 LTS|4.18.0 – 1023|
||18,04 LTS|5.0.0 – 1025|
||18,10|4.18.0 – 1023|
||19,04|5.0.0 – 1010|
||19,04|5.3.0 – 1004|
|RHEL och% OS|6,10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7,4|3.10.0-693.50.3|
||7.5|3.10.0-862.34.2|
||7,6|3.10.0-957.21.3|
||7,7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0 – 147|
||"7-RAW" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL – SAP 7,4|TBD|
||RHEL – SAP 7,5|TBD|
|SLES|SLES11SP4 (inklusive SAP)|3.0.101-108.95.2|
||SLES12SP1 för SAP|3.12.74-60.64.115.1|
||SLES12SP2 för SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (kernel-Azure)|
||SLES12SP3 för SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (kernel-Azure)|
||SLES12SP4 för SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (kernel-Azure)|
||SLES15 för SAP|4.12.14-5.30.1 (kernel-Azure)|
||SLES15SP1|4.12.14-5.30.1 (kernel-Azure)|
|Oracle|6,10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0 – 7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK följer RHEL ovan|
||7,6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|Core-stabila 2079.6.0|4.19.43*|
||Beta 2135.3.1|4.19.50*|
||Alpha-2163.2.1|4.19.50*|
|Debian|Jessie (säkerhet)|3.16.68-2|
||Jessie backports|4.9.168-1 + deb9u3|
||Sträck ut (säkerhet)|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0-18 + deb9u1|
||Buster, sid (sträck ut backports)|4.19.37-5|
|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>Bild storleken måste vara i multipler av megabyte

Alla virtuella hård diskar på Azure måste ha en virtuell storlek som är justerad till multipler av 1 megabyte (MB). Om din virtuella hård disk inte följer den rekommenderade virtuella storleken kan din begäran avvisas.

Följ rikt linjerna när du konverterar från en RAW-disk till en virtuell hård disk och se till att storleken på den råa disken är en multipel av 1 MB. Mer information finns i [information om icke-godkända distributioner](../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Åtkomst till virtuell dator nekades

Om du kommer över problem med nekad åtkomst när du kör test fallen på den virtuella datorn kan det bero på otillräcklig behörighet att köra test fallen.

Kontrol lera om rätt åtkomst är aktive rad för det konto som självtest-fall körs på. Om åtkomst inte är aktive rad kan du köra test fallen. Om du inte vill aktivera åtkomst kan du dela resultatet av självtesten med support teamet.

Om du vill skicka in din begäran med SSH-inaktive rad avbildning för certifierings processen följer du stegen nedan.

1. Kör Azure Toolkit på din avbildning. (Hämta det [senaste verktyget](https://aka.ms/AzureCertificationTestTool)

2. Skapa ett [support ärende](https://aka.ms/marketplacepublishersupport), bifoga verktygs rapporten och ange erbjudande information – erbjudandets namn, utgivar namn, plan-ID/SKU och version.

3. Skicka in din certifikatbegäran igen.


## <a name="download-failure"></a>Nedladdnings problem
    
I följande tabell finns några problem som kan uppstå när du hämtar den virtuella dator avbildningen med hjälp av en URL för signatur för delad åtkomst (SAS).

|Scenario|Fel|Orsak|Lösning|
|---|---|---|---|
|1|Blobben hittades inte|Den virtuella hård disken kan antingen tas bort eller flyttas från den angivna platsen.|| 
|2|BLOB som används|Den virtuella hård disken används av en annan intern process.|Den virtuella hård disken ska vara i ett använt tillstånd när du laddar ned den med hjälp av en SAS-URL.|
|3|Ogiltig SAS-URL|Den tillhör ande SAS-URL: en för den virtuella hård disken är felaktig.|Hämta rätt SAS-URL.|
|4|Ogiltig signatur|Den tillhör ande SAS-URL: en för den virtuella hård disken är felaktig.|Hämta rätt SAS-URL.|
|6|Villkorlig HTTP-rubrik|SAS-webbadressen är ogiltig.|Hämta rätt SAS-URL.|
|7|Ogiltigt VHD-namn|Kontrol lera om det finns specialtecken, till exempel ett procent tecken (%) eller citat tecken (") finns i VHD-namnet.|Byt namn på VHD-filen genom att ta bort specialtecknen.|
|

## <a name="first-mb-2048-kb-partition-only-for-linux"></a>Första MB (2048 KB) partition (endast för Linux)

När du skickar den virtuella hård disken måste du se till att den första 2048 KB av den virtuella hård disken är tom. Annars kommer din begäran att Miss Missing *.

>[!NOTE]
>* För vissa särskilda bilder, till exempel de som skapats ovanpå Azure Windows Base-avbildningar som tagits från Azure Marketplace, kontrollerar vi om det finns en fakturerings etikett och ignorerar MB-partitionen om fakturerings tag gen är närvarande och matchar våra interna tillgängliga värden.


## <a name="steps-for-creating-first-mb-2048-kb-partition-only-for-linux-on-an-empty-vhd"></a>Steg för att skapa den första MB-partitionen (2048 KB) (endast för Linux) på en tom virtuell hård disk

Steg 1: skapa valfri typ av virtuell dator (exempel: Ubuntu,% OS osv.). Fyll i de obligatoriska fälten och klicka på "Nästa: diskar>" \
![Nästa: disks-kommando](./media/create-vm/vm-certification-issues-solutions-15.png)

Steg 2: skapa en icke-hanterad disk för den virtuella datorn.
![Skapa en icke-hanterad disk](./media/create-vm/vm-certification-issues-solutions-16.png)

Observera att du kan gå med standardvärden eller ange ett värde för fält som NIC, NSG och offentlig IP-adress.

Steg 3: när du har skapat den virtuella datorn klickar du på "diskar" som finns på den vänstra sidan, som du ser nedan ![ Klicka på "diskar"](./media/create-vm/vm-certification-issues-solutions-17.png)

Steg 4: Anslut den virtuella hård disken som data disk till den virtuella datorn ovan för att skapa en partitionstabell enligt nedan.
![Anslut din virtuella hård disk](./media/create-vm/vm-certification-issues-solutions-18.png)

Klicka på Lägg till DataDisk-> befintlig BLOB-> bläddra ditt lagrings konto för virtuella hård diskar – > container-> Välj VHD-> Klicka på OK enligt nedan \
![Välj virtuell hård disk](./media/create-vm/vm-certification-issues-solutions-19.png)

Din virtuella hård disk kommer att läggas till som data disk LUN 0 och starta om den virtuella datorn när disken har lagts till

Steg 5: när du startar om den virtuella datorn loggar du in på den virtuella datorn med hjälp av SparaTillFil (eller någon annan klient) och kör kommandot "sudo-i" för att få rot åtkomst.

![Logga in på den virtuella datorn](./media/create-vm/vm-certification-issues-solutions-20.png)

Steg 6: Följ stegen nedan för att skapa en partition på din virtuella hård disk.

a) Skriv kommandot fdisk/dev/SDB

b) om du vill visa listan över befintliga partitioner från din virtuella hård disk skriver du p

c) Skriv d för att ta bort alla befintliga partitioner som är tillgängliga i din virtuella hård disk (du kan hoppa över det här steget om det inte behövs) ![ ta bort alla befintliga partitioner](./media/create-vm/vm-certification-issues-solutions-21.png)

d) Skriv n om du vill skapa en ny partition och välj p för (primär partition).

e) ange 2048 som "första sektor"-värdet och du kan lämna "sista sektorn" eftersom det kommer att ta standardvärdet. Observera att alla data kommer att raderas till 2048 KB.
           
>[!NOTE]
>* Observera att när du skapar partitionen som ovan kommer alla befintliga data att raderas till 2048 KB, och därför bör du säkerhetskopiera den virtuella hård disken innan du kör kommandot ovan.

Se efter i skärm bilden nedan för din referens.
![Raderade data](./media/create-vm/vm-certification-issues-solutions-22.png)

f) Skriv w för att bekräfta skapandet av partitionen. 

![Skapa partition](./media/create-vm/vm-certification-issues-solutions-23.png)

g) du kan kontrol lera partitionstabellen genom att köra kommandot n fdisk/dev/SDB och skriva p. sedan kan du se som nedan. partitionen skapas med 2048 offset-värde. 

 ![2048 förskjutning](./media/create-vm/vm-certification-issues-solutions-24.png)

Steg 7: koppla från den virtuella hård disken från den virtuella datorn och ta bort den virtuella datorn.

         
## <a name="steps-for-creating-first-mb-2048-kb-partition-only-for-linux-by-moving-the-existing-data-on-vhd"></a>Steg för att skapa den första MB-partitionen (2048 KB) (endast för Linux) genom att flytta befintliga data på den virtuella hård disken

Steg 1: skapa valfri typ av virtuell dator (exempel: Ubuntu,% OS osv.). Fyll i de obligatoriska fälten och klicka på "Nästa: diskar>" \
![Klicka på Nästa: diskar>](./media/create-vm/vm-certification-issues-solutions-15.png)

Steg 2: skapa en icke-hanterad disk för den virtuella datorn.
![Skapa en icke-hanterad disk](./media/create-vm/vm-certification-issues-solutions-16.png)

Observera att du kan gå med standardvärden eller ange ett värde för fält som NIC, NSG och offentlig IP-adress.

Steg 3: när du har skapat den virtuella datorn klickar du på "diskar" som finns på den vänstra sidan, som du ser nedan ![ Klicka på "diskar"](./media/create-vm/vm-certification-issues-solutions-17.png)

Steg 4: Anslut den virtuella hård disken som data disk till den virtuella datorn ovan för att skapa en partitionstabell enligt nedan.
![Partitionstabell](./media/create-vm/vm-certification-issues-solutions-18.png)

Klicka på Lägg till DataDisk-> befintlig BLOB-> bläddra ditt lagrings konto för virtuella hård diskar – > container-> Välj VHD-> Klicka på OK enligt nedan \
![Välj virtuell hård disk](./media/create-vm/vm-certification-issues-solutions-19.png)

Din virtuella hård disk kommer att läggas till som data disk LUN 0 och starta om den virtuella datorn när disken har lagts till

Steg 5: när du startar om den virtuella datorn loggar du in på den virtuella datorn med hjälp av SparaTillFil och kör kommandot "sudo-i" för att få rot åtkomst. \
![Logga in efter omstart](./media/create-vm/vm-certification-issues-solutions-20.png)

Steg 6: Excute kommandot ECHO ' + 1M ' | sfdisk--move-data/dev/SDC-N 1 ![ Kör kommando](./media/create-vm/vm-certification-issues-solutions-25.png)

>[!NOTE]
>* Observera att kommandot ovan kan ta längre tid att slutföra, beroende på diskens storlek

Steg 7: koppla från den virtuella hård disken från den virtuella datorn och ta bort den virtuella datorn.


## <a name="default-credentials"></a>Standardautentiseringsuppgifter

Se alltid till att standardautentiseringsuppgifterna inte skickas med den skickade virtuella hård disken. Om du lägger till standardautentiseringsuppgifter blir den virtuella hård disken mer sårbar för säkerhetshot. Skapa i stället dina egna autentiseringsuppgifter när du skickar den virtuella hård disken.
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk mappas felaktigt

När en begäran skickas med flera data diskar, men deras ordning inte är i följd, betraktas detta som ett mappnings problem. Om det till exempel finns tre data diskar, måste nummer ordningen vara *0, 1, 2*. En annan ordning behandlas som ett mappnings problem.

Skicka begäran igen med korrekt ordningsföljd av data diskar.

## <a name="incorrect-os-mapping"></a>Felaktig OS-mappning

När en avbildning skapas, kan den mappas till eller tilldelas fel OS-etikett. Om du till exempel väljer **Windows** som en del av namnet på operativ systemet när du skapar avbildningen bör operativ system disken endast installeras med Windows. Samma krav gäller för Linux.

## <a name="vm-not-generalized"></a>Den virtuella datorn är inte generaliserad

Om alla avbildningar som tas från Azure Marketplace ska återanvändas måste det virtuella operativ systemet vara generaliserat.

* För **Linux** generaliserar följande process en virtuell Linux-dator och distribuerar den igen som en separat virtuell dator.

  I SSH-fönstret anger du följande kommando: `sudo waagent -deprovision+user`

* För **Windows** generaliserar du Windows-avbildningar med hjälp av `sysreptool` .

Mer information om det här verktyget finns i [Översikt över system förberedelse (Sysprep)]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>DataDisk-fel

Använd följande tabell för lösningar på fel som är relaterade till data disken:

|Fel|Orsak|Lösning|
|---|---|---|
|`DataDisk- InvalidUrl:`|Det här felet kan inträffa på grund av ett ogiltigt antal som angetts för Logical Unit Number (LUN) när erbjudandet skickas.|Kontrol lera att LUN-nummersekvensen för data disken är i Partner Center.|
|`DataDisk- NotFound:`|Det här felet kan inträffa på grund av att en datadisk inte finns på en angiven SAS-URL.|Kontrol lera att data disken finns på SAS-URL: en som anges i begäran.|
|

## <a name="remote-access-issue"></a>Problem med fjärråtkomst

Om alternativet Remote Desktop Protocol (RDP) inte är aktiverat för Windows-avbildningen visas det här felet. 

Aktivera RDP-åtkomst för Windows-avbildningar innan du skickar dem.

## <a name="bash-history-failed"></a>Bash-historik misslyckades

Du ser det här felet om storleken på bash-historiken i den skickade avbildningen är större än 1 KB. Storleken är begränsad till 1 KB för att säkerställa att all potentiellt känslig information inte samlas in i din bash-historik fil.

Nedan visas stegen för att ta bort "bash historik".

Steg 1. Distribuera den virtuella datorn och klicka på alternativet Kör kommando på Azure Portal.
![Kör kommandot på Azure Portal](./media/create-vm/vm-certification-issues-solutions-3.png)

Steg 2. Välj det första alternativet "RunShellScript" och kör kommandot nedan.

Kommando: "Cat/dev/null > ~/.bash_history && historik-c" ![ bash historik kommando på Azure Portal](./media/create-vm/vm-certification-issues-solutions-4.png)

Steg 3. När du har kört kommandot startar du om den virtuella datorn.

Steg 4. Generalisera den virtuella datorn, ta avbildningen av den virtuella hård disken och stoppa den virtuella datorn.

Steg 5. Re-Submit den generaliserade avbildningen.

## <a name="requesting-exceptions-custom-templates-on-vm-images-for-selective-tests"></a>Begär undantag (anpassade mallar) på VM-avbildningar för selektiva tester

Utgivare kan begära undantag för få tester som utförs under en VM-certifiering. Undantag anges i ytterst sällsynta fall när Publisher tillhandahåller bevis för att stödja begäran. Certifierings teamet förbehåller sig rätten att neka eller godkänna undantag när som helst.

I avsnitten nedan kommer vi att prata om huvud scenarier där undantag begärs och hur du begär det.

### <a name="scenarios-for-exception"></a>Scenarier för undantag

Det finns vanligt vis tre scenarier/fall där utgivare begär undantag.

- **Undantag för en eller flera testfall** – utgivare kontaktar [support](https://aka.ms/marketplacepublishersupport) för att begära undantag för test ärenden.

- **Låsning av virtuella datorer/ingen rot åtkomst** – få utgivare har scenarier där virtuella datorer måste låsas eftersom de har program vara, till exempel brand väggar som är installerade på den virtuella datorn. I det här fallet kan utgivare Ladda ned [verktyget Certified test](https://aka.ms/AzureCertificationTestTool) och skicka rapporten till [supporten](https://aka.ms/marketplacepublishersupport)för partner Center.

- **Anpassade mallar** – vissa utgivare publicerar VM-avbildningar som kräver en anpassad arm-mall för att distribuera de virtuella datorerna. I det här fallet ska utgivare skicka in de anpassade mallarna vid [support](https://aka.ms/marketplacepublishersupport) för partner Center så att samma kan användas av certifierings teamet för verifiering.

### <a name="information-to-provide-for-exception-scenarios"></a>Information som ska tillhandahållas för undantags scenarier

Utgivare ska kontakta Partner Center- [supporten](https://aka.ms/marketplacepublishersupport) för att begära undantag för scenariot ovan med ytterligare följande information:

   1. Utgivar-ID – utgivarens ID på Partner Center-portalen
   2. Erbjudande-ID/namn – erbjudande-ID/namn för vilket undantag begärs
   3. SKU/plan-ID – plan-ID/SKU för det virtuella dator erbjudandet för vilket undantag begärs
   4. Version – den version av VM-erbjudandet för vilket undantag begärs
   5. Undantags typ – test, låst virtuell dator, anpassade mallar
   6. Orsak till begäran – orsak till detta undantag och information om test som ska undantas
   7. Tids linje – datum till vilket detta undantag har begärts
   8. Bifogade filer – bifoga alla dokument med viktig bevisning. För låsta virtuella datorer, koppla test rapporten och för anpassade mallar, anger du den anpassade ARM-mallen som bilaga. Det gick inte att bifoga rapporten för låsta virtuella datorer och en anpassad ARM-mall för anpassade mallar, vilket leder till att begäran nekas

## <a name="address-a-vulnerability-or-exploit-in-a-vm-offer"></a>Åtgärda ett sårbarhets-eller sårbarhets erbjudande i ett virtuellt dator erbjudande

I det här avsnittet beskrivs hur du anger en ny VM-avbildning när en sårbarhet eller sårbarhet identifieras med en av dina VM-avbildningar. Detta gäller endast för virtuella Azure-datorer som har publicerats på Azure Marketplace.

> [!NOTE]
> Du kan inte ta bort den senaste virtuella dator avbildningen från en plan eller stoppa – sälja den sista planen för ett erbjudande.

Gör något av följande:

- Om du har en ny VM-avbildning för att ersätta den sårbara VM-avbildningen, se [Ange en fast VM-avbildning](#provide-a-fixed-vm-image) nedan.
- Om du inte har en ny VM-avbildning för att ersätta den enda avbildningen av den virtuella datorn i en plan, eller om du är klar med planen, [slutar du att sälja planen](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).
- Om du inte planerar att ersätta den enda avbildningen av den virtuella datorn i erbjudandet rekommenderar vi att du [slutar sälja erbjudandet](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="provide-a-fixed-vm-image"></a>Ange en fast VM-avbildning

För att tillhandahålla en fast VM-avbildning som ersätter en VM-avbildning som har sårbarhet eller sårbarhet, gör du följande:

1. Ange en ny VM-avbildning för att lösa säkerhets risker eller sårbarhet.
2. Ta bort den virtuella dator avbildningen med säkerhets sårbarhet eller sårbarhet.
3. Publicera om erbjudandet.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Skapa en ny avbildning av virtuella datorer för att lösa säkerhets risker eller sårbarhet

För att slutföra de här stegen måste du förbereda de tekniska till gångarna för den VM-avbildning som du vill lägga till. Mer information finns i [skapa en virtuell dator med en godkänd bas](azure-vm-create-using-approved-base.md) eller [skapa en virtuell dator med hjälp av en egen avbildning](azure-vm-create-using-own-image.md)och [Generera en SAS-URI för din VM-avbildning](azure-vm-get-sas-uri.md).

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På den vänstra navigerings menyn väljer du **kommersiell Marketplace** -  >  **Översikt**.
3. I kolumnen **erbjudande alias** väljer du erbjudandet.
4. På fliken **plan översikt** i kolumnen **namn** väljer du den plan som du vill lägga till den virtuella datorn i.
5. På fliken **teknisk konfiguration** , under **VM-avbildningar** , väljer du **+ Lägg till avbildning av virtuell dator**.

> [!NOTE]
> Du kan bara lägga till en avbildning av en virtuell dator till en plan i taget. Om du vill lägga till flera VM-avbildningar publicerar du den första Live-avbildningen innan du lägger till nästa avbildning av virtuella datorer.

6. I rutorna som visas anger du en ny disk version och avbildningen av den virtuella datorn.
7. Välj **Spara utkast**.

Fortsätt med nästa avsnitt nedan för att ta bort den virtuella dator avbildningen med säkerhets risken.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>Ta bort den virtuella dator avbildningen med säkerhets sårbarhet eller sårbarhet

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På den vänstra navigerings menyn väljer du **kommersiell Marketplace** -  >  **Översikt**.
3. I kolumnen **erbjudande alias** väljer du erbjudandet.
4. På fliken **plan översikt** i kolumnen **namn** väljer du den plan med den virtuella dator som du vill ta bort.
5. På fliken **teknisk konfiguration** , under **VM-avbildningar** , bredvid den VM-avbildning som du vill ta bort, väljer du **ta bort avbildning av virtuell dator**.
6. I dialog rutan som visas väljer du **Fortsätt**.
7. Välj **Spara utkast**.

Fortsätt med nästa avsnitt nedan för att publicera om erbjudandet.

#### <a name="republish-the-offer"></a>Publicera om erbjudandet

1. Välj **Granska och publicera**.
2. Om du behöver ange någon information till certifierings teamet lägger du till den i rutan **kommentarer för certifiering** .
3. Välj **Publicera**.

Information om hur du slutför publicerings processen finns i [Granska och publicera erbjudanden](review-publish-offer.md).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera egenskaper för VM-erbjudande](azure-vm-create-properties.md)
- [Aktiva marknads förmåner](partner-center-portal/marketplace-rewards.md)
- Kontakta [supporten](https://aka.ms/marketplacepublishersupport)för partner Center om du har frågor eller feedback om förbättringar.
