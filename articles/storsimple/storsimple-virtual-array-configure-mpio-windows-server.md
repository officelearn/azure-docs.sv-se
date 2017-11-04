---
title: "Konfigurerar MPIO på värden är ansluten till virtuella StorSimple-matris | Microsoft Docs"
description: "Beskriver hur du konfigurerar MPIO (Multipath I/O) för din virtuella StorSimple-matrisen ansluten till en värd som kör Windows Server 2012 R2."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5b7a7f99-ee5b-4b7d-ab32-483a5a1fa504
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/01/2017
ms.author: alkohli
ms.openlocfilehash: c75c6ed40754aee964e2b68f4f569dc1422507f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Konfigurera Multipath i/o på Windows Server-värd för den virtuella StorSimple-matrisen
## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du installerar funktionen Multipath I/O (MPIO) på Windows Server-värd, tillämpa specifika konfigurationsinställningar för endast StorSimple-volymer och kontrollera sedan MPIO för StorSimple-volymer. Proceduren förutsätter att din virtuella StorSimple 1200-matris med två nätverksgränssnitt är ansluten till en Windows Server-värd med två nätverksgränssnitt. Informationen i den här artikeln gäller enbart för den virtuella matrisen. Information om StorSimple 8000-serien enheter går du till [konfigurera MPIO för StorSimple värden](storsimple-configure-mpio-windows-server.md). 

