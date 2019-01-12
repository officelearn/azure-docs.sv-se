---
title: Etablera StorSimple Virtual Array i VMware | Microsoft Docs
description: Den här andra självstudien i serien av StorSimple Virtual Array distribution innebär att etablera en virtuell enhet i VMware.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c9fe597957057dc61da5c2b1cf6f9216711764a
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247851"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Distribuera StorSimple Virtual Array - etablera i VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Översikt
Den här självstudien beskrivs hur du etablerar och ansluter till en StorSimple Virtual Array i ett värdsystem som kör VMware ESXi 5.0, 5.5, 6.0 eller 6.5. Den här artikeln gäller för distribution av StorSimple Virtual Array i Azure portal och Microsoft Azure Government-molnet.

Du måste ha administratörsbehörighet för att etablera och ansluta till en virtuell enhet. Etableringen och den inledande installationen kan ta ungefär 10 minuter att slutföra.

## <a name="provisioning-prerequisites"></a>Krav för etablering
Kraven för att etablera en virtuell enhet i ett värdsystem som kör VMware ESXi 5.0, 5.5, 6.0 eller 6.5, är som följer.

### <a name="for-the-storsimple-device-manager-service"></a>För StorSimple Device Manager-tjänsten
Innan du börjar ska du kontrollera att:

