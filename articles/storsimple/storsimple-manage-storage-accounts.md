---
title: Hantera ditt lagringskonto i StorSimple | Microsoft Docs
description: "Beskriver hur du kan använda sidan Konfigurera StorSimple Manager för att lägga till, redigera, ta bort eller rotera tangenterna säkerhet för ett lagringskonto."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 93207c40-e0eb-489e-8724-59fb94907081
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/29/2016
ms.author: v-sharos
ms.openlocfilehash: 68b767c9c93f2daff476a21029b9813f347590b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>Använda StorSimple Manager-tjänsten för att hantera ditt lagringskonto
## <a name="overview"></a>Översikt
Den **konfigurera** visar alla tjänsten för global-parametrar som kan skapas i StorSimple Manager-tjänsten. De här parametrarna kan tillämpas på alla enheter som är anslutna till tjänsten och inkluderar:

* Lagringskonton 
* Bandbredd-mallar 
* Access control-poster 

Den här självstudiekursen beskrivs hur du kan använda den **konfigurera** sidan om du vill lägga till, redigera eller ta bort storage-konton eller rotera tangenterna säkerhet för ett lagringskonto.

 ![Konfigurera](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

Storage-konton innehåller de autentiseringsuppgifter som används av enheten för att få åtkomst till ditt lagringskonto med din molntjänstleverantör. För Microsoft Azure storage-konton är dessa autentiseringsuppgifter, till exempel namnet på kontot och den primära åtkomstnyckeln. 

På den **konfigurera** sidan alla lagringskonton som skapats för fakturering prenumerationen visas i tabellformat som innehåller följande information:

* **Namnet** – det unika namnet som tilldelas kontot när den skapades.
* **SSL aktiverat** – om SSL är aktiverat och enhet till moln kommunikation via den säkra kanalen.
* **Används av** – antal volymer med storage-konto.

De vanligaste uppgifterna som är relaterade till lagringskonton som kan utföras på den **konfigurera** sidan är:

* Lägg till ett lagringskonto 
* Redigera ett lagringskonto 
* Ta bort ett lagringskonto 
* Viktiga rotation av storage-konton 

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton
Det finns tre typer av lagringskonton som kan användas med din StorSimple-enhet.

* **Automatiskt genererade lagringskonton** – som namnet antyder den här typen av lagringskonto genereras automatiskt när tjänsten skapas. Läs mer om hur det här lagringskontot har skapats i [steg 1: skapa en ny tjänst](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) i [distribuera din lokala StorSimple-enhet](storsimple-deployment-walkthrough.md). 
* **Storage-konton i prenumerationen på tjänsten** – dessa är Azure storage-konton som är associerade med samma prenumeration som för tjänsten. Läs mer om hur dessa lagring konton skapas i [om Azure Lagringskonton](../storage/common/storage-create-storage-account.md). 
* **Storage-konton utanför prenumerationen på tjänsten** – dessa är Azure storage-konton som inte tillhör med din tjänst och troligtvis fanns innan tjänsten skapades.

## <a name="add-a-storage-account"></a>Lägg till ett lagringskonto
Du kan lägga till ett lagringskonto genom att ange ett unikt namn och autentiseringsuppgifter som är kopplade till storage-konto (med angivna molntjänstleverantören). Du har också alternativet att aktivera secure sockets layer (SSL)-läge för att skapa en säker kanal för nätverkskommunikation mellan din enhet och molnet.

Du kan skapa flera konton för en viss molntjänstleverantören. Tänk dock på att du inte kan ändra molntjänstleverantören efter ett lagringskonto skapas.

Tjänsten försöker kommunicera med din molntjänstleverantör medan lagringskontot sparas. Autentiseringsuppgifterna och åtkomst-material som du har angett autentiseras just nu. Ett lagringskonto skapas endast om autentiseringen lyckas. Om autentiseringen misslyckas visas ett felmeddelande visas.

Resource Manager-lagringskonton som skapats i Azure portal även stöds med StorSimple. Resource Manager storage-konton visas inte i listrutan för val när försök att skapa en volymbehållare, endast de lagringskonton som skapats i den klassiska Azure-portalen kommer att visas. Hanteraren för filserverresurser storage-konton måste läggas till med hjälp av proceduren för att lägga till ett lagringskonto som beskrivs nedan.

> [!NOTE]
> Proceduren för att lägga till ett lagringskonto skiljer sig beroende på StorSimple-programvaruversion som du använder. Se till att följa rätt för din StorSimple-version.
> 
> 

[!INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[!INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Redigera ett lagringskonto
Du kan redigera ett lagringskonto som används av en volymbehållare. Om du redigerar ett lagringskonto som används för närvarande är endast användas för att ändra fältet åtkomstnyckeln för lagringskontot. Du kan ange den nya lagringsåtkomstnyckel och spara de uppdaterade inställningarna.

#### <a name="to-edit-a-storage-account"></a>Så här redigerar du ett storage-konto
1. Välj tjänsten på Landningssida för tjänsten, dubbelklickar du på namnet på tjänsten och sedan på **konfigurera**.
2. Klicka på **lägga till eller redigera Lagringskonton**.
3. I den **Lägg till/redigera Lagringskonton** dialogrutan:
   
   1. I listrutan för **Lagringskonton**, välja ett befintligt konto som du vill ändra. Detta kan också omfatta de lagringskonton som har genererats automatiskt när tjänsten skapades.
   2. Om nödvändigt, kan du ändra den **aktivera SSL-läge** val.
   3. Du kan välja att rotera åtkomstnycklar för lagring konto. Se [nyckeln rotation av lagringskonton](#key-rotation-of-storage-accounts) för mer information om hur du utför viktiga rotation.
   4. Klicka på kryssikonen ![kryssikon](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) du vill spara inställningarna. Inställningarna uppdateras på den **konfigurera** sidan. Klicka på **spara** att spara de uppdaterade inställningarna.
      
      ![Redigera ett lagringskonto](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)

## <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto
> [!IMPORTANT]
> Du kan ta bort ett lagringskonto endast om den inte används av en volymbehållare. Om ett lagringskonto används av en volymbehållare, först ta bort volymbehållaren och ta sedan bort det associerade lagringskontot.
> 
> 

#### <a name="to-delete-a-storage-account"></a>Ta bort ett lagringskonto
1. Välj tjänsten på Landningssida för StorSimple Manager-tjänsten, dubbelklicka på namnet på tjänsten och sedan på **konfigurera**.
2. Hovra över det konto som du vill ta bort i listan tabular för storage-konton.
3. En borttagningsikonen (**x**) visas i den yttersta högra kolumnen för det lagringskontot. Klicka på den **x** ikon för att ta bort autentiseringsuppgifterna.
4. När du uppmanas att bekräfta, klickar du på **Ja** att ta bort. Tabell listan kommer att uppdateras för att återspegla ändringarna.

## <a name="key-rotation-of-storage-accounts"></a>Viktiga rotation av storage-konton
Av säkerhetsskäl är viktiga rotation ofta ett krav i datacenter. 

> [!NOTE]
> Följande viktiga rotation information och rotation proceduren gäller endast Microsoft Azure storage-konton. Om du använder en annan molntjänstleverantör kan du hantera nycklar för lagringskonto via instrumentpanelen för den providern.
> 
> 

Varje Microsoft Azure-prenumeration kan ha en eller flera associerade storage-konton. Åtkomst till dessa konton styrs av prenumeration och åtkomst nycklarna för varje lagringskonto. 

När du skapar ett lagringskonto genererar Microsoft Azure två 512-bitars lagring åtkomstnycklar som används för autentisering när lagringskontot används. Har två åtkomstnycklar för lagring kan du återskapa nycklarna utan avbrott i lagringstjänsten eller åtkomst till tjänsten. Den nyckel som används för närvarande är den *primära* och den säkerhetskopiera nyckeln kallas den *sekundära* nyckel. En av dessa två nycklar måste anges när Microsoft Azure StorSimple-enheten ansluter till din molntjänstleverantör för lagring.

## <a name="what-is-key-rotation"></a>Vad är viktiga rotation?
Normalt använder program endast en av nycklarna åtkomst till data. Efter en viss tidsperiod, kan du ha dina program växla till med hjälp av den andra nyckeln. När du har växlat dina program till den sekundära nyckeln kan du dra tillbaka den första nyckeln och sedan skapa en ny nyckel. Med hjälp av två sätt kan ditt program åtkomst till data utan att det medför några driftavbrott.

Lagringskontonycklar lagras alltid i tjänsten i krypterad form. Dessa kan dock återställas via StorSimple Manager-tjänsten. Tjänsten kan hämta primärnyckeln och sekundärnyckeln för alla lagringskonton i samma prenumeration, inklusive konton som har skapats i lagringstjänsten samt lagringsplatsen standard som genereras när StorSimple Manager-tjänsten först skapas. StorSimple Manager-tjänsten kommer alltid hämta nycklarna från den klassiska Azure-portalen och lagra dem på ett sätt som är krypterade.

## <a name="rotation-workflow"></a>Rotation arbetsflöde
En Microsoft Azure-administratör kan återskapa eller ändra de primära och sekundära nycklarna genom direkt åtkomst till lagringskontot (via Microsoft Azure Storage service). StorSimple Manager-tjänsten kan inte se ändringen automatiskt.

För att informera StorSimple Manager-tjänsten för behöver du åtkomst till StorSimple Manager-tjänsten åtkomst till lagringskontot, och synkronisera primär eller sekundär nyckeln (beroende på vilket som ändrats). Tjänsten sedan hämtar den senaste nyckeln krypterar nycklarna och skickar den krypterade nyckeln till enheten.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>Att synkronisera nycklar för lagringskonton med samma prenumeration som tjänst (Azure)
1. På den **Services** klickar du på den **konfigurera** fliken.
2. Klicka på **lägga till eller redigera Lagringskonton**.
3. I dialogrutan för att göra följande:
   
   1. Välj lagringskonto med den nyckel som du vill synkronisera. Lagringskontonycklarna krypteras när de visas.
   2. Du måste uppdatera den nyckel som tidigare har ändrats i Microsoft Azure Storage-tjänsten i StorSimple Manager-tjänsten. Om den primära åtkomstnyckeln har ändrats (återskapats), klickar du på **synkronisera primärnyckeln**. Om den sekundära nyckeln har ändrats, klickar du på **synkronisera sekundärnyckeln**.
      
      ![Synkronisera nycklar](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Att synkronisera nycklar för lagringskonton utanför prenumerationen på tjänsten
1. På den **Services** klickar du på den **konfigurera** fliken.
2. Klicka på **lägga till eller redigera Lagringskonton**.
3. I dialogrutan för att göra följande:
   
   1. Välj lagringskontot med åtkomstnyckel som du vill uppdatera.
   2. Du behöver uppdatera lagringsåtkomstnyckel i StorSimple Manager-tjänsten. I det här fallet kan du se lagringsåtkomstnyckel. Ange den nya nyckeln i den **åtkomstnyckeln för Lagringskontot**y-rutan. 
   3. Spara ändringarna. Din lagringsåtkomstnyckel för kontot ska nu uppdateras.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [StorSimple-säkerhet](storsimple-security.md).
* Lär dig mer om [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

