---
title: Hantera virtuella StorSimple-matris resurser | Microsoft Docs
description: "Beskriver Enhetshanteraren StorSimple och förklarar hur du hanterar resurser på din virtuella StorSimple-matrisen."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: e5c62689de36baa175001f5f4f70d87568876ef0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att hantera resurser på den virtuella StorSimple-matris

## <a name="overview"></a>Översikt

Den här självstudiekursen beskrivs hur du använda Enhetshanteraren för StorSimple-tjänsten för att skapa och hantera resurser på din virtuella StorSimple-matrisen.

Tjänsten StorSimple Device Manager är ett tillägg i Azure-portalen där du kan hantera din StorSimple-lösning från ett enda webbgränssnitt. Förutom att hantera filresurser och volymer kan använda du StorSimple Enhetshanteraren-tjänsten för att visa och hantera enheter, Visa aviseringar, hantera principer för säkerhetskopiering och hantera katalogen för säkerhetskopieringen.

## <a name="share-types"></a>Dela typer

StorSimple resurser kan vara:

* **Lokalt Fäst**: Data på resurserna förblir på matrisen och inte läcker över till molnet.
* **Nivåer**: Data i resurserna kan läcker över till molnet. När du skapar en nivåindelad resurs cirka 10% av området är etablerad på den lokala nivån och 90% av utrymmet som har etablerats i molnet. Till exempel om du har etablerat en resurs för 1 TB 100 GB skulle finns i det lokala utrymmet och 900 GB ska användas i molnet när datanivåer. I sin tur innebär detta att köras utanför det lokala utrymmet på enheten inte kan etablera en skiktad resurs (eftersom de 10% krävs på den lokala nivån är inte tillgänglig).

### <a name="provisioned-capacity"></a>Etablerad kapacitet

Se följande tabell för maximal etablerad kapacitet för varje resurstyp av.

| **Gränsen identifierare** | **Gränsen** |
| --- | --- |
| Minsta storlek på en skiktad resurs |500 GB |
| Maximal storlek för en skiktad resurs |20 TB |
| Minsta storleken för en lokalt Fäst resurs |50 GB |
| Maximal storlek för en lokalt Fäst resurs |2 TB |

## <a name="the-shares-blade"></a>Bladet resurser

Den **resurser** -menyn på din StorSimple-tjänsten sammanfattning bladet visar listan över resurser för lagring på en viss StorSimple-matris och gör att du kan hantera dem.

