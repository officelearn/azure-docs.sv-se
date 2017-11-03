---
title: "Principer för säkerhetskopiering av StorSimple Snapshot Manager | Microsoft Docs"
description: "Beskriver hur du använder StorSimple Snapshot Manager MMC-snapin-modulen för att skapa och hantera principer för säkerhetskopiering som styr schemalagda säkerhetskopieringar."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 218c89e403673c16c72da95aa2c1d685bbed5a86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Använd StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering
## <a name="overview"></a>Översikt
En princip för säkerhetskopiering skapar ett schema för att säkerhetskopiera volymdata lokalt eller i molnet. När du skapar en princip för säkerhetskopiering måste ange du också en bevarandeprincip. (Du kan lagra maximalt 64 ögonblicksbilder.) Mer information om principer för säkerhetskopiering finns [säkerhetskopiera typer](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) i [StorSimple 8000-serien: en hybridmolnlösningen](storsimple-overview.md).

Den här självstudiekursen beskrivs hur du:

* Skapa en princip för säkerhetskopiering
* Redigera en princip för säkerhetskopiering
* Ta bort en princip för säkerhetskopiering

## <a name="create-a-backup-policy"></a>Skapa en princip för säkerhetskopiering
Använd följande procedur för att skapa en ny säkerhetskopieringsprincip.

#### <a name="to-create-a-backup-policy"></a>Så här skapar du en princip för säkerhetskopiering
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret högerklickar du på **Säkerhetskopieringsprinciper**, och klicka på **skapa princip för säkerhetskopiering**.

    ![Skapa en princip för säkerhetskopiering](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Den **skapa en princip** dialogrutan visas.

    ![Skapa en princip – fliken Allmänt](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. På den **allmänna** gör du följande information:

   1. I den **namnet** skriver du ett namn för principen.
   2. I den **volym grupp** skriver du namnet på gruppen volym som är associerade med principen.
   3. Välj antingen **lokal ögonblicksbild** eller **molnet ögonblicksbild**.
   4. Välj antalet ögonblicksbilder ska bevaras. Om du väljer **alla**, 64 ögonblicksbilder kommer att hållas kvar (max).
4. Klicka på den **schema** fliken.

    ![Skapa en princip - fliken schema](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. På den **schema** gör du följande information:

   1. Klicka på den **aktivera** kryssrutan om du vill schemalägga under nästa säkerhetskopiering.
   2. Under **inställningar**väljer **en gång**, **dagliga**, **veckovisa**, eller **månatliga**.
   3. I den **starta** textrutan, klicka på kalenderikonen och välj ett startdatum.
   4. Under **avancerade inställningar**, kan du ange valfria Upprepa scheman och ett slutdatum.
   5. Klicka på **OK**.

När du har skapat en princip för säkerhetskopiering, visas följande information i den **resultat** fönstret:

* **Namnet** – namnet på säkerhetskopieringsprincipen.
* **Typen** – lokal ögonblicksbild eller ögonblicksbild i molnet.
* **Volymen grupp** – gruppen volym som är associerade med principen.
* **Kvarhållning** – antalet ögonblicksbilder behålls; det maximala antalet är 64.
* **Skapa** – det datum som principen skapades.
* **Aktiverad** – om principen är för närvarande i praktiken: **True** anger att det är aktivt; **FALSKT** anger att det inte är aktiv.

## <a name="edit-a-backup-policy"></a>Redigera en princip för säkerhetskopiering
Använd följande procedur om du vill redigera en befintlig princip för säkerhetskopiering.

#### <a name="to-edit-a-backup-policy"></a>Så här redigerar du en princip för säkerhetskopiering
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan klickar du på den **Säkerhetskopieringsprinciper** nod. Alla principer för säkerhetskopiering som visas i den **resultat** fönstret.
3. Högerklicka på den princip som du vill redigera och klicka sedan på **redigera**.

    ![Redigera en princip för säkerhetskopiering](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. När den **skapa en princip** visas, gör ändringarna och klicka sedan på **OK**.

## <a name="delete-a-backup-policy"></a>Ta bort en princip för säkerhetskopiering
Använd följande procedur för att ta bort en princip för säkerhetskopiering.

#### <a name="to-delete-a-backup-policy"></a>Ta bort en princip för säkerhetskopiering
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan klickar du på den **Säkerhetskopieringsprinciper** nod. Alla principer för säkerhetskopiering som visas i den **resultat** fönstret.
3. Högerklicka på principen för säkerhetskopiering som du vill ta bort och klicka sedan på **ta bort**.
4. När bekräftelsemeddelandet visas, klickar du på **Ja**.

    ![Princip för säkerhetskopiering bekräftelse på borttagning](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använda StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringsjobb](storsimple-snapshot-manager-manage-backup-jobs.md).
