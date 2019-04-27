---
title: StorSimple Virtual Array web UI administration | Microsoft Docs
description: Beskriver hur du utför grundläggande administrationsuppgifter via webbgränssnittet för StorSimple Virtual Array.
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
ms.openlocfilehash: 92671206a4171ca838423f55b526191ef30e5c35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630519"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Använda Webbgränssnittet för att administrera din StorSimple Virtual Array
![processflöde för installationen](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Översikt
Självstudier i den här artikeln gäller för Microsoft Azure StorSimple Virtual Array (även kallat den lokala virtuella enheten StorSimple) som kör mars 2016 allmänt tillgänglig (GA) version. Den här artikeln beskriver några av de komplexa arbetsflöden och de administrativa uppgifter som kan utföras på StorSimple Virtual Array. Du kan hantera StorSimple Virtual Array med StorSimple Manager-Gränssnittet (kallas portalens användargränssnitt) och det lokala webbgränssnittet för enheten. Den här artikeln handlar om de uppgifter som att du kan utföra med webbgränssnittet.

Den här artikeln innehåller följande självstudier:

* Hämta krypteringsnyckeln för tjänstdata
* Felsöka web UI-installationsfel
* Skapa ett paket för log
* Stänga av eller starta om enheten

## <a name="get-the-service-data-encryption-key"></a>Hämta krypteringsnyckeln för tjänstdata
En krypteringsnyckeln för tjänstdata genereras när du registrerar din första enhet med StorSimple Manager-tjänsten. Den här nyckeln är sedan krävs med tjänstregistreringsnyckeln för att registrera ytterligare enheter med StorSimple Manager-tjänsten.

Om du har tappat bort din krypteringsnyckeln för tjänstdata och du behöver hämta den, kan du utföra följande steg i det lokala webbgränssnittet på enheten som är registrerade med tjänsten.

#### <a name="to-get-the-service-data-encryption-key"></a>Att hämta krypteringsnyckeln för tjänstdata
1. Ansluta till det lokala webbgränssnittet. Gå till **Configuration** > **Molninställningar**.
2. Längst ned på sidan klickar du på **Get krypteringsnyckeln för tjänstdata**. En nyckel visas. Kopiera och spara den här nyckeln.
   
    ![Hämta krypteringsnyckeln för tjänstdata 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Felsöka web UI-installationsfel
I vissa fall när du konfigurerar enheten via det lokala webbgränssnittet kanske du stöter på fel. Du kan köra diagnostiktesterna för att diagnostisera och felsöka dessa fel.

#### <a name="to-run-the-diagnostic-tests"></a>Att köra diagnostiktest
1. I det lokala webbgränssnittet går du till **felsökning** > **diagnostiska testerna**.
   
    ![Kör diagnostik 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. Längst ned på sidan klickar du på **kör diagnostiktest**. Detta kommer att initieras tester för att diagnostisera möjliga problem med ditt nätverk, enhet, webbproxy, tid eller molninställningar. Du kommer att meddelas att enheten kör testerna.
3. När testerna har slutförts visas resultatet. I följande exempel visar resultatet av diagnostiktest. Observera att webbproxyinställningarna inte är konfigurerade på den här enheten och därför web proxy testet kördes inte. Alla andra tester för nätverksinställningar, DNS-server och tidsinställningar har installerats.
   
    ![Kör diagnostik 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Skapa ett paket för log
Ett paket för log består av alla relevanta loggar som kan hjälpa Microsoft Support med att felsöka eventuella problem med. I den här versionen kan ett log-paket skapas via det lokala webbgränssnittet.

#### <a name="to-generate-the-log-package"></a>Att generera log-paketet
1. I det lokala webbgränssnittet går du till **felsökning** > **systemloggar**.
   
    ![Generera log paketet 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. Längst ned på sidan klickar du på **skapa log paketet**. Ett paket med systemloggar kommer att skapas. Detta tar några minuter.
   
    ![Generera log paketet 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Du kommer att meddelas när paketet har skapats och sidan kommer att uppdateras för att visa tid och datum när paketet skapades.
   
    ![Skapa log paket 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Klicka på **log hämtningspaketet**. Ett komprimerade paket laddas ned på datorn.
   
    ![Skapa log paket 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Du kan packa upp paketet hämtade log och visa systemloggfilerna.

## <a name="shut-down-and-restart-your-device"></a>Starta om din enhet
Du kan stänga av eller starta om den virtuella enheten med hjälp av det lokala webbgränssnittet. Vi rekommenderar att innan du startar om ta volymer eller resurser offline på värden och sedan på enheten. Detta minimerar risken för risk att data skadas. 

#### <a name="to-shut-down-your-virtual-device"></a>Att stänga av den virtuella enheten
1. I det lokala webbgränssnittet går du till **Underhåll** > **energiinställningar**.
2. Längst ned på sidan klickar du på **avstängning**.
   
    ![Stäng av enheten 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. En varning visas om att stänga av enheten kommer att avbryta alla I/O som pågår, vilket resulterar i ett driftstopp. Klicka på kryssikonen ![kryssikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![enheten avstängning varning](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Du kommer att meddelas att avstängningen har påbörjats.
   
    ![igång att Stäng av enheten](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Enheten stängs nu. Om du vill starta enheten behöver du göra det via Hyper-V Manager.

#### <a name="to-restart-your-virtual-device"></a>Starta om den virtuella enheten
1. I det lokala webbgränssnittet går du till **Underhåll** > **energiinställningar**.
2. Längst ned på sidan klickar du på **starta om**.
   
    ![omstart av enheten](./media/storsimple-ova-web-ui-admin/image36.png)
3. En varning visas om att starta om enheten avbryter alla IOs som pågår, vilket resulterar i ett driftstopp. Klicka på kryssikonen ![kryssikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Starta om varning](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Du kommer att meddelas att omstart har påbörjats.
   
    ![omstart har initierats](./media/storsimple-ova-web-ui-admin/image39.png)
   
    När omstarten pågår, förlorar anslutningen till Användargränssnittet. Du kan övervaka omstarten genom att uppdatera Användargränssnittet med jämna mellanrum. Du kan också övervaka omstart Enhetsstatus genom Hyper-V Manager.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [använda StorSimple Manager-tjänsten för att hantera din enhet](storsimple-virtual-array-manager-service-administration.md).

