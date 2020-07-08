---
title: Etablera StorSimple virtuell matris i Hyper-V | Microsoft Docs
description: Den här andra själv studie kursen om distribution av virtuella StorSimple-matriser innebär att en virtuell matris ingår i Hyper-V.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d3f4f4ab6cc1c928761fce740d39f3f73426e62
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79267539"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Distribuera StorSimple virtuell matris – etablera i Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

I den här självstudien beskrivs hur du etablerar en virtuell StorSimple-matris på ett värd system som kör Hyper-V på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2. Den här artikeln gäller distributionen av virtuella StorSimple-matriser i Azure Portal och Microsoft Azure Government molnet.

Du behöver administratörs behörighet för att etablera och konfigurera en virtuell matris. Etableringen och den inledande installationen kan ta ungefär 10 minuter att slutföra.

## <a name="provisioning-prerequisites"></a>Krav för etablering
Här hittar du förutsättningarna för att etablera en virtuell matris på ett värd system som kör Hyper-V på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>För StorSimple Device Manager-tjänsten
Innan du börjar ska du kontrollera att:

* Du har slutfört alla steg i [förbereda portalen för StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Du har laddat ned den virtuella mat ris avbildningen för Hyper-V från Azure Portal. Mer information finns i **steg 3: Ladda ned den virtuella mat ris avbildningen** av [förbereda portalen för StorSimple Virtual Array guide](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > Den program vara som körs på den virtuella StorSimple-matrisen kan bara användas med StorSimple Enhetshanteraren-tjänsten.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>För den virtuella StorSimple-matrisen
Innan du distribuerar en virtuell matris måste du kontrol lera att:

* Du har åtkomst till ett värd system som kör Hyper-V på Windows Server 2008 R2 eller senare och som kan användas för att etablera en enhet.
* Värd systemet kan dedikera följande resurser för att etablera den virtuella matrisen:

  * Minst 4 kärnor.
  * Minst 8 GB RAM. Om du planerar att konfigurera den virtuella matrisen som fil Server stöder 8 GB färre än 2 000 000 filer. Du behöver 16 GB RAM-minne för att kunna hantera 2-4 miljoner filer.
  * Ett nätverksgränssnitt.
  * En virtuell disk på 500 GB för data.

### <a name="for-the-network-in-the-datacenter"></a>För nätverket i datacentret
Innan du börjar kan du läsa nätverks kraven för att distribuera en virtuell StorSimple-matris och konfigurera Data Center nätverket på lämpligt sätt. Mer information finns i [StorSimple-krav för virtuell nätverks mat ris](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Steg för steg-etablering
Om du vill etablera och ansluta till en virtuell matris måste du utföra följande steg:

1. Se till att värd systemet har tillräckligt med resurser för att uppfylla minimi kraven för virtuella matriser.
2. Etablera en virtuell matris i hypervisor-programmets.
3. Starta den virtuella matrisen och hämta IP-adressen.

Vart och ett av dessa steg beskrivs i följande avsnitt.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Steg 1: kontrol lera att värd systemet uppfyller minimi kraven för virtuella matriser
Om du vill skapa en virtuell matris behöver du:

* Hyper-V-rollen som är installerad på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2 SP1.
* Microsoft Hyper-V Manager på en Microsoft Windows-klient som är ansluten till värden.

Kontrol lera att den underliggande maskin varan (värd systemet) som du skapar den virtuella matrisen på kan dedikera följande resurser till den virtuella matrisen:

* Minst 4 kärnor.
* Minst 8 GB RAM. Om du planerar att konfigurera den virtuella matrisen som fil Server stöder 8 GB färre än 2 000 000 filer. Du behöver 16 GB RAM-minne för att kunna hantera 2-4 miljoner filer.
* Ett nätverksgränssnitt.
* En virtuell disk på 500 GB för system data.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Steg 2: etablera en virtuell matris i hypervisor
Utför följande steg för att etablera en enhet i ditt hypervisor-program.

#### <a name="to-provision-a-virtual-array"></a>Etablera en virtuell matris
1. På Windows Server-värden kopierar du den virtuella mat ris avbildningen till en lokal enhet. Du laddade ned avbildningen (VHD eller VHDX) via Azure Portal. Anteckna den plats dit du har kopierat avbildningen eftersom du använder den här avbildningen senare i proceduren.
2. Öppna **Serverhanteraren**. I det övre högra hörnet klickar du på **Verktyg** och väljer **Hyper-V Manager**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Om du kör Windows Server 2008 R2 öppnar du Hyper-V Manager. I Serverhanteraren klickar du på **roller > Hyper-v > Hyper-v Manager**.
3. I **Hyper-V Manager** går du till fönsterrutan för omfång, högerklickar på din systemnod för att öppna snabbmenyn och klickar sedan på **Ny** > **Virtuell dator**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. På sidan **Innan du börjar** i guiden för ny virtuell dator klickar du på **Nästa**.
5. På sidan **Ange namn och plats** anger du ett **namn** för den virtuella matrisen. Klicka på **Nästa**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. På sidan **Ange generation** väljer du enhets avbildnings typ och klickar sedan på **Nästa**. Den här sidan visas inte om du använder Windows Server 2008 R2.

   * Välj **generation 2** om du har hämtat en. vhdx-avbildning för Windows Server 2012 eller senare.
   * Välj **generation 1** om du har hämtat en. VHD-avbildning för Windows Server 2008 R2 eller senare.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. På sidan **Tilldela minne** anger du ett **Startminne** på minst **8 192 MB**. Aktivera inte dynamiskt minne. Klicka sedan på **Nästa**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. På sidan **Konfigurera nätverk** anger du den virtuella växel som är ansluten till Internet och klickar sedan på **Nästa**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. På sidan **Anslut virtuell hård disk** väljer du **Använd en befintlig virtuell hård disk**, anger platsen för den virtuella mat ris avbildningen (. vhdx eller. VHD) och klickar sedan på **Nästa**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Granska **sammanfattningen** och klicka sedan på **Slutför** för att skapa den virtuella datorn.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. För att uppfylla minimikraven behöver du 4 kärnor. Om du vill lägga till 4 virtuella processorer väljer du ditt värdsystem i **Hyper-V Manager**-fönstret. I den högra fönsterrutan går du till listan över **virtuella datorer** och letar upp den virtuella dator som du nyss skapade. Markera och högerklicka på datornamnet och välj **Inställningar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. På sidan **Inställningar** går du till den vänstra fönsterruta och klickar på **Processor**. I den högra fönsterrutan ställer du in **antal virtuella processorer** på 4 (eller fler). Klicka på **Använd**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. För att uppfylla minimi kraven måste du också lägga till en virtuell data disk på 500 GB. På sidan **Inställningar**:

    1. I den vänstra fönsterrutan väljer du **SCSI Controller**.
    2. I den högra fönsterrutan väljer du **Hårddisk** och klickar på **Lägg till**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. På sidan **Hårddisk** väljer du alternativet **Virtuell hårddisk** och klickar på **Ny**. **Guiden för ny virtuell hårddisk** startar.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. På sidan **Innan du börjar** i guiden för ny virtuell hårddisk klickar du på **Nästa**.
16. På **sidan för att välja diskformat** godkänner du standardalternativet för **VHDX**-format. Klicka på **Nästa**. Den här skärmen visas inte om du kör Windows Server 2008 R2.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. På **sidan för att välja disktyp** ställer du in typen av virtuell hårddisk till **Dynamiskt expanderande** (rekommenderas). En disk med **Fast storlek** skulle också fungera, men då kan du behöva vänta länge. Vi rekommenderar att du inte använder alternativet **Differentierande**. Klicka på **Nästa**. I Windows Server 2012 R2 och Windows Server 2012 är **dynamiskt expanderande** standard alternativet i windows Server 2008 R2, standard **storleken är fast**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. På sidan **Ange namn och plats** anger du ett **Namn** och en **Plats** (du kan bläddra till en) för datadisken. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. På sidan **Konfigurera disk** väljer du alternativet **skapa en ny tom virtuell hård disk** och anger storleken som **500 GB** (eller mer). Även om 500 GB är minimi kravet, kan du alltid etablera en större disk. Observera att det inte går att utöka eller krympa disken när den har allokerats. Mer information om storleken på disken som ska etableras finns i avsnittet storlek i [dokumentet metod tips](storsimple-ova-best-practices.md). Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. På sidan **Sammanfattning** läser du informationen om din virtuella datadisk, och om allt stämmer klickar du på **Slutför** för att skapa disken. Guiden stängs och en virtuell hårddisk läggs till i datorn.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Gå tillbaka till sidan **Inställningar**. Klicka på **OK** för att stänga sidan **Inställningar** och gå tillbaka till Hyper-V Manager-fönstret.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Steg 3: starta den virtuella matrisen och hämta IP-adressen
Utför följande steg för att starta den virtuella matrisen och ansluta till den.

#### <a name="to-start-the-virtual-array"></a>Starta den virtuella matrisen
1. Starta den virtuella matrisen.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. När enheten körs väljer du enheten, högerklickar och väljer **Anslut**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Du kan behöva vänta 5-10 minuter tills enheten är klar. Ett statusmeddelande visas på konsolen som visar förloppet. När enheten är klar går du till **Åtgärd**. Tryck `Ctrl + Alt + Delete` för att logga in på den virtuella matrisen. Standard användaren är *StorSimpleAdmin* och standard lösen ordet är *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Av säkerhetsskäl upphör enhetens administratörslösenord vid första inloggningen. Du uppmanas att ändra lösenordet.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Ange ett lösenord som innehåller minst 8 tecken. Lösenordet måste uppfylla minst 3 av följande 4 krav: versaler, gemener, siffror och specialtecken. Ange lösenordet igen för att bekräfta det. Du meddelas om att lösenordet har ändrats.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. När lösen ordet har ändrats kan den virtuella matrisen starta om. Vänta tills enheten har startat.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Enhetens Windows PowerShell-konsolen visas tillsammans med en förloppsindikator.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Steg 6–8 gäller bara när du startar i en icke-DHCP-miljö. Om du använder en DHCP-miljö hoppar du över dessa steg och går till steg 9. Om du har startat enheten i icke-DHCP-miljö visas följande skärm bild.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Konfigurera sedan nätverket.
7. Använd `Get-HcsIpAddress` kommandot för att visa en lista över nätverks gränssnitt som är aktiverade på den virtuella matrisen. Om enheten har ett enda nätverksgränssnitt aktiverad är det tilldelade standardnamnet för gränssnittet `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Använd cmdleten `Set-HcsIpAddress` för att konfigurera nätverket. Se följande exempel:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. När den inledande installationen är klar och enheten har startats visas enhetens banderollstext. Anteckna den IP-adress och den URL som visas i banderollstexten för att hantera enheten. Använd den här IP-adressen för att ansluta till webb gränssnittet för den virtuella matrisen och slutföra den lokala installationen och registreringen.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. Valfritt Utför bara det här steget om du distribuerar din enhet i det offentliga molnet. Du kommer nu att aktivera FIPS-läget (USA Federal Information Processing Standard) på enheten. FIPS 140-standarden definierar kryptografiska algoritmer som godkänns för användning av amerikanska federala myndighets dator system för skydd av känsliga data.

    1. Om du vill aktivera FIPS-läget kör du följande cmdlet:

        `Enable-HcsFIPSMode`
    2. Starta om enheten när du har aktiverat FIPS-läget så att de kryptografiska verifieringarna börjar gälla.

       > [!NOTE]
       > Du kan antingen aktivera eller inaktivera FIPS-läge på enheten. Det finns inte stöd för att växla mellan enheter mellan FIPS-och icke-FIPS-läge.
       >
       >

Om enheten inte uppfyller de lägsta konfigurations kraven visas följande fel i banderollbilden (visas nedan). Ändra enhetskonfigurationen så att datorn har tillräckliga resurser för att uppfylla minimikraven. Du kan sedan starta om och ansluta till enheten. Se minimi kraven för konfiguration i steg 1: kontrol lera att värd systemet uppfyller minimi kraven för virtuella matriser.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Om du möter andra fel under den inledande konfigurationen med hjälp av det lokala webb gränssnittet, se följande arbets flöden:

* Kör diagnostiska tester för att [Felsöka konfiguration av webb gränssnitt](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generera logg paket och visa loggfiler](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Nästa steg
* [Konfigurera din virtuella StorSimple-matris som en fil Server](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurera din virtuella StorSimple-matris som en iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md)
