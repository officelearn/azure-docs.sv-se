---
title: "Etablera virtuella StorSimple-matris på VMware | Microsoft Docs"
description: "Den här andra kursen i virtuella StorSimple-matris deployment serien innebär att etablera en virtuell enhet i VMware."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 495ef6a93ee06423495269306ad06e76dda13e10
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Distribuera StorSimple virtuell matris - etablera i VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Översikt
Den här självstudiekursen beskrivs hur du etablera och ansluta till en virtuell StorSimple-matris på ett värdsystem med VMware ESXi 5.0, 5.5 eller 6.0. Den här artikeln gäller för distribution av virtuella StorSimple-matriser i Azure-portalen och Microsoft Azure Government-molnet.

Du måste ha administratörsbehörighet för att etablera och ansluta till en virtuell enhet. Etablering och inledande installationen kan ta cirka 10 minuter för att slutföra.

## <a name="provisioning-prerequisites"></a>Etablering av förutsättningar
Förutsättningar för att kunna etablera en virtuell enhet på ett värdsystem med VMware ESXi 5.0, 5.5 eller 6.0 är som följer.

### <a name="for-the-storsimple-device-manager-service"></a>För StorSimple Device Manager-tjänsten
Innan du börjar bör du kontrollera att:

* Du har slutfört alla steg i [förbereda portalen för virtuell StorSimple-matrisen](storsimple-virtual-array-deploy1-portal-prep.md).
* Du har hämtat den virtuella enhet avbildningen för VMware från Azure-portalen. Mer information finns i **steg3: ladda ned avbildningen virtuella enheten** av [förbereda portal för virtuell StorSimple-matris guiden](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>För den virtuella enheten StorSimple
Kontrollera följande innan du distribuerar en virtuell enhet:

* Du har åtkomst till ett värdsystem som kör Hyper-V (2008 R2 eller senare) som kan användas för att en etablera en enhet.
* Värddatorn är att dedikera följande resurser för att etablera din virtuella enhet:

  * Minst 4 kärnor.
  * Minst 8 GB RAM-minne. Om du planerar att konfigurera virtuella matrisen som filserver stöder 8 GB mindre än 2 miljoner filer. Behöver du 16 GB RAM-MINNET 2-4 miljoner stödfiler.
  * Ett nätverksgränssnitt.
  * En virtuell disk 500 GB efter systemdata.

### <a name="for-the-network-in-datacenter"></a>För nätverket i datacentret
Innan du börjar bör du kontrollera att:

* Du har granskat nätverkskrav för att distribuera en virtuell StorSimple-enhet och konfigurerat datacenternätverket enligt kraven. 

## <a name="step-by-step-provisioning"></a>Steg för steg-etablering
För att etablera och ansluta till en virtuell enhet, måste du utföra följande steg:

1. Kontrollera att värddatorn har tillräckligt med resurser för att uppfylla kraven för minsta virtuella enheten.
2. Etablera en virtuell enhet i din hypervisor-program.
3. Starta den virtuella enheten och hämta IP-adress.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Steg 1: Kontrollera värdsystem uppfyller minsta virtuell enhet
Om du vill skapa en virtuell enhet behöver du:

* Åtkomst till ett värdsystem med VMware ESXi Server 5.0, 5.5 eller 6.0.
* VMware vSphere-klient på datorn att hantera ESXi-värd.

  * Minst 4 kärnor.
  * Minst 8 GB RAM-minne. Om du planerar att konfigurera virtuella matrisen som filserver stöder 8 GB mindre än 2 miljoner filer. Behöver du 16 GB RAM-MINNET 2-4 miljoner stödfiler.
  * Ett nätverksgränssnitt som är ansluten till nätverket kan dirigera trafiken till Internet. Den minsta bandbredden för Internet ska vara 5 Mbit/s för att tillåta för att enheten ska fungera optimalt.
  * En virtuell disk 500 GB för data.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Steg 2: Etablera en virtuell enhet i hypervisor-program
Utför följande steg för att etablera en virtuell enhet i din hypervisor-program.

1. Kopiera den virtuella enhet avbildningen på systemet. Du har hämtat den här virtuella avbildningen via Azure-portalen.

   1. Se till att du har hämtat den senaste avbildningsfilen. Om du tidigare hämtade bilden hämta igen för att kontrollera att du har den senaste avbildningen. Senaste avbildningen har två filer (i stället för en).
   2. Anteckna den plats dit du kopierade bilden som du använder den här avbildningen senare under proceduren.

2. Logga in på ESXi-servern med hjälp av vSphere-klienten. Du måste ha administratörsbehörighet för att skapa en virtuell dator.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. Välj ESXi-servern i vSphere-klienten i avsnittet lagret i den vänstra rutan.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Ladda upp VMDK ESXi-servern. Navigera till den **Configuration** fliken i den högra rutan. Under **maskinvara**väljer **lagring**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. I den högra rutan under **Datastores**, markera databasen som du vill ladda upp VMDK. Databasen måste ha tillräckligt med diskutrymme för Operativsystemet och datadiskarna.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Högerklicka och välj **Bläddra Datastore**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. En **Datastore webbläsare** visas.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. I verktygsfältet, klickar du på ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ikon för att skapa en ny mapp. Ange namnet på mappen och anteckna den. Du använder den här mappnamn senare när du skapar en virtuell dator (rekommenderas för bästa praxis). Klicka på **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Den nya mappen visas i den vänstra rutan i **Datastore webbläsare**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klicka på ikonen överför ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) och välj **överför filen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Bläddra och peka på VMDK-filer som du hämtat. Det finns två filer. Välj en fil att överföra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klicka på **öppna**. Överföringen av VMDK-filen till den angivna datalagringen startar. Det kan ta flera minuter att ladda upp filen.
13. När överföringen är klar finns i filen i datalagret i mappen som du skapade.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Ladda upp den andra VMDK-filen till samma datalager.
14. Gå tillbaka till fönstret vSphere-klienten. Med ESXi-servern är markerad, högerklicka och välj **ny virtuell dator**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. En **Skapa ny virtuell dator** fönster visas. På den **Configuration** väljer den **anpassad** alternativet. Klicka på **Nästa**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. På den **namn och plats** anger du namnet på den virtuella datorn. Det här namnet ska matcha namnet på mappen (rekommenderas) du angav tidigare i steg 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. På den **lagring** väljer du ett datalager som du vill använda för att etablera den virtuella datorn.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. På den **virtuella Version** väljer **virtuella Version: 8**. 8-11 versioner alla.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. På den **gästoperativsystemet** väljer den **gästoperativsystemet** som **Windows**. För **Version**, i listrutan väljer **Microsoft Windows Server 2012 (64-bitars)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. På den **processorer** sidan, justera det **antalet virtuella uttag** och **antalet kärnor per virtuell socket** så att den **Totalt antal kärnor** 4 (eller mer). Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. På den **minne** anger 8 GB (eller mer) RAM-minne. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. På den **nätverk** anger antalet nätverksgränssnitt. Minimikravet är ett nätverksgränssnitt.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. På den **SCSI-styrenhet** godkänner standardinställningen **LSI Logic SAS-styrenhet**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. På den **Välj en Disk** väljer **använder en befintlig virtuell disk**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. På den **Välj befintlig Disk** sidan under **Disk filsökväg**, klickar du på **Bläddra**. Då öppnas en **Bläddra Datastores** dialogrutan. Navigera till den plats där du överförde VMDK. Du kan nu se endast en fil i datalager som har samman de två filerna som du ursprungligen har överförts. Markera filen och klicka på **OK**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. På den **avancerade alternativ** , acceptera standardinställningarna och klickar på **nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. På den **redo att Slutför** granskar alla inställningar som är associerade med den nya virtuella datorn. Kontrollera **redigera inställningarna för virtuella datorer före slutförande**. Klicka på **fortsätta**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. På den **egenskaper för virtuella datorer** sidan den **maskinvara** , letar du reda på maskinvara. Välj **ny hårddisk**. Klicka på **Lägg till**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Du ser en **Lägg till maskinvara** fönster. På den **enhetstyp** sidan under **väljer du typ av enhet som du vill lägga till**väljer **hårddisk**, och klicka på **nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. På den **Välj en Disk** väljer **skapar en ny virtuell disk**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. På den **skapar du en Disk** ändrar du den **diskstorleken** till 500 GB (eller mer). 500 GB är minimikravet, kan du alltid etablera en större disk. Observera att du kan öka eller minska den disk som etablerats en gång. Mer information om storleken på disken för att etablera Läs avsnittet storlek i den [bästa praxis dokumentet](storsimple-ova-best-practices.md). Under **Disk etablering**väljer **tunn**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. På den **avancerade alternativ** godkänner du standardvärdet.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. På den **redo att Slutför** läser du igenom alternativen disk. Klicka på **Slutför**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Gå tillbaka till sidan Egenskaper för virtuell dator. En ny hårddisk läggs till den virtuella datorn. Klicka på **Slutför**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Med den valda virtuella datorn i den högra rutan, navigera till den **sammanfattning** fliken. Granska inställningarna för den virtuella datorn.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Den virtuella datorn har nu etablerats. Nästa steg är att slå på den här datorn och hämta IP-adress.

