---
title: StorSimple virtuell matris web UI administration | Microsoft Docs
description: Beskriver hur du utför grundläggande enhet administrationsuppgifter via webbgränssnittet för virtuell StorSimple-matris.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 989e7b697f9b527df549fb32be18edd1d3c8d224
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875888"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Använda Webbgränssnittet för att administrera din virtuella StorSimple-matris
![processflöde för installationen](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Översikt
Självstudiekurser i den här artikeln gäller för Microsoft Azure StorSimple virtuell matrisen (kallas även den lokala virtuella enheten StorSimple) kör mars 2016 allmän tillgänglighet (GA) version. Den här artikeln beskrivs några av komplexa arbetsflöden och administrativa uppgifter som kan utföras på den virtuella StorSimple-matrisen. Du kan hantera den virtuella StorSimple-matrisen med hjälp av StorSimple Manager service användargränssnitt (kallas i portalens användargränssnitt) och lokala webbgränssnittet för enheten. Den här artikeln fokuserar på de uppgifter som du kan utföra med hjälp av webbgränssnittet.

Den här artikeln innehåller följande kurser:

* Hämta krypteringsnyckel för tjänstdata
* Felsöka web UI installationen
* Generera en logg-paket
* Stänga av eller starta om enheten

## <a name="get-the-service-data-encryption-key"></a>Hämta krypteringsnyckel för tjänstdata
En krypteringsnyckel för tjänstdata ska genereras när du registrerar din första enhet med StorSimple Manager-tjänsten. Den här nyckeln är sedan krävs med nyckeln för tjänstregistrering för att registrera ytterligare enheter med StorSimple Manager-tjänsten.

Om du har tappat bort din krypteringsnyckel för tjänstdata och behovet av att hämta den, utför följande steg i det lokala webbgränssnittet för enheten registreras med din tjänst.

#### <a name="to-get-the-service-data-encryption-key"></a>Hämta krypteringsnyckel för tjänstdata
1. Ansluta till lokala webbgränssnittet. Gå till **Configuration** > **Molninställningar**.
2. Längst ned på sidan klickar du på **hämta krypteringsnyckel för tjänstdata**. En nyckel visas. Kopiera och spara den här nyckeln.
   
    ![Hämta krypteringsnyckel för tjänstdata 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Felsöka web UI installationen
I vissa fall när du konfigurerar enheten via det lokala webbgränssnittet kanske du stöter på fel. Du kan köra testerna diagnostik för att diagnostisera och felsöka dessa.

#### <a name="to-run-the-diagnostic-tests"></a>Köra diagnostiska tester
1. Gå till i det lokala webbgränssnittet **felsökning** > **diagnostiska testerna**.
   
    ![Kör diagnostik 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. Längst ned på sidan klickar du på **kör diagnostiktest**. Detta kommer att initiera tester för att diagnostisera eventuella problem med nätverket, enhet, webbproxy, tid eller molninställningar. Du meddelas att enheten kör test.
3. När testerna har slutförts visas resultatet. I följande exempel visas resultatet av diagnostiska test. Observera att webbproxyinställningarna inte har konfigurerats på den här enheten och därför web proxy testet kördes inte. Alla andra test för nätverksinställningar, DNS-server och tidsinställningar lyckades.
   
    ![Kör diagnostik 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Generera en logg-paket
En logg paketet består av alla relevanta loggar som kan hjälpa Microsoft-supporten att felsöka eventuella enhetsproblem. I den här versionen kan en logg-paketet skapas via lokala webbgränssnittet.

#### <a name="to-generate-the-log-package"></a>Generera log-paketet
1. Gå till i det lokala webbgränssnittet **felsökning** > **systemloggar**.
   
    ![Generera loggen paketet 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. Längst ned på sidan klickar du på **skapa loggen paketet**. Ett paket med systemloggarna kommer att skapas. Det kan ta några minuter.
   
    ![Generera loggen paketet 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Du meddelas när paketet har skapats och sidan uppdateras för att visa tid och datum när paketet har skapats.
   
    ![Generera loggen paketet 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Klicka på **loggen hämtningspaketet**. Komprimerade paketet kommer att hämtas i systemet.
   
    ![Generera loggen paketet 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Du kan packa upp paketet hämtade loggen och visa systemloggfilerna.

## <a name="shut-down-and-restart-your-device"></a>Stäng och starta om enheten
Du kan stänga av eller starta om din virtuella enhet använder lokala webbgränssnittet. Vi rekommenderar att innan du startar om ta volymer eller resurser offline på värden och sedan på enheten. Detta minimerar risken för varje möjlighet att data skadas. 

#### <a name="to-shut-down-your-virtual-device"></a>Att stänga av den virtuella enheten
1. Gå till i det lokala webbgränssnittet **Underhåll** > **energiinställningar**.
2. Längst ned på sidan klickar du på **avstängning**.
   
    ![enheten avstängning 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. En varning visas om att stängas av enheten kommer att avbryta alla i/o som pågår, vilket resulterar i ett driftstopp. Klicka på kryssikonen ![kryssikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![enheten avstängning varning](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Du meddelas att avstängningen har initierats.
   
    ![enheten avstängning igång](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Enheten stängs nu. Om du vill starta enheten behöver du göra det via Hyper-V Manager.

#### <a name="to-restart-your-virtual-device"></a>Starta om din virtuella enhet
1. Gå till i det lokala webbgränssnittet **Underhåll** > **energiinställningar**.
2. Längst ned på sidan klickar du på **starta om**.
   
    ![omstart av enheten](./media/storsimple-ova-web-ui-admin/image36.png)
3. En varning visas om att du startar om enheten att avbryta alla IOs som pågår, vilket resulterar i ett driftstopp. Klicka på kryssikonen ![kryssikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Starta om varning](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Du meddelas att omstarten har initierats.
   
    ![Starta om initieras](./media/storsimple-ova-web-ui-admin/image39.png)
   
    När omstarten pågår förlorar anslutningen till Användargränssnittet. Du kan övervaka omstarten genom att uppdatera Användargränssnittet med jämna mellanrum. Du kan också övervaka omstart Enhetsstatus via Hyper-V Manager.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [använda StorSimple Manager-tjänsten för att hantera enheten](storsimple-virtual-array-manager-service-administration.md).

