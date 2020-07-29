---
title: Vanliga frågor och svar om SMB-prestanda för Azure NetApp Files | Microsoft Docs
description: Svar på vanliga frågor om SMB-prestanda för Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 24b3710861f0ee158619ae9103584dcdb181f3d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460457"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Vanliga frågor och svar om SMB-prestanda för Azure NetApp Files

I den här artikeln besvaras vanliga frågor och svar om metod tips för SMB-prestanda för Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Är SMB Multichannel aktiverat i SMB-resurser? 

Ja, SMB Multichannel är aktiverat som standard, en ändrings plats i början av januari 2020. Alla SMB-resurser som dating befintliga SMB-volymer har haft funktionen aktive rad och alla nya volymer kommer också att ha funktionen aktive rad när den skapas. 

Alla SMB-anslutningar som upprättats innan funktionen aktive ras måste återställas för att dra nytta av SMB Multichannel-funktionen. För att återställa kan du koppla från och återansluta SMB-resursen.

## <a name="is-rss-supported"></a>Stöds RSS?

Ja, Azure NetApp Files stöder Receive-sidans skalning (RSS).

När SMB Multichannel är aktiverat upprättar en SMB3-klient flera TCP-anslutningar till den Azure NetApp Files SMB-servern via ett nätverkskort (NIC) som är en enkel RSS-kompatibel. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Vilka Windows-versioner stöder SMB Multichannel?

Windows har stöd för SMB Multichannel sedan Windows 2012 för att möjliggöra bästa prestanda.  Mer information finns i [Distribuera SMB Multichannel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) och [grunderna i SMB Multichannel](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) . 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Stöder min virtuella Azure-dator RSS?

För att se om din virtuella Azure-dators nätverkskort stöder RSS, kör du kommandot `Get-SmbClientNetworkInterface` enligt följande och kontrollerar fältet `RSS Capable` : 

