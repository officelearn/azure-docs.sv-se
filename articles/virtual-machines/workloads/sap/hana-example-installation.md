---
title: Hur du installerar HANA på SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Så här installerar du HANA på SAP HANA på Azure (stora instanser).
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
ms.openlocfilehash: f4629894933507bda7359fb034c4079d38100029
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098495"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Installera HANA på SAP HANA på Azure (stora instanser)

Om du vill installera HANA på SAP HANA på Azure (stora instanser), måste du först göra följande:
- Du kan ange Microsoft med alla data för att distribuera åt dig på en SAP HANA stor instans.
- Du får den SAP HANA stora-instansen från Microsoft.
- Du skapar ett Azure-nätverk som är ansluten till ditt lokala nätverk.
- Du kan ansluta ExpressRoute-kretsen för stora HANA-instanser till samma Azure-nätverk.
- Installation av en Azure-dator som du använder som en jumpbox för stora HANA-instanser.
- Du se till att du kan ansluta från jumpboxen till din enhet för stora HANA-instansen, och vice versa.
- Du kan kontrollera om alla nödvändiga paket och korrigeringsprogram som är installerade.
- Du kan läsa SAP notes och dokumentation om HANA-installation på vilket operativsystem du använder. Se till att HANA-versionen av val stöds på versionen av operativsystemet.

Nästa avsnitt visar ett exempel för att hämta Installationspaketen HANA till jump box virtuell dator. I det här fallet är operativsystemet Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Ladda ned bitarna för SAP HANA-installation
Enheter för stora HANA-instansen inte är direkt anslutna till internet. Du kan inte ladda ned installationspaket direkt från SAP till den virtuella datorn för stora HANA-instansen. I stället kan du hämta paketen till jump box virtuell dator.

Du behöver en SAP-S-användare eller andra användare, där du kan komma åt Marketplace SAP.

1. Logga in och gå till [SAP Service Marketplace](https://support.sap.com/en/index.html). Välj **ladda ned programvara** > **installationer och uppgradering** > **efter alfabetisk Index**. Välj sedan **H Under – SAP HANA-plattformen Edition** > **SAP HANA-plattformen version 2.0** > **Installation**. Ladda ned filerna som visas i följande skärmbild.

   ![Skärmbild av filer att ladda ned](./media/hana-installation/image16_download_hana.PNG)

2. I det här exemplet hämtade vi installationspaket för SAP HANA 2.0. Sätta igång direkt box virtuell dator på Azure, expandera självextraherande arkiv till katalogen som visas nedan.

   ![Skärmbild av självextraherande Arkiv](./media/hana-installation/image17_extract_hana.PNG)

3. Kopiera den katalog som skapats av extraheringen (i det här fallet 51052030) som har extraherats arkivet. Kopiera katalogen från volymen /hana/shared stora HANA-instansen till en katalog som du skapade.

   > [!Important]
   > Kopiera inte installationspaket till rot- eller boot LUN, eftersom utrymme är begränsad och måste användas av andra processer samt.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installera SAP HANA på stora HANA-instansen-enhet
Logga in som användarrot för att kunna installera SAP HANA. Endast rot har tillräcklig behörighet för att installera SAP HANA. Ange behörigheter för den katalog som du kopierade över till /hana/shared.

```
chmod –R 744 <Installation bits folder>
```

Om du vill installera SAP HANA med hjälp av installationsprogrammet för grafiska gränssnittet måste gtk2 paketet vara installerad på stora HANA-instanser. Om du vill kontrollera om programmet har installerats, kör du följande kommando:

```
rpm –qa | grep gtk2
```

(I senare steg visar vi SAP HANA-installation med det grafiska användargränssnittet.)

Gå till installationskatalogen och navigera till underkatalogen HDB_LCM_LINUX_X86_64. 

Starta från katalogen:

```
./hdblcmgui 
```
AT detta pekar visas förloppet via en sekvens av skärmar där du kan ange data för installationen. I det här exemplet installerar vi databasservern för SAP HANA och SAP HANA-klientkomponenter. Vårt val är därför **SAP HANA-databas**.

![Skärmbild av SAP HANA livscykelhantering fönster, där du har valt för SAP HANA-databas](./media/hana-installation/image18_hana_selection.PNG)

På nästa skärm väljer **installera nya systemet**.

![Skärmbild av SAP HANA livscykelhantering fönster, där du installerar nya systemet valt](./media/hana-installation/image19_select_new.PNG)

Välj bland flera ytterligare komponenter som du kan installera.

![Skärmbild av SAP HANA Livscykelhantering för skärmen med lista över ytterligare komponenter](./media/hana-installation/image20_select_components.PNG)

Här kan välja vi SAP HANA-klienten och SAP HANA-Studio. Vi kan även installera en upp-instans. Välj sedan **enda värdsystem**. 

![Skärmbild av SAP HANA Livscykelhantering för skärmen med enkel värdsystem som valts](./media/hana-installation/image21_single_host.PNG)

Ange därefter vissa data.

![Skärmbild av SAP HANA Livscykelhantering för skärmen med systemfält egenskaper för att definiera](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Du måste ange samma SID som du angav Microsoft när du har beställt distributionen stora HANA-instansen som HANA System-ID (SID). Välja ett annat SID gör att installationen misslyckas på grund av problem med åtkomst till behörigheten i olika volymer.

Använda /hana/shared-katalogen för installationssökvägen. I nästa steg anger du platserna för HANA datafiler och loggfiler för HANA.


![Skärmbild av SAP HANA livscykelhantering fönster, där data och loggfiler fält](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> SID som du angav när du har definierat Systemegenskaper (två skärmar sedan) måste matcha monteringspunkterna SID. Om det finns ett matchningsfel, gå tillbaka och justera SID till det värde som du har på monteringspunkterna.

Granska värdnamnet och så småningom rätta till det i nästa steg. 

![Skärmbild av SAP HANA Livscykelhantering för skärmen med värdnamn](./media/hana-installation/image24_review_host_name.PNG)

I nästa steg måste du också hämta data som du gav till Microsoft när du har beställt distributionen stora HANA-instansen. 

![Skärmbild av SAP HANA livscykelhantering, med systemfält administratör att definiera](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Ange samma **System administratör användar-ID** och **ID användargrupp** som du tillhandahåller Microsoft, som du beställer enhet-distributionen. Annars misslyckas installationen av SAP HANA på stora HANA-instansen-enhet.

Följande två skärmar visas inte här. De kan du ange lösenordet för systemanvändare av SAP HANA-databas och lösenordet för användaren sapadm. Det senare används för SAP-Värdagenten som installeras som en del av SAP HANA-databasinstansen.

När du har definierat lösenordet visas en bekräftelseskärm. Kontrollera alla data i listan och fortsätta med installationen. Du når en förlopp som dokumenterar under installationen, som det här:

![Skärmbild av SAP HANA Livscykelhantering för skärmen med förloppsindikator för installationen](./media/hana-installation/image27_show_progress.PNG)

När installationen är klar bör du se en skärm som liknar denna:

![Skärmbild av SAP HANA livscykelhantering skärmen, som anger installationen är klar](./media/hana-installation/image28_install_finished.PNG)

SAP HANA-instans ska nu vara igång och körs och är tillgänglig för användning. Du ska kunna ansluta till den från SAP HANA-Studio. Kontrollera också att du söka efter och installerar de senaste uppdateringarna.


## <a name="next-steps"></a>Nästa steg

- [SAP HANA stora instanser hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md)