MPIO-funktionen i Windows Server hjälper dig att skapa hög tillgänglighet, feltolerant lagringskonfigurationer. MPIO använder redundanta fysiska sökvägskomponenter – nätverkskort, kablar och växlar – att skapa logiska sökvägar mellan servern och lagringsenheten. Om det finns ett komponentfel orsakar logiska sökvägen misslyckas, använder multipathing-logik en alternativ sökväg för I/O så att program fortfarande kommer åt sina data. Dessutom beroende på din konfiguration kan MPIO också förbättra prestanda genom att belastningsutjämning mellan dessa sökvägar. Mer information finns i [översikt över MPIO](https://technet.microsoft.com/library/cc725907.aspx "MPIO översikt och funktioner").

Konfigurera MPIO på Windows Server-värdar som är anslutna till din virtuella StorSimple-matris (model 1200) för den hög tillgängligheten i StorSimple-lösningen. Värdservrar tolereras sedan en länk, nätverk eller fel. 

Du behöver att följa dessa steg för att konfigurera MPIO: 

* Förutsättningar för konfiguration
* Steg 1: Installera MPIO på Windows Server-värden
* Steg 2: Konfigurera MPIO för StorSimple-volymer
* Steg 3: Montera StorSimple-volymer på värden

Var och en av dessa steg beskrivs i följande avsnitt.

## <a name="prerequisites"></a>Krav
Det här avsnittet beskrivs konfigurationskraven för Windows Server-värden och virtuella matrisen.

### <a name="on-windows-server-host"></a>På Windows Server-värd
* Se till att Windows Server-värd har 2 nätverksgränssnitt som är aktiverad.

### <a name="on-storsimple-virtual-array"></a>På virtuella StorSimple-matris
* Virtuella matrisen ska konfigureras som en iSCSI-server. Läs mer i [Konfigurera virtuella matris som en iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md). En eller flera nätverksgränssnitt måste vara aktiverad på matrisen.   
* Nätverksgränssnitt på din virtuella matrisen ska kunna nås från Windows Server-värd.
* En eller flera volymer ska skapas på din virtuella StorSimple-matrisen. Läs mer i [lägga till en volym](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume) på din virtuella StorSimple-matrisen. I den här proceduren skapat vi 3 volymer (1 lokalt Fäst och 2 nivåindelade volymer som visas nedan) på virtuella matrisen.
  
    ![mpio0](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>Maskinvarukonfiguration för virtuell StorSimple-matris
Bilden nedan visar maskinvarukonfiguration för hög tillgänglighet och belastningsutjämning MPIO för din Windows Server-värden och virtuella StorSimple matris som används i den här proceduren.

![maskinvarukonfiguration för MPIO](./media/storsimple-virtual-array-configure-mpio-windows-server/1200hardwareconfig.png)

Som visas i föregående bild:

* Din virtuella StorSimple-matris som har etablerats på Hyper-V är en enskild nod active enhet som konfigurerats som en iSCSI-server.
* Två virtuella nätverksgränssnitt är aktiverade på matrisen. I det lokala webbgränssnittet för ditt virtuella matrisen, kontrollera att två nätverksgränssnitt har aktiverats genom att gå till **nätverksinställningar** enligt nedan:
  
    ![Nätverksgränssnitt som är aktiverad på 1200](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio9.png)
  
    Notera IPv4-adresser aktiverade nätverkskort (Ethernet, Ethernet 2 som standard) och spara för senare användning på värden.
* Två nätverksgränssnitt är aktiverade på Windows Server-värd. Om de anslutna gränssnitt för värden och matrisen är i samma undernät, att kommer det finnas 4 sökvägar tillgängliga. Detta var skiftläget för den här proceduren. Men om varje nätverksgränssnitt på gränssnittet för matrisen och värden är i ett annat IP-undernät (och inte dirigerbara) kan blir endast 2 sökvägar tillgängliga.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Steg 1: Installera MPIO på Windows Server-värden
MPIO är en valfri funktion i Windows Server och installeras inte som standard. Den installeras som en funktion via Server Manager. Slutför följande procedur om du vill installera funktionen på Windows Server-värd.

[!INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Steg 2: Konfigurera MPIO för StorSimple-volymer
MPIO måste konfigureras för att identifiera StorSimple-volymer. Utför följande steg för att konfigurera MPIO för att identifiera StorSimple-volymer.

[!INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Steg 3: Montera StorSimple-volymer på värden
När MPIO har konfigurerats på Windows Server, volymerna som skapats på StorSimple-matrisen kan monteras och sedan dra nytta av MPIO för redundans. Utför följande steg om du vill montera en volym.

#### <a name="to-mount-volumes-on-the-host"></a>Montera volymer på värden
1. Öppna den **iSCSI-Initieraregenskaper** fönster på Windows Server-värden. Gå till **Serverhanteraren > instrumentpanelen > Verktyg > iSCSI-initieraren**.
2. I den **iSCSI-Initieraregenskaper** dialogrutan klickar du på **identifiering**, och klicka sedan på **identifiera målportal**.
3. I den **identifiera målportal** dialogrutan Gör följande:
   
    1. Ange IP-adressen för det första aktivera nätverksgränssnittet på din virtuella StorSimple-matrisen. Detta är som standard **Ethernet**. 
    2. Klicka på **OK** att återgå till den **iSCSI-Initieraregenskaper** dialogrutan.
     
    > [!IMPORTANT]
    > Om du använder ett privat nätverk för iSCSI-anslutningar, ange IP-adressen för DATA-porten som är ansluten till det privata nätverket.
     
4. Upprepa steg 2 – 3 för ett andra nätverksgränssnitt (exempelvis Ethernet 2) på matrisen. 
5. Välj den **mål** fliken i den **iSCSI-Initieraregenskaper** dialogrutan. För din virtuella matrisen du bör se varje volym yta som mål under **upptäckta mål**. I det här fallet skulle tre mål (motsvarande tre volymer) identifieras.
   
    ![mpio1](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio1.png)
6. Klicka på **Anslut** att upprätta en iSCSI-session med din virtuella StorSimple-matris. En **Anslut till målet** visas dialogrutan. Välj den **aktivera Multipath** kryssrutan. Klicka på **avancerade**.
   
    ![mpio2](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio2.png)
7. I den **avancerade inställningar** dialogrutan Gör följande:                                        
   
    1. På den **lokala kortet** listrutan, Välj **Microsoft iSCSI Initiator**.
    2. På den **initieraren IP** listrutan väljer du IP-adressen för värden.
    3. På den **målportal** IP i listrutan väljer du IP-Adressen för gränssnittet för matrisen.
    4. Klicka på **OK** att återgå till den **iSCSI-Initieraregenskaper** dialogrutan.
     
        ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

8. Klicka på **Egenskaper**. 
   
    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)

9. I den **egenskaper** dialogrutan klickar du på **lägga till sessionen**.
   
   ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)
10. I den **Anslut till målet** dialogrutan markerar du den **aktivera Multipath** kryssrutan. Klicka på **avancerade**.
11. I den **avancerade inställningar** dialogrutan:                                        
    
    1. På den **lokala kortet** listrutan, Välj Microsoft iSCSI-initieraren.

    2. På den **initieraren IP** listrutan väljer du den IP-adress som motsvarar värden. I så fall måste ansluter du två nätverksgränssnitt på matrisen till ett nätverksgränssnitt på värden. Därför är det här gränssnittet på samma sätt som angetts för den första sessionen.

    3. På den **Target Portal IP** listrutan, Välj IP-adressen för gränssnittet andra data aktiverat på matrisen.

    4. Klicka på **OK** kan gå tillbaka till dialogrutan iSCSI-Initieraregenskaper. Du har lagt till ytterligare en session till målet.
      
       ![mpio11](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio11.png)
    
    5. När du lägger till önskade sessioner (sökvägar), i den **iSCSI-Initieraregenskaper** dialogrutan Välj målobjektet och klickar på **egenskaper**. På fliken sessioner i den **egenskaper** dialogrutan, anteckning fyra sessionen identifierare som motsvarar möjliga sökvägar permutationer. Avbryta en session, markera kryssrutan bredvid ett sessions-ID och klicka sedan på **frånkoppling**.

    6. Om du vill visa enheter som visas i sessioner, Välj den **enheter** fliken. Om du vill konfigurera MPIO-principen för en vald enhet klickar du på **MPIO**.

    7. Den **information** visas dialogrutan. På den **MPIO** fliken kan du välja rätt **princip för belastningsutjämning** inställningar. Du kan också visa den **Active** eller **vänteläge** Sökvägstyp.
12. Upprepa steg 8-11 för att lägga till ytterligare sessioner (sökvägar) till målet. Du kan lägga till summan av fyra sessioner för varje mål med två gränssnitt på värden och två på virtuella matrisen. 
    
    ![mpio14](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio14.png)
13. Du måste upprepa dessa steg för varje volym (hämtar som mål).
    
    ![mpio15](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio15.png)
14. Öppna **Datorhantering** genom att gå till **Serverhanteraren > instrumentpanelen > Datorhantering**. I den vänstra rutan klickar du på **lagring > Diskhantering**. Den volym som skapas på den virtuella StorSimple-matrisen som är synliga för den här värden visas under **Diskhantering** som nya diskarna.
15. Initiera disken och skapa en ny volym. Under formateringsprocessen, väljer du en storlek på allokeringsenhet (Australien) på 64 KB. Upprepa processen för alla tillgängliga volymer.
    
    ![Diskhantering](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio20.png)
16. Under **Diskhantering**, högerklicka på den **Disk** och välj **egenskaper**.
17. I den **Multipath Disk enhetsegenskaper** dialogrutan klickar du på den **MPIO** fliken.
    
    ![Diskegenskaper](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio21.png)
18. I den **DSM namnet** klickar du på **information** och kontrollera att parametrarna är inställda på standardparametrar. Standardparametrar är:
    
    * Kontrollera Period = 30
    * Antal försök = 3
    * PDO ta bort Period = 20
    * Återförsöksintervall = 1
    * Kontrollera aktiverat = avmarkerad.
    
    > [!NOTE]
    > **Ändra inte standardparametrar.**
   
## <a name="next-steps"></a>Nästa steg
Lär dig mer om [använder Enhetshanteraren för StorSimple-tjänsten för att administrera din virtuella StorSimple-matris](storsimple-virtual-array-manager-service-administration.md).

