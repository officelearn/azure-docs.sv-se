---
title: "Hantera volymer på virtuella StorSimple-matrisen | Microsoft Docs"
description: "Beskriver Enhetshanteraren StorSimple och förklarar hur du hanterar volymer på din virtuella StorSimple-matrisen."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: a507bf1866952cb79fa6334fed80c88cd207cd0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Använd Enhetshanteraren för StorSimple-tjänsten för att hantera volymer på den virtuella StorSimple-matris

## <a name="overview"></a>Översikt

Den här självstudiekursen beskrivs hur du använda Enhetshanteraren för StorSimple-tjänsten för att skapa och hantera volymer på din virtuella StorSimple-matrisen.

Tjänsten StorSimple Device Manager är ett tillägg i Azure-portalen där du kan hantera din StorSimple-lösning från ett enda webbgränssnitt. Förutom att hantera filresurser och volymer kan använda du tjänsten StorSimple Enhetshanteraren att visa och hantera enheter, Visa aviseringar, och visa och hantera principer för säkerhetskopiering och den säkerhetskopiera katalogen.

## <a name="volume-types"></a>Volymtyper

StorSimple-volymer kan vara:

* **Lokalt Fäst**: Data på dessa volymer förblir på matrisen och inte läcker över till molnet.
* **Nivåer**: Data i dessa volymer kan läcker över till molnet. När du skapar en nivåindelad volym etableras cirka 10% av utrymmet på den lokala nivån och 90% av utrymmet som har etablerats i molnet. Till exempel om du har etablerat en volym 1 TB 100 GB skulle finns i det lokala utrymmet och 900 GB ska användas i molnet när datanivåer. I sin tur innebär detta att köras utanför det lokala utrymmet på enheten inte kan etablera en nivåindelad volym (eftersom de 10% krävs på den lokala nivån är inte tillgänglig).

### <a name="provisioned-capacity"></a>Etablerad kapacitet
Se följande tabell för maximal etablerad kapacitet för varje volym.

| **Gränsen identifierare**                                       | **Gränsen**     |
|------------------------------------------------------------|---------------|
| Minsta storlek på en nivåindelad volym                            | 500 GB        |
| Maximal storlek för en nivåindelad volym                            | 5 TB          |
| Minsta storleken för en lokalt Fäst volym                    | 50 GB         |
| Maximal storlek för en lokalt Fäst volym                    | 500 GB        |

## <a name="the-volumes-blade"></a>Bladet volymer
Den **volymer** -menyn på din StorSimple-tjänsten sammanfattning bladet visar en lista över lagringsvolymer på en viss StorSimple-matris och gör att du kan hantera dem.

