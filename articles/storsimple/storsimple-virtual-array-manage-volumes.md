---
title: Hantera volymer på StorSimple Virtual Array | Microsoft-dokument
description: Beskriver StorSimple Device Manager och förklarar hur du använder den för att hantera volymer på din StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 2dbbe6bcd4957a108cc3eae4d41816b130cf8f07
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633867"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Använda StorSimple Device Manager-tjänsten för att hantera volymer i StorSimple Virtual Array

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du använder Tjänsten StorSimple Device Manager för att skapa och hantera volymer på den virtuella storsimple-matrisen.

Tjänsten StorSimple Device Manager är ett tillägg i Azure-portalen som gör att du kan hantera din StorSimple-lösning från ett enda webbgränssnitt. Förutom att hantera resurser och volymer kan du använda Tjänsten StorSimple Device Manager för att visa och hantera enheter, visa aviseringar och visa och hantera principer för säkerhetskopiering och säkerhetskopieringskatalogen.

## <a name="volume-types"></a>Volymtyper

StorSimple volymer kan vara:

* **Lokalt fäst**: Data i dessa volymer finns kvar på matrisen hela tiden och spills inte till molnet.
* **Nivåindelade**: Data i dessa volymer kan spillas till molnet. När du skapar en nivåindelad volym etableras cirka 10 % av utrymmet på den lokala nivån och 90 % av utrymmet etableras i molnet. Om du till exempel har etablerat en volym på 1 TB skulle 100 GB finnas i det lokala utrymmet och 900 GB skulle användas i molnet när datanivåerna. Detta innebär i sin tur att om du får på allt lokalt utrymme på enheten, kan du inte etablera en nivåindelad volym (eftersom de 10 % som krävs på den lokala nivån inte kommer att vara tillgängliga).

### <a name="provisioned-capacity"></a>Etablerad kapacitet
Se följande tabell för maximal etablerad kapacitet för varje volymtyp.

| **Gräns för identifierare**                                       | **Gräns**     |
|------------------------------------------------------------|---------------|
| Minsta storlek för en nivåindelad volym                            | 500 GB        |
| Maximal storlek för en nivåindelad volym                            | 5 TB          |
| Minsta storlek för en lokalt fäst volym                    | 50 GB         |
| Maximal storlek för en lokalt fäst volym                    | 200 GB        |

## <a name="the-volumes-blade"></a>Bladet Volymer
Menyn Volymer på **sammanfattningsbladet** för StorSimple-tjänsten visar listan över lagringsvolymer på en viss StorSimple-matris och låter dig hantera dem.

