---
title: Microsoft Azure StorSimple Virtual Array backup tutorial | Microsoft-dokument
description: Beskriver hur du säkerhetskopierar StorSimple Virtual Array-resurser och volymer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60581402"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Säkerhetskopiera resurser eller volymer på din StorSimple Virtual Array

## <a name="overview"></a>Översikt

StorSimple Virtual Array är en lokal virtuell hybridlagringsenhet som kan konfigureras som en filserver eller en iSCSI-server. Den virtuella matrisen gör det möjligt för användaren att skapa schemalagda och manuella säkerhetskopior av alla resurser eller volymer på enheten. När den konfigureras som en filserver tillåter den också återställning på objektnivå. I den här självstudien beskrivs hur du skapar schemalagda och manuella säkerhetskopior och utför återställning på objektnivå för att återställa en borttagen fil på den virtuella matrisen.

Den här självstudien gäller endast storsimple-virtuella matriser. Information om 8000-serien finns i [Skapa en säkerhetskopia för 8000-seriens enhet](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Säkerhetskopiera aktier och volymer

Säkerhetskopior ger punkt-i-tid skydd, förbättra återvinningsbarheten och minimera återställningstider för resurser och volymer. Du kan säkerhetskopiera en resurs eller volym på Din StorSimple-enhet på två sätt: **Schemalagd** eller **Manuell**. Var och en av metoderna diskuteras i följande avsnitt.

## <a name="change-the-backup-start-time"></a>Ändra starttiden för säkerhetskopian

> [!NOTE]
> I den här versionen skapas schemalagda säkerhetskopior av en standardprincip som körs dagligen vid en viss tidpunkt och säkerhetskopierar alla resurser eller volymer på enheten. Det går inte att skapa anpassade principer för schemalagda säkerhetskopieringar just nu.


Din StorSimple Virtual Array har en standardprincip för säkerhetskopiering som startar vid en angiven tid på dagen (22:30) och säkerhetskopierar alla resurser eller volymer på enheten en gång om dagen. Du kan ändra den tid då säkerhetskopian startar, men frekvensen och kvarhållningen (som anger antalet säkerhetskopior som ska behållas) kan inte ändras. Under dessa säkerhetskopior säkerhetskopieras hela den virtuella enheten. Detta kan potentiellt påverka enhetens prestanda och påverka arbetsbelastningarna som distribueras på enheten. Därför rekommenderar vi att du schemalägger dessa säkerhetskopior för lågtrafik.

 Om du vill ändra standardstarttiden för säkerhetskopiering utför du följande steg i [Azure-portalen](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Så här ändrar du starttiden för standardprincipen för säkerhetskopiering

1. Gå till **Enheter**. Listan över enheter som är registrerade hos Tjänsten StorSimple Device Manager visas. 
   
    ![navigera till enheter](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Markera och klicka på enheten. **Bladet Inställningar** visas. Gå till **Hantera principer för > säkerhetskopiering**.
   
    ![välj enhet](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. I bladet **Principer för säkerhetskopiering** är standardstarttiden 22:30. Du kan ange den nya starttiden för det dagliga schemat i enhetens tidszon.
   
    ![navigera till principer för säkerhetskopiering](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Klicka på **Spara**.

### <a name="take-a-manual-backup"></a>Gör en manuell säkerhetskopiering

Förutom schemalagda säkerhetskopior kan du när som helst säkerhetskopiera enhetsdata (on-demand) av enhetsdata.

#### <a name="to-create-a-manual-backup"></a>Så här skapar du en manuell säkerhetskopia

1. Gå till **Enheter**. Välj din enhet och högerklicka **...** längst till höger i den valda raden. Välj **Ta backup**på snabbmenyn .
   
    ![navigera för att ta backup](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. Klicka på **Ta förstärkning**i bladet Ta **förstärkning.** Detta kommer att säkerhetskopiera alla resurser på filservern eller alla volymer på din iSCSI-server. 
   
    ![säkerhetskopiering startar](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    En säkerhetskopiering på begäran startar och du ser att ett säkerhetskopieringsjobb har startat.
   
    ![säkerhetskopiering startar](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    När jobbet har slutförts meddelas du igen. Säkerhetskopieringen startar sedan.
   
    ![säkerhetskopieringsjobb som skapats](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Om du vill spåra förloppet för säkerhetskopiorna och titta på jobbinformationen klickar du på meddelandet. Detta tar dig till **Jobbinformation**.
   
     ![information om säkerhetskopieringsjobb](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. När säkerhetskopieringen är klar går du till **Hantering > Säkerhetskopieringskatalog .** Du kommer att se en ögonblicksbild av alla resurser (eller volymer) på din enhet.
   
    ![Slutförd säkerhetskopiering](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Visa befintliga säkerhetskopior
Om du vill visa befintliga säkerhetskopior utför du följande steg i Azure-portalen.

#### <a name="to-view-existing-backups"></a>Så här visar du befintliga säkerhetskopior

1. Gå till **bladet Enheter.** Markera och klicka på enheten. Gå till **Hantering > säkerhetskopieringskatalog i**bladet **Inställningar** .
   
    ![Navigera till säkerhetskopieringskatalog](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Ange följande villkor som ska användas för filtrering:
   
   - **Tidsintervall** – kan vara **senaste 1 timme,** **senaste 24 timmarna**, **Senaste 7 dagarna**, **Senaste 30 dagarna,** **Föregående år**och **Anpassat datum**.
    
   - **Enheter** – välj från listan över filservrar eller iSCSI-servrar som är registrerade med tjänsten StorSimple Device Manager.
   
   - **Initierad** – kan **schemaläggas** automatiskt (av en säkerhetskopieringsprincip) eller **initieras manuellt** (av dig).
   
     ![Filtrera säkerhetskopior](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Klicka på **Använd**. Den filtrerade listan över säkerhetskopior visas i **bladet Säkerhetskopieringskatalog.** Endast 100 säkerhetskopieringselement kan visas vid en viss tidpunkt.
   
    ![Uppdaterad säkerhetskopieringskatalog](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur du administrerar den virtuella storsimple-matrisen](storsimple-ova-web-ui-admin.md).