![RSS-stöd för virtuell Azure-dator](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Stöder Azure NetApp Files SMB Direct?

Nej, Azure NetApp Files har inte stöd för SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Vad är fördelen med SMB Multichannel? 

Funktionen SMB Multichannel gör det möjligt för en SMB3-klient att upprätta en pool av anslutningar över ett nätverkskort (NIC) eller flera nätverkskort och att använda dem för att skicka begär Anden för en enda SMB-session. Enligt design, SMB1 och SMB2 kräver att klienten upprättar en anslutning och skickar all SMB-trafik för en specifik session över anslutningen. Denna enda anslutning begränsar det övergripande protokollets prestanda som kan uppnås från en enda klient.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Bör jag konfigurera flera nätverkskort på min klient för SMB?

Nej. SMB-klienten matchar antalet nätverkskort som returneras av SMB-servern.  Varje lagrings volym är åtkomlig från en och endast en lagrings slut punkt.  Det innebär att bara ett nätverkskort kommer att användas för en specifik SMB-relation.  

När utdata från `Get-SmbClientNetworkInterace` nedan visas har den virtuella datorn två nätverks gränssnitt – 15 och 12.  Som du ser nedan under kommandot `Get-SmbMultichannelConnection` , även om det finns två RSS-kompatibla nätverkskort, används bara gränssnitt 12 i samband med SMB-resursen. gränssnitt 15 används inte.

![RSS-kompatibla nätverkskort](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Stöds NIC Teaming i Azure?

NIC Teaming stöds inte i Azure. Även om flera nätverks gränssnitt stöds på virtuella Azure-datorer, representerar de ett logiskt snarare än en fysisk konstruktion. Därför ger de ingen fel tolerans.  Dessutom beräknas bandbredden som är tillgänglig för en virtuell Azure-dator för själva datorn och inte något enskilt nätverks gränssnitt.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Vad är prestandan som för SMB Multichannel?

Följande tester och diagram visar kraften i SMB Multichannel på arbets belastningar med en instans.

### <a name="random-io"></a>Slumpmässig I/O  

När SMB Multichannel är inaktive rad på klienten utfördes rena 8-KiB läsnings-och skriv test med FIO och en arbets uppsättning på 40 GiB.  SMB-resursen kopplades från varje test, med stegvisa anslutnings antal för SMB-klienter per RSS-nätverks gränssnitt inställningar för,,,, `1` `4` `8` `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` . Testerna visar att standardinställningen `4` är tillräcklig för i/O-intensiva arbets belastningar och att den `8` `16` inte har någon inverkan. 

Kommandot `netstat -na | findstr 445` visade att ytterligare anslutningar upprättades med ökningar från `1` till `4` `8` och till `16` .  Fyra processor kärnor utnyttjades fullt ut för SMB under varje test, som bekräftat av perfmon `Per Processor Network Activity Cycles` -statistik (ingår inte i den här artikeln.)

![Slumpmässiga I/O-tester](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Den virtuella Azure-datorn påverkar inte I/O-gränser för lagring I SMB (eller NFS).  Som det visas nedan har D16-instansnamnet en begränsad hastighet på 32 000 för cachelagrad lagrings-IOPS och 25 600 för uncacheed Storage IOPS.  Diagrammet ovan visar dock betydligt mer I/O över SMB.

![Slumpmässig I/O-jämförelse](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sekventiell i/o 

Test som liknar de slumpmässiga I/O-testerna som beskrivs ovan utfördes med 64-KiB sekventiella I/O. Även om antalet ökningar i antal klient anslutningar per RSS-nätverk utöver 4 inte hade någon märkbar påverkan på slumpmässiga I/O, gäller inte samma för sekventiella i/O. I följande diagram visas varje ökning som är associerad med en motsvarande ökning av Läs data flödet. Write-dataflöde förblev flat på grund av begränsningar i nätverks bandbredden som har placerats av Azure för varje instans/storlek. 

![Sekventiella I/O-tester](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure begränsar nätverks frekvensen för varje virtuell dator typ/storlek. Hastighets begränsningen påförs endast utgående trafik. Antalet nätverkskort som finns på en virtuell dator har ingen betydelse för den totala mängden bandbredd som är tillgänglig för datorn.  D16-instans typen har till exempel en infört nätverks gräns på 8000 Mbit/s (1 000 MiB/s).  Som det sekventiella diagrammet ovan visar, påverkar gränsen utgående trafik (skrivningar) men inte Multichannel-läsningar.

![Sekventiell I/O-jämförelse](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>Rekommenderas snabbare nätverk?

För högsta prestanda rekommenderar vi att du konfigurerar [accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) där det är möjligt. Tänk på följande:  

* Azure Portal aktiverar accelererat nätverk som standard för virtuella datorer som stöder den här funktionen.  Andra distributions metoder som Ansible och liknande konfigurations verktyg kanske inte heller.  Det gick inte att aktivera accelererad anslutning hobble datorns prestanda.  
* Om det accelererade nätverket inte är aktiverat på nätverks gränssnittet för en virtuell dator på grund av bristande stöd för en instans typ eller storlek förblir det inaktiverat med större instans typer. Du behöver manuellt ingrepp i dessa fall.

## <a name="are-jumbo-frames-supported"></a>Stöds jumboramar-ramar?

Jumboramar-ramar stöds inte med virtuella Azure-datorer.

## <a name="is-smb-signing-supported"></a>Stöds SMB-signering? 

SMB-protokollet utgör grunden för fil-och skrivar delning och andra nätverks åtgärder, till exempel fjärr administration av Windows. För att förhindra man-i-mitten-attacker som ändrar SMB-paket under överföring, stöder SMB-protokollet digital signering av SMB-paket. 

SMB-signering stöds för alla SMB-protokollfel som stöds av Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Vilken prestanda påverkas av SMB-signering?  

SMB-signeringen har en deleterious-effekt vid SMB-prestanda. Bland andra möjliga orsaker till prestanda försämringen använder den digitala signeringen av varje paket ytterligare processor på klient sidan som perfmon-utdata nedan. I det här fallet visas Core 0 som ansvarig för SMB, inklusive SMB-signering.  En jämförelse med data flödes numren som inte är Multichannel i föregående avsnitt visar att SMB-signering minskar det totala data flödet från 875MiB/s till cirka 250MiB/s. 

![Prestanda påverkan för SMB-signering](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Nästa steg  

- [Vanliga frågor och svar om Azure NetApp Files](azure-netapp-files-faqs.md)
- Se [Azure NetApp Files: hanterade företags fil resurser för SMB-arbetsbelastningar](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) om att använda SMB-filresurser med Azure NetApp Files.