![Bladet Volymer](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

En volym består av en serie attribut:

* **Volymnamn** – Ett beskrivande namn som måste vara unikt och hjälper till att identifiera volymen.
* **Status** – Kan vara online eller offline. Om en volym är offline är den inte synlig för initierare (servrar) som har åtkomst att använda volymen.
* **Typ** – Anger om volymen är **nivåindelade** (standard) eller **Lokalt fäst**.
* **Kapacitet** – anger mängden data som används jämfört med den totala mängden data som kan lagras av initieraren (servern).
* **Säkerhetskopiering** – Vid StorSimple Virtual Array aktiveras alla volymer automatiskt för säkerhetskopiering.
* **Anslutna värdar** – Anger de initierare (servrar) som får åtkomst till den här volymen.

![Information om volymer](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Använd instruktionerna i den här självstudien för att utföra följande uppgifter:

* Lägga till en volym
* Ändra en volym
* Koppla från en volym
* Ta bort en volym

## <a name="add-a-volume"></a>Lägga till en volym

1. Klicka på + Lägg till **volym** från kommandofältet i sammanfattningsbladet för StorSimple-tjänsten. Detta öppnar **bladet Lägg till volym.**
   
    ![Lägg till volym](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. Gör följande i bladet **Lägg till volym:**
   
   * Ange ett unikt namn för volymen i fältet **Volymnamn.** Namnet måste vara en sträng som innehåller 3 till 127 tecken.
   * I listrutan **Typ** anger du om du vill skapa en **nivåindelad** eller **Lokalt fäst** volym. För arbetsbelastningar som kräver lokala garantier, låga svarstider och högre prestanda väljer du **Lokalt fäst volym**. För alla andra data väljer du **Nivåinerad** volym.
   * Ange volymens storlek i fältet **Kapacitet.** En nivåindelad volym måste vara mellan 500 GB och 5 TB och en lokalt fäst volym måste vara mellan 50 GB och 500 GB.
   * * Klicka på **Anslutna värdar,** välj en åtkomstkontrollpost (ACR) som motsvarar den iSCSI-initierare som du vill ansluta till den här volymen och klicka sedan på **Välj**.
3. Om du vill lägga till en ny ansluten värd, klickar du på **Lägg till ny,** anger ett namn för värden och dess iSCSI-kvalificerat namn (IQN) och klickar sedan på **Lägg till**.
   
    ![Lägg till volym](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. När du har konfigurerat volymen klickar du på **Skapa**. En volym skapas med de angivna inställningarna och du kommer att se ett meddelande om att skapa samma. Som standard aktiveras säkerhetskopiering för volymen.
5. Om du vill bekräfta att volymen har skapats går du till bladet **Volymer.** Du bör se volymen i listan.
   
    ![Volym skapa framgång](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Ändra en volym

Ändra en volym när du behöver ändra de värdar som kommer åt volymen. De andra attributen för en volym kan inte ändras när volymen har skapats.

#### <a name="to-modify-a-volume"></a>Så här ändrar du en volym

1. Välj den virtuella matris som du vill att du ska ändra finns i inställningen **Volymer** på storsimple-tjänstens sammanfattningsblad.
2. **Markera** volymen och klicka på **Anslutna värdar** om du vill visa den anslutna värden och ändra den till en annan server.
   
    ![Redigera volym](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Spara ändringarna genom att klicka på kommandofältet **Spara.** Dina angivna inställningar kommer att tillämpas och du kommer att se ett meddelande.

## <a name="take-a-volume-offline"></a>Koppla från en volym

Du kan behöva koppla från en volym när du planerar att ändra den eller ta bort den. När en volym är offline är den inte tillgänglig för läs- och skrivåtkomst. Du måste koppla från volymen på värden och på enheten.

#### <a name="to-take-a-volume-offline"></a>Så här kopplar du från en volym

1. Kontrollera att volymen i fråga inte används innan du kopplar från den.
2. Ta volymen offline på värden först. Detta eliminerar alla potentiella risker för data korruption på volymen. Mer information om hur du gör det finns i instruktionerna för värdoperativsystemet.
3. När volymen på värden är offline tar du volymen på matrisen offline genom att utföra följande steg:
   
   * Välj den virtuella matris som du vill att du ska koppla från i **sammanfattningsbladet** för StorSimple-tjänsten på.
   * **Markera** volymen och klicka på **...** (högerklicka alternativt på den här raden) och välj **Koppla offline**på snabbmenyn .
     
        ![Offlinevolym](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Granska informationen i bladet **Ta offline** och bekräfta att du godkänner åtgärden. Klicka på **Koppla från** om du vill koppla från volymen. Du kommer att se ett meddelande om den pågående åtgärden.
   * Om du vill bekräfta att volymen har tagits offline går du till bladet **Volymer.** Du bör se volymens status som offline.
     
       ![Bekräftelse av offlinevolym](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Ta bort en volym

> [!IMPORTANT]
> Du kan bara ta bort en volym om den är offline.
> 
> 

Slutför följande steg för att ta bort en volym.

#### <a name="to-delete-a-volume"></a>Så här tar du bort en volym

1. Välj den virtuella matris som du vill ta bort finns i inställningen **Volymer** på storsimple-tjänstens sammanfattningsblad.
2. **Markera** volymen och klicka på **...** (högerklicka alternativt på den här raden) och välj **Ta bort**på snabbmenyn .
   
    ![Ta bort volym](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Kontrollera status för den volym som du vill ta bort. Om volymen som du vill ta bort inte är offline tar du den offline först genom att följa stegen i [Koppla från volymen](#take-a-volume-offline).
4. När du uppmanas att bekräfta i bladet **Ta bort** godkänner du bekräftelsen och klickar på **Ta bort**. Volymen tas nu bort och **bladet Volymer** visar den uppdaterade listan över volymer i den virtuella matrisen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [klonar en StorSimple-volym](storsimple-virtual-array-clone.md).

