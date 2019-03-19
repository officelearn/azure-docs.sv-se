---
title: Hantera StorSimple Virtual Array filresurser | Microsoft Docs
description: Beskriver StorSimple Device Manager och som förklarar hur du använder den för att hantera resurser på din StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 82a6cdb6c9a39a0d196049a7ba662681ea06b36a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092437"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Använda StorSimple Device Manager-tjänsten för att hantera resurser på StorSimple Virtual Array

## <a name="overview"></a>Översikt

Den här självstudien beskrivs hur du använder StorSimple Device Manager-tjänsten för att skapa och hantera resurser på din StorSimple Virtual Array.

StorSimple Device Manager-tjänsten är ett tillägg i Azure-portalen som låter dig hantera din StorSimple-lösning från ett enda webbgränssnitt. Du kan använda StorSimple Device Manager-tjänsten att visa och hantera enheter, Visa aviseringar, hantera principer för säkerhetskopiering och hantera säkerhetskopieringskatalogen utöver att hantera resurserna och volymerna.

## <a name="share-types"></a>Dela typer

StorSimple-resurser kan vara:

* **Lokalt fixerade**: Data i dessa resurser stannar i matrisen hela tiden och läcker över inte till molnet.
* **Nivåindelad**: Data i dessa resurser kan läcker över till molnet. När du skapar en nivåindelad resurs cirka 10% av utrymmet är etablerad på den lokala nivån och 90% av utrymmet som har etablerats i molnet. Till exempel om du har etablerat en resurs som 1 TB, 100 GB skulle finnas i det lokala utrymmet och 900 GB skulle användas i molnet när de data-nivåerna. Detta innebär i sin tur att du kör utanför det lokala utrymmet på enheten, inte kan etablera en nivåindelad resurs (eftersom dessa 10% som krävs på den lokala nivån inte är tillgängligt).

### <a name="provisioned-capacity"></a>Etablerad kapacitet

Se tabellen nedan för högsta etablerade kapaciteten för varje resurstyp av.

| **Gräns för identifierare** | **Gräns** |
| --- | --- |
| Minsta storlek på en nivåindelad resurs |500 GB |
| Maximal storlek för en nivåindelad resurs |20 TB |
| Minsta storlek på en lokalt Fäst resurs |50 GB |
| Maximal storlek på en lokalt Fäst resurs |2 TB |

## <a name="the-shares-blade"></a>Bladet för filresurser

Den **resurser** menyn på din sammanfattningsbladet för StorSimple-tjänsten visar listan över resurser för lagring på en viss StorSimple-matris och gör att du kan hantera dem.

