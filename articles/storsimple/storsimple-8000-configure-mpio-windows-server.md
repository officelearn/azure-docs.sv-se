---
title: Konfigurera MPIO för din StorSimple-enhet | Microsoft Docs
description: Beskriver hur du konfigurerar MPIO (Multipath I/O) för StorSimple-enheten är ansluten till en värd som kör Windows Server 2012 R2.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60363400"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurera MPIO för din StorSimple-enhet

Den här självstudien beskrivs de steg som du bör följa för att installera och använda funktionen Multipath I/O (MPIO) på en värd som kör Windows Server 2012 R2 och ansluten till en fysisk StorSimple-enhet. Riktlinjerna i den här artikeln gäller StorSimple 8000-serien fysiska enheter. MPIO stöds för närvarande inte på en StorSimple Cloud Appliance.

Microsoft har byggt stöd för MPIO (Multipath I/O)-funktionen i Windows Server för att skapa högtillgängliga, feltoleranta iSCSI nätverkskonfigurationer. MPIO använder redundanta fysiska sökvägskomponenter – nätverkskort, kablar och växlar – att skapa logiska sökvägar mellan servern och lagringsenheten. Om det finns ett komponentfel orsakar en logiska sökvägen misslyckas, använder multipathing-logik en alternativ sökväg för I/O så att program kan fortfarande komma åt sina data. Dessutom beroende på din konfiguration kan MPIO också förbättra prestanda genom att balansera belastningen mellan dessa sökvägar. Mer information finns i [MPIO översikt](https://technet.microsoft.com/library/cc725907.aspx "MPIO översikt och funktioner").

MPIO ska konfigureras på StorSimple-enheten för den hög tillgängligheten i StorSimple-lösningen. När MPIO installeras på dina värdservrar som kör Windows Server 2012 R2, tolereras sedan servrarna en länk, nätverk eller gränssnitt.

## <a name="mpio-configuration-summary"></a>Sammanfattning av MPIO-konfiguration

MPIO är en valfri funktion i Windows Server och installeras inte som standard. Den installeras som en funktion via Server Manager.

Följ dessa steg för att konfigurera MPIO på din StorSimple-enhet:

* Steg 1: Installera MPIO på Windows Server-värd
* Steg 2: Konfigurera MPIO för StorSimple-volymer
* Steg 3: Montera StorSimple-volymer på värden
* Steg 4: Konfigurera MPIO för hög tillgänglighet och belastningsutjämning

Var och en av de föregående stegen beskrivs i följande avsnitt.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Steg 1: Installera MPIO på Windows Server-värd

Slutför följande procedur för att installera den här funktionen på Windows Server-värd.

#### <a name="to-install-mpio-on-the-host"></a>Du installerar MPIO på värden

1. Öppna Serverhanteraren på Windows Server-värd. Som standard startar Server Manager när en medlem i administratörsgruppern loggar in på en dator som kör Windows Server 2012 R2 eller Windows Server 2012. Om Serverhanteraren inte redan är öppen klickar du på **starta > Serverhanteraren**.
   
   ![Serverhanteraren](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Klicka på **Server Manager > instrumentpanelen > Lägg till roller och funktioner**. Detta startar den **Lägg till roller och funktioner** guiden.
   
   ![Lägg till roller och funktioner som guiden 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. I den **Lägg till roller och funktioner** guiden, utför följande steg:
   
   1. På den **innan du börjar** klickar du på **nästa**.
   2. På den **Välj installationstyp** godkänner du standardinställningen **rollbaserad eller funktionsbaserad** installation. Klicka på **Nästa**.
   
       ![Lägg till roller och funktioner som guiden 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. På den **väljer målservern** väljer **Välj en server från serverpoolen**. Värdservern bör identifieras automatiskt. Klicka på **Nästa**.
   4. På den **Välj serverroller** klickar du på **nästa**.
   5. På den **Välj vilka funktioner du** väljer **Multipath I/O**, och klicka på **nästa**.
   
       ![Lägg till roller och funktioner som guiden 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. På den **Bekräfta installationsinställningarna** , bekräfta valet, och välj sedan **starta om målservern automatiskt om det behövs**, enligt nedan. Klicka på **Installera**.
   
       ![Lägg till roller och funktioner som guiden 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Du meddelas när installationen är klar. Stäng guiden genom att klicka på **Stäng**.
   
       ![Lägg till roller och funktioner som guiden 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Steg 2: Konfigurera MPIO för StorSimple-volymer

MPIO måste konfigureras för att identifiera StorSimple-volymer. Utför följande steg för att konfigurera MPIO för att identifiera StorSimple-volymer.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Konfigurera MPIO för StorSimple-volymer

1. Öppna den **MPIO-konfiguration**. Klicka på **Serverhanteraren > instrumentpanelen > Verktyg > MPIO**.
2. I den **MPIO-egenskaper** dialogrutan den **Upptäck flera sökvägar** fliken.
3. Välj **lägga till stöd för iSCSI-enheter**, och klicka sedan på **Lägg till**.  
   ![MPIO-egenskaper identifiera med flera sökvägar](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Starta om servern när du tillfrågas.
5. I den **MPIO-egenskaper** dialogrutan klickar du på den **MPIO enheter** fliken. Klicka på **Lägg till**.
    </br>![MPIO egenskaper MPIO-enheter](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. I den **lägga till stöd för MPIO** dialogrutan **enhetens maskinvaru-ID**, ange serienumret för enheten. Åtkomst till din StorSimple Device Manager-tjänsten för att hämta enhetens serienummer. Gå till **enheter > instrumentpanel**. Enhetens serienummer visas i högra **snabb översiktlig** rutan i instrumentpanelen för enheten.
    </br>
    ![Lägga till MPIO-stöd](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Starta om servern när du tillfrågas.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Steg 3: Montera StorSimple-volymer på värden

När MPIO har konfigurerats på Windows Server, volymer som skapats på StorSimple-enheten kan monteras och sedan kan dra nytta av MPIO för redundans. Utför följande steg om du vill montera en volym.

#### <a name="to-mount-volumes-on-the-host"></a>Montera volymer på värden

1. Öppna den **iSCSI-Initieraregenskaper** fönster på Windows Server-värd. Klicka på **Server Manager > instrumentpanelen > Verktyg > iSCSI-initierare**.
2. I den **iSCSI-Initieraregenskaper** dialogrutan, klicka på fliken Identifiering och klicka sedan på **identifiera målportal**.
3. I den **identifiera målportal** dialogrutan utför följande steg:
   
   1. Ange IP-adressen för DATA-porten för din StorSimple-enhet (till exempel ange DATA 0).
   2. Klicka på **OK** att återgå till den **iSCSI-Initieraregenskaper** dialogrutan.
     
      > [!IMPORTANT]
      > **Om du använder ett privat nätverk för iSCSI-anslutningar, anger du IP-adressen för den DATA-port som är ansluten till det privata nätverket.**
    
4. Upprepa steg 2 – 3 för ett andra nätverksgränssnitt (till exempel DATA 1) på din enhet. Tänk på att dessa gränssnitt ska aktiveras för iSCSI. Mer information finns i [ändra nätverksgränssnitt](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Välj den **mål** fliken i den **iSCSI-Initieraregenskaper** dialogrutan. Du bör se StorSimple-enheten rikta IQN under **upptäckta mål**.

   ![iSCSI-initierare fliken mål](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Klicka på **Connect** att upprätta en iSCSI-session med din StorSimple-enhet. En **Anslut till målet** dialogrutan visas.
7. I den **Anslut till målet** dialogrutan den **aktivera flersökvägslösningen Multipath** markerar du kryssrutan. Klicka på **Avancerat**.
8. I den **avancerade inställningar** dialogrutan utför följande steg:
   
   1. På den **lokala kortet** listrutan, väljer **Microsoft iSCSI Initiator**.
   2. På den **IP för initierare** listrutan väljer du IP-adressen för värden.
   3. På den **målportal** IP-listrutan väljer du den IP-Adressen för enhetsgränssnittet.
   4. Klicka på **OK** att återgå till den **iSCSI-Initieraregenskaper** dialogrutan.
9. Klicka på **Egenskaper**. I den **egenskaper** dialogrutan klickar du på **lägga till sessionen**.
10. I den **Anslut till målet** dialogrutan den **aktivera flersökvägslösningen Multipath** markerar du kryssrutan. Klicka på **Avancerat**.
11. I den **avancerade inställningar** dialogrutan:

    1. På den **lokala kortet** listrutan, väljer Microsoft iSCSI-initierare.
    2. På den **IP för initierare** listrutan väljer du den IP-adress för värden. I detta fall ansluter du två nätverksgränssnitt på enheten till ett enda nätverksgränssnitt på värden. Det här gränssnittet är därför lika som angetts för den första sessionen.
    3. På den **Target Portal IP** listrutan, väljer IP-adressen för andra data-gränssnittet är aktiverat på enheten.
    4. Klicka på **OK** att gå tillbaka till dialogrutan för iSCSI-Initieraregenskaper. Du har lagt till en andra session till målet.
12. Öppna **Datorhantering** genom att gå till **Server Manager > instrumentpanelen > Datorhantering**. I den vänstra rutan klickar du på **Storage > Diskhantering**. Den volym som har skapats på StorSimple-enheten som är synliga för den här värden visas under **Diskhantering** som nya diskar.
13. Initiera disken och skapa en ny volym. Välj en blockstorlek på 64 KB under format.
    
    ![Diskhantering](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Under **Diskhantering**, högerklicka på den **Disk** och välj **egenskaper**.
15. I StorSimple-modellen ### **Multipath Disk enhetsegenskaper** dialogrutan klickar du på den **MPIO** fliken.
    
    ![StorSimple 8100 Disk med flera sökvägar DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. I den **DSM namn** klickar du på **information** och kontrollera att parametrarna är inställda på standardparametrar. Standardparametrar är:
    
    * Sökvägen verifiera Period = 30
    * Antal nya försök = 3
    * PDO ta bort Period = 20
    * Återförsöksintervall = 1
    * Kontrollera sökvägen aktiverad = avmarkerat.

> [!NOTE]
> **Ändra inte standardparametrar.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Steg 4: Konfigurera MPIO för hög tillgänglighet och belastningsutjämning

För Multipath-baserad hög tillgänglighet och belastningsutjämning, måste flera sessioner läggas till manuellt deklarera de olika sökvägarna som är tillgängliga. Till exempel om värden har två gränssnitt som är ansluten till iSCSI-nätverk och enheten har två gränssnitt som är ansluten till iSCSI-nätverk så du behöver fyra sessioner som är konfigurerade med rätt sökväg permutationer (endast två sessioner som måste utföras om varje gränssnitt för DATA och värd-gränssnittet är i ett annat IP-undernät och är inte dirigerbart).

**Vi rekommenderar att du har minst 8 aktiva parallella sessioner mellan enheten och din programvärd.** Detta kan uppnås genom att aktivera 4 nätverksgränssnitt på Windows Server-systemet. Använda fysiska nätverksgränssnitt eller virtuella gränssnitt via nätverket virtualiseringsteknik vilken maskinvara eller operativsystem på Windows Server-värd. Med två nätverksgränssnitt på enheten leder den här konfigurationen 8 aktiva sessioner. Den här konfigurationen hjälper till att optimera dataflödet enhet och moln.

> [!IMPORTANT]
> **Vi rekommenderar att du inte blandar 1 GbE och 10 GbE-nätverksgränssnitt. Om du använder två nätverksgränssnitt, vara båda gränssnitten identiska typu.**

Följande procedur beskriver hur du lägger till sessioner när en StorSimple-enhet med två nätverksgränssnitt är ansluten till en värd med två nätverksgränssnitt. Detta ger dig endast 4 sessioner. Använd samma procedur med en StorSimple-enhet med två nätverksgränssnitt som är anslutna till en värd med fyra nätverksgränssnitt. Du måste konfigurera 8 i stället för 4 sessioner som beskrivs här.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Konfigurera MPIO för hög tillgänglighet och belastningsutjämning

1. Göra en identifiering för mål: i den **iSCSI-Initieraregenskaper** dialogrutan den **identifiering** fliken **identifiera Portal**.
2. I den **Anslut till målet** dialogrutan anger du IP-adressen för en av nätverksgränssnitt för enheten.
3. Klicka på **OK** att återgå till den **iSCSI-Initieraregenskaper** dialogrutan.
4. I den **iSCSI-Initieraregenskaper** dialogrutan den **mål** fliken, markerar upptäckta mål och klicka sedan på **Connect**. Den **Anslut till målet** dialogrutan visas.
5. I den **Anslut till målet** dialogrutan:
   
   1. Lämna standardinställningen markerade Målinställningar för **lägga till den här anslutningen** i listan över Favoriter mål. Detta gör att enheten automatiskt att försöka starta om anslutningen varje gång datorn startas om.
   2. Välj den **aktivera flersökvägslösningen Multipath** markerar du kryssrutan.
   3. Klicka på **Avancerat**.
6. I den **avancerade inställningar** dialogrutan:
   
   1. På den **lokala kortet** listrutan, väljer **Microsoft iSCSI Initiator**.
   2. På den **IP för initierare** listrutan väljer du den IP-adressen som motsvarar det första gränssnittet på värden (iSCSI-gränssnitt).
   3. På den **Target Portal IP** listrutan, väljer IP-adressen för första data-gränssnittet är aktiverat på enheten.
   4. Klicka på **OK** att gå tillbaka till dialogrutan för iSCSI-Initieraregenskaper.
7. Klicka på **egenskaper**, och i den **egenskaper** dialogrutan klickar du på **lägga till sessionen**.
8. I den **Anslut till målet** dialogrutan den **aktivera flersökvägslösningen Multipath** och klicka sedan på **Avancerat**.
9. I den **avancerade inställningar** dialogrutan:
   
   1. På den **lokala kortet** listrutan, väljer **Microsoft iSCSI Initiator**.
   2. På den **IP för initierare** listrutan väljer du den IP-adressen för det andra iSCSI-gränssnittet på värden.
   3. På den **Target Portal IP** listrutan, väljer IP-adressen för andra data-gränssnittet är aktiverat på enheten.
   4. Klicka på **OK** att återgå till den **iSCSI-Initieraregenskaper** dialogrutan. Nu har du lagt till en andra session till målet.
10. Upprepa steg 8 – 10 för att lägga till ytterligare sessioner (sökvägar) till målet. Du kan lägga till totalt fyra sessioner med två gränssnitt på värden och två på enheten.
11. När du lägger till de önskade sessionerna (sökvägar) i den **iSCSI-Initieraregenskaper** dialogrutan väljer mål och klicka på **egenskaper**. På fliken sessioner i den **egenskaper** dialogrutan, anteckning fyra sessionen identifierare som motsvarar möjliga vägen permutationer. Avbryta en session, markera kryssrutan bredvid ett sessions-ID och klicka sedan på **Disconnect**.
12. Om du vill visa enheter som visas i sessioner, Välj den **enheter** fliken. Om du vill konfigurera MPIO-principen för en vald enhet, klickar du på **MPIO**. Den **enhetsinformation** dialogrutan visas. På den **MPIO** fliken kan du välja lämplig **princip för belastningsutjämning** inställningar. Du kan också visa den **Active** eller **vänteläge** Sökvägstyp.

## <a name="next-steps"></a>Nästa steg

Läs mer om [med StorSimple Device Manager-tjänsten för att ändra din konfiguration för StorSimple-enheten](storsimple-8000-modify-device-config.md).

