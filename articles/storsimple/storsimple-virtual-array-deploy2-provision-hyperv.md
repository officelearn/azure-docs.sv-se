---
title: Etablera virtuella StorSimple-matris i Hyper-V | Microsoft Docs
description: "Självstudierna andra i virtuella StorSimple-matris distribution innebär att etablera en virtuell Hyper-V-matris."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/15/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bad431c8958f7d381bb9c0410caa3a57c6e75c19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Distribuera StorSimple virtuell matris - etablera i Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Översikt
Den här självstudiekursen beskrivs hur du etablerar en virtuell StorSimple-matris på ett värdsystem som kör Hyper-V på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2. Den här artikeln gäller för distribution av virtuella StorSimple-matriser i Azure-portalen och Microsoft Azure Government-molnet.

Du måste ha administratörsbehörighet för att etablera och konfigurera en virtuell matris. Etablering och inledande installationen kan ta cirka 10 minuter för att slutföra.

## <a name="provisioning-prerequisites"></a>Etablering av förutsättningar
Här hittar du förutsättningar för att kunna etablera en virtuell matrisen på ett värdsystem som kör Hyper-V på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>För StorSimple Device Manager-tjänsten
Innan du börjar bör du kontrollera att:

* Du har slutfört alla steg i [förbereda portalen för virtuell StorSimple-matrisen](storsimple-virtual-array-deploy1-portal-prep.md).
* Du har hämtat virtuella matris avbildningen för Hyper-V från Azure-portalen. Mer information finns i **steg3: ladda ned avbildningen virtuella matris** av [förbereda portal för virtuell StorSimple-matris guiden](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > Programmet som körs på den virtuella StorSimple-matrisen får endast användas med Enhetshanteraren för StorSimple-tjänsten.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>För den virtuella StorSimple-matrisen
Innan du distribuerar en virtuell matris, kontrollerar du att:

* Du har åtkomst till ett värdsystem som kör Hyper-V på Windows Server 2008 R2 eller senare som kan användas för att en etablera en enhet.
* Värddatorn är att dedikera följande resurser för att etablera virtuella matrisen:

  * Minst 4 kärnor.
  * Minst 8 GB RAM-minne. Om du planerar att konfigurera virtuella matrisen som filserver stöder 8 GB mindre än 2 miljoner filer. Behöver du 16 GB RAM-MINNET 2-4 miljoner stödfiler.
  * Ett nätverksgränssnitt.
  * En virtuell disk 500 GB för data.

### <a name="for-the-network-in-the-datacenter"></a>För nätverket i datacentret
Innan du kan granska nätverkskrav för att distribuera en virtuell StorSimple-matris och konfigurera datacenternätverket på lämpligt sätt. Mer information finns i [StorSimple virtuell matris nätverkskrav](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Steg för steg-etablering
För att etablera och ansluta till en virtuell matris, måste du utföra följande steg:

1. Kontrollera att värddatorn har tillräckligt med resurser för att uppfylla kraven för minsta virtuella matris.
2. Etablera en virtuell matris i din hypervisor-program.
3. Starta den virtuella matrisen och hämta IP-adress.

Var och en av de här stegen beskrivs i följande avsnitt.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Steg 1: Kontrollera att värddatorn uppfyller minsta virtuella matris
Om du vill skapa en virtuell matris, behöver du:

* Hyper-V-rollen installerad på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2 SP1.
* Microsoft Hyper-V Manager på en Microsoft Windows-klient som är anslutet till värden.

Se till att den underliggande maskinvaran (värdsystem) som du skapar virtuella matrisen är kan för din virtuella matris i följande resurser:

* Minst 4 kärnor.
* Minst 8 GB RAM-minne. Om du planerar att konfigurera virtuella matrisen som filserver stöder 8 GB mindre än 2 miljoner filer. Behöver du 16 GB RAM-MINNET 2-4 miljoner stödfiler.
* Ett nätverksgränssnitt.
* En virtuell disk 500 GB efter systemdata.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Steg 2: Etablera en virtuell matris i hypervisor-program
Utför följande steg för att etablera en enhet i din hypervisor.

#### <a name="to-provision-a-virtual-array"></a>Att etablera en virtuell matris
1. Kopiera virtuella matris-avbildning till en lokal enhet på Windows Server-värd. Du har hämtat den här avbildningen (VHD eller VHDX) via Azure-portalen. Anteckna den plats dit du kopierade bilden som du använder den här avbildningen senare under proceduren.
2. Öppna **Serverhanteraren**. I övre högra hörnet, klickar du på **verktyg** och välj **Hyper-V Manager**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Öppna Hyper-V Manager om du kör Windows Server 2008 R2. I Serverhanteraren klickar du på **roller > Hyper-V > Hyper-V Manager**.
3. I **Hyper-V Manager**i scope-fönstret högerklickar du på noden system för att öppna snabbmenyn och klicka sedan på **ny** > **virtuella**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. På den **innan du börjar** sidan i guiden Ny virtuell dator klickar du på **nästa**.
5. På den **ange namn och plats** anger en **namn** för din virtuella matrisen. Klicka på **Nästa**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. På den **ange generation** , Välj typ av enhet avbildningen, och klickar sedan på **nästa**. Den här sidan visas inte om du använder Windows Server 2008 R2.

   * Välj **Generation 2** om du har hämtat en vhdx-avbildning för Windows Server 2012 eller senare.
   * Välj **Generation 1** om du har hämtat en VHD-avbildning för Windows Server 2008 R2 eller senare.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. På den **tilldela minne** anger en **startminne** av minst **8192 MB**inte aktivera dynamiskt minne, och klicka sedan på **nästa**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. På den **Konfigurera nätverk** anger den virtuella växeln som är ansluten till Internet och klicka sedan på **nästa**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. På den **Anslut virtuell hårddisk** väljer **använder en befintlig virtuell hårddisk**, ange platsen för virtuella matris-bild (vhdx eller VHD) och klicka sedan på **nästa**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Granska de **sammanfattning** och klicka sedan på **Slutför** att skapa den virtuella datorn.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. För att uppfylla minimikraven måste 4 kärnor. Om du vill lägga till 4 virtuella processorer, Välj din värdsystem i den **Hyper-V Manager** fönster. I den högra rutan under listan över **virtuella datorer**, leta upp den virtuella datorn som du nyss skapade. Markera och högerklicka på namnet på datorn och välj **inställningar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. På den **inställningar** , i den vänstra rutan klickar du på **Processor**. I den högra rutan Ange **antal virtuella processorer** 4 (eller mer). Klicka på **Använd**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. För att uppfylla minimikraven måste du också lägga till en 500 GB data för virtuell disk. I den **inställningar** sidan:

    1. I den vänstra rutan, Välj **SCSI-styrenhet**.
    2. I den högra rutan, Välj **hårddisk,** och på **Lägg till**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. På den **hårddisken** väljer den **virtuella hårddisk** och klickar på **ny**. Den **guiden för ny virtuell hårddisk** startar.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. På den **innan du börjar** sidan i guiden Ny virtuell hårddisk, klickar du på **nästa**.
16. På den **sidan Välj diskformat**, godkänner du standardalternativet **VHDX** format. Klicka på **Nästa**. Den här skärmen visas inte om du kör Windows Server 2008 R2.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. På den **sidan Välj disktyp**, ange typen av virtuell hårddisk som **dynamiskt expanderande** (rekommenderas). **En fast storlek** disk skulle fungera men du kan behöva vänta längre tid. Vi rekommenderar att du inte använder den **Differencing** alternativet. Klicka på **Nästa**. I Windows Server 2012 R2 och Windows Server 2012 **dynamiskt expanderande** är standardalternativet i Windows Server 2008 R2, standard är **fast storlek**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. På den **ange namn och plats** anger en **namn** samt **plats** (du kan bläddra till en) för datadisk. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. På den **konfigurera Disk** markerar du alternativet **skapa en ny tom virtuell hårddisk** och ange storlek som **500 GB** (eller mer). 500 GB är minimikravet, kan du alltid etablera en större disk. Observera att du kan öka eller minska den disk som etablerats en gång. Mer information om storleken på disken för att etablera Läs avsnittet storlek i den [bästa praxis dokumentet](storsimple-ova-best-practices.md). Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. På den **sammanfattning** granskar information om din virtuella datadisk och om uppfyllt, klickar du på **Slutför** att skapa disken. Guiden har stängts och en virtuell hårddisk har lagts till i din dator.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Gå tillbaka till den **inställningar** sidan. Klicka på **OK** att stänga den **inställningar** sidan och återgå till Hyper-V Manager-fönstret.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Steg 3: Starta den virtuella matrisen och hämta IP-Adressen
Utför följande steg om du vill starta virtuella matrisen och ansluta till den.

#### <a name="to-start-the-virtual-array"></a>Starta den virtuella matrisen
1. Starta den virtuella matrisen.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. När enheten körs väljer du enheten, högerklicka och välj **Anslut**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Du kan behöva vänta 5-10 minuter för enheten inte redo. Ett meddelande visas på konsolen för att indikera förlopp. När enheten är klar, går du till **åtgärd**. Tryck på `Ctrl + Alt + Delete` att logga in på den virtuella matrisen. Standardanvändaren är *StorSimpleAdmin* och standardlösenordet är *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Av säkerhetsskäl upphör enhetens administratörslösenord vid första inloggningen. Du uppmanas att ändra lösenordet.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Ange ett lösenord som innehåller minst 8 tecken. Lösenordet måste uppfylla minst 3 av följande 4 krav: versaler, gemener, siffror och särskilda tecken. Ange lösenordet för att bekräfta det. igen. Du meddelas att lösenordet har ändrats.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Efter att lösenordet har ändrats kan starta om den virtuella matrisen. Vänta tills enheten att starta.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Windows PowerShell-konsolen för enheten visas tillsammans med en förloppsindikator.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Steg 6 – 8 gäller endast när du startar upp i en icke-DHCP-miljö. Om du har en DHCP-miljö, hoppa över de här stegen och gå till steg 9. Om du har startat upp din enhet i DHCP-miljön visas följande skärm.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Konfigurera nätverket.
7. Använd den `Get-HcsIpAddress` kommando för att visa en lista över nätverksgränssnitt som är aktiverade på din virtuella matrisen. Om enheten har ett enda nätverksgränssnitt som är aktiverad, standardnamnet som tilldelats det här gränssnittet är `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Använd den `Set-HcsIpAddress` cmdlet för att konfigurera nätverket. Se följande exempel:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. När installationen är klar och enheten har startats in, visas enheten texten. Anteckna IP-adressen och den URL som visas i den här texten kan hantera enheten. Använd denna IP-adress att ansluta till webbgränssnittet för ditt virtuella matrisen och slutföra lokal installation och registrering.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Valfritt) Utför det här steget endast om du distribuerar din enhet i det offentliga molnet. Du kan nu USA FIPS Federal Information Processing Standard ()-läge på enheten. Standarden FIPS 140 definierar kryptografiska algoritmer som godkänts för användning av oss federala myndigheter datorsystem för att skydda känsliga data.

    1. Om du vill aktivera FIPS-läge, kör du följande cmdlet:

        `Enable-HcsFIPSMode`
    2. Starta om enheten när du har aktiverat FIPS-läge så att de kryptografiska kontroller börjar gälla.

       > [!NOTE]
       > Du kan aktivera eller inaktivera FIPS-läge på enheten. Växla mellan FIPS och icke-FIPS-läge stöds inte.
       >
       >

Om enheten inte uppfyller minimikraven för konfiguration, se följande fel i den här texten (se nedan). Ändra enhetskonfigurationen så att datorn inte har tillräckliga resurser för att uppfylla minimikraven. Du kan sedan starta om och ansluta till enheten. Referera till de lägsta konfigurationskrav i [steg 1: Kontrollera att värddatorn uppfyller minsta virtuella matris](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Om du står inför ett fel under den första konfigurationen med lokala webbgränssnittet, se följande arbetsflöden:

* Kör diagnostiktest till [felsöka installationen av UI](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generera loggen paketet och visa loggfiler](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Nästa steg
* [Konfigurera din virtuella StorSimple-matris som en filserver](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurera din virtuella StorSimple-matris som en iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md)
