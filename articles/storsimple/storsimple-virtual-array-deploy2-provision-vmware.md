---
title: Etablering StorSimple Virtual Array i VMware
description: Den här andra självstudien i StorSimple Virtual Array-distributionsserien innebär att en virtuell enhet etableras i VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f5ded3faec3a080022eea70de2cca5d27529c4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76272098"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Distribuera StorSimple Virtual Array - Etablering i VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Den här självstudien beskriver hur du etablerar och ansluter till en StorSimple Virtual Array på ett värdsystem som kör VMware ESXi 5.0, 5.5, 6.0 eller 6.5. Den här artikeln gäller distributionen av StorSimple Virtual Arrays i Azure-portalen och Microsoft Azure Government Cloud.

Du måste ha administratörsbehörighet för att etablera och ansluta till en virtuell enhet. Etableringen och den inledande installationen kan ta ungefär 10 minuter att slutföra.

## <a name="provisioning-prerequisites"></a>Etableringsförutsättningarna
Förutsättningarna för att etablera en virtuell enhet på ett värdsystem som kör VMware ESXi 5.0, 5.5, 6.0 eller 6.5 är följande.

### <a name="for-the-storsimple-device-manager-service"></a>För StorSimple Device Manager-tjänsten
Innan du börjar bör du kontrollera att:

