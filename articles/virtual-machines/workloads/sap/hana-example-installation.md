---
title: Hur du installerar HANA på SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Så här installerar du HANA på SAP HANA på Azure (stor instans).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76a7ce99799b85d81aa6e127ebe1e57e2df3e59a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164771"
---
# <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Exempel på en installation av SAP HANA på stora HANA-instanser

Det här avsnittet visar hur du installerar SAP HANA på en enhet för stora HANA-instansen. Starttillstånd har vi ut:

- Du har angett Microsoft alla data för att distribuera du en SAP HANA stor instans.
- Du har fått SAP HANA för stora-instansen från Microsoft.
- Du har skapat ett virtuellt Azure nätverk som är ansluten till ditt lokala nätverk.
- Du har anslutit ExpressRotue kretsen för stora HANA-instanser till samma Azure VNet.
- Du har installerat en Azure-dator som du använder som en jumpbox för stora HANA-instanser.
- Du har gjort till att du kan ansluta från jumpboxen till stora HANA-instansen-enhet och vice versa.
- Du har markerat om alla nödvändiga paket och korrigeringsprogram som är installerade.
- Du läser SAP notes och dokumentation om HANA-installation på operativsystem du använder och kontrollerat att HANA-versionen av val stöds på Operativsystemets version.

Vad som visas i nästa sekvenser är hämtningen av HANA-paket till jumpboxen virtuell dator, i det här fallet körs på en Windows-OS, kopia av paket till stora HANA-instansen-enhet och sekvens med installationen.

## <a name="download-of-the-sap-hana-installation-bits"></a>Hämta SAP HANA-installation-bitar
Eftersom enheter för stora HANA-instansen inte har direkt anslutning till internet, kan du direkt hämta installationspaket från SAP för HANA stora instans VM. Om du vill lösa saknas direkt Internetanslutning, behöver du jumpbox. Du kan hämta paketen till jumpboxen VM.

För att kunna hämta Installationspaketen HANA, behöver du en SAP-S-användare eller andra användare, där du kan komma åt Marketplace SAP. Gå igenom den här sekvensen av skärmar när du loggar in:

Gå till [SAP Service Marketplace](https://support.sap.com/en/index.html) > Klicka på ladda ned programvara > installationer och uppgradering > efter alfabetisk Index > Under H – SAP HANA-plattformen Edition > SAP HANA-plattformen Edition 2.0 > installationen > ladda ned den följande filer

![Ladda ned HANA-installation](./media/hana-installation/image16_download_hana.PNG)

I fallet demonstration hämtade vi installationspaket för SAP HANA 2.0. På Azure jumpbox VM Expandera du självextraherande arkiv till katalogen som visas nedan.

![Extrahera HANA-installation](./media/hana-installation/image17_extract_hana.PNG)

Kopiera den katalog som skapats av extrahering till stora HANA-instans-enhet till /hana/shared volym i en katalog som du skapade i fallet ovan 51052030, eftersom arkiven har extraherats.

> [!Important]
> Kopiera inte installationspaket till rot- eller LUN-Start eftersom utrymme är begränsad och måste användas av andra processer samt.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installera SAP HANA på stora HANA-instansen-enhet
Du måste logga in som användarrot för att kunna installera SAP HANA. Endast rot har tillräcklig behörighet för att installera SAP HANA.
Det första du behöver göra är att ange behörigheter för den katalog som du kopierade över till/hana/delas. Behörigheterna måste du tycker

```
chmod –R 744 <Installation bits folder>
```

Om du vill installera SAP HANA med den grafiska installationen måste gtk2 paketet vara installerad på stora HANA-instanser. Kontrollera om programmet har installerats med kommandot

```
rpm –qa | grep gtk2
```

Vi har bevisat SAP HANA-installation med det grafiska användargränssnittet i ytterligare steg. Gå till installationskatalogen och navigera till katalogen sub HDB_LCM_LINUX_X86_64 som nästa steg. Start

```
./hdblcmgui 
```
utanför den katalogen. Nu komma vägleds du genom en sekvens av skärmar där du måste ange data för installationen. I det fallet visas installerar vi databasservern för SAP HANA och SAP HANA-klientkomponenter. Vårt val är därför SAP HANA-databas som visas nedan

![Välj HANA i installationen](./media/hana-installation/image18_hana_selection.PNG)

På nästa skärm väljer du alternativet Installera nya System

![Välj ny HANA-installation](./media/hana-installation/image19_select_new.PNG)

När det här steget måste du välja mellan flera ytterligare komponenter som kan installeras dessutom till SAP HANA-databasservern.

![Välj ytterligare HANA-komponenter](./media/hana-installation/image20_select_components.PNG)

I den här dokumentationen valde vi SAP HANA-klienten och SAP HANA-Studio. Vi också har installerats en upp-instans. därför på nästa skärm måste du välja ”enda värd systemet” 

![Välj skala upp installationen](./media/hana-installation/image21_single_host.PNG)

På nästa skärm måste du ange vissa data

![Ange SAP HANA-SID](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Som HANA System-ID (SID) som du måste du ange samma SID, som du angav Microsoft när du har beställt distributionen stora HANA-instansen. Välja ett annat SID kan installationen misslyckas på grund av problem med åtkomst till behörigheten i olika volymer

Som installationen delade katalogen som du använder/hana/directory. I nästa steg måste du ange platserna för HANA datafiler och HANA-loggfiler


![Ange HANA loggplatsen](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Du bör definiera som data och loggfiler volymerna som redan medföljde monteringspunkter som innehåller SID som du valde i skärmen val innan den här skärmen. På skärmen innan du går du tillbaka och anpassa SID till det värde som du har på monteringspunkterna om SID matchningsfel med det namn du angav i.

Granska värdnamnet och så småningom rätta till det i nästa steg. 

![Granska värdnamn](./media/hana-installation/image24_review_host_name.PNG)

I nästa steg måste du också hämta data som du gav till Microsoft när du har beställt distributionen stora HANA-instansen. 

![Ange systemanvändare UID och GID](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Du måste ange samma System användar-ID och ID användargrupp som du angav Microsoft som din beställning enhet distributionen. Om du inte ge samma ID: N, misslyckas installationen av SAP HANA på stora HANA-instansen-enhet.

I följande två skärmar, som vi inte visas i denna dokumentation måste du ange lösenordet för systemanvändare av SAP HANA-databas och lösenordet för användaren sapadm som används för SAP-Värdagenten som installeras som en del av SAP HANA-datab ase-instans.

När du har definierat lösenordet visas en bekräftelseskärm. Kontrollera alla data i listan och fortsätta med installationen. Du når en förlopp som dokumenterar under installationen, som den nedan

![Kontrollera installationens förlopp](./media/hana-installation/image27_show_progress.PNG)

När installationen är klar bör du en bild som det nedanstående

![Installationen är klar](./media/hana-installation/image28_install_finished.PNG)

SAP HANA-instans ska nu vara igång och körs och är tillgänglig för användning. Du ska kunna ansluta till den från SAP HANA-Studio. Kontrollera också att du söka efter de senaste uppdateringarna av SAP HANA och installerar dessa korrigeringar.


**Nästa steg**

- Se [SAP HANA stora instanser hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md).

