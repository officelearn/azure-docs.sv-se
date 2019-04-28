---
title: Hantera volymer på StorSimple Virtual Array | Microsoft Docs
description: Beskriver StorSimple Device Manager och som förklarar hur du använder den för att hantera volymer på StorSimple Virtual Array.
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
ms.openlocfilehash: a233a9deb58a7c1abc87a622a4f1f2581ee2e477
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125805"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Använd StorSimple Device Manager-tjänsten för att hantera volymer på StorSimple Virtual Array

## <a name="overview"></a>Översikt

Den här självstudien beskrivs hur du använder StorSimple Device Manager-tjänsten för att skapa och hantera volymer på StorSimple Virtual Array.

StorSimple Device Manager-tjänsten är ett tillägg i Azure-portalen som låter dig hantera din StorSimple-lösning från ett enda webbgränssnitt. Utöver att hantera resurserna och volymerna, kan du använda StorSimple Device Manager-tjänsten för att visa och hantera enheter, Visa aviseringar, och visa och hantera säkerhetskopieringsprinciper och säkerhetskopieringskatalogen.

## <a name="volume-types"></a>Volymtyper

StorSimple-volymer kan vara:

* **Lokalt fixerade**: Data i dessa volymer stannar i matrisen hela tiden och läcker över inte till molnet.
* **Nivåindelad**: Data i dessa volymer kan läcker över till molnet. När du skapar en nivåindelad volym cirka 10% av utrymmet är etablerad på den lokala nivån och 90% av utrymmet som har etablerats i molnet. Till exempel om du har etablerat en volym på 1 TB, 100 GB skulle finnas i det lokala utrymmet och 900 GB skulle användas i molnet när de data-nivåerna. Detta innebär i sin tur att du kör utanför det lokala utrymmet på enheten, inte kan etablera en nivåindelad volym (eftersom dessa 10% som krävs på den lokala nivån inte är tillgängligt).

### <a name="provisioned-capacity"></a>Etablerad kapacitet
Se tabellen nedan för högsta etablerade kapaciteten för varje volym.

| **Gräns för identifierare**                                       | **Gräns**     |
|------------------------------------------------------------|---------------|
| Minsta storlek på en nivåindelad volym                            | 500 GB        |
| Maximal storlek på en nivåindelad volym                            | 5 TB          |
| Minsta storlek på en lokalt Fäst volym                    | 50 GB         |
| Maximal storlek på en lokalt Fäst volym                    | 500 GB        |

## <a name="the-volumes-blade"></a>Bladet volymer
Den **volymer** menyn på din sammanfattningsbladet för StorSimple-tjänsten visar en lista över lagringsvolymer på en viss StorSimple-matris och gör att du kan hantera dem.

