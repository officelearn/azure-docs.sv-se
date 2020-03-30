---
title: Konfigurera MPIO för Din StorSimple-enhet | Microsoft-dokument
description: I artikeln beskrivs hur du konfigurerar Multipath I/O (MPIO) fÃ¶r storsimple-enheten som är ansluten till en värd som kör Windows Server 2012 R2.
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
ms.openlocfilehash: eda134257edb851eea076459b44e02fc59028f46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60363400"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurera Multipath I/O för StorSimple-enheten

I den här självstudien beskrivs de steg du bör följa för att installera och använda funktionen Multipath I/O (MPIO) på en värd som kör Windows Server 2012 R2 och som är ansluten till en fysisk StorSimple-enhet. Vägledningen i den här artikeln gäller endast fysiska enheter i StorSimple 8000-serien. MPIO stöds för närvarande inte på en StorSimple Cloud Appliance.

Microsoft har skapat stöd för funktionen Multipath I/O (MPIO) i Windows Server för att skapa högtillgänglig, feltoleranta iSCSI-nätverkskonfigurationer. MPIO använder redundanta fysiska sökvägskomponenter – adaptrar, kablar och växlar – för att skapa logiska sökvägar mellan servern och lagringsenheten. Om det finns ett komponentfel, vilket gör att en logisk sökväg misslyckas, använder multipathing logic en alternativ sökväg för I/O så att program fortfarande kan komma åt sina data. Dessutom beroende på din konfiguration kan MPIO också förbättra prestanda genom att balansera om belastningen över alla dessa sökvägar. Mer information finns i [MPIO översikt](https://technet.microsoft.com/library/cc725907.aspx "MPIO översikt och funktioner").

För att storsimple-lösningen ska vara hög tillgänglighet bör MPIO konfigureras på din StorSimple-enhet. När MPIO är installerat på värdservrarna som kör Windows Server 2012 R2 kan servrarna sedan tolerera ett länk-, nätverks- eller gränssnittsfel.

## <a name="mpio-configuration-summary"></a>Sammanfattning av MPIO-konfiguration

MPIO är en valfri funktion på Windows Server och installeras inte som standard. Den installeras som en funktion via Server Manager.

Så här konfigurerar du MPIO på StorSimple-enheten:

* Steg 1: Installera MPIO på Windows Server-värden
* Steg 2: Konfigurera MPIO för StorSimple-volymer
* Steg 3: Montera StorSimple-volymer på värden
* Steg 4: Konfigurera MPIO för hög tillgänglighet och belastningsutjämning

Vart och ett av föregående steg beskrivs i följande avsnitt.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Steg 1: Installera MPIO på Windows Server-värden

Om du vill installera den här funktionen på windows servervärden slutför du följande procedur.

#### <a name="to-install-mpio-on-the-host"></a>Så här installerar du MPIO på värden

1. Öppna Serverhanteraren på värdvärden för Windows Server. Som standard startar Serverhanteraren när en medlem i gruppen Administratörer loggar in på en dator med Windows Server 2012 R2 eller Windows Server 2012. Om Serverhanteraren inte redan är öppen klickar du på **Starta > Serverhanteraren**.
   
   ![Serverhanteraren](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Klicka på **Serverhanteraren > instrumentpanelen > Lägg till roller och funktioner**. Då startas guiden **Lägg till roller och funktioner.**
   
   ![Guiden Lägg till roller och funktioner 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. Gör följande i guiden **Lägg till roller och funktioner:**
   
   1. Klicka på **Nästa** på sidan **Innan du börjar**.
   2. På sidan **Välj installationstyp** godkänner du standardinställningen **för rollbaserad eller funktionsbaserad** installation. Klicka på **Nästa**.
   
       ![Guiden Lägg till roller och funktioner 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. På sidan **Välj målserver** väljer du **Välj en server i serverpoolen**. Värdservern bör identifieras automatiskt. Klicka på **Nästa**.
   4. På sidan **Välj serverroller**, klickar du på **Nästa**.
   5. På sidan **Välj funktioner** väljer du **Multipath I/O**och klickar på **Nästa**.
   
       ![Guiden Lägg till roller och funktioner 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. På sidan **Bekräfta installationsval** bekräftar du markeringen och väljer sedan **Starta om målservern automatiskt om det behövs**, enligt nedan. Klicka på **Installera**.
   
       ![Guiden Lägg till roller och funktioner 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Du meddelas när installationen är klar. Stäng guiden genom att klicka på **Stäng**.
   
       ![Guiden Lägg till roller och funktioner 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Steg 2: Konfigurera MPIO för StorSimple-volymer

MPIO måste konfigureras för att identifiera StorSimple-volymer. Om du vill konfigurera MPIO för att känna igen StorSimple-volymer utför du följande steg.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Så här konfigurerar du MPIO för StorSimple-volymer

1. Öppna **MPIO-konfigurationen**. Klicka på **Serverhanteraren > instrumentpanelen > verktyg > MPIO**.
2. Välj fliken **Upptäck fler sökvägar** i dialogrutan **MPIO-egenskaper.**
3. Välj **Lägg till stöd för iSCSI-enheter**och klicka sedan på Lägg **till**.  
   ![MPIO-egenskaper Upptäck flera sökvägar](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Starta om servern när du uppmanas att göra det.
5. Klicka på fliken MPIO-enheter i dialogrutan **Add** **MPIO-egenskaper.** **MPIO Devices**
    </br>![MPIO-egenskaper MPIO-enheter](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Ange **Add MPIO Support** enhetens serienummer under **Enhetsmaskinvaras-ID under Enhetsmaskinvaras-ID.** Om du vill hämta enhetens serienummer öppnar du tjänsten StorSimple Device Manager. Navigera till **Enheter > instrumentpanel .** Enhetens serienummer visas i fönstret **Snabböversikt** i enhetens instrumentpanel.
    </br>
    ![Lägg till MPIO-stöd](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Starta om servern när du uppmanas att göra det.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Steg 3: Montera StorSimple-volymer på värden

När MPIO har konfigurerats på Windows Server kan volymen/volymerna som skapats på StorSimple-enheten monteras och kan sedan dra nytta av MPIO för redundans. Om du vill montera en volym utför du följande steg.

#### <a name="to-mount-volumes-on-the-host"></a>Så här monterar du volymer på värden

1. Öppna fönstret egenskaper för **iSCSI-initierare** på Windows Server-värden. Klicka på **Serverhanteraren > instrumentpanelen > verktyg > iSCSI-initieraren**.
2. Klicka på fliken Identifiering i dialogrutan **egenskaper för iSCSI-initierare** och klicka sedan på **Upptäck målportalen**.
3. Gör följande i dialogrutan **Upptäck målportal:**
   
   1. Ange IP-adressen för DATA-porten på din StorSimple-enhet (ange till exempel DATA 0).
   2. Klicka på **OK** för att återgå till dialogrutan egenskaper för **iSCSI-initierare.**
     
      > [!IMPORTANT]
      > **Om du använder ett privat nätverk för iSCSI-anslutningar anger du IP-adressen för dataporten som är ansluten till det privata nätverket.**
    
4. Upprepa steg 2-3 för ett andra nätverksgränssnitt (till exempel DATA 1) på enheten. Tänk på att dessa gränssnitt bör aktiveras för iSCSI. Mer information finns i [Ändra nätverksgränssnitt](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Välj fliken **Mål** i dialogrutan **egenskaper för iSCSI-initierare.** Du bör se StorSimple-enhetens mål IQN under **Identifierade mål**.

   ![Fliken mål för iSCSI-initierare](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Klicka på **Anslut** om du vill upprätta en iSCSI-session med StorSimple-enheten. Dialogrutan **Anslut till mål** visas.
7. Markera kryssrutan **Aktivera flersöksväg** i dialogrutan **Anslut till mål.** Klicka på **Avancerat**.
8. Gör följande i dialogrutan **Avancerade inställningar:**
   
   1. Välj **Microsoft iSCSI-initierare i**listrutan **Lokalt kort** .
   2. I listrutan **Initierare IP** väljer du värdens IP-adress.
   3. Välj IP för enhetsgränssnitt i listrutan **Målportal** IP.
   4. Klicka på **OK** för att återgå till dialogrutan egenskaper för **iSCSI-initierare.**
9. Klicka på **Egenskaper**. Klicka på **Lägg till session**i dialogrutan **Egenskaper** .
10. Markera kryssrutan **Aktivera flersöksväg** i dialogrutan **Anslut till mål.** Klicka på **Avancerat**.
11. I dialogrutan **Avancerade inställningar:**

    1. Välj Microsoft iSCSI-initierare i listrutan **Lokalt kort.**
    2. I listrutan **Initierare IP** väljer du den IP-adress som motsvarar värden. I det här fallet ansluter du två nätverksgränssnitt på enheten till ett enda nätverksgränssnitt på värden. Därför är det här gränssnittet samma som det som angavs för den första sessionen.
    3. I listrutan **Målportal IP** väljer du IP-adressen för det andra datagränssnittet aktiverat på enheten.
    4. Klicka på **OK** för att återgå till dialogrutan egenskaper för iSCSI-initierare. Du har lagt till en andra session i målet.
12. Öppna **Datorhantering** genom att navigera till **Serverhanteraren > instrumentpanel > datorhantering**. Klicka på Lagring **> Diskhantering i**den vänstra rutan. Volymen som skapas på StorSimple-enheten som är synliga för den här värden visas under **Diskhantering** som nya diskar.
13. Initiera disken och skapa en ny volym. Under formatprocessen väljer du en blockstorlek på 64 kB.
    
    ![Diskhantering](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Högerklicka på **disken** under **Diskhantering**och välj **Egenskaper**.
15. Klicka på fliken **MPIO** i dialogrutan Egenskaper för flera sökvägar för storsimple-modell ### **med flera banor.**
    
    ![StorSimple 8100 Multi-Path Disk DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. Klicka på **Information** i avsnittet **DSM-namn** och kontrollera att parametrarna är inställda på standardparametrarna. Standardparametrarna är:
    
    * Perioden för sökvägsverifiering = 30
    * Antal försök igen = 3
    * TA BORT PUNKT FÖR SUB = 20
    * Intervall för återförsök = 1
    * Sökvägsverifiering aktiverad = Avmarkerad.

> [!NOTE]
> **Ändra inte standardparametrarna.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Steg 4: Konfigurera MPIO för hög tillgänglighet och belastningsutjämning

För flera sökvägsbaserade hög tillgänglighet och belastningsutjämning måste flera sessioner läggas till manuellt för att deklarera de olika sökvägarna tillgängliga. Om värden till exempel har två gränssnitt anslutna till iSCSI-nätverket och enheten har två gränssnitt anslutna till iSCSI-nätverket, behöver du fyra sessioner konfigurerade med rätt sökvägspermutationer (endast två sessioner krävs om varje DATA-gränssnitt och värdgränssnittet finns i ett annat IP-undernät och är inte dirigerbart).

**Vi rekommenderar att du har minst 8 aktiva parallella sessioner mellan enheten och programvärden.** Detta kan uppnås genom att aktivera 4 nätverksgränssnitt på Ditt Windows Server-system. Använd fysiska nätverksgränssnitt eller virtuella gränssnitt via nätverksvirtualiseringsteknik på maskinvaru- eller operativsystemsnivå på Windows Server-värden. Med de två nätverksgränssnitten på enheten resulterar den här konfigurationen i 8 aktiva sessioner. Den här konfigurationen hjälper till att optimera enheten och molndataflödet.

> [!IMPORTANT]
> **Vi rekommenderar att du inte blandar 1 GbE- och 10 GbE-nätverksgränssnitt. Om du använder två nätverksgränssnitt bör båda gränssnitten vara av samma typ.**

I följande procedur beskrivs hur du lägger till sessioner när en StorSimple-enhet med två nätverksgränssnitt är ansluten till en värd med två nätverksgränssnitt. Detta ger dig bara 4 sessioner. Använd samma procedur med en StorSimple-enhet med två nätverksgränssnitt anslutna till en värd med fyra nätverksgränssnitt. Du måste konfigurera 8 i stället för de 4 sessioner som beskrivs här.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Så här konfigurerar du MPIO för hög tillgänglighet och belastningsutjämning

1. Utför en identifiering av målet: Klicka på Upptäck portal på fliken **iSCSI Initiator Properties** Identifieringspanel på fliken **Identifiering.** **Discover Portal**
2. I dialogrutan **Anslut till mål** anger du IP-adressen för ett av enhetsnätverksgränssnitten.
3. Klicka på **OK** för att återgå till dialogrutan egenskaper för **iSCSI-initierare.**
4. I dialogrutan **egenskaper för iSCSI-initierare** väljer du fliken **Mål,** markerar det identifierade målet och klickar sedan på **Anslut**. Dialogrutan **Anslut till mål** visas.
5. I dialogrutan **Anslut till mål:**
   
   1. Lämna standardvald målinställning för **Lägg till den här anslutningen** i listan över favoritmål. Detta gör att enheten automatiskt försöker starta om anslutningen varje gång datorn startas om.
   2. Markera kryssrutan **Aktivera flersöksväg.**
   3. Klicka på **Avancerat**.
6. I dialogrutan **Avancerade inställningar:**
   
   1. Välj **Microsoft iSCSI-initierare i**listrutan **Lokalt kort** .
   2. I listrutan **Initierar IP** väljer du den IP-adress som motsvarar det första gränssnittet på värden (iSCSI-gränssnittet).
   3. I listrutan **Målportal IP** väljer du IP-adressen för det första datagränssnittet som är aktiverat på enheten.
   4. Klicka på **OK** för att återgå till dialogrutan egenskaper för iSCSI-initierare.
7. Klicka på **Egenskaper**och klicka på **Lägg till session**i dialogrutan **Egenskaper** .
8. Markera kryssrutan **Aktivera flersöksväg** i dialogrutan **Anslut till mål** och klicka sedan på **Avancerat**.
9. I dialogrutan **Avancerade inställningar:**
   
   1. Välj **Microsoft iSCSI-initierare i**listrutan **Lokalt kort** .
   2. I listrutan **Initierar IP** väljer du den IP-adress som motsvarar det andra iSCSI-gränssnittet på värden.
   3. I listrutan **Målportal IP** väljer du IP-adressen för det andra datagränssnittet aktiverat på enheten.
   4. Klicka på **OK** för att återgå till dialogrutan egenskaper för **iSCSI-initierare.** Du har nu lagt till en andra session i målet.
10. Upprepa steg 8-10 om du vill lägga till ytterligare sessioner (sökvägar) i målet. Med två gränssnitt på värden och två på enheten kan du lägga till totalt fyra sessioner.
11. När du har lagt till önskade sessioner (sökvägar) markerar du målet i dialogrutan **egenskaper för iSCSI-initierare** och klickar på **Egenskaper**. På fliken Sessioner i dialogrutan **Egenskaper** noterar du de fyra sessionsidentifierare som motsvarar möjliga sökvägspermutationer. Om du vill avbryta en session markerar du kryssrutan bredvid en sessionsidentifierare och klickar sedan på **Koppla från**.
12. Om du vill visa enheter som visas i sessioner väljer du fliken **Enheter.** Om du vill konfigurera MPIO-principen för en vald enhet klickar du på **MPIO**. Dialogrutan **Enhetsinformation** visas. På fliken **MPIO** kan du välja lämpliga inställningar för **belastningsutjämningsprincip.** Du kan också visa sökvägstypen **Aktiv** eller **Vänteläge.**

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur du använder Tjänsten StorSimple Device Manager för att ändra konfigurationen av StorSimple-enheten](storsimple-8000-modify-device-config.md).