* Du har slutfört alla steg i [förbereda portalen för StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Du har hämtat den virtuella enhet avbildningen för VMware från Azure-portalen. Mer information finns i **steg3: Ladda ned den virtuella enhet avbildningen** av [förbereda portalen för StorSimple Virtual Array för](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>För StorSimple-enhet
Innan du distribuerar en virtuell enhet kontrollerar du att:

* Du har åtkomst till ett värdsystem som kör Hyper-V (2008 R2 eller senare) som kan vara används för att en etablera en enhet.
* Värdsystemet kan dedikera följande resurser för att etablera den virtuella enheten:

  * Minst 4 kärnor.
  * Minst 8 GB RAM. Om du vill konfigurera den virtuella matrisen som filserver stöder 8 GB mindre än 2 miljoner filer. Behöver du 16 GB RAM-minne till 2-4 miljoner stödfiler.
  * Ett nätverksgränssnitt.
  * En virtuell disk 500 GB för systemdata.

### <a name="for-the-network-in-datacenter"></a>För nätverket i datacentret
Innan du börjar ska du kontrollera att:

* Du har granskat nätverkskraven för att distribuera en virtuell StorSimple-enhet och konfigurerat datacenternätverket enligt kraven. 

## <a name="step-by-step-provisioning"></a>Steg för steg-etablering
För att etablera och ansluta till en virtuell enhet, måste du utföra följande steg:

1. Kontrollera att värddatorn har tillräckligt med resurser för att uppfylla kraven för minsta virtuella enheten.
2. Etablera en virtuell enhet i din hypervisor-program.
3. Starta den virtuella enheten och hämta IP-adressen.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Steg 1: Se till att värdsystemet uppfyller minsta virtuella enheten
Om du vill skapa en virtuell enhet behöver du:

* Åtkomst till ett värdsystem som kör VMware ESXi Server 5.0, 5.5, 6.0 eller 6.5.
* VMware vSphere-klient i systemet för att hantera ESXi-värden.

  * Minst 4 kärnor.
  * Minst 8 GB RAM. Om du vill konfigurera den virtuella matrisen som filserver stöder 8 GB mindre än 2 miljoner filer. Behöver du 16 GB RAM-minne till 2-4 miljoner stödfiler.
  * Ett nätverksgränssnitt anslutet till det nätverk som kan dirigera trafik till Internet. Den minsta bandbredden som Internet bör vara 5 Mbit/s för optimal fungerande för enheten.
  * En virtuell disk 500 GB för data.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Steg 2: Etablera en virtuell enhet i hypervisor-program
Utför följande steg för att etablera en virtuell enhet i ditt hypervisor-program.

1. Kopiera avbildningen av den virtuella enheten i ditt system. Du har hämtat den här virtuella avbildningen via Azure portal.

   1. Se till att du har hämtat den senaste avbildningsfilen. Om du har hämtat avbildningen tidigare kan du ladda ned den igen för att kontrollera att du har den senaste avbildningen. Den senaste avbildningen har två filer (i stället för en).
   2. Anteckna den plats dit du kopierade avbildningen eftersom du använder den här avbildningen senare under proceduren.

2. Logga in på ESXi-servern med vSphere-klienten. Du måste ha administratörsbehörighet för att kunna skapa en virtuell dator.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. Välj den ESXi-Server i vSphere-klienten i avsnittet inventering i den vänstra rutan.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Ladda upp VMDK till ESXi-servern. Navigera till den **Configuration** fliken i den högra rutan. Under **maskinvara**väljer **Storage**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. I det högra fönstret, under **Datastores** (Datalager), väljer du det datalager där du vill ladda upp VMDK. Databasen måste ha tillräckligt med ledigt utrymme för operativsystem och datadiskar.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Högerklicka och välj **Browse Datastore** (Bläddra i datalager).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Ett **Datastore Browser**-fönster öppnas.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. I verktygsfältet klickar du på ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ikon för att skapa en ny mapp. Ange mappnamnet och anteckna det. Du använder det här mappnamnet senare när du skapar en virtuell dator (rekommenderad bästa praxis). Klicka på **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Den nya mappen visas i det vänstrat fönstret i **Datastore Browser**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klicka på ikonen ladda upp ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) och välj **Överför fil**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Bläddra till och peka på de VMDK-filer du har laddat ned. Det finns två filer. Välj en fil att ladda upp.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klicka på **Open** (Öppna). Uppladdningen av VMDK-filen till det angivna datalagret startar. Det kan ta flera minuter för filen att laddas upp.
13. När uppladdningen är klar visas filen i datalagret i den mapp du har skapat.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Ladda nu upp den andra VMDK-filen till samma datalager.
14. Återgå till vSphere-klientfönstret. Med ESXi-servern är valt, högerklicka och välj **ny virtuell dator**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. En **Skapa ny virtuell dator** fönster visas. På den **Configuration** väljer den **anpassad** alternativet. Klicka på **Nästa**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. På den **namn och plats** anger du namnet på den virtuella datorn. Det här namnet måste matcha namnet på mappen (rekommenderad metod) du angav tidigare i steg 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. På den **Storage** väljer ett datalager som du vill använda för att etablera den virtuella datorn.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. På den **virtuell Datorversion** väljer **VM-Version: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. På den **gästoperativsystemet** väljer den **gästoperativsystemet** som **Windows**. För **Version**, i listrutan Välj **Microsoft Windows Server 2012 (64-bitars)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. På den **processorer** sidan, justera den **antalet virtuella sockets** och **antalet kärnor per virtuell socket** så att den **totala antalet kärnor** är 4 (eller mer). Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. På den **minne** anger 8 GB (eller mer) RAM-minne. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. På den **nätverk** anger antalet nätverksgränssnitt. Minimikravet är ett nätverksgränssnitt.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. På den **SCSI-styrenhet** godkänner standard **LSI Logic SAS-styrenhet**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. På den **väljer du en Disk** väljer **använder en befintlig virtuell disk**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. På den **Välj befintlig Disk** sidan under **Disk filsökväg**, klickar du på **Bläddra**. Då öppnas en **Bläddra datalager** dialogrutan. Gå till platsen där du laddade upp VMDK. Du kan nu se endast en fil i datalager som har samman de två filerna som du överförde från början. Markera filen och klicka på **OK**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. På den **avancerade alternativ** , acceptera standardinställningarna och klicka på **nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. På sidan **Ready to Complete** (Redo att slutföra)granskar du alla inställningar associerade med den nya virtuella datorn. Kontrollera **redigera inställningarna för virtuella datorn innan slutförande**. Klicka på **Fortsätt**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. På den **egenskaper för virtuella datorer** sidan den **maskinvara** fliken, leta upp enhetens maskinvara. Välj **ny hårddisk**. Klicka på **Lägg till**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Du ser en **Lägg till maskinvara** fönster. På den **enhetstyp** sidan under **väljer vilken typ av enhet som du vill lägga till**väljer **hårddisk**, och klicka på **nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. På den **väljer du en Disk** väljer **skapa en ny virtuell disk**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. På den **skapar du en Disk** , ändra den **diskstorlek** till 500 GB (eller mer). 500 GB är minimikravet, kan du alltid etablera en större disk. Observera att du kan öka eller minska den disk som etablerats en gång. Läs avsnittet storlek i mer information om storleken på disken för att etablera den [bästa praxis dokumentet](storsimple-ova-best-practices.md). Under **Disk etablering**väljer **tunn**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. På den **avancerade alternativ** godkänner du standardvärdet.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. På den **redo att Slutför** granskar du alternativen för diskar. Klicka på **Slutför**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Gå tillbaka till sidan Egenskaper för virtuell dator. En ny hårddisk har lagts till den virtuella datorn. Klicka på **Slutför**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Med din virtuella dator som valts i den högra rutan, gå till den **sammanfattning** fliken. Granska inställningarna för den virtuella datorn.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Den virtuella datorn är nu etablerad. Nästa steg är att aktivera datorn och hämta IP-adressen.

> [!NOTE]
> Vi rekommenderar att du inte installerar VMware-verktyg på din virtuella matris (som etablerats ovan). Installation av VMware-verktyg resulterar i en konfiguration som inte stöds.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Steg 3: Starta den virtuella enheten och hämta IP-adressen
Utför följande steg för att starta den virtuella enheten och ansluta till den.

#### <a name="to-start-the-virtual-device"></a>Så startar du den virtuella enheten
1. Starta den virtuella enheten. Välj din enhet i vSphere Configuration Manager i den vänstra rutan och högerklicka för att ta fram på snabbmenyn. Välj **Power** (Av/på) och sedan **Power on** (På). Den virtuella datorn bör aktiveras. Du kan visa statusen i nederkant **senaste aktiviteterna** rutan i vSphere-klienten.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Aktiviteter för databasinstallation tar några minuter att slutföra. När enheten är i gång, navigera till den **konsolen** fliken. Skicka Ctrl + Alt + Delete för att logga in på enheten. Du kan också peka markören på konsolfönstret och trycka på Ctrl + Alt + Insert. Standardanvändaren är *StorSimpleAdmin* och standardlösenordet är *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Av säkerhetsskäl upphör enhetens administratörslösenord vid första inloggningen. Du uppmanas att ändra lösenordet.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Ange ett lösenord som innehåller minst 8 tecken. Lösenordet måste innehålla 3 av 4 av dessa krav: versaler, gemener, siffror och specialtecken. Ange lösenordet igen för att bekräfta det. Du kommer att meddelas att lösenordet har ändrats.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. När lösenordet ändras, kan du starta om den virtuella enheten. Vänta tills startas om för att slutföra. Windows PowerShell-konsolen för enheten visas tillsammans med en förloppsindikator.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Steg 6–8 gäller bara när du startar i en icke-DHCP-miljö. Om du använder en DHCP-miljö hoppar du över dessa steg och går till steg 9. Om du har startat upp din enhet i icke-DHCP-miljö kan visas följande skärmbild.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Konfigurera nätverket.
7. Använd den `Get-HcsIpAddress` kommando för att lista de nätverksgränssnitt som är aktiverad på den virtuella enheten. Om enheten har ett enda nätverksgränssnitt aktiverad är det tilldelade standardnamnet för gränssnittet `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Använd cmdleten `Set-HcsIpAddress` för att konfigurera nätverket. Ett exempel på detta visas nedan:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. När den inledande installationen är klar och enheten har startats visas enhetens banderollstext. Anteckna den IP-adress och den URL som visas i banderollstexten för att hantera enheten. Du använder denna IP-adress för att ansluta till webbgränssnittet på den virtuella enheten och slutför den lokala installation och registrering.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Valfritt) Utför det här steget endast om du distribuerar din enhet i Government-molnet. Du kan nu USA FIPS Federal Information Processing Standard ()-läge på din enhet. Standarden FIPS 140 definierar kryptografiska algoritmer som godkänts för användning av amerikanska federala myndigheter government datorsystem för skydd av känsliga data.

    1. Om du vill aktivera FIPS-läge, kör du följande cmdlet:

        `Enable-HcsFIPSMode`
    2. Starta om din enhet när du har aktiverat FIPS-läge så att de kryptografiska kontroller träder i kraft.

       > [!NOTE]
       > Du kan aktivera eller inaktivera FIPS-läge på din enhet. Alternerande enheten mellan FIPS- och icke-FIPS-läge stöds inte.
       >
       >

Om enheten inte uppfyller minimikraven för konfiguration visas ett felmeddelande i banderollstexten (visas nedan). Du måste ändra enhetskonfigurationen så att den har tillräckliga resurser för att uppfylla minimikraven. Du kan sedan starta om och ansluta till enheten. Referera till de lägsta konfigurationskrav i [steg 1: Kontrollera att värddatorn uppfyller minsta virtuella enheten](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Om du får ett fel under den första konfigurationen med hjälp av det lokala webbgränssnittet finns följande arbetsflöden:

* Kör diagnostiktest till [felsökning av web UI installationsprogrammet](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Skapa log paket och visa loggfiler](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Nästa steg
* [Konfigurera din StorSimple Virtual Array som en filserver](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurera din StorSimple Virtual Array som en iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md)
