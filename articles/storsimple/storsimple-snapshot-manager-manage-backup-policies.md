---
title: Principer för säkerhetskopiering av StorSimple Snapshot Manager | Microsoft Docs
description: Beskriver hur du använder StorSimple Snapshot Manager MMC-snapin-modulen för att skapa och hantera principer för säkerhetskopiering som styr schemalagda säkerhetskopieringar.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 7dac26b058b959011e38b4373369b8a1115d2705
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303267"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Använd StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering
## <a name="overview"></a>Översikt
En princip för säkerhetskopiering skapar ett schema för säkerhetskopiering av volymdata lokalt eller i molnet. När du skapar en princip för säkerhetskopiering kan ange du också en bevarandeprincip. (Du kan lagra högst 64 ögonblicksbilder.) Läs mer om principer för säkerhetskopiering, [säkerhetskopiera typer](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) i [StorSimple 8000-serien: en hybridmolnlösning](storsimple-overview.md).

I den här självstudien beskrivs hur du:

* Skapa en säkerhetskopieringspolicy
* Redigera en princip för säkerhetskopiering
* Ta bort en princip för säkerhetskopiering

## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy
Använd följande procedur för att skapa en ny säkerhetskopieringsprincip.

#### <a name="to-create-a-backup-policy"></a>Skapa en princip för säkerhetskopiering
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret högerklickar du på **principer för säkerhetskopiering**, och klicka på **skapa Säkerhetskopieringspolicyn**.

    ![Skapa en säkerhetskopieringspolicy](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Den **skapa en princip** dialogrutan visas.

    ![Skapa en princip – fliken Allmänt](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. På den **allmänna** och ange följande information:

   1. I den **namn** anger du ett namn för principen.
   2. I den **volym grupp** textrutan, skriver du in namnet på gruppen volym som är associerade med principen.
   3. Välj antingen **lokal ögonblicksbild** eller **ögonblicksbild i molnet**.
   4. Välj antalet ögonblicksbilder att behålla. Om du väljer **alla**, 64 ögonblicksbilder kommer att bevaras (maximalt).
4. Klicka på den **schema** fliken.

    ![Skapa en princip – fliken schema](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. På den **schema** och ange följande information:

   1. Klicka på den **aktivera** kryssrutan för att schemalägga nästa säkerhetskopiering.
   2. Under **inställningar**väljer **en gång**, **dagliga**, **veckovisa**, eller **månatliga**.
   3. I den **starta** textrutan, klicka på kalenderikonen och välj ett startdatum.
   4. Under **avancerade inställningar**, du kan ange valfri Upprepa scheman och ett slutdatum.
   5. Klicka på **OK**.

När du har skapat en princip för säkerhetskopiering, visas följande information i den **resultat** fönstret:

* **Namn på** – namnet på säkerhetskopieringsprincipen.
* **Typ** – lokal ögonblicksbild eller ögonblicksbild i molnet.
* **Volymen grupp** – gruppen volym som är associerade med principen.
* **Kvarhållning** – antalet ögonblicksbilder bevaras; det maximala värdet är 64.
* **Skapade** – det datum som den här principen har skapats.
* **Aktiverad** – om principen är för närvarande i kraft: **SANT** indikerar att det är aktivt; **FALSKT** indikerar att det inte är i kraft.

## <a name="edit-a-backup-policy"></a>Redigera en princip för säkerhetskopiering
Använd följande procedur om du vill redigera en befintlig säkerhetskopieringspolicy.

#### <a name="to-edit-a-backup-policy"></a>Så här redigerar du en princip för säkerhetskopiering
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret klickar du på den **principer för säkerhetskopiering** noden. Alla principer för säkerhetskopiering visas i den **resultat** fönstret.
3. Högerklicka på den princip som du vill redigera och klicka sedan på **redigera**.

    ![Redigera en princip för säkerhetskopiering](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. När den **skapa en princip** fönster visas anger du dina ändringar och klicka sedan på **OK**.

## <a name="delete-a-backup-policy"></a>Ta bort en princip för säkerhetskopiering
Använd följande procedur för att ta bort en princip för säkerhetskopiering.

#### <a name="to-delete-a-backup-policy"></a>Att ta bort en princip för säkerhetskopiering
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret klickar du på den **principer för säkerhetskopiering** noden. Alla principer för säkerhetskopiering visas i den **resultat** fönstret.
3. Högerklicka på principen för säkerhetskopiering som du vill ta bort och klicka sedan på **ta bort**.
4. När bekräftelsemeddelandet visas, klickar du på **Ja**.

    ![Ta bort säkerhetskopieringsprincipen bekräftelse](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använda StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringsjobb](storsimple-snapshot-manager-manage-backup-jobs.md).
