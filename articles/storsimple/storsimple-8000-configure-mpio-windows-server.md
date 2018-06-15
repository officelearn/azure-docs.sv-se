---
title: Konfigurera MPIO för din StorSimple-enhet | Microsoft Docs
description: Beskriver hur du konfigurerar MPIO (Multipath I/O) för din StorSimple-enhet som är ansluten till en värd som kör Windows Server 2012 R2.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: 4f2b094604f486d283574f4669fcad6f72bd4431
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30245745"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurera MPIO för din StorSimple-enhet

Den här självstudiekursen beskriver de steg du bör följa för att installera och använda funktionen Multipath I/O (MPIO) på en värd som kör Windows Server 2012 R2 och ansluten till en fysiska StorSimple-enheten. Riktlinjerna i den här artikeln gäller StorSimple 8000-serien fysiska enheter. MPIO stöds för närvarande inte på en StorSimple-enhet för molnet.

Microsoft har inbyggt stöd för MPIO (Multipath I/O)-funktion i Windows Server för att skapa hög tillgänglighet, feltolerant iSCSI nätverkskonfigurationer. MPIO använder redundanta fysiska sökvägskomponenter – nätverkskort, kablar och växlar – att skapa logiska sökvägar mellan servern och lagringsenheten. Om det finns ett komponentfel orsakar logiska sökvägen misslyckas, använder multipathing-logik en alternativ sökväg för I/O så att program fortfarande kommer åt sina data. Dessutom beroende på din konfiguration kan MPIO också förbättra prestanda genom ombalansering belastningen över dessa sökvägar. Mer information finns i [översikt över MPIO](https://technet.microsoft.com/library/cc725907.aspx "MPIO översikt och funktioner").

MPIO bör vara konfigurerade på StorSimple-enheten för den hög tillgängligheten i StorSimple-lösningen. När MPIO har installerats på dina värdservrar som kör Windows Server 2012 R2 kan kan servrarna sedan tolerera länken, nätverk och fel.

## <a name="mpio-configuration-summary"></a>Översikt över MPIO-konfiguration

MPIO är en valfri funktion i Windows Server och installeras inte som standard. Den installeras som en funktion via Server Manager.

Följ dessa steg för att konfigurera MPIO på din StorSimple-enhet:

* Steg 1: Installera MPIO på Windows Server-värden
* Steg 2: Konfigurera MPIO för StorSimple-volymer
* Steg 3: Montera StorSimple-volymer på värden
* Steg 4: Konfigurera MPIO för hög tillgänglighet och belastningsutjämning

Var och en av de föregående stegen beskrivs i följande avsnitt.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Steg 1: Installera MPIO på Windows Server-värden

Slutför följande procedur om du vill installera funktionen på Windows Server-värd.

#### <a name="to-install-mpio-on-the-host"></a>Installera MPIO på värden

1. Öppna Serverhanteraren på Windows Server-värd. Serverhanteraren startar som standard när en medlem i gruppen Administratörer loggar in på en dator som kör Windows Server 2012 R2 eller Windows Server 2012. Om Serverhanteraren inte redan är öppen klickar du på **Start > Serverhanteraren**.
   
   ![Server Manager](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Klicka på **Serverhanteraren > instrumentpanelen > Lägg till roller och funktioner**. Detta startar den **Lägg till roller och funktioner** guiden.
   
   ![Guiden Lägg till roller och funktioner 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. I den **Lägg till roller och funktioner** guiden, utför följande steg:
   
   1. På den **innan du börjar** klickar du på **nästa**.
   2. På den **Välj installationstyp** godkänner du standardinställningen **rollbaserad eller funktionsbaserad** installation. Klicka på **Nästa**.
   
       ![Guiden Lägg till roller och funktioner 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. På den **väljer målservern** väljer **Välj en server från serverpoolen**. Värdservern bör identifieras automatiskt. Klicka på **Nästa**.
   4. På den **Välj serverroller** klickar du på **nästa**.
   5. På den **Välj funktioner** väljer **Multipath i/o**, och klicka på **nästa**.
   
       ![Guiden Lägg till roller och funktioner 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. På den **Bekräfta installationsinställningarna** sidan bekräfta valet och välj sedan **starta om målservern automatiskt om det behövs**, enligt nedan. Klicka på **Installera**.
   
       ![Guiden Lägg till roller och funktioner 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Du meddelas när installationen är klar. Stäng guiden genom att klicka på **Stäng**.
   
       ![Guiden Lägg till roller och funktioner 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Steg 2: Konfigurera MPIO för StorSimple-volymer

MPIO måste konfigureras för att identifiera StorSimple-volymer. Utför följande steg för att konfigurera MPIO för att identifiera StorSimple-volymer.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Konfigurera MPIO för StorSimple-volymer

1. Öppna den **MPIO-konfiguration**. Klicka på **Serverhanteraren > instrumentpanelen > Verktyg > MPIO**.
2. I den **MPIO-egenskaper** dialogrutan markerar du den **Upptäck flera sökvägar** fliken.
3. Välj **lägga till stöd för iSCSI-enheter**, och klicka sedan på **Lägg till**.  
   ![Identifiera flera sökvägar för MPIO-egenskaper](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Starta om servern när du tillfrågas.
5. I den **MPIO-egenskaper** dialogrutan klickar du på den **MPIO enheter** fliken. Klicka på **lägga till**.
    </br>![MPIO egenskaper MPIO enheter](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. I den **lägga till stöd för MPIO** dialogrutan under **enhetens maskinvaru-ID**, ange serienumret för enheten. Komma åt Enhetshanteraren för StorSimple-tjänsten för att hämta enhetens serienummer. Gå till **enheter > instrumentpanelen**. Enhetens serienummer visas till höger **snabb i korthet** rutan i instrumentpanelen för enheten.
    </br>
    ![Lägga till stöd för MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Starta om servern när du tillfrågas.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Steg 3: Montera StorSimple-volymer på värden

När MPIO har konfigurerats på Windows Server, volymerna som skapats på StorSimple-enheten kan monteras och sedan dra nytta av MPIO för redundans. Utför följande steg om du vill montera en volym.

#### <a name="to-mount-volumes-on-the-host"></a>Montera volymer på värden

1. Öppna den **iSCSI-Initieraregenskaper** fönster på Windows Server-värden. Klicka på **Serverhanteraren > instrumentpanelen > Verktyg > iSCSI-initieraren**.
2. I den **iSCSI-Initieraregenskaper** dialogrutan, klicka på fliken Identifiering och klicka sedan på **identifiera målportal**.
3. I den **identifiera målportal** dialogrutan utför följande steg:
   
   1. Ange IP-adressen för din StorSimple-enhet DATA port (till exempel ange DATA 0).
   2. Klicka på **OK** att återgå till den **iSCSI-Initieraregenskaper** dialogrutan.
     
     > [!IMPORTANT]
     > **Om du använder ett privat nätverk för iSCSI-anslutningar, ange IP-adressen för DATA-porten som är ansluten till det privata nätverket.**
    
4. Upprepa steg 2 – 3 för ett andra nätverksgränssnitt (till exempel DATA 1) på enheten. Tänk på att dessa gränssnitt ska aktiveras för iSCSI. Mer information finns i [ändra nätverksgränssnitt](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Välj den **mål** fliken i den **iSCSI-Initieraregenskaper** dialogrutan. Du bör se StorSimple-enheten mål IQN under **upptäckta mål**.

   ![iSCSI-initieraren egenskaper för mål](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Klicka på **Anslut** att upprätta en iSCSI-session med din StorSimple-enhet. En **Anslut till målet** dialogrutan visas.
7. I den **Anslut till målet** dialogrutan markerar du den **aktivera Multipath** kryssrutan. Klicka på **avancerade**.
8. I den **avancerade inställningar** dialogrutan utför följande steg:
   
   1. På den **lokala kortet** listrutan, Välj **Microsoft iSCSI Initiator**.
   2. På den **initieraren IP** listrutan väljer du IP-adressen för värden.
   3. På den **målportal** IP i listrutan väljer du IP-Adressen för enhetsgränssnittet.
   4. Klicka på **OK** att återgå till den **iSCSI-Initieraregenskaper** dialogrutan.
9. Klicka på **Egenskaper**. I den **egenskaper** dialogrutan klickar du på **lägga till sessionen**.
10. I den **Anslut till målet** dialogrutan markerar du den **aktivera Multipath** kryssrutan. Klicka på **avancerade**.
11. I den **avancerade inställningar** dialogrutan:

    1. På den **lokala kortet** listrutan, Välj Microsoft iSCSI-initieraren.
    2. På den **initieraren IP** listrutan väljer du den IP-adress som motsvarar värden. I så fall måste ansluter du två nätverksgränssnitt på enheten till ett nätverksgränssnitt på värden. Därför är det här gränssnittet på samma sätt som angetts för den första sessionen.
    3. På den **Target Portal IP** listrutan, Välj IP-adressen för gränssnittet andra data aktiverad på enheten.
    4. Klicka på **OK** kan gå tillbaka till dialogrutan iSCSI-Initieraregenskaper. Du har lagt till ytterligare en session till målet.
12. Öppna **Datorhantering** genom att gå till **Serverhanteraren > instrumentpanelen > Datorhantering**. I den vänstra rutan klickar du på **lagring > Diskhantering**. Den volym som har skapats på StorSimple-enheten som är synliga för den här värden visas under **Diskhantering** som nya diskarna.
13. Initiera disken och skapa en ny volym. Under formateringsprocessen, väljer du en blockstorlek på 64 KB.
    
    ![Diskhantering](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Under **Diskhantering**, högerklicka på den **Disk** och välj **egenskaper**.
15. I StorSimple-modellen ### **Multipath Disk enhetsegenskaper** dialogrutan klickar du på den **MPIO** fliken.
    
    ![StorSimple 8100 Disk med flera sökvägar DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. I den **DSM namnet** klickar du på **information** och kontrollera att parametrarna är inställda på standardparametrar. Standardparametrar är:
    
    * Kontrollera Period = 30
    * Antal försök = 3
    * PDO ta bort Period = 20
    * Återförsöksintervall = 1
    * Kontrollera aktiverat = avmarkerad.

> [!NOTE]
> **Ändra inte standardparametrar.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Steg 4: Konfigurera MPIO för hög tillgänglighet och belastningsutjämning

För Multipath-baserad hög tillgänglighet och belastningsutjämning, måste flera sessioner läggas till manuellt deklarera olika sökvägar som är tillgängliga. Till exempel om värden har två gränssnitt som är ansluten till iSCSI-nätverk och enheten har två gränssnitt som är ansluten till iSCSI-nätverk och du behöver fyra sessioner som är konfigurerad med rätt sökväg permutationer (endast två sessioner måste utföras om varje gränssnitt för DATA och värd-gränssnittet är i ett annat IP-undernät och är inte dirigerbart).

**Vi rekommenderar att du har minst 8 parallella aktiva sessioner mellan enheten och programmet-värden.** Detta kan uppnås genom att aktivera 4 nätverkskort på Windows Server-systemet. Använda fysiska nätverkskort eller virtuella gränssnitt via nätverket virtualiseringsteknik vilken maskinvara eller operativsystem på Windows Server-värd. Med två nätverksgränssnitt på enheten leder den här konfigurationen 8 aktiva sessioner. Den här konfigurationen hjälper till att optimera genomflödet enheten och i molnet.

> [!IMPORTANT]
> **Vi rekommenderar att du inte blandar 1 GbE och 10 GbE-nätverkskort. Om du använder två nätverksgränssnitt ska båda gränssnitten vara av en typ av identiska.**

Följande procedur beskriver hur du lägger till sessioner när en StorSimple-enhet med två nätverkskort är anslutet till en värd med två nätverksgränssnitt. Detta ger dig endast 4 sessioner. Använd samma procedur med en StorSimple-enhet med två nätverksgränssnitt som är ansluten till en värd med fyra nätverksgränssnitt. Du behöver konfigurera 8 i stället för 4 sessioner som beskrivs här.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Konfigurera MPIO för hög tillgänglighet och belastningsutjämning

1. Utföra en identifiering av mål: i den **iSCSI-Initieraregenskaper** dialogrutan den **identifiering** klickar du på **identifiera Portal**.
2. I den **Anslut till målet** dialogrutan Ange IP-adressen för ett nätverksgränssnitt för enheten.
3. Klicka på **OK** att återgå till den **iSCSI-Initieraregenskaper** dialogrutan.
4. I den **iSCSI-Initieraregenskaper** dialogrutan markerar du den **mål** fliken Markera upptäckta mål och klicka sedan på **Anslut**. Den **Anslut till målet** dialogrutan visas.
5. I den **Anslut till målet** dialogrutan:
   
   1. Lämna standardinställningen markerade Målinställningar för **lägga till den här anslutningen** i listan över favorit mål. Det gör att enheten automatiskt att försöka starta om anslutningen varje gång datorn startas om.
   2. Välj den **aktivera Multipath** kryssrutan.
   3. Klicka på **avancerade**.
6. I den **avancerade inställningar** dialogrutan:
   
   1. På den **lokala kortet** listrutan, Välj **Microsoft iSCSI Initiator**.
   2. På den **initieraren IP** listrutan väljer du den IP-adress som motsvarar det första gränssnittet på värden (iSCSI-gränssnitt).
   3. På den **Target Portal IP** listrutan, Välj IP-adressen för gränssnittet första data aktiverad på enheten.
   4. Klicka på **OK** kan gå tillbaka till dialogrutan iSCSI-Initieraregenskaper.
7. Klicka på **egenskaper**, och i den **egenskaper** dialogrutan klickar du på **lägga till sessionen**.
8. I den **Anslut till målet** dialogrutan markerar du den **aktivera Multipath** kryssrutan och klicka sedan på **Avancerat**.
9. I den **avancerade inställningar** dialogrutan:
   
   1. På den **lokala kortet** listrutan, Välj **Microsoft iSCSI Initiator**.
   2. På den **initieraren IP** listrutan väljer du den IP-adress som motsvarar det andra iSCSI-gränssnittet på värden.
   3. På den **Target Portal IP** listrutan, Välj IP-adressen för gränssnittet andra data aktiverad på enheten.
   4. Klicka på **OK** att återgå till den **iSCSI-Initieraregenskaper** dialogrutan. Du har nu lagt till ytterligare en session till målet.
10. Upprepa steg 8-10 för att lägga till ytterligare sessioner (sökvägar) till målet. Du kan lägga till summan av fyra sessioner med två gränssnitt på värden och två på enheten.
11. När du lägger till önskade sessioner (sökvägar), i den **iSCSI-Initieraregenskaper** dialogrutan Välj målobjektet och klickar på **egenskaper**. På fliken sessioner i den **egenskaper** dialogrutan, anteckning fyra sessionen identifierare som motsvarar möjliga sökvägar permutationer. Avbryta en session, markera kryssrutan bredvid ett sessions-ID och klicka sedan på **frånkoppling**.
12. Om du vill visa enheter som visas i sessioner, Välj den **enheter** fliken. Om du vill konfigurera MPIO-principen för en vald enhet klickar du på **MPIO**. Den **enhetsinformation** dialogrutan visas. På den **MPIO** fliken kan du välja rätt **princip för belastningsutjämning** inställningar. Du kan också visa den **Active** eller **vänteläge** Sökvägstyp.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [använder StorSimple enheten Manager-tjänsten för att ändra din StorSimple-enhetskonfiguration](storsimple-8000-modify-device-config.md).

