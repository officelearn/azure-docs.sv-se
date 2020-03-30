---
title: StorSimple Virtual Array webbgränssnitt administration | Microsoft-dokument
description: Beskriver hur du utför grundläggande enhetsadministrationsuppgifter via webbgränssnittet StorSimple Virtual Array.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254734"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Använda webbgränssnittet för att administrera den virtuella storsimple-matrisen
![flödet för installationsprocess](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Översikt
Självstudierna i den här artikeln gäller för Microsoft Azure StorSimple Virtual Array (kallas även StorSimple lokalt virtuell enhet) som kör ga-versionen (General Availability) från mars 2016. I den här artikeln beskrivs några av de komplexa arbetsflöden och hanteringsuppgifter som kan utföras på StorSimple Virtual Array. Du kan hantera StorSimple Virtual Array med hjälp av StorSimple Manager-tjänstgränssnittet (kallat portalgränssnittet) och det lokala webbgränssnittet för enheten. Den här artikeln fokuserar på de uppgifter som du kan utföra med webbgränssnittet.

Den här artikeln innehåller följande självstudier:

* Hämta krypteringsnyckeln för tjänstdata
* Felsöka installationsfel för webbgränssnitt
* Generera ett loggpaket
* Stänga av eller starta om enheten

## <a name="get-the-service-data-encryption-key"></a>Hämta krypteringsnyckeln för tjänstdata
En krypteringsnyckel för tjänstdata genereras när du registrerar din första enhet med StorSimple Manager-tjänsten. Den här nyckeln krävs sedan med tjänstens registreringsnyckel för att registrera ytterligare enheter med StorSimple Manager-tjänsten.

Om du har tappat bort krypteringsnyckeln för tjänstdata och behöver hämta den utför du följande steg i det lokala webbgränssnittet för den enhet som är registrerad hos tjänsten.

#### <a name="to-get-the-service-data-encryption-key"></a>Så här hämtar du krypteringsnyckeln för tjänstdata
1. Anslut till det lokala webbgränssnittet. Gå till > **Konfigurationsmolninställningar**. **Configuration**
2. Klicka på **Hämta krypteringsnyckel**för tjänstdata längst ned på sidan . En nyckel visas. Kopiera och spara den här nyckeln.
   
    ![hämta krypteringsnyckel för tjänstdata 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Felsöka installationsfel för webbgränssnitt
I vissa fall när du konfigurerar enheten via det lokala webbgränssnittet kan du stöta på fel. Om du vill diagnostisera och felsöka sådana fel kan du köra diagnostiktesterna.

#### <a name="to-run-the-diagnostic-tests"></a>Så här kör du diagnostiktesterna
1. Gå till **Felsöka** > **diagnostiska tester**i det lokala webbgränssnittet .
   
    ![köra diagnostik 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. Klicka på Kör diagnostiska **tester**längst ned på sidan . Detta initierar tester för att diagnostisera eventuella problem med nätverks-, enhets-, webbproxy-, tids- eller molninställningar. Du får ett meddelande om att enheten kör tester.
3. När testerna är klara visas resultaten. Följande exempel visar resultaten av diagnostiska tester. Observera att webbproxyinställningarna inte har konfigurerats på den här enheten och därför kördes inte webbproxytestet. Alla andra tester för nätverksinställningar, DNS-server och tidsinställningar lyckades.
   
    ![köra diagnostik 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Generera ett loggpaket
Ett loggpaket består av alla relevanta loggar som kan hjälpa Microsoft Support med felsökning av eventuella enhetsproblem. I den här versionen kan ett loggpaket genereras via det lokala webbgränssnittet.

#### <a name="to-generate-the-log-package"></a>Så här genererar du loggpaketet
1. Gå till **Felsöka** > **systemloggar**i det lokala webbgränssnittet .
   
    ![generera loggpaket 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. Klicka på **Skapa loggpaket**längst ned på sidan . Ett paket med systemloggarna skapas. Det här tar ett par minuter.
   
    ![generera loggpaket 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Du meddelas när paketet har skapats och sidan uppdateras för att ange tid och datum då paketet skapades.
   
    ![generera loggpaket 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Klicka på **Hämta loggpaket**. Ett paket med dragkedja kommer att laddas ner på ditt system.
   
    ![generera loggpaket 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Du kan packa upp det nedladdade loggpaketet och visa systemloggfilerna.

## <a name="shut-down-and-restart-your-device"></a>Stänga av och starta om enheten
Du kan stänga av eller starta om den virtuella enheten med hjälp av det lokala webbgränssnittet. Vi rekommenderar att du tar volymerna eller resurserna offline på värden innan du startar om. Detta minimerar risken för datakorruption. 

#### <a name="to-shut-down-your-virtual-device"></a>Så här stänger du av den virtuella enheten
1. Gå till > **Underhållsströminställningar** **Maintenance**i det lokala webbgränssnittet .
2. Klicka på Stäng av **den**längst ned på sidan.
   
    ![enhetens avstängning 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. En varning visas som anger att en avstängning av enheten kommer att avbryta alla IO som pågick, vilket resulterar i ett driftstopp. Klicka på kryssikonen ![kryssikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![varning för avstängning av enheten](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Du får ett meddelande om att avstängningen har initierats.
   
    ![enhetens avstängning har startats](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Enheten stängs nu av. Om du vill starta enheten måste du göra det via Hyper-V-hanteraren.

#### <a name="to-restart-your-virtual-device"></a>Så här startar du om den virtuella enheten
1. Gå till > **Underhållsströminställningar** **Maintenance**i det lokala webbgränssnittet .
2. Klicka på **Starta om**längst ned på sidan .
   
    ![omstart av enheten](./media/storsimple-ova-web-ui-admin/image36.png)
3. En varning visas som anger att omstart av enheten kommer att avbryta alla IOs som pågick, vilket resulterar i ett driftstopp. Klicka på kryssikonen ![kryssikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![starta om varning](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Du får ett meddelande om att omstarten har initierats.
   
    ![starta om initierad](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Medan omstarten pågår förlorar du anslutningen till användargränssnittet. Du kan övervaka omstarten genom att uppdatera användargränssnittet med jämna mellanrum. Du kan också övervaka enhetens omstartsstatus via Hyper-V-hanteraren.

## <a name="next-steps"></a>Nästa steg
Läs om hur du [använder StorSimple Manager-tjänsten för att hantera din enhet](storsimple-virtual-array-manager-service-administration.md).