![Volymer bladet](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

En volym som består av en serie med attribut:

* **Volymnamn** – ett beskrivande namn som måste vara unikt och hjälper till att identifiera volymen.
* **Status för** – kan vara online eller offline. Om en volym är offline är den inte synlig för initierarna (servrarna) som har behörighet att använda volymen.
* **Typen** – anger om volymen är **skiktindelade** (standard) eller **lokalt Fäst**.
* **Kapacitet** – Anger mängden data som används jämfört med den totala mängden data som kan lagras av initieraren (server).
* **Säkerhetskopiering** – om virtuella matrisens StorSimple aktiveras automatiskt alla volymer för säkerhetskopiering.
* **Ansluten värdar** – anger initierarna (servrarna) som har åtkomst till den här volymen.

![Information om volymer](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Följ instruktionerna i den här självstudiekursen för att utföra följande uppgifter:

* Lägg till en volym
* Ändra en volym
* Koppla från en volym
* Ta bort en volym

## <a name="add-a-volume"></a>Lägg till en volym

1. Klicka på Sammanfattning bladet StorSimple-tjänsten **+ Lägg till volymen** från kommandofältet. Detta öppnar den **Lägg till volymen** bladet.
   
    ![Lägg till volym](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. I den **Lägg till volymen** bladet gör du följande:
   
   * I den **volymnamn** , ange ett unikt namn för volymen. Namnet måste vara en sträng som innehåller 3 till 127 tecken.
   * I den **typen** nedrullningsbara listan, ange om du vill skapa en **skiktindelade** eller **lokalt Fäst** volym. Arbetsbelastningar som kräver lokala garantier, låg latens och hög prestanda, Välj **lokalt Fäst volym**. Alla andra data, Välj **skiktindelade** volym.
   * I den **kapacitet** anger storleken på volymen. En nivåindelad volym måste vara mellan 500 GB och 5 TB och en lokalt Fäst volym måste vara mellan 50 och 500 GB.
   * * Klicka på **anslutna värdar**, Välj en åtkomstkontrollpost (ACR) som motsvarar iSCSI-initierare som du vill ansluta till den här volymen och klickar sedan på **Välj**.
3. Om du vill lägga till en ny anslutna värddator, klickar du på **Lägg till ny**, ange ett namn för värden och dess iSCSI-kvalificerade namn (IQN) och klicka sedan på **Lägg till**.
   
    ![Lägg till volym](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. När du har konfigurerat din volym klickar du på **skapa**. En volym skapas med de angivna inställningarna och ett meddelande visas på har skapats av samma. Som standard ska säkerhetskopiering aktiveras för volymen.
5. Kontrollera att volymen har skapats genom att gå till den **volymer** bladet. Du bör se den volym som visas.
   
    ![Volymen skapa lyckades](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Ändra en volym

Ändra en volym när du behöver ändra värdar som har åtkomst till volymen. Andra attribut för en volym kan inte ändras när volymen har skapats.

#### <a name="to-modify-a-volume"></a>Så här ändrar du en volym

1. Från den **volymer** inställningen på StorSimple-tjänsten sammanfattning bladet välj virtuella matrisen som du vill du ändra volymen finns.
2. **Välj** volymen och klickar på **anslutna värdar** att visa anslutna värden och ändra den till en annan server.
   
    ![Redigera volym](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Spara dina ändringar genom att klicka på den **spara** kommandofältet. Inställningarna för angivna tillämpas och ett meddelande visas.

## <a name="take-a-volume-offline"></a>Koppla från en volym

Du kan behöva koppla från en volym när du planerar att ändra eller ta bort den. När en volym är offline, är den inte tillgänglig för skrivskyddad åtkomst. Du måste ta volymen offline på värden samt på enheten.

#### <a name="to-take-a-volume-offline"></a>Att koppla från en volym

1. Kontrollera att den aktuella volymen inte används innan den tas offline.
2. Ta volymen offline på värden första. Detta tar bort alla potentiella risken för korrupta data på volymen. Specifikt steg finns anvisningar för operativsystemet för värden.
3. När volymen på värden är offline kan du vidta volymen på matrisen offline genom att utföra följande steg:
   
   * Från den **volymer** inställningen på StorSimple-tjänsten sammanfattning bladet välj virtuella matrisen som den volym som du vill du ta offline finns.
   * **Välj** volymen och klickar på **...**  (alternativt Högerklicka på den här raden) och på snabbmenyn Välj **ta offline**.
     
        ![Offline volym](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Granska informationen i den **ta offline** bladet och bekräfta ditt godkännande av åtgärden. Klicka på **ta offline** att kopplas ifrån volymen. Du ser ett meddelande om den pågående åtgärden.
   * För att bekräfta att volymen har sparats offline, gå till den **volymer** bladet. Du bör se status för volymen som offline.
     
       ![Offline volym bekräftelse](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Ta bort en volym

> [!IMPORTANT]
> Du kan ta bort en volym endast om den är offline.
> 
> 

Utför följande steg för att ta bort en volym.

#### <a name="to-delete-a-volume"></a>Ta bort en volym

1. Från den **volymer** inställningen på StorSimple-tjänsten sammanfattning bladet välj virtuella matrisen som du vill du ta bort volymen finns.
2. **Välj** volymen och klickar på **...**  (alternativt Högerklicka på den här raden) och på snabbmenyn Välj **ta bort**.
   
    ![Ta bort volym](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Kontrollera status för den volym som du vill ta bort. Om du vill ta bort volymen inte är offline, kopplar från den först följa stegen i [kopplar från en volym](#take-a-volume-offline).
4. När du uppmanas att bekräfta i den **ta bort** bladet Godkänn bekräftelsen och klicka på **ta bort**. Volymen tas bort och **volymer** bladet visar uppdaterad lista över volymer i virtuella matrisen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [klona en StorSimple-volym](storsimple-virtual-array-clone.md).

