---
title: Vanliga frågor om SMB-prestanda för Azure NetApp-filer| Microsoft-dokument
description: Svar vanliga frågor om SMB-prestanda för Azure NetApp-filer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460457"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Vanliga frågor om SMB-prestanda för Azure NetApp-filer

Den här artikeln besvarar vanliga frågor och svar om metodtips för SMB-prestanda för Azure NetApp-filer.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Är SMB Multichannel aktiverat i SMB-aktier? 

Ja, SMB Multichannel är aktiverat som standard, en ändring som infördes i början av januari 2020. Alla SMB-aktier som redan har dateras befintliga SMB-volymer har haft funktionen aktiverad, och alla nyskapade volymer kommer också att ha funktionen aktiverad vid tidpunkten för skapandet. 

Alla SMB-anslutningar som upprättats innan funktionsaktiveringen måste återställas för att dra nytta av SMB Multichannel-funktionen. Om du vill återställa kan du koppla från och återansluta SMB-resursen.

## <a name="is-rss-supported"></a>Stöds RSS?

Ja, Azure NetApp Files stöder RSS (receive-side-scaling).

Med SMB Multichannel aktiverat upprättar en SMB3-klient flera TCP-anslutningar till Azure NetApp Files SMB-servern över ett nätverkskort (NIC) som är en enda RSS-kompatibel. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Vilka Windows-versioner har stöd för SMB Multichannel?

