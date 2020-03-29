---
title: Hantera StorSimple Virtual Array-resurser | Microsoft-dokument
description: Beskriver StorSimple Device Manager och förklarar hur du använder den för att hantera resurser på din StorSimple Virtual Array.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "62116874"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Använda StorSimple Device Manager-tjänsten för att hantera resurser i StorSimple Virtual Array

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du använder Tjänsten StorSimple Device Manager för att skapa och hantera resurser på den virtuella storsimple-matrisen.

Tjänsten StorSimple Device Manager är ett tillägg i Azure-portalen som gör att du kan hantera din StorSimple-lösning från ett enda webbgränssnitt. Förutom att hantera resurser och volymer kan du använda Tjänsten StorSimple Device Manager för att visa och hantera enheter, visa aviseringar, hantera principer för säkerhetskopiering och hantera säkerhetskopieringskatalogen.

## <a name="share-types"></a>Resurstyper

StorSimple-aktier kan vara:

* **Lokalt fäst**: Data i dessa resurser finns kvar på matrisen hela tiden och spills inte till molnet.
* **Nivåindelade**: Data i dessa resurser kan spillas till molnet. När du skapar en nivåindelad resurs etableras cirka 10 % av utrymmet på den lokala nivån och 90 % av utrymmet etableras i molnet. Om du till exempel har etablerat en 1 TB-resurs skulle 100 GB finnas i det lokala utrymmet och 900 GB skulle användas i molnet när datanivåerna. Detta innebär i sin tur att om du får på allt lokalt utrymme på enheten, kan du inte etablera en nivåindelad resurs (eftersom de 10 % som krävs på den lokala nivån inte kommer att vara tillgängliga).

### <a name="provisioned-capacity"></a>Etablerad kapacitet

Se följande tabell för maximal etablerad kapacitet för varje resurstyp.

| **Gräns för identifierare** | **Gräns** |
| --- | --- |
| Minsta storlek för en nivåindelad resurs |500 GB |
| Maximal storlek på en nivåindelad resurs |20 TB |
| Minsta storlek för en lokalt fäst resurs |50 GB |
| Maximal storlek för en lokalt fäst resurs |2 TB |

## <a name="the-shares-blade"></a>Bladet Aktier

På sammanfattningsbladet **för resurser** i storsimple-tjänsten visas listan över lagringsresurser på en viss StorSimple-matris och du kan hantera dem.