![Volymer bladet](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

En volym består av en serie med attribut:

* **Volymnamn** – ett beskrivande namn som måste vara unikt och hjälper dig att identifiera volymen.
* **Status för** – kan vara online eller offline. Om en volym är offline, är det inte synliga för initierarna (servrarna) som har åtkomst till att använda volymen.
* **Typ** – anger om volymen är **Nivåindelad** (standard) eller **lokalt fixerade**.
* **Kapacitet** – anger hur mycket data som används jämfört med den totala mängden data som kan lagras av initieraren (server).
* **Säkerhetskopiering** – om av StorSimple Virtual Array aktiveras automatiskt alla volymer för säkerhetskopiering.
* **Anslutna värdar** – anger initierarna (servrarna) som har åtkomst till den här volymen.

![Information om volymer](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Följ instruktionerna i den här självstudien för att utföra följande uppgifter:

* Lägg till en volym
* Ändra en volym
* Koppla från en volym
* Ta bort en volym

## <a name="add-a-volume"></a>Lägg till en volym

1. StorSimple sammanfattningsbladet för tjänsten, klicka på **+ Lägg till volymen** i kommandofältet. Gör det öppnas den **volym** bladet.
   
    ![Lägg till volym](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. I den **volym** bladet gör du följande:
   
   * I den **volymnamn** fältet, anger du ett unikt namn för volymen. Namnet måste vara en sträng som innehåller 3 till 127 tecken.
   * I den **typ** listrutan anger du om du vill skapa en **Nivåindelad** eller **lokalt fixerade** volym. För arbetsbelastningar som kräver lokala garantier, Låg fördröjning och högre prestanda, väljer **lokalt fixerade volymen**. All övrig data, Välj **Nivåindelad** volym.
   * I den **kapacitet** fältet, ange storleken på volymen. En nivåindelad volym måste vara mellan 500 GB och 5 TB och en lokalt Fäst volym måste vara mellan 50 GB och 500 GB.
   * * Klicka på **anslutna värdar**, Välj en åtkomstkontrollpost (ACR) som motsvarar den iSCSI-initierare som du vill ansluta till den här volymen och klickar sedan på **Välj**.
3. Lägg till en ny anslutna värd, klicka på **Lägg till ny**, ange ett namn för värden och dess iSCSI-kvalificerade namn (IQN) och klicka sedan på **Lägg till**.
   
    ![Lägg till volym](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. När du har konfigurerat din volym klickar du på **skapa**. En volym skapas med de angivna inställningarna och du ser ett meddelande har skapats av samma. Som standard aktiveras säkerhetskopiering för volymen.
5. Kontrollera att volymen har skapats genom att gå till den **volymer** bladet. Du bör se den volym som visas.
   
    ![Volymen skapa lyckades](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Ändra en volym

Ändra en volym när du vill ändra de värdar som har åtkomst till volymen. Andra attribut för en volym kan inte ändras när volymen har skapats.

#### <a name="to-modify-a-volume"></a>Ändra en volym

1. Från den **volymer** inställningen på sammanfattningsbladet för StorSimple-tjänsten, Välj den virtuella matrisen som du vill du ändra volymen finns.
2. **Välj** volym och klicka på **anslutna värdar** visa anslutna värden och ändra den till en annan server.
   
    ![Redigera volymen](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Spara ändringarna genom att klicka på den **spara** kommandofältet. De angivna inställningarna kommer att tillämpas och du ser ett meddelande.

## <a name="take-a-volume-offline"></a>Koppla från en volym

Du kan behöva koppla från en volym när du planerar att ändra eller ta bort den. När en volym är offline, är det inte tillgänglig för läs-/ skrivåtkomst. Du behöver vidta frånkoppla volymen på värden och på enheten.

#### <a name="to-take-a-volume-offline"></a>Att koppla från en volym

1. Kontrollera att volymen i fråga inte är i användning innan du koppla från den.
2. Ta frånkoppla volymen på värden första. Detta eliminerar alla potentiella risken för korrupta data på volymen. Detaljerade anvisningar om hur, läser du anvisningarna för din värdoperativsystemet.
3. När volymen på värden är offline kan du vidta volymen på matrisen offline genom att utföra följande steg:
   
   * Från den **volymer** inställningen på sammanfattningsbladet för StorSimple-tjänsten, Välj den virtuella matrisen som den volym som du vill du ta offline finns.
   * **Välj** volym och klicka på **...**  (alternativt Högerklicka i den här raden) och på snabbmenyn väljer **ta offline**.
     
        ![Offline volym](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Granska informationen i den **ta offline** bladet och bekräftar ditt godkännande av åtgärden. Klicka på **ta offline** du koppla från volymen. Du ser en avisering om en åtgärd pågår.
   * Kontrollera att volymen har har kopplats från genom att gå till den **volymer** bladet. Du bör se status för volymen som offline.
     
       ![Offline volym bekräftelse](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Ta bort en volym

> [!IMPORTANT]
> Du kan ta bort en volym endast om den är offline.
> 
> 

Utför följande steg för att ta bort en volym.

#### <a name="to-delete-a-volume"></a>Att ta bort en volym

1. Från den **volymer** inställningen på sammanfattningsbladet för StorSimple-tjänsten, Välj den virtuella matrisen som du vill du ta bort volymen finns.
2. **Välj** volym och klicka på **...**  (alternativt Högerklicka i den här raden) och på snabbmenyn väljer **ta bort**.
   
    ![Ta bort volym](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Kontrollera status på den volym som du vill ta bort. Om du vill ta bort volymen inte är offline, kopplar från den först följa stegen i [koppla från en volym](#take-a-volume-offline).
4. När du uppmanas att bekräfta i den **ta bort** bladet Godkänn bekräftelsen och klicka på **ta bort**. Volymen kommer nu att tas bort och **volymer** bladet visar den uppdaterade listan med volymer i den virtuella matrisen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [klona en StorSimple-volym](storsimple-virtual-array-clone.md).

