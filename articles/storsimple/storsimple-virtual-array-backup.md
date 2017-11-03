---
title: "Microsoft Azure StorSimple virtuell matris säkerhetskopiering kursen | Microsoft Docs"
description: "Beskriver hur du säkerhetskopierar virtuella StorSimple-matris filresurser och volymer."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c926f0c80ce56cac3106ad97ec3ec2e18a8e2cc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Säkerhetskopiera resurser eller volymer på din virtuella StorSimple-matris

## <a name="overview"></a>Översikt

Den virtuella StorSimple-matrisen är en hybrid cloud lokalt virtuell lagringsenhet som kan konfigureras som en filserver eller en iSCSI-server. Virtuella matrisen tillåter användare att skapa schemalagda säkerhetskopieringar och manuella säkerhetskopieringar för alla resurser eller volymer på enheten. När du har konfigurerats som en filserver, kan också objektnivååterställning. Den här självstudiekursen beskrivs hur du skapar schemalagda säkerhetskopieringar och manuella säkerhetskopieringar och utföra återställning på objektnivå om du vill återställa en borttagen fil på din virtuella matrisen.

Den här kursen gäller StorSimple virtuell matriserna endast. För information om 8000-serien, gå till [skapa en säkerhetskopia för enheten i 8000-serien](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Säkerhetskopiera filresurser och volymer

Säkerhetskopieringar ger tidpunkts-skydd, förbättrar återställningsmöjligheterna och minimera återställningstider för filresurser och volymer. Du kan säkerhetskopiera en resurs eller en volym på din StorSimple-enhet på två sätt: **schemalagda** eller **manuell**. Var och en av metoderna som beskrivs i följande avsnitt.

## <a name="change-the-backup-start-time"></a>Ändra starttiden för säkerhetskopiering

> [!NOTE]
> Schemalagda säkerhetskopieringar skapas i den här versionen av en standardprincip som körs varje dag vid en viss tidpunkt och säkerhetskopierar alla resurser eller volymer på enheten. Det går inte att skapa anpassade principer för schemalagda säkerhetskopieringar just nu.


Din virtuella StorSimple-matris har en standardprincip för säkerhetskopiering som börjar med en angiven tid på dagen (22:30) och säkerhetskopierar alla resurser eller volymer på enheten en gång om dagen. Du kan ändra den tid då säkerhetskopieringen startar, men frekvensen och kvarhållning (som anger hur många säkerhetskopior av ska behålla) inte kan ändras. Hela virtuella enheten har säkerhetskopierats under dessa säkerhetskopior. Detta kan potentiellt påverka prestandan för enheten och påverkar arbetsbelastningar som har distribuerats på enheten. Därför rekommenderar vi att du schemalägger dessa säkerhetskopior för låg belastning.

 Om du vill ändra standard säkerhetskopiering starttiden, utför följande steg i den [Azure-portalen](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Ändra starttiden för standardprincip för säkerhetskopiering

1. Gå till **enheter**. Lista över enheter som registrerats StorSimple Device Manager-tjänsten ska visas. 
   
    ![Navigera till enheter](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Välj och klicka på enheten. Den **inställningar** bladet visas. Gå till **hantera > Säkerhetskopieringsprinciper**.
   
    ![Välj enhet](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. I den **Säkerhetskopieringsprinciper** bladet starttiden som standard är 22:30. Du kan ange nya starttiden för schemat i enheten tidszon.
   
    ![Navigera till principer för säkerhetskopiering](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Klicka på **Spara**.

### <a name="take-a-manual-backup"></a>Gör en manuell säkerhetskopia

Förutom schemalagda säkerhetskopieringar, kan du utföra en manuell (på begäran) säkerhetskopiering av data på enheten när som helst.

#### <a name="to-create-a-manual-backup"></a>Så här skapar du en manuell säkerhetskopia

1. Gå till **enheter**. Välj enhet och högerklicka på **...**  längst till höger i den markerade raden. På snabbmenyn Välj **ta säkerhetskopia**.
   
    ![Navigera för att göra säkerhetskopieringar](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. I den **ta säkerhetskopia** bladet, klickar du på **ta säkerhetskopia**. Detta ska säkerhetskopiera alla filresurser på filservern eller alla volymer på iSCSI-servern. 
   
    ![Starta Säkerhetskopiering](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    En säkerhetskopiering på begäran startar och du ser att ett säkerhetskopieringsjobb har startats.
   
    ![Starta Säkerhetskopiering](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    När jobbet har slutförts, meddelas du igen. Säkerhetskopieringen startas.
   
    ![Säkerhetskopieringsjobbet har skapats](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Klicka på meddelandet för att följa förloppet för säkerhetskopieringar och granska Jobbinformationen. Då kommer du att **Jobbdetaljer**.
   
     ![information om säkerhetskopieringsjobb](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. När säkerhetskopieringen är klar, går du till **Management > säkerhetskopieringskatalog**. En ögonblicksbild i molnet för alla resurser (eller volymer) visas på enheten.
   
    ![Slutförda säkerhetskopiering](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Visa befintliga säkerhetskopior
Utför följande steg om du vill visa befintliga säkerhetskopior i Azure-portalen.

#### <a name="to-view-existing-backups"></a>Visa befintliga säkerhetskopior

1. Gå till **enheter** bladet. Välj och klicka på enheten. I den **inställningar** gå till bladet **Management > säkerhetskopieringskatalog**.
   
    ![Gå till katalogen för säkerhetskopiering](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Ange följande kriterier som ska användas för att filtrera:
   
    - **Tidsintervallet** – kan vara **senaste 1 timme**, **föregående 24 timmar**, **senaste 7 dagarna**, **de senaste 30 dagarna**, **senaste året** , och **anpassade datum**.
    
    - **Enheter** – Välj i listan över filservrar eller iSCSI-servrar har registrerats med din StorSimple Device Manager-tjänst.
   
    - **Initierade** – kan vara automatiskt **schemalagda** (genom en princip för säkerhetskopiering) eller **manuellt** initieras (av du).
   
    ![Filtrera säkerhetskopieringar](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Klicka på **Använd**. Filtrerad lista över säkerhetskopieringar visas i den **säkerhetskopieringskatalog** bladet. Obs endast 100 säkerhetskopiering elementen kan visas vid en given tidpunkt.
   
    ![Uppdaterade säkerhetskopieringskatalogen](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

