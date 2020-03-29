---
title: Säkerhetspolicy för StorSimple Snapshot Manager | Microsoft-dokument
description: Beskriver hur du använder MMC-snapin-modulen StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering som styr schemalagda säkerhetskopior.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 7b2df6c8384e78094d80a17d8982b0db9edf73e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933363"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Använda StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering
## <a name="overview"></a>Översikt
En princip för säkerhetskopiering skapar ett schema för säkerhetskopiering av volymdata lokalt eller i molnet. När du skapar en princip för säkerhetskopiering kan du också ange en bevarandeprincip. (Du kan behålla högst 64 ögonblicksbilder.) Mer information om principer för säkerhetskopiering finns i [Säkerhetskopieringstyper](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) i [StorSimple 8000-serien: en hybridmolnlösning](storsimple-overview.md).

I den här självstudien beskrivs hur du:

* Skapa en säkerhetskopieringspolicy
* Redigera en princip för säkerhetskopiering
* Ta bort en princip för säkerhetskopiering

## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy
Använd följande procedur för att skapa en ny princip för säkerhetskopiering.

#### <a name="to-create-a-backup-policy"></a>Så här skapar du en princip för säkerhetskopiering
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Högerklicka på principer för **säkerhetskopiering**i **scopefönstret** och klicka på **Skapa princip för säkerhetskopiering**.

    ![Skapa en säkerhetskopieringspolicy](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Dialogrutan **Skapa en princip** visas.

    ![Fliken Skapa en princip - fliken Allmänt](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Fyll i följande information på fliken **Allmänt:**

   1. Skriv ett namn på principen i textrutan **Namn.**
   2. Skriv namnet på den volymgrupp som är associerad med principen i textrutan **Volymgrupp.**
   3. Välj antingen **Lokal ögonblicksbild** eller Ögonblicksbild **av molnet**.
   4. Välj antalet ögonblicksbilder som ska behållas. Om du väljer **Alla**kommer 64 ögonblicksbilder att behållas (det maximala).
4. Klicka på fliken **Schemalägg** .

    ![Fliken Skapa en princip – fliken Schema](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Fyll i följande information på fliken **Schema:**

   1. Klicka på kryssrutan **Aktivera** om du vill schemalägga nästa säkerhetskopia.
   2. Under **Inställningar**väljer du **En gång**, Varje **dag**, **Varje vecka**eller **Månadsvis**.
   3. Klicka på kalenderikonen i textrutan **Start** och välj ett startdatum.
   4. Under **Avancerade inställningar**kan du ange valfria upprepningsscheman och ett slutdatum.
   5. Klicka på **OK**.

När du har skapat en princip för säkerhetskopiering visas följande information i **resultatfönstret:**

* **Namn** – namnet på säkerhetskopieringsprincipen.
* **Typ** – lokal ögonblicksbild eller ögonblicksbild av molnet.
* **Volymgrupp** – den volymgrupp som är associerad med principen.
* **Kvarhållning** – antalet ögonblicksbilder som behålls. den maximala är 64.
* **Skapad** – det datum då den här principen skapades.
* **Aktiverad** – om principen för närvarande är i kraft: **Sant** anger att den är i kraft. **Falskt** indikerar att det inte är i kraft.

## <a name="edit-a-backup-policy"></a>Redigera en princip för säkerhetskopiering
Använd följande procedur för att redigera en befintlig princip för säkerhetskopiering.

#### <a name="to-edit-a-backup-policy"></a>Så här redigerar du en princip för säkerhetskopiering
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Klicka på **noden Principer för säkerhetskopiering i** fönstret **Scope.** Alla principer för säkerhetskopiering visas i **resultatfönstret.**
3. Högerklicka på den princip som du vill redigera och klicka sedan på **Redigera**.

    ![Redigera en princip för säkerhetskopiering](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. När fönstret **Skapa en princip** visas anger du ändringarna och klickar sedan på **OK**.

## <a name="delete-a-backup-policy"></a>Ta bort en princip för säkerhetskopiering
Använd följande procedur för att ta bort en princip för säkerhetskopiering.

#### <a name="to-delete-a-backup-policy"></a>Så här tar du bort en princip för säkerhetskopiering
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Klicka på **noden Principer för säkerhetskopiering i** fönstret **Scope.** Alla principer för säkerhetskopiering visas i **resultatfönstret.**
3. Högerklicka på den säkerhetskopieringsprincip som du vill ta bort och klicka sedan på **Ta bort**.
4. När bekräftelsemeddelandet visas klickar du på **Ja**.

    ![Ta bort bekräftelse av principer för säkerhetskopiering](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringsjobb](storsimple-snapshot-manager-manage-backup-jobs.md).