![Resurser-bladet](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

En resurs består av en serie med attribut:

* **Resursnamn** – ett beskrivande namn som måste vara unikt och hjälper till att identifiera resursen.
* **Status för** – kan vara online eller offline. Om en resurs som är offline, kommer användare av resursen inte att komma åt den.
* **Typen** – anger om resursen är **skiktindelade** (standard) eller **lokalt Fäst**.
* **Kapacitet** – Anger mängden data som används jämfört med den totala mängden data som kan lagras på resursen.
* **Beskrivning** – en valfri inställning som hjälper till att beskriva resursen.
* **Behörigheter** -NTFS-behörigheter för resursen som kan hanteras via Utforskaren.
* **Säkerhetskopiering** – om virtuella matrisens StorSimple aktiveras automatiskt alla resurser för säkerhetskopiering.

![Delar information](./media/storsimple-virtual-array-manage-shares/share-details.png)

Följ instruktionerna i den här självstudiekursen för att utföra följande uppgifter:

* Lägg till en resurs
* Ändra en resurs
* Koppla från en resurs
* Ta bort en resurs

## <a name="add-a-share"></a>Lägg till en resurs

1. Klicka på Sammanfattning bladet StorSimple-tjänsten **+ Lägg till resursen** från kommandofältet. Detta öppnar den **Lägg till resursen** bladet.

    ![Lägg till resurs](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. I den **Lägg till resursen** bladet gör du följande:
   
    1. I den **resursnamn** , ange ett unikt namn för din resurs. Namnet måste vara en sträng som innehåller 3 till 127 tecken.

    2. En valfri **beskrivning** för resursen. Beskrivningen hjälper dig att identifiera resurs-ägare.

    3. I den **typen** nedrullningsbara listan, ange om du vill skapa en **skiktindelade** eller **lokalt Fäst** delar. Arbetsbelastningar som kräver lokala garantier, låg latens och hög prestanda, Välj **lokalt Fäst resursen**. Alla andra data, Välj **skiktindelade** delar.

    4. I den **kapacitet** anger storleken på resursen. En skiktad resursen måste vara mellan 500 GB och 20 TB och en lokalt Fäst resursen måste vara mellan 50 GB och 2 TB.

    5. I den **Ange standard fullständig behörighet** fältet, tilldela behörigheter till användaren eller gruppen som har åtkomst till resursen. Ange namnet på användaren eller användargruppen i  _john@contoso.com_  format. Vi rekommenderar att du använder en användargrupp (i stället för en enskild användare) så att administratören behörighet att komma åt resurserna. När du har tilldelat behörigheter här kan använda du sedan Utforskaren för att ändra dessa behörigheter.
3. När du har konfigurerat din resurs klickar du på **skapa**. En resurs ska skapas med de angivna inställningarna och ett meddelande visas. Som standard ska säkerhetskopiering aktiveras för resursen.
4. Kontrollera att resursen har skapats genom att gå till den **resurser** bladet. Du bör se den resurs som visas.
   
    ![Dela skapa lyckades](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Ändra en resurs

Ändra en resurs när du behöver ändra beskrivningen av resursen. Inga andra resursegenskaper kan inte ändras när resursen har skapats.

#### <a name="to-modify-a-share"></a>Att ändra en resurs

1. Från den **resurser** inställningen på StorSimple-tjänsten sammanfattning bladet välj virtuella matrisen som resursen du vill du ändra finns.
2. **Välj** Visa aktuella beskrivningen och ändra den resursen.
3. Spara dina ändringar genom att klicka på den **spara** kommandofältet. Inställningarna för angivna tillämpas och ett meddelande visas.
   
    ![ Redigera resurs](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Koppla från en resurs

Du kan behöva koppla från en resurs när du planerar att ändra eller ta bort den. När en resurs är offline, är den inte tillgänglig för skrivskyddad åtkomst. Du måste ta resursen offline på värden samt på enheten.

#### <a name="to-take-a-share-offline"></a>Att koppla från en resurs

1. Kontrollera att resursen i fråga inte används innan den tas offline.
2. Ta resursen på matrisen offline genom att utföra följande steg:
   
    1. Från den **resurser** inställningen på StorSimple-tjänsten sammanfattning bladet välj virtuella matrisen som resursen du vill du ta offline finns.

    2. **Välj** resursen och klickar på **...**  (alternativt Högerklicka på den här raden) och på snabbmenyn Välj **ta offline**.
     
        ![Frånkopplad resurs](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Granska informationen i den **ta offline** bladet och bekräfta ditt godkännande av åtgärden. Klicka på **ta offline** att ta resursen offline. Du ser ett meddelande om den pågående åtgärden.

    4. För att bekräfta att resursen har sparats offline, gå till den **resurser** bladet. Du bör se status för resursen som offline.

## <a name="delete-a-share"></a>Ta bort en resurs

> [!IMPORTANT]
> Du kan ta bort en resurs bara om den är offline.


Utför följande steg för att ta bort en resurs.

#### <a name="to-delete-a-share"></a>Ta bort en resurs

1. Från den **resurser** inställningen på StorSimple-tjänsten sammanfattning bladet välj virtuella matrisen som du vill ta bort resursen finns.
2. **Välj** resursen och klickar på **...**  (alternativt Högerklicka på den här raden) och på snabbmenyn Välj **ta bort**.
   
    ![Ta bort resursen](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Kontrollera status för resursen du vill ta bort. Om du vill ta bort resursen inte är offline, koppla från den först. Följ stegen i [koppla en resurs från](#take-a-share-offline).
4. När du uppmanas att bekräfta i den **ta bort** bladet Godkänn bekräftelsen och klicka på **ta bort**. Resursen tas bort och **resurser** bladet visar uppdaterade listan över resurser i en virtuell matris.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [klona en StorSimple-resurs](storsimple-virtual-array-clone.md).

