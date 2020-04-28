---
title: Etablera StorSimple-virtuell matris i VMware
description: Den här andra själv studie kursen om distributions serien i StorSimple Virtual Array inbegriper etablering av en virtuell enhet i VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f5ded3faec3a080022eea70de2cca5d27529c4d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76272098"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Distribuera StorSimple Virtual Array-etablera i VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

I den här självstudien beskrivs hur du etablerar och ansluter till en virtuell StorSimple-matris på ett värd system som kör VMware ESXi 5,0, 5,5, 6,0 eller 6,5. Den här artikeln gäller distributionen av virtuella StorSimple-matriser i Azure Portal och Microsoft Azure Government molnet.

Du måste ha administratörsbehörighet för att etablera och ansluta till en virtuell enhet. Etableringen och den inledande installationen kan ta ungefär 10 minuter att slutföra.

## <a name="provisioning-prerequisites"></a>Krav för etablering
Kraven för att etablera en virtuell enhet på ett värd system som kör VMware ESXi 5,0, 5,5, 6,0 eller 6,5 är följande.

### <a name="for-the-storsimple-device-manager-service"></a>För StorSimple Device Manager-tjänsten
Innan du börjar ska du kontrollera att:

* Du har slutfört alla steg i [förbereda portalen för StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Du har laddat ned den virtuella enhets avbildningen för VMware från Azure Portal. Mer information finns i **steg 3: Hämta avbildningen av den virtuella enheten** [Förbered portalen för StorSimple Virtual Array guide](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>För den virtuella StorSimple-enheten
Innan du distribuerar en virtuell enhet kontrollerar du att:

* Du har åtkomst till ett värd system som kör Hyper-V (2008 R2 eller senare) som kan användas för att etablera en enhet.
* Värdsystemet kan dedikera följande resurser för att etablera den virtuella enheten:

  * Minst 4 kärnor.
  * Minst 8 GB RAM. Om du planerar att konfigurera den virtuella matrisen som fil Server stöder 8 GB färre än 2 000 000 filer. Du behöver 16 GB RAM-minne för att kunna hantera 2-4 miljoner filer.
  * Ett nätverksgränssnitt.
  * En virtuell disk på 500 GB för system data.

### <a name="for-the-network-in-datacenter"></a>För nätverket i datacentret
Innan du börjar ska du kontrollera att:

* Du har granskat nätverks kraven för att distribuera en virtuell StorSimple-enhet och konfigurerat Data Center nätverket enligt kraven. 

## <a name="step-by-step-provisioning"></a>Steg för steg-etablering
För att etablera och ansluta till en virtuell enhet måste du utföra följande steg:

1. Se till att värd systemet har tillräckligt med resurser för att uppfylla minimi kraven för virtuella enheter.
2. Etablera en virtuell enhet i hypervisor-programmets.
3. Starta den virtuella enheten och hämta IP-adressen.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Steg 1: se till att värd systemet uppfyller minimi kraven för virtuella enheter
Om du vill skapa en virtuell enhet behöver du:

* Åtkomst till ett värd system som kör VMware ESXi Server 5,0, 5,5, 6,0 eller 6,5.
* VMware vSphere-klient i systemet för att hantera ESXi-värden.

  * Minst 4 kärnor.
  * Minst 8 GB RAM. Om du planerar att konfigurera den virtuella matrisen som fil Server stöder 8 GB färre än 2 000 000 filer. Du behöver 16 GB RAM-minne för att kunna hantera 2-4 miljoner filer.
  * Ett nätverksgränssnitt anslutet till det nätverk som kan dirigera trafik till Internet. Den minsta Internet bandbredden bör vara 5 Mbit/s för att enheten ska fungera optimalt.
  * En virtuell disk på 500 GB för data.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Steg 2: etablera en virtuell enhet i hypervisor
Utför följande steg för att etablera en virtuell enhet i ditt hypervisor-program.

1. Kopiera avbildningen av den virtuella enheten i ditt system. Du laddade ned den här virtuella avbildningen via Azure Portal.

   1. Se till att du har hämtat den senaste avbildnings filen. Om du laddade ned avbildningen tidigare kan du hämta den igen för att se till att du har den senaste avbildningen. Den senaste avbildningen har två filer (i stället för en).
   2. Anteckna den plats dit du har kopierat avbildningen eftersom du använder den här avbildningen senare i proceduren.

2. Logga in på ESXi-servern med vSphere-klienten. Du måste ha administratörsbehörighet för att kunna skapa en virtuell dator.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. I vSphere-klienten i avsnittet inventering i det vänstra fönstret väljer du ESXi-servern.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Ladda upp VMDK till ESXi-servern. Gå till fliken **konfiguration** i den högra rutan. Under **maskin vara**väljer du **lagring**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. I det högra fönstret, under **Datastores** (Datalager), väljer du det datalager där du vill ladda upp VMDK. Data lagret måste ha tillräckligt med ledigt utrymme för operativ system och data diskar.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Högerklicka och välj **Browse Datastore** (Bläddra i datalager).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Ett **Datastore Browser**-fönster öppnas.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. I verktygsfältet klickar du på ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ikon för att skapa en ny mapp. Ange mappnamnet och anteckna det. Du använder det här mappnamnet senare när du skapar en virtuell dator (rekommenderad bästa praxis). Klicka på **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Den nya mappen visas i det vänstrat fönstret i **Datastore Browser**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klicka på ikonen ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) Ladda upp och välj **överför fil**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Bläddra till och peka på de VMDK-filer du har laddat ned. Det finns två filer. Välj en fil att ladda upp.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klicka på **Open** (Öppna). Uppladdningen av VMDK-filen till det angivna datalagret startar. Det kan ta flera minuter för filen att laddas upp.
13. När uppladdningen är klar visas filen i datalagret i den mapp du har skapat.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Ladda nu upp den andra VMDK-filen till samma datalager.
14. Återgå till vSphere-klientfönstret. När ESXi Server är markerat högerklickar du på den **nya virtuella datorn**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Ett fönster för att **skapa en ny virtuell dator** visas. På sidan **konfiguration** väljer du alternativet **anpassad** . Klicka på **Nästa**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. På sidan **namn och plats** anger du namnet på den virtuella datorn. Namnet måste matcha mappnamnet (rekommenderad metod) som du angav tidigare i steg 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. På sidan **lagring** väljer du ett data lager som du vill använda för att etablera den virtuella datorn.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. På sidan **version av virtuell dator** väljer du **version för virtuell dator: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. På sidan **gäst operativ system** väljer du **gäst operativ systemet** som **Windows**. För **version**väljer du **Microsoft Windows Server 2012 (64-bitars)** i list rutan.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. På sidan **processorer** justerar du **antalet virtuella Sockets** och **antalet kärnor per virtuell socket** så att det **totala antalet kärnor** är 4 (eller mer). Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. På sidan **minne** anger du 8 GB (eller mer) RAM-minne. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. På sidan **nätverk** anger du antalet nätverks gränssnitt. Minimi kravet är ett nätverks gränssnitt.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. På sidan **SCSI-styrenhet** accepterar du standard **SAS-styrenheten för LSI Logic**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. På sidan **Välj en disk** väljer du **Använd en befintlig virtuell disk**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. På sidan **Välj befintlig disk** , under **sökväg för disk fil**, klickar du på **Bläddra**. Då öppnas dialog rutan **Bläddra i data lager** . Navigera till den plats där du laddade upp VMDK. Nu visas bara en fil i data lagret eftersom de två filer som du ursprungligen laddade upp har slagits samman. Markera filen och klicka på **OK**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. På sidan **Avancerade alternativ** godkänner du standardvärdet och klickar på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. På sidan **Ready to Complete** (Redo att slutföra)granskar du alla inställningar associerade med den nya virtuella datorn. Markera **Redigera inställningarna för den virtuella datorn innan du slutförs**. Klicka på **Fortsätt**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. På sidan **Virtual Machines egenskaper** går du till fliken **maskin vara** och letar upp enhetens maskin vara. Välj **ny hård disk**. Klicka på **Lägg till**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Du ser ett fönster för att **lägga till maskin vara** . Välj **hård disk**under **Välj den typ av enhet som du vill lägga till**på sidan **enhets typ** och klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. På sidan **Välj en disk** väljer du **skapa en ny virtuell disk**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. På sidan **skapa en disk** ändrar du **Disk storleken** till 500 GB (eller mer). Även om 500 GB är minimi kravet, kan du alltid etablera en större disk. Observera att det inte går att utöka eller krympa disken när den har allokerats. Mer information om storleken på disken som ska etableras finns i avsnittet storlek i [dokumentet metod tips](storsimple-ova-best-practices.md). Under **disk etablering**väljer du **tunn etablering**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. På sidan **Avancerade alternativ** godkänner du standardvärdet.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. På sidan **redo att slutföra** granskar du disk alternativen. Klicka på **Slutför**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Gå tillbaka till egenskaps sidan för den virtuella datorn. En ny hård disk läggs till i den virtuella datorn. Klicka på **Slutför**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. När du har valt den virtuella datorn i den högra rutan går du till fliken **Sammanfattning** . Granska inställningarna för den virtuella datorn.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Den virtuella datorn är nu etablerad. Nästa steg är att aktivera datorn och hämta IP-adressen.

> [!NOTE]
> Vi rekommenderar att du inte installerar VMware-verktyg på den virtuella matrisen (som tillhandahålls ovan). Installation av VMware-verktyg resulterar i en konfiguration som inte stöds.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Steg 3: starta den virtuella enheten och hämta IP-adressen
Utför följande steg för att starta den virtuella enheten och ansluta till den.

#### <a name="to-start-the-virtual-device"></a>Så startar du den virtuella enheten
1. Starta den virtuella enheten. I vSphere Configuration Manager i det vänstra fönstret väljer du din enhet och högerklickar för att öppna snabb menyn. Välj **Power** (Av/på) och sedan **Power on** (På). Den virtuella datorn bör aktiveras. Du kan visa statusen i det nedre fönstret med **senaste aktiviteter** i vSphere-klienten.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Det tar några minuter att slutföra installations uppgifterna. När enheten är igång navigerar du till fliken **konsol** . skicka CTRL + ALT + Delete för att logga in på enheten. Alternativt kan du peka markören i konsol fönstret och trycka på CTRL + ALT + INSERT. Standard användaren är *StorSimpleAdmin* och standard lösen ordet är *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Av säkerhetsskäl upphör enhetens administratörslösenord vid första inloggningen. Du uppmanas att ändra lösenordet.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Ange ett lösenord som innehåller minst 8 tecken. Lösen ordet måste innehålla 3 av 4 av dessa krav: versaler, gemener, siffror och specialtecken. Ange lösenordet igen för att bekräfta det. Du får ett meddelande om att lösen ordet har ändrats.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. När lösen ordet har ändrats kan den virtuella enheten startas om. Vänta tills omstarten har slutförts. Windows PowerShell-konsolen för enheten kan visas tillsammans med en förlopps indikator.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Steg 6–8 gäller bara när du startar i en icke-DHCP-miljö. Om du använder en DHCP-miljö hoppar du över dessa steg och går till steg 9. Om du har startat enheten i icke-DHCP-miljö visas följande skärm bild.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Konfigurera sedan nätverket.
7. Använd `Get-HcsIpAddress` kommandot för att visa en lista över nätverks gränssnitt som är aktiverade på den virtuella enheten. Om enheten har ett enda nätverksgränssnitt aktiverad är det tilldelade standardnamnet för gränssnittet `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Använd cmdleten `Set-HcsIpAddress` för att konfigurera nätverket. Ett exempel på detta visas nedan:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. När den inledande installationen är klar och enheten har startats visas enhetens banderollstext. Anteckna den IP-adress och den URL som visas i banderollstexten för att hantera enheten. Du kommer att använda den här IP-adressen för att ansluta till webb gränssnittet för den virtuella enheten och slutföra den lokala installationen och registreringen.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. Valfritt Utför bara det här steget om du distribuerar din enhet i det offentliga molnet. Du kommer nu att aktivera FIPS-läget (USA Federal Information Processing Standard) på enheten. FIPS 140-standarden definierar kryptografiska algoritmer som godkänns för användning av amerikanska federala myndighets dator system för skydd av känsliga data.

    1. Om du vill aktivera FIPS-läget kör du följande cmdlet:

        `Enable-HcsFIPSMode`
    2. Starta om enheten när du har aktiverat FIPS-läget så att de kryptografiska verifieringarna börjar gälla.

       > [!NOTE]
       > Du kan antingen aktivera eller inaktivera FIPS-läge på enheten. Det finns inte stöd för att växla mellan enheter mellan FIPS-och icke-FIPS-läge.
       >
       >

Om enheten inte uppfyller minimikraven för konfiguration visas ett felmeddelande i banderollstexten (visas nedan). Du måste ändra enhetskonfigurationen så att den har tillräckliga resurser för att uppfylla minimikraven. Du kan sedan starta om och ansluta till enheten. Se till minikraven för konfiguration i [Steg 1: Kontrollera att värdsystemet uppfyller minimikraven för virtuella enheter](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Om du möter andra fel under den inledande konfigurationen med hjälp av det lokala webb gränssnittet, se följande arbets flöden:

* Kör diagnostiska tester för att [Felsöka konfiguration av webb gränssnitt](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generera logg paket och visa loggfiler](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Nästa steg
* [Konfigurera din virtuella StorSimple-matris som en fil Server](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurera din virtuella StorSimple-matris som en iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md)
