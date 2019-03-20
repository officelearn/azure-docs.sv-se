---
title: Microsoft Azure StorSimple Virtual Array säkerhetskopiering självstudien | Microsoft Docs
description: Beskriver hur du säkerhetskopierar StorSimple Virtual Array resurserna och volymerna.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a61dcca1f78b6ba444a2deefcf6b8bb4fd5c5087
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121986"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Säkerhetskopiera resurser eller volymer på StorSimple Virtual Array

## <a name="overview"></a>Översikt

StorSimple Virtual Array är en hybrid cloud storage lokala virtuell enhet som kan konfigureras som en filserver eller en iSCSI-server. Den virtuella matrisen tillåter användare att skapa schemalagda säkerhetskopieringar och manuella säkerhetskopieringar av alla resurser eller volymer på enheten. När konfigurerad som en filserver, kan också objektnivååterställning. Den här självstudien beskrivs hur du skapar schemalagda säkerhetskopieringar och manuella säkerhetskopieringar och utföra återställning på objektnivå om du vill återställa en borttagen fil på din virtuella matris.

Den här självstudiekursen gäller för de StorSimple Virtual Array endast. För information om 8000-serien, går du till [skapar en säkerhetskopia för enheten i 8000-serien](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Säkerhetskopiering av filresurser och volymer

Säkerhetskopieringar ger point-in-time-skydd, förbättrar återställningsmöjligheterna och minimera återställningstiderna för resurserna och volymerna. Du kan säkerhetskopiera en resurs eller en volym på StorSimple-enheten på två sätt: **Schemalagda** eller **manuell**. Var och en av metoderna som beskrivs i följande avsnitt.

## <a name="change-the-backup-start-time"></a>Ändra starttid för säkerhetskopiering

> [!NOTE]
> I den här versionen skapas schemalagda säkerhetskopieringar av en standardprincip som körs varje dag vid en viss tidpunkt och säkerhetskopierar den filresurser eller volymer på enheten. Det går inte att skapa anpassade principer för schemalagda säkerhetskopieringar just nu.


StorSimple Virtual Array har en standardprincip för säkerhetskopiering som börjar vid en angiven tid på dagen (22:30) och säkerhetskopierar den filresurser eller volymer på enheten en gång om dagen. Du kan ändra den tid då säkerhetskopiering startar, men frekvensen och kvarhållning (som anger antalet säkerhetskopior som ska kvarhållas) inte kan ändras. Hela virtuella enheten har säkerhetskopierats under dessa säkerhetskopior. Detta kan potentiellt påverka prestandan för enheten och påverkar arbetsbelastningar som distribuerats på enheten. Därför rekommenderar vi att du schemalägger säkerhetskopiorna för låg belastning.

 Ändra starttid för standard säkerhetskopiering genom att utföra följande steg i den [Azure-portalen](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Ändra starttiden för standardprincip för säkerhetskopiering

1. Gå till **enheter**. I listan med enheter som registreras med StorSimple Device Manager-tjänsten visas. 
   
    ![Gå till enheter](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Välj och klicka på din enhet. Den **inställningar** bladet visas. Gå till **hantera > principer för säkerhetskopiering**.
   
    ![Välj din enhet](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. I den **principer för säkerhetskopiering** bladet starttiden som standard är 22:30. Du kan ange nya starttiden för dagligt schema i enhetens tidszon.
   
    ![Gå till principer för säkerhetskopiering](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Klicka på **Spara**.

### <a name="take-a-manual-backup"></a>Gör en manuell säkerhetskopia

Förutom schemalagda säkerhetskopieringar, kan du utföra en manuell (på begäran) säkerhetskopiering av enhetsdata när som helst.

#### <a name="to-create-a-manual-backup"></a>Så här skapar du en manuell säkerhetskopia

1. Gå till **enheter**. Välj din enhet och högerklicka på **...**  längst till höger i den markerade raden. På snabbmenyn väljer **säkerhetskopiera**.
   
    ![Navigera för att säkerhetskopiera](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. I den **säkerhetskopiera** bladet klickar du på **säkerhetskopiera**. Detta kommer att säkerhetskopiera alla resurser på servern eller alla volymer på iSCSI-servern. 
   
    ![säkerhetskopieringen från](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Startar en säkerhetskopiering på begäran och du ser att ett säkerhetskopieringsjobb har startats.
   
    ![säkerhetskopieringen från](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    När jobbet har slutförts, meddelas du igen. Säkerhetskopieringen startas.
   
    ![säkerhetskopieringsjobbet har skapats](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Klicka på meddelandet för att följa förloppet för säkerhetskopiorna och titta på Jobbinformationen. Då kommer du till **Jobbdetaljer**.
   
     ![information om säkerhetskopieringsjobb](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. När säkerhetskopieringen är klar, går du till **Management > säkerhetskopieringskatalog**. En molnögonblicksbild av alla resurser (eller volymer) visas på din enhet.
   
    ![Slutförda säkerhetskopiering](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Visa befintliga säkerhetskopieringar
Utför följande steg om du vill visa befintliga säkerhetskopior i Azure-portalen.

#### <a name="to-view-existing-backups"></a>Visa befintliga säkerhetskopior

1. Gå till **enheter** bladet. Välj och klicka på din enhet. I den **inställningar** gå till bladet **Management > säkerhetskopieringskatalog**.
   
    ![Gå till säkerhetskopieringskatalogen](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Ange följande villkor som ska användas för filtrering:
   
   - **Tidsintervall** – kan vara **senaste 1 timme**, **senaste 24 timmarna**, **senaste 7 dagarna**, **de senaste 30 dagarna**, **senaste året** , och **anpassade datum**.
    
   - **Enheter** – Välj i listan över filservrar eller iSCSI-servrar som är registrerade med StorSimple Device Manager-tjänsten.
   
   - **Initierade** – kan vara automatiskt **schemalagd** (genom en princip för säkerhetskopiering) eller **manuellt** initieras (av du).
   
     ![Filtrera säkerhetskopior](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Klicka på **Verkställ**. Den filtrerade listan över säkerhetskopieringar visas i den **säkerhetskopieringskatalog** bladet. Obs endast 100 säkerhetskopierade element kan visas vid en given tidpunkt.
   
    ![Uppdaterade säkerhetskopieringskatalogen](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om [administrera StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