> [!NOTE]
> Vi rekommenderar att du inte installerar VMware-verktyg på din virtuella matrisen (som etablerats ovan). Installationen av VMware-verktyg resulterar i en konfiguration som inte stöds.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Steg 3: Starta den virtuella enheten och hämta IP-Adressen
Utför följande steg för att starta den virtuella enheten och ansluta till den.

#### <a name="to-start-the-virtual-device"></a>Starta den virtuella enheten
1. Starta den virtuella enheten. Välj din enhet i vSphere Configuration Manager i den vänstra rutan och högerklicka om du vill visa snabbmenyn. Välj **Power** och välj sedan **ström på**. Detta bör sätta på den virtuella datorn. Du kan visa statusen i nederkant **senaste aktiviteter** rutan vSphere-klienten.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Konfigurationsåtgärderna tar några minuter att slutföra. När enheten körs, navigera till den **konsolen** fliken. Skicka Ctrl + Alt + Delete för att logga in på enheten. Du kan också peka markören på konsolfönstret och tryck på Ctrl + Alt + Insert. Standardanvändaren är *StorSimpleAdmin* och standardlösenordet är *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Av säkerhetsskäl upphör enhetens administratörslösenord vid första inloggningen. Du uppmanas att ändra lösenordet.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Ange ett lösenord som innehåller minst 8 tecken. Lösenordet måste innehålla 3 av 4 av dessa krav: versaler, gemener, siffror och särskilda tecken. Ange lösenordet för att bekräfta det. igen. Du meddelas att lösenordet har ändrats.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Efter att lösenordet har ändrats, kan starta om den virtuella enheten. Vänta tills startas om för att slutföra. Windows PowerShell-konsolen på enheten som kan visas tillsammans med en förloppsindikator.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Steg 6 – 8 gäller endast när du startar upp i en icke-DHCP-miljö. Om du har en DHCP-miljö, hoppa över de här stegen och gå till steg 9. Om du har startat upp din enhet i DHCP-miljön visas följande skärm.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Konfigurera nätverket.
7. Använd den `Get-HcsIpAddress` kommando för att visa en lista över nätverksgränssnitt som är aktiverad på din virtuella enhet. Om enheten har ett enda nätverksgränssnitt som är aktiverad, standardnamnet som tilldelats det här gränssnittet är `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Använd den `Set-HcsIpAddress` cmdlet för att konfigurera nätverket. Ett exempel visas nedan:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. När installationen är klar och enheten har startats in, visas enheten texten. Anteckna IP-adressen och den URL som visas i den här texten kan hantera enheten. Du använder denna IP-adress för att ansluta till webbgränssnittet för din virtuella enhet och slutföra lokal installation och registrering av.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Valfritt) Utför det här steget endast om du distribuerar din enhet i det offentliga molnet. Du kan nu USA FIPS Federal Information Processing Standard ()-läge på enheten. Standarden FIPS 140 definierar kryptografiska algoritmer som godkänts för användning av oss federala myndigheter datorsystem för att skydda känsliga data.

    1. Om du vill aktivera FIPS-läge, kör du följande cmdlet:

        `Enable-HcsFIPSMode`
    2. Starta om enheten när du har aktiverat FIPS-läge så att de kryptografiska kontroller börjar gälla.

       > [!NOTE]
       > Du kan aktivera eller inaktivera FIPS-läge på enheten. Växla mellan FIPS och icke-FIPS-läge stöds inte.
       >
       >

Om enheten inte uppfyller minimikraven för konfiguration, visas ett fel i den här texten (se nedan). Du behöver ändra enhetskonfigurationen så att den har tillräckliga resurser för att uppfylla minimikraven. Du kan sedan starta om och ansluta till enheten. Referera till de lägsta konfigurationskrav i [steg 1: Kontrollera att värddatorn uppfyller minsta virtuella enheten](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Om du står inför ett fel under den första konfigurationen med lokala webbgränssnittet, se följande arbetsflöden:

* Kör diagnostiktest till [felsöka installationen av UI](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generera loggen paketet och visa loggfiler](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Nästa steg
* [Konfigurera din virtuella StorSimple-matris som en filserver](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurera din virtuella StorSimple-matris som en iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md)