Windows har stött SMB Multichannel sedan Windows 2012 för att möjliggöra bästa prestanda.  Mer information finns [i Distribuera SMB Flerkanal och](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) Grunderna i [SMB Flerkanalskanal.](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Stöder min virtuella Azure-dator RSS?

Om du vill se om dina Azure-nätverkskort för virtuella datorer stöder RSS kör du kommandot `Get-SmbClientNetworkInterface` på följande sätt och kontrollerar fältet: `RSS Capable` 

![RSS-stöd för virtuell Azure-dator](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Stöder Azure NetApp Files SMB Direct?

Nej, Azure NetApp-filer stöder inte SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Vad är fördelen med SMB Multichannel? 

SMB Multichannel-funktionen gör det möjligt för en SMB3-klient att upprätta en pool av anslutningar över ett enda nätverkskort (NIC) eller flera nätverkskort och att använda dem för att skicka begäranden om en enda SMB-session. Däremot, avsiktligt, SMB1 och SMB2 kräver klienten att upprätta en anslutning och skicka alla SMB trafik för en viss session över den anslutningen. Den här enskilda anslutningen begränsar den övergripande protokollprestanda som kan uppnås från en enskild klient.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Ska jag konfigurera flera nätverkskort på min klient för SMB?

Nej. SMB-klienten matchar antalet nätverkskort som returneras av SMB-servern.  Varje lagringsvolym är tillgänglig från en och endast en lagringsslutpunkt.  Det innebär att endast ett nätverkskort kommer att användas för en viss SMB-relation.  

Som utdata `Get-SmbClientNetworkInterace` från nedan visar har den virtuella datorn två nätverksgränssnitt - 15 och 12.  Som visas nedan `Get-SmbMultichannelConnection`under kommandot , även om det finns två RSS-kompatibla NICS, endast gränssnitt 12 används i samband med SMB-resursen; gränssnitt 15 inte används.

![RSS-kompatibel NICS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Stöds NIC Teaming i Azure?

NIC Teaming stöds inte i Azure. Även om flera nätverksgränssnitt stöds på virtuella Azure-datorer, representerar de en logisk snarare än en fysisk konstruktion. Som sådan ger de ingen feltolerans.  Dessutom beräknas den tillgängliga bandbredden för en virtuell Azure-dator för själva datorn och inte något enskilt nätverksgränssnitt.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Hur är prestandan för SMB Multichannel?

Följande tester och diagram visar kraften i SMB Multichannel på arbetsbelastningar med en instans.

### <a name="random-io"></a>Slumpmässig i/o  

Med SMB Multichannel inaktiveras på klienten, ren 8-KiB läsa och skriva tester utfördes med FIO och en 40-GiB arbetsset.  SMB-resursen lossnade mellan varje test, med steg av SMB-klientanslutningsantalet per RSS-nätverksgränssnittsinställningar `1`för`4`, ,`8`,`16`, `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`. Testerna visar att standardinställningen för `4` är tillräcklig för I/O-intensiva arbetsbelastningar. till `8` och `16` hade ingen effekt. 

`netstat -na | findstr 445` Kommandot visade att ytterligare anslutningar upprättades `1` med `4` `8` steg `16`från till och till .  Fyra CPU-kärnor användes fullt ut för SMB under varje test, vilket bekräftas av perfmon `Per Processor Network Activity Cycles` statistik (ingår inte i denna artikel.)

![Slumpmässiga I/O-tester](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure-virtuella datorn påverkar inte I/O-begränsningar för SMB-lagring (eller NFS).  Som visas nedan har D16-instanstypen en begränsad hastighet på 32 000 för cachelagrad lagring iOPS och 25 600 för IOPS för oansluten lagring.  Diagrammet ovan visar dock betydligt mer I/O över SMB.

![Jämförelse av slumpmässig i/o](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sekventiell IO 

Tester som liknar de slumpmässiga I/O-tester som beskrivs ovan utfördes med 64-KiB sekventiell I/O. Även om ökningen av antalet klientanslutningar per RSS-nätverksgränssnitt efter 4 hade ingen märkbar effekt på slumpmässig I/O, gäller inte samma sak för sekventiell I/O. Som följande diagram visar är varje ökning associerad med en motsvarande ökning av läsdataflödet. Skrivdataflödet förblev platt på grund av begränsningar av nätverksbandbredden som Azure har placerat för varje instanstyp/storlek. 

![Sekventiella I/O-tester](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure placerar nätverkshastighetsgränser för varje typ/storlek för virtuella datorer. Hastighetsgränsen gäller endast för utgående trafik. Antalet nätverkskort som finns på en virtuell dator har ingen betydelse för den totala mängden bandbredd som är tillgänglig för datorn.  D16-instanstypen har till exempel en påtvingad nätverksgräns på 8 000 Mbit/s (1 000 MiB/s).  Som det sekventiella diagrammet ovan visar påverkar gränsen den utgående trafiken (skriver) men inte flera kanaler.

![Jämförelse med sekventiell I/O](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>Rekommenderas Accelerated Networking?

För maximal prestanda rekommenderar vi att du konfigurerar [Accelererat nätverk där](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) det är möjligt. Tänk på följande:  

* Azure-portalen möjliggör accelererat nätverk som standard för virtuella datorer som stöder den här funktionen.  Andra distributionsmetoder som Ansible och liknande konfigurationsverktyg kanske inte.  Om du inte aktiverar Accelerated Networking kan det vara möjligt att förstöra en dators prestanda.  
* Om Accelererat nätverk inte är aktiverat i nätverksgränssnittet på en virtuell dator på grund av dess brist på stöd för en instanstyp eller storlek, förblir det inaktiverat med större instanstyper. Du behöver manuell intervention i dessa fall.

## <a name="are-jumbo-frames-supported"></a>Stöds jumboramar?

Jumboramar stöds inte med virtuella Azure-datorer.

## <a name="is-smb-signing-supported"></a>Stöds SMB-signering? 

SMB-protokollet utgör grunden för fil- och utskriftsdelning och andra nätverksåtgärder, till exempel fjärr-Windows-administration. För att förhindra man-i-mitten-attacker som ändrar SMB-paket under överföring, stöder SMB-protokollet digital signering av SMB-paket. 

SMB-signering stöds för alla SMB-protokollversioner som stöds av Azure NetApp-filer. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Vad är prestandapåverkan av SMB-signering?  

SMB-signering har en skadlig effekt på SMB-prestanda. Bland andra potentiella orsaker till prestandaförsämring, den digitala signering av varje paket förbrukar ytterligare klientsidan CPU som perfmon utdata nedan visar. I det här fallet verkar Core 0 ansvarig för SMB, inklusive SMB-signering.  En jämförelse med de icke-flerkanaliga sekventiella läsdataflödesnumren i föregående avsnitt visar att SMB-signering minskar det totala dataflödet från 875MiB/s till cirka 250MiB/s. 

![Prestandapåverkan för SMB-signering](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Nästa steg  

- [Vanliga frågor om Azure NetApp-filer](azure-netapp-files-faqs.md)
- Se [Azure NetApp-filer: Hanterade Enterprise File Shares för SMB-arbetsbelastningar](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) om hur du använder SMB-filresurser med Azure NetApp-filer.
