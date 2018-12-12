---
title: Hantera StorSimple Virtual Array lagringskontouppgifter | Microsoft Docs
description: Beskriver hur du kan använda konfigurationssidan för StorSimple Device Manager för att lägga till, redigera, ta bort eller rotera säkerhetsnycklarna för autentiseringsuppgifterna för lagringskontot som är associerade med StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: a992851deda0659509c0ee4ea5de76b19734f017
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867622"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Använd StorSimple Device Manager för att hantera autentiseringsuppgifter för lagringskonto för StorSimple Virtual Array

## <a name="overview"></a>Översikt
Den **Configuration** på bladet för StorSimple Device Manager-tjänsten för din StorSimple Virtual Array anger parametrarna global tjänst som kan skapas i StorSimple Manager-tjänsten. Dessa parametrar kan tillämpas på alla enheter som är anslutna till tjänsten och inkluderar:

* Autentiseringsuppgifter för lagringskonto
* Åtkomstkontrollposter
  
  ![Instrumentpanel för Device Manager-tjänsten](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Den här självstudien beskrivs hur du kan lägga till, redigera eller ta bort autentiseringsuppgifter för lagringskonto för StorSimple Virtual Array. Informationen i den här självstudien gäller endast för StorSimple Virtual Array. Information om hur du hanterar storage-konton i 8000-serien finns i [använda StorSimple Manager-tjänsten för att hantera ditt lagringskonto](storsimple-manage-storage-accounts.md).

Autentiseringsuppgifter för lagringskonto innehåller de autentiseringsuppgifter som enheten använder för att få åtkomst till ditt lagringskonto med din molntjänstleverantör. För Microsoft Azure storage-konton är dessa autentiseringsuppgifter, till exempel namnet på kontot och den primära åtkomstnyckeln.

På den **lagringskontouppgifter** bladet, alla autentiseringsuppgifter för lagringskonto som har skapats för faktureringsprenumerationen visas i tabellformat som innehåller följande information:

* **Namn på** – det unika namnet som tilldelas kontot när den skapades.
* **SSL aktiverat** – om SSL är aktiverat och enhet till molnet kommunikation via den säkra kanalen.
  
  ![Konfigurationsavsnittet](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

De vanligaste uppgifterna som är relaterade till autentiseringsuppgifterna för lagringskontot som kan utföras på den **lagringskontouppgifter** bladet är:

* Lägg till autentiseringsuppgift för lagringskonto
* Redigera autentiseringsuppgift för lagringskonto
* Ta bort autentiseringsuppgift för lagringskonto

## <a name="types-of-storage-account-credentials"></a>Typer av autentiseringsuppgifter för lagringskonto
Det finns tre typer av autentiseringsuppgifter för lagringskonto som kan användas med din StorSimple-enhet.

* **Automatiskt genererade lagringskontouppgifter** – som namnet antyder, den här typen av autentiseringsuppgift för lagringskonto skapas automatiskt när tjänsten skapas. Läs mer om hur den här autentiseringsuppgiften för lagringskontot har skapats i [skapa en ny tjänst](storsimple-virtual-array-manage-service.md#create-a-service).
* **autentiseringsuppgifterna för lagringskontot i tjänstprenumeration** – är dessa autentiseringsuppgifter för Azure storage-konto som är associerade med samma prenumeration som för tjänsten. Läs mer om hur dessa storage autentiseringsuppgifter skapas i [om Azure Storage-konton](../storage/common/storage-create-storage-account.md).
* **autentiseringsuppgifter för lagringskonto utanför tjänstprenumeration** – det är dessa autentiseringsuppgifter för Azure storage-konto som inte är kopplad till din tjänst och sannolikt fanns innan tjänsten har skapats.

## <a name="add-a-storage-account-credential"></a>Lägg till autentiseringsuppgift för lagringskonto
Du kan lägga till autentiseringsuppgift för lagringskonto i StorSimple Device Manager service konfigurationen genom att ange ett unikt namn och autentiseringsuppgifter som är länkade till lagringskontot. Du har också alternativet för aktivering av secure sockets layer (SSL)-läge för att skapa en säker kanal för nätverkskommunikation mellan enheten och molnet.

Du kan skapa flera konton för en viss molntjänstleverantör. När autentiseringsuppgifterna för lagringskontot sparas försöker tjänsten kommunicera med din molntjänstleverantör. Autentiseringsuppgifterna och åtkomst-material som du har angett verifieras just nu. Autentiseringsuppgift för lagringskonto skapas endast om autentiseringen lyckas. Om autentiseringen misslyckas visas ett felmeddelande visas.

Använd följande procedurer för att lägga till autentiseringsuppgifter för Azure lagringskonto:

* Lägg till autentiseringsuppgift för lagringskonto har som samma Azure-prenumeration som Device Manager-tjänsten
* Att lägga till en autentiseringsuppgift för Azure storage-konto som ligger utanför den Device Manager-tjänstprenumeration

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Lägg till autentiseringsuppgift för lagringskonto har som samma Azure-prenumeration som Device Manager-tjänsten

1. Navigera till Device Manager-tjänsten, väljer och dubbelklicka på den. Då öppnas det **översikt** bladet.
2. Välj **lagringskontouppgifter** inom den **Configuration** avsnittet.
3. Klicka på **Lägg till**.
4. I den **Lägg till ett lagringskonto** bladet gör du följande:
   
    1. För **prenumeration**väljer **aktuella**.
    2. Ange namnet på ditt Azure storage-konto.
    3. Välj **aktivera** att skapa en säker kanal för nätverkskommunikation mellan din StorSimple-enhet och molnet. Välj **inaktivera** endast om du arbetar i ett privat moln.
    4. Klicka på **Lägg till**. Du meddelas när lagringskontot har skapats.<br></br>
   
        ![Lägg till en befintlig autentiseringsuppgift för lagringskonto](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Att lägga till en autentiseringsuppgift för Azure storage-konto som ligger utanför den Device Manager-tjänstprenumeration

1. Navigera till Device Manager-tjänsten, väljer och dubbelklicka på den. Då öppnas det **översikt** bladet.
2. Välj **lagringskontouppgifter** inom den **Configuration** avsnittet. Här visas alla befintliga autentiseringsuppgifter för lagringskonto som är associerade med StorSimple Device Manager-tjänsten.
3. Klicka på **Lägg till**.
4. I den **Lägg till ett lagringskonto** bladet gör du följande:
   
    1. För **prenumeration**väljer **andra**.
   
    2. Ange namnet på dina autentiseringsuppgifter för Azure storage-konto.
   
    3. I den **åtkomstnyckel för Lagringskontot** text anger du den primära åtkomstnyckeln för dina autentiseringsuppgifter för Azure storage-konto. Den här nyckeln, gå till Azure Storage-tjänsten väljer du din autentiseringsuppgiften för lagringskontot och klicka på **hantera nycklar**. Nu kan du kopiera den primära åtkomstnyckeln.
   
    4. Aktivera SSL genom att klicka på den **aktivera** för att skapa en säker kanal för nätverkskommunikation mellan din StorSimple Device Manager-tjänst och molnet. Klicka på den **inaktivera** endast om du arbetar i ett privat moln.
   
    5. Klicka på **Lägg till**. Du meddelas när autentiseringsuppgifterna för lagringskontot har skapats.

5. Den nyligen skapade autentiseringsuppgiften för lagringskontot visas på bladet för StorSimple konfigurera Device Manager-tjänsten under **lagringskontouppgifter**.
   
    ![Lägg till autentiseringsuppgift för lagringskonto utanför den Device Manager-tjänstprenumeration](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Redigera autentiseringsuppgift för lagringskonto
Du kan redigera autentiseringsuppgift för lagringskonto används av din enhet. Om du redigerar en autentiseringsuppgift för lagringskonto som används för närvarande är tillgängliga fält för att ändra åtkomstnyckeln och SSL-läge för autentiseringsuppgiften för lagringskontot. Du kan ange nya lagringsåtkomstnyckel eller ändra den **aktivera SSL-läge** val och spara de uppdaterade inställningarna.

#### <a name="to-edit-a-storage-account-credential"></a>Så här redigerar du autentiseringsuppgift för lagringskonto
1. Navigera till Device Manager-tjänsten, väljer och dubbelklicka på den. Då öppnas det **översikt** bladet.
2. Välj **lagringskontouppgifter** inom den **Configuration** avsnittet. Här visas alla befintliga autentiseringsuppgifter för lagringskonto som är associerade med StorSimple Device Manager-tjänsten.
3. Välj i listan med autentiseringsuppgifterna för lagringskontot, och dubbelklicka på det konto som du vill ändra.
4. I autentiseringsuppgifterna för lagringskontot **egenskaper** bladet gör du följande:
   
   1. Om behövs kan du ändra den **aktivera SSL** läge val.
   2. Du kan välja att återskapa åtkomstnycklar för lagring konto autentiseringsuppgifter. Mer information finns i [återskapa lagringskontonycklarna](../storage/common/storage-account-manage.md#access-keys). Ange ny autentiseringsuppgift lagringskontonyckeln. Detta är den primära åtkomstnyckeln för ett Azure storage-konto.
   3. Klicka på **spara** överst i den **egenskaper** bladet för att spara inställningarna. Inställningarna har uppdaterats vid den **lagringskontouppgifter** bladet.
      
      ![Redigera autentiseringsuppgift för lagringskonto](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Ta bort autentiseringsuppgift för lagringskonto
> [!IMPORTANT]
> Du kan ta bort autentiseringsuppgift för lagringskonto endast om den inte används. Om autentiseringsuppgift för lagringskonto används, du får ett meddelande.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Att ta bort autentiseringsuppgift för lagringskonto
1. Navigera till Device Manager-tjänsten, väljer och dubbelklicka på den. Då öppnas det **översikt** bladet.
2. Välj **lagringskontouppgifter** inom den **Configuration** avsnittet. Här visas alla befintliga autentiseringsuppgifter för lagringskonto som är associerade med StorSimple Device Manager-tjänsten.
3. Välj i listan med autentiseringsuppgifterna för lagringskontot, och dubbelklicka på det konto som du vill ta bort.
4. I autentiseringsuppgifterna för lagringskontot **egenskaper** bladet gör du följande:
   
   1. Klicka på **ta bort** att ta bort autentiseringsuppgifterna.
   2. När du uppmanas att bekräfta klickar du på **Ja** att ta bort. Listan uppdateras för att återspegla ändringarna.
      
      ![Ta bort autentiseringsuppgift för lagringskonto](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synkronisering av autentiseringsuppgifter för lagringskontonycklar
Av säkerhetsskäl är nyckelrotation ofta ett krav i datacenter. En Microsoft Azure-administratör kan återskapa eller ändra den primära eller sekundära nyckeln genom att direkt komma åt autentiseringsuppgifterna för lagringskontot (via Microsoft Azure Storage-tjänsten). StorSimple Device Manager-tjänsten finns inte den här ändringen automatiskt.

För att informera StorSimple Device Manager-tjänsten för ändringen behöver åtkomst till StorSimple Device Manager-tjänsten komma åt autentiseringsuppgifterna för lagringskontot och synkronisera sedan den primära eller sekundära nyckeln (beroende på vilket som ändrats). Tjänsten sedan hämtar den senaste nyckeln, krypterar nycklarna och skickar den krypterade nyckeln till enheten.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Synkronisera nycklar för autentiseringsuppgifter för lagringskonto i samma prenumeration som tjänst (endast Azure)
1. På bladet för tjänsten startsida väljer du din tjänst, dubbelklickar du på namnet på tjänsten och sedan i den **Configuration** klickar du på **lagringskontouppgifter**.
2. På den **lagringskontouppgifter** bladet i listan med autentiseringsuppgifterna för Lagringskontot, Välj lagringskontot autentiseringsuppgifter vars nycklar som du vill synkronisera.
3. I den **egenskaper** bladet för den valda säkerhetskopieringsprincipen gör du följande:
   
    1. Klicka på **mer**, och klicka sedan på **synkronisera åtkomstnyckel**.
   
    2. När du uppmanas att bekräfta klickar du på **synkronisera nyckel** för synkroniseringen.
    
4. Du måste uppdatera den nyckel som tidigare har ändrats i Microsoft Azure Storage-tjänsten i StorSimple Device Manager-tjänsten. I den **synkronisera lagringskontonyckel** bladet om den primära åtkomstnyckeln har ändrats (återskapade), klickar du på primära och klicka sedan på **synkronisera nyckel**. Om den sekundära nyckeln har ändrats klickar du på **sekundära**, och klicka sedan på **synkronisera nyckel**.
   
    ![Synkronisera åtkomstnyckel](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [administrera StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

