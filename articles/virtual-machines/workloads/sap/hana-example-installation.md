---
title: Så här installerar du HANA på SAP HANA på Azure (Stora instanser) | Microsoft-dokument
description: Så här installerar du HANA på SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 023f32fce01ffbd974b182fa89fd604e62332936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617211"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Installera HANA på SAP HANA på Azure (stora instanser)

Om du vill installera HANA på SAP HANA på Azure (Stora instanser) måste du först göra följande:
- Du förser Microsoft med alla data som ska distribueras åt dig på en STOR SAP HANA-instans.
- Du får den stora SAP HANA-instansen från Microsoft.
- Du skapar ett virtuellt Azure-nätverk som är anslutet till ditt lokala nätverk.
- Du ansluter ExpressRoute-kretsen för STORA HANA-instanser till samma virtuella Azure-nätverk.
- Du installerar en virtuell Azure-dator som du använder som en hoppruta för STORA HANA-instanser.
- Du ser till att du kan ansluta från hopprutan till din HANA Large Instance-enhet och vice versa.
- Du kontrollerar om alla nödvändiga paket och patchar är installerade.
- Du läser SAP-anteckningar och dokumentation om HANA-installation på operativsystemet du använder. Kontrollera att hana-versionen stöds på operativsystemets utgåva.

I nästa avsnitt visas ett exempel på hur du hämtar HANA-installationspaketen till den virtuella startrutan. I det här fallet är operativsystemet Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Ladda ned SAP HANA-installationsbitarna
Hana-enheterna för stora instanser är inte direkt anslutna till internet. Du kan inte direkt hämta installationspaketen från SAP till den virtuella hana-instansen. I stället hämtar du paketen till den virtuella startrutan.

Du behöver en SAP S-användare eller annan användare, vilket gör att du kan komma åt SAP Marketplace.

1. Logga in och gå till [SAP Service Marketplace](https://support.sap.com/en/index.html). Välj Hämta > **programvaruinstallationer och uppgradera** > **efter alfabetiskt index**. **Download Software** Välj sedan **Under H – SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **Installation**. Ladda ner filerna som visas i följande skärmdump.

   ![Skärmbild av filerna som ska hämtas](./media/hana-installation/image16_download_hana.PNG)

2. I det här exemplet hämtade vi SAP HANA 2.0-installationspaket. På den virtuella azure-hopprutan expanderar du självextraherande arkiven till katalogen enligt nedan.

   ![Skärmbild av självextraherande arkiv](./media/hana-installation/image17_extract_hana.PNG)

3. När arkiven extraheras, kopiera den katalog som skapats av extrahering (i detta fall 51052030). Kopiera katalogen från HANA Large Instance-enheten /hana/delad volym till en katalog som du har skapat.

   > [!Important]
   > Kopiera inte installationspaketen till roten eller starta LUN, eftersom utrymmet är begränsat och måste användas av andra processer också.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installera SAP HANA på HANA Large Instance-enheten
För att installera SAP HANA loggar du in som användarrot. Endast rot har tillräckligt med behörigheter för att installera SAP HANA. Ange behörigheter för katalogen som du kopierade till /hana/shared.

```
chmod –R 744 <Installation bits folder>
```

Om du vill installera SAP HANA med hjälp av den grafiska användargränssnittskonfigurationen måste gtk2-paketet installeras på STORA HANA-instanser. Kontrollera om det är installerat genom att köra följande kommando:

```
rpm –qa | grep gtk2
```

(I senare steg visar vi SAP HANA-installationen med det grafiska användargränssnittet.)

Gå in i installationskatalogen och navigera till underkatalogen HDB_LCM_LINUX_X86_64. 

Utanför den katalogen börjar du:

```
./hdblcmgui 
```
Nu går du igenom en sekvens av skärmar där du tillhandahåller data för installationen. I det här exemplet installerar vi SAP HANA-databasservern och SAP HANA-klientkomponenterna. Därför är vårt urval **SAP HANA Database**.

![Skärmbild av skärmen SAP HANA Lifecycle Management, med SAP HANA-databas markerad](./media/hana-installation/image18_hana_selection.PNG)

På nästa skärm väljer du **Installera nytt system**.

![Skärmbild av skärmen SAP HANA Lifecycle Management, med Installera nytt system valt](./media/hana-installation/image19_select_new.PNG)

Välj sedan bland flera ytterligare komponenter som du kan installera.

![Skärmbild av skärmen SAP HANA Lifecycle Management, med en lista över ytterligare komponenter](./media/hana-installation/image20_select_components.PNG)

Här väljer vi SAP HANA-klienten och SAP HANA Studio. Vi installerar också en uppskalningsinstans. Välj sedan **Single-Host System**. 

![Skärmbild av skärmen SAP HANA Lifecycle Management, med single host system valt](./media/hana-installation/image21_single_host.PNG)

Ange sedan vissa data.

![Skärmbild av skärmen SAP HANA Lifecycle Management, med systemegenskaper för att definiera](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Som SID (HANA System ID) måste du ange samma SID som du angav Microsoft när du beställde distributionen av HANA-stora instanser. Om du väljer ett annat SID misslyckas installationen på grund av åtkomstbehörighetsproblem på de olika volymerna.

Använd katalogen /hana/shared för installationssökvägen. I nästa steg anger du platserna för HANA-datafilerna och HANA-loggfilerna.


![Skärmbild av skärmen SAP HANA Lifecycle Management, med data- och loggområdesfält](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Det SID som du angav när du definierade systemegenskaper (för två skärmar sedan) ska matcha SID för monteringspunkterna. Om det finns en obalans går du tillbaka och justerar SID till det värde du har på monteringspunkterna.

I nästa steg granskar du värdnamnet och korrigerar det så småningom. 

![Skärmbild av skärmen SAP HANA Lifecycle Management med värdnamn](./media/hana-installation/image24_review_host_name.PNG)

I nästa steg måste du också hämta data som du gav till Microsoft när du beställde distributionen av stora HANA-instanser. 

![Skärmbild av SAP HANA Lifecycle Management, med systemadministratörsfält för att definiera](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Ange samma **användar-ID** för systemadministratör och ID för **användargruppen** som du angav för Microsoft, när du beställer enhetsdistributionen. Annars misslyckas installationen av SAP HANA på HANA Large Instance-enheten.

De följande två skärmarna visas inte här. De gör att du kan ange lösenordet för SYSTEM-användaren av SAP HANA-databasen och lösenordet för sapadm-användaren. Den senare används för SAP-värdagenten som installeras som en del av SAP HANA-databasinstansen.

När du har definierat lösenordet visas en bekräftelseskärm. kontrollera alla data som anges och fortsätt med installationen. Du når en förloppsskärm som dokumenterar installationsframsteget, som den här:

![Skärmbild av skärmen SAP HANA Lifecycle Management, med indikatorer för installationsstatus](./media/hana-installation/image27_show_progress.PNG)

När installationen är klar bör du se en skärm som den här:

![Skärmbild av skärmen SAP HANA Lifecycle Management, vilket indikerar att installationen är klar](./media/hana-installation/image28_install_finished.PNG)

SAP HANA-instansen ska nu vara igång och klar för användning. Du bör kunna ansluta till den från SAP HANA Studio. Se också till att du söker efter och installerar de senaste uppdateringarna.


## <a name="next-steps"></a>Nästa steg

- [SAP HANA Stora instanser med hög tillgänglighet och haveriberedskap på Azure](hana-overview-high-availability-disaster-recovery.md)