![Delar blad](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

En aktie består av en serie attribut:

* **Resursnamn** – Ett beskrivande namn som måste vara unikt och hjälper till att identifiera resursen.
* **Status** – Kan vara online eller offline. Om en resurs är offline kan användarna av resursen inte komma åt den.
* **Typ** – Anger om resursen är **nivåindelning** (standard) eller **Lokalt fäst**.
* **Kapacitet** – anger mängden data som används jämfört med den totala mängden data som kan lagras på resursen.
* **Beskrivning** – En valfri inställning som hjälper till att beskriva resursen.
* **Behörigheter** - NTFS-behörigheterna till resursen som kan hanteras via Utforskaren.
* **Säkerhetskopiering** – Vid StorSimple Virtual Array aktiveras alla resurser automatiskt för säkerhetskopiering.

![Dela information](./media/storsimple-virtual-array-manage-shares/share-details.png)

Använd instruktionerna i den här självstudien för att utföra följande uppgifter:

* Lägga till en resurs
* Ändra en resurs
* Koppla från en resurs
* Ta bort en resurs

## <a name="add-a-share"></a>Lägga till en resurs

1. Klicka på + Lägg till **resurs** från kommandofältet i sammanfattningsbladet för StorSimple-tjänsten. Detta öppnar **bladet Lägg till delning.**

    ![Lägg till resurs](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. Gör följande i bladet **Lägg till resurs:**
   
   1. Ange ett unikt namn för resursen i fältet **Dela namn.** Namnet måste vara en sträng som innehåller 3 till 127 tecken.

   2. En valfri **beskrivning** för resursen. Beskrivningen hjälper till att identifiera aktieägarna.

   3. I listrutan **Typ** anger du om du vill skapa en **nivåindelad** eller **Lokalt fäst** resurs. För arbetsbelastningar som kräver lokala garantier, låga svarstider och högre prestanda väljer du **Lokalt fäst resurs**. För alla andra data väljer du **Nivåindelning.**

   4. Ange resursens storlek i fältet **Kapacitet.** En nivåindelad resurs måste vara mellan 500 GB och 20 TB och en lokalt fäst resurs måste vara mellan 50 GB och 2 TB.

   5. I fältet **Ange fullständiga standardbehörigheter till,** tilldela behörigheterna till användaren eller den grupp som använder den här resursen. Ange namnet på användaren eller användargruppen i _john@contoso.com_ format. Vi rekommenderar att du använder en användargrupp (i stället för en enskild användare) för att tillåta administratörsbehörighet att komma åt dessa resurser. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren till att ändra dessa behörigheter.
3. När du har konfigurerat resursen klickar du på **Skapa**. En resurs skapas med de angivna inställningarna och du kommer att se ett meddelande. Som standard aktiveras säkerhetskopiering för resursen.
4. Om du vill bekräfta att resursen har skapats går du till bladet **Aktier.** Du bör se resursen i listan.
   
    ![Dela skapa framgång](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Ändra en resurs

Ändra en resurs när du behöver ändra beskrivningen av resursen. Inga andra resursegenskaper kan ändras när resursen har skapats.

#### <a name="to-modify-a-share"></a>Så här ändrar du en resurs

1. Välj den virtuella matris som resursen du vill att du ska ändra finns på inställningen **Resurser** på sammanfattningsbladet för StorSimple-tjänsten.
2. **Markera** resursen om du vill visa den aktuella beskrivningen och ändra den.
3. Spara ändringarna genom att klicka på kommandofältet **Spara.** Dina angivna inställningar kommer att tillämpas och du kommer att se ett meddelande.
   
    ![ Redigera resurs](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Koppla från en resurs

Du kan behöva koppla från en resurs när du planerar att ändra den eller ta bort den. När en resurs är offline är den inte tillgänglig för läs- och skrivåtkomst. Du måste koppla från resursen på värden och på enheten.

#### <a name="to-take-a-share-offline"></a>Så här kopplar du en resurs

1. Kontrollera att resursen i fråga inte används innan du kopplar från den.
2. Koppla från resursen på matrisen genom att utföra följande steg:
   
    1. Välj **den** virtuella matris som resursen du vill att du ska koppla offline på i sammanfattningsbladet StorSimple-tjänsten.

    2. **Markera** resursen och klicka **...** (högerklicka alternativt på den här raden) och välj **Koppla offline**på snabbmenyn .
     
        ![Offline-resurs](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Granska informationen i bladet **Ta offline** och bekräfta att du godkänner åtgärden. Klicka på **Koppla från** om du vill koppla från resursen. Du kommer att se ett meddelande om den pågående åtgärden.

    4. Om du vill bekräfta att resursen har tagits offline går du till bladet **Aktier.** Du bör se resursens status som offline.

## <a name="delete-a-share"></a>Ta bort en resurs

> [!IMPORTANT]
> Du kan bara ta bort en resurs om den är offline.


Slutför följande steg för att ta bort en resurs.

#### <a name="to-delete-a-share"></a>Så här tar du bort en resurs

1. Välj den virtuella matris som resursen du vill ta bort finns på inställningen **Resurser** på sammanfattningsbladet för StorSimple-tjänsten.
2. **Markera** resursen och klicka **...** (högerklicka alternativt på den här raden) och välj **Ta bort**på snabbmenyn .
   
    ![Ta bort resurs](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Kontrollera status för den resurs som du vill ta bort. Om resursen som du vill ta bort inte är offline tar du den offline först. Följ stegen i [Koppla från delning](#take-a-share-offline).
4. När du uppmanas att bekräfta i bladet **Ta bort** godkänner du bekräftelsen och klickar på **Ta bort**. Resursen tas nu bort och bladet **Aktier** visar den uppdaterade listan över resurser i den virtuella matrisen.

## <a name="next-steps"></a>Nästa steg
Läs om hur du [klonar en StorSimple-resurs](storsimple-virtual-array-clone.md).