![Resurser-bladet](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

En resurs består av en serie med attribut:

* **Resursnamn** – ett beskrivande namn som måste vara unikt och hjälper dig att identifiera resursen.
* **Status för** – kan vara online eller offline. Om en resurs är offline, kan användare av resursen inte komma åt den.
* **Typ** – anger om resursen är **Nivåindelad** (standard) eller **lokalt fixerade**.
* **Kapacitet** – anger hur mycket data som används jämfört med den totala mängden data som kan lagras på resursen.
* **Beskrivning av** – en valfri inställning som hjälper till att beskriva resursen.
* **Behörigheter** -NTFS-behörigheter till resursen som kan hanteras via Windows Explorer.
* **Säkerhetskopiering** – om av StorSimple Virtual Array aktiveras automatiskt alla resurser för säkerhetskopiering.

![Delar information](./media/storsimple-virtual-array-manage-shares/share-details.png)

Följ instruktionerna i den här självstudien för att utföra följande uppgifter:

* Lägga till en resurs
* Ändra en resurs
* Koppla från en resurs
* Ta bort en resurs

## <a name="add-a-share"></a>Lägga till en resurs

1. StorSimple sammanfattningsbladet för tjänsten, klicka på **+ Lägg till resurs** i kommandofältet. Gör det öppnas den **Lägg till resurs** bladet.

    ![Lägg till resurs](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. I den **Lägg till resurs** bladet gör du följande:
   
   1. I den **resursnamn** fältet, anger du ett unikt namn för din resurs. Namnet måste vara en sträng som innehåller 3 till 127 tecken.

   2. En valfri **beskrivning** för resursen. Beskrivningen kan du identifiera resurs-ägare.

   3. I den **typ** listrutan anger du om du vill skapa en **Nivåindelad** eller **lokalt fixerade** dela. För arbetsbelastningar som kräver lokala garantier, Låg fördröjning och högre prestanda, väljer **lokalt fixerade resursen**. All övrig data, Välj **Nivåindelad** dela.

   4. I den **kapacitet** fältet, ange storleken på resursen. En nivåindelad resurs måste vara mellan 500 GB och 20 TB och en lokalt Fäst resurs måste vara mellan 50 GB och 2 TB.

   5. I den **Ange standard fullständig behörighet** fältet, tilldela behörigheter till användaren eller gruppen som har åtkomst till den här resursen. Ange namnet på användaren eller användargruppen i _john@contoso.com_ format. Vi rekommenderar att du använder en användargrupp (i stället för en enskild användare) för att tillåta admin-behörighet att komma åt dessa resurser. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren till att ändra dessa behörigheter.
3. När du har konfigurerat din filresurs klickar du på **skapa**. En resurs ska skapas med de angivna inställningarna och du ser ett meddelande. Som standard aktiveras säkerhetskopiering för resursen.
4. Kontrollera att resursen har skapats genom att gå till den **resurser** bladet. Du bör se den resurs som visas.
   
    ![Dela skapa lyckades](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Ändra en resurs

Ändra en resurs när du behöver ändra beskrivning av resursen. Inga andra resursegenskaper kan ändras när resursen har skapats.

#### <a name="to-modify-a-share"></a>Ändra en resurs

1. Från den **resurser** inställningen på sammanfattningsbladet för StorSimple-tjänsten, Välj den virtuella matrisen som resursen som du vill du ändra finns.
2. **Välj** resursen att visa aktuella beskrivningen och ändra den.
3. Spara ändringarna genom att klicka på den **spara** kommandofältet. De angivna inställningarna kommer att tillämpas och du ser ett meddelande.
   
    ![ Redigera resurs](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Koppla från en resurs

Du kan behöva koppla från en resurs när du planerar att ändra eller ta bort den. När en resurs är offline, är det inte tillgänglig för läs-/ skrivåtkomst. Du behöver ta resursen offline på värden och på enheten.

#### <a name="to-take-a-share-offline"></a>Att koppla från en resurs

1. Kontrollera att resursen i fråga inte är i användning innan du koppla från den.
2. Ta resursen på matrisen offline genom att utföra följande steg:
   
    1. Från den **resurser** inställningen på sammanfattningsbladet för StorSimple-tjänsten, Välj den virtuella matrisen som resursen som du vill du ta offline finns.

    2. **Välj** till resursen och klicka på **...**  (alternativt Högerklicka i den här raden) och på snabbmenyn väljer **ta offline**.
     
        ![Offline-resurs](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Granska informationen i den **ta offline** bladet och bekräftar ditt godkännande av åtgärden. Klicka på **ta offline** att ta resursen offline. Du ser en avisering om en åtgärd pågår.

    4. Kontrollera att resursen har har kopplats från genom att gå till den **resurser** bladet. Du bör se status för resursen som offline.

## <a name="delete-a-share"></a>Ta bort en resurs

> [!IMPORTANT]
> Du kan ta bort en resurs endast om den är offline.


Utför följande steg för att ta bort en resurs.

#### <a name="to-delete-a-share"></a>Att ta bort en resurs

1. Från den **resurser** inställningen på sammanfattningsbladet för StorSimple-tjänsten, Välj den virtuella matrisen som du vill ta bort resursen finns.
2. **Välj** till resursen och klicka på **...**  (alternativt Högerklicka i den här raden) och på snabbmenyn väljer **ta bort**.
   
    ![Ta bort resurs](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Kontrollera status för den resurs som du vill ta bort. Om du vill ta bort resursen inte är offline, koppla från den först. Följ stegen i [koppla från en resurs](#take-a-share-offline).
4. När du uppmanas att bekräfta i den **ta bort** bladet Godkänn bekräftelsen och klicka på **ta bort**. Resursen tas bort och **resurser** bladet visar den uppdaterade listan med resurser i den virtuella matrisen.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [klona en StorSimple-resurs](storsimple-virtual-array-clone.md).