* Du har slutfört alla steg i [Förbered portalen för StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Du har hämtat den virtuella enhetsavbildningen för VMware från Azure-portalen. Mer information finns i **steg 3: Hämta den virtuella enhetsbilden** av [Förbered portalen för StorSimple Virtual Array guide](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>För den virtuella StorSimple-enheten
Innan du distribuerar en virtuell enhet kontrollerar du att:

* Du har tillgång till ett värdsystem som kör Hyper-V (2008 R2 eller senare) som kan användas för att etablera en enhet.
* Värdsystemet kan dedikera följande resurser för att etablera den virtuella enheten:

  * Minst 4 kärnor.
  * Minst 8 GB RAM. Om du planerar att konfigurera den virtuella matrisen som filserver stöder 8 GB mindre än 2 miljoner filer. Du behöver 16 GB RAM för att stödja 2 - 4 miljoner filer.
  * Ett nätverksgränssnitt.
  * En virtuell disk på 500 GB för systemdata.

### <a name="for-the-network-in-datacenter"></a>För nätverket i datacentret
Innan du börjar bör du kontrollera att:

* Du har granskat nätverkskraven för att distribuera en StorSimple-virtuell enhet och konfigurerat datacenternätverket enligt kraven. 

## <a name="step-by-step-provisioning"></a>Stegvis etablering
Om du vill etablera och ansluta till en virtuell enhet måste du utföra följande steg:

1. Se till att värdsystemet har tillräckliga resurser för att uppfylla minimikraven för virtuella enheter.
2. Etablera en virtuell enhet i hypervisorn.
3. Starta den virtuella enheten och hämta IP-adressen.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Steg 1: Se till att värdsystemet uppfyller minimikraven för virtuella enheter
Om du vill skapa en virtuell enhet behöver du:

* Tillgång till ett värdsystem som kör VMware ESXi Server 5.0, 5.5, 6.0 eller 6.5.
* VMware vSphere-klient i systemet för att hantera ESXi-värden.

  * Minst 4 kärnor.
  * Minst 8 GB RAM. Om du planerar att konfigurera den virtuella matrisen som filserver stöder 8 GB mindre än 2 miljoner filer. Du behöver 16 GB RAM för att stödja 2 - 4 miljoner filer.
  * Ett nätverksgränssnitt anslutet till det nätverk som kan dirigera trafik till Internet. Den minsta internetbandbredden bör vara 5 Mbit/s för att möjliggöra optimal bearbetning av enheten.
  * En virtuell disk på 500 GB för data.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Steg 2: Etablera en virtuell enhet i hypervisor
Utför följande steg för att etablera en virtuell enhet i ditt hypervisor-program.

1. Kopiera avbildningen av den virtuella enheten i ditt system. Du har hämtat den här virtuella avbildningen via Azure-portalen.

   1. Kontrollera att du har hämtat den senaste bildfilen. Om du hämtade bilden tidigare laddar du ned den igen för att se till att du har den senaste bilden. Den senaste bilden har två filer (i stället för en).
   2. Anteckna den plats dit du har kopierat avbildningen eftersom du använder den här avbildningen senare i proceduren.

2. Logga in på ESXi-servern med vSphere-klienten. Du måste ha administratörsbehörighet för att kunna skapa en virtuell dator.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. Välj ESXi-servern i lageravsnittet i den vänstra rutan i vSphere-klienten.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Ladda upp VMDK till ESXi-servern. Navigera till fliken **Konfiguration** i den högra rutan. Under **Maskinvara**väljer du **Lagring**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. I det högra fönstret, under **Datastores** (Datalager), väljer du det datalager där du vill ladda upp VMDK. Datalagret måste ha tillräckligt med ledigt utrymme för operativsystemet och datadiskarna.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Högerklicka och välj **Browse Datastore** (Bläddra i datalager).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Ett **Datastore Browser**-fönster öppnas.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Klicka på ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ikonen i verktygsfältet för att skapa en ny mapp. Ange mappnamnet och anteckna det. Du använder det här mappnamnet senare när du skapar en virtuell dator (rekommenderad bästa praxis). Klicka på **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Den nya mappen visas i det vänstrat fönstret i **Datastore Browser**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klicka på ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) ikonen Ladda upp och välj **Ladda upp fil**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Bläddra till och peka på de VMDK-filer du har laddat ned. Det finns två filer. Välj en fil att ladda upp.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klicka på **Open** (Öppna). Uppladdningen av VMDK-filen till det angivna datalagret startar. Det kan ta flera minuter för filen att laddas upp.
13. När uppladdningen är klar visas filen i datalagret i den mapp du har skapat.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Ladda nu upp den andra VMDK-filen till samma datalager.
14. Återgå till vSphere-klientfönstret. När ESXi-servern är markerad högerklickar du och väljer **Ny virtuell dator**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Ett fönstret **Skapa ny virtuell dator** visas. Välj alternativet **Anpassad** på sidan **Konfiguration.** Klicka på **Nästa**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. På sidan **Namn och plats** anger du namnet på den virtuella datorn. Det här namnet ska matcha mappnamnet (rekommenderad bästa praxis) som du angav tidigare i steg 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. På sidan **Lagring** väljer du ett datalager som du vill använda för att etablera den virtuella datorn.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. På sidan **Version av virtuell dator** väljer du Version för virtuell **dator: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. På sidan **Gästoperativsystem** väljer du **gästoperativsystemet** som **Windows**. För **Version**väljer du Microsoft **Windows Server 2012 (64-bitars) i**listrutan .

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. På sidan **Processorer** **justerar du antalet virtuella sockets** och **antal kärnor per virtuell socket** så att det totala antalet **kärnor** är 4 (eller mer). Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. På **sidan Minne** anger du 8 GB (eller mer) RAM-minne. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Ange numret på nätverksgränssnitten på sidan **Nätverk.** Minimikravet är ett nätverksgränssnitt.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. På sidan **SCSI-styrenhet** godkänner du **standard-LSI Logic SAS-styrenheten**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. På sidan **Välj en disk** väljer du Använd en befintlig virtuell **disk**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Klicka på **Bläddra**under Sök efter **filsökväg**på sidan **Välj befintlig disk.** Då öppnas dialogrutan **Bläddra i datalager.** Navigera till den plats där du laddade upp VMDK. Nu ser du bara en fil i datalagret eftersom de två filer som du ursprungligen laddade upp har sammanfogats. Markera filen och klicka på **OK**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. På sidan **Avancerade alternativ** godkänner du standardinställningen och klickar på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. På sidan **Ready to Complete** (Redo att slutföra)granskar du alla inställningar associerade med den nya virtuella datorn. Kontrollera **Redigera inställningarna för den virtuella datorn innan du är klar**. Klicka på **Fortsätt**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Leta reda på enhetens **Hardware** maskinvara på sidan **Egenskaper för virtuella datorer.** Välj **Ny hårddisk**. Klicka på **Lägg till**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Du ser ett **fönstret Lägg till maskinvara.** Välj Hårddisk under **Välj vilken typ av enhet du vill lägga till**på sidan **Enhetstyp** och klicka på **Nästa**. **Hard Disk**

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. På sidan **Välj en disk** väljer du Skapa en ny virtuell **disk**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Ändra **diskstorleken** till 500 GB (eller mer) på sidan **Skapa en disk.** 500 GB är minimikravet, men du kan alltid etablera en större disk. Observera att du inte kan expandera eller förminska disken när den har etablerats. Mer information om storleken på disken som ska etableras finns i storleksfältet i [dokumentet med metodtips](storsimple-ova-best-practices.md). Under **Disketablering**väljer du **Tunn etablering**. Klicka på **Nästa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. På sidan **Avancerade alternativ** godkänner du standardinställningen.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Granska diskalternativen på sidan **Klar att slutföra.** Klicka på **Slutför**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Gå tillbaka till sidan Egenskaper för virtuella datorer. En ny hårddisk läggs till på den virtuella datorn. Klicka på **Slutför**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. När den virtuella datorn har valts i den högra rutan navigerar du till fliken **Sammanfattning.** Granska inställningarna för den virtuella datorn.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Den virtuella datorn är nu etablerad. Nästa steg är att aktivera datorn och hämta IP-adressen.

> [!NOTE]
> Vi rekommenderar att du inte installerar VMware-verktyg på den virtuella matrisen (enligt ovan). Installation av VMware-verktyg resulterar i en konfiguration som inte stöds.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Steg 3: Starta den virtuella enheten och få IP
Utför följande steg för att starta den virtuella enheten och ansluta till den.

#### <a name="to-start-the-virtual-device"></a>Så startar du den virtuella enheten
1. Starta den virtuella enheten. I konfigurationshanteraren vSphere i den vänstra rutan väljer du enheten och högerklickar för att visa snabbmenyn. Välj **Power** (Av/på) och sedan **Power on** (På). Den virtuella datorn bör aktiveras. Du kan visa status i fönstret **Senaste aktiviteter** i vSphere-klienten.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Inställningsuppgifterna tar några minuter att slutföra. När enheten är igång navigerar du till fliken **Konsol.** Skicka Ctrl+Alt+Delete för att logga in på enheten. Du kan också peka markören i konsolfönstret och trycka på Ctrl+Alt+Infoga. Standardanvändaren är *StorSimpleAdmin* och standardlösenordet är *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Av säkerhetsskäl upphör enhetens administratörslösenord vid första inloggningen. Du uppmanas att ändra lösenordet.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Ange ett lösenord som innehåller minst 8 tecken. Lösenordet måste innehålla 3 av 4 av dessa krav: versaler, gemener, numeriska och specialtecken. Ange lösenordet igen för att bekräfta det. Du kommer att meddelas om att lösenordet har ändrats.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. När lösenordet har ändrats kan den virtuella enheten startas om. Vänta tills omstarten är klar. Enhetens Windows PowerShell-konsol kan visas tillsammans med ett förloppsfält.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Steg 6–8 gäller bara när du startar i en icke-DHCP-miljö. Om du använder en DHCP-miljö hoppar du över dessa steg och går till steg 9. Om du har startat upp enheten i icke-DHCP-miljö visas följande skärm.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Konfigurera sedan nätverket.
7. Använd `Get-HcsIpAddress` kommandot för att lista de nätverksgränssnitt som är aktiverade på den virtuella enheten. Om enheten har ett enda nätverksgränssnitt aktiverad är det tilldelade standardnamnet för gränssnittet `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Använd cmdleten `Set-HcsIpAddress` för att konfigurera nätverket. Ett exempel på detta visas nedan:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. När den inledande installationen är klar och enheten har startats visas enhetens banderollstext. Anteckna den IP-adress och den URL som visas i banderollstexten för att hantera enheten. Du kommer att använda den här IP-adressen för att ansluta till webbgränssnittet för din virtuella enhet och slutföra den lokala installationen och registreringen.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Valfritt) Gör bara det här steget om du distribuerar enheten i myndighetsmolnet. Du aktiverar nu FIPS-läget (Federal Information Processing Standard) på enheten. FIPS 140-standarden definierar kryptografiska algoritmer som godkänts för användning av amerikanska federala myndighetsdatasystem för skydd av känsliga data.

    1. Om du vill aktivera FIPS-läget kör du följande cmdlet:

        `Enable-HcsFIPSMode`
    2. Starta om enheten när du har aktiverat FIPS-läget så att kryptografiska valideringar börjar gälla.

       > [!NOTE]
       > Du kan antingen aktivera eller inaktivera FIPS-läge på enheten. Det går inte att växla mellan FIPS- och icke-FIPS-läge.
       >
       >

Om enheten inte uppfyller minimikraven för konfiguration visas ett felmeddelande i banderollstexten (visas nedan). Du måste ändra enhetskonfigurationen så att den har tillräckliga resurser för att uppfylla minimikraven. Du kan sedan starta om och ansluta till enheten. Se till minikraven för konfiguration i [Steg 1: Kontrollera att värdsystemet uppfyller minimikraven för virtuella enheter](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Om du stöter på något annat fel under den första konfigurationen med hjälp av det lokala webbgränssnittet läser du följande arbetsflöden:

* Kör diagnostiska tester för att [felsöka konfigurationen av webbgränssnitt .](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)
* [Generera loggpaket och visa loggfiler](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Nästa steg
* [Konfigurera den virtuella storsimple-matrisen som en filserver](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurera din Virtuella StorSimple-matris som en iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md)
