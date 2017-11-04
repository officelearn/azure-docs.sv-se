---
title: Hantera virtuella StorSimple-matris lagringskontouppgifter | Microsoft Docs
description: "Beskriver hur du kan använda sidan Konfigurera Enhetshanteraren för StorSimple för att lägga till, redigera, ta bort eller rotera säkerhetsnycklarna för lagringskontouppgifter som är associerade med den virtuella StorSimple-matrisen."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: a4ce2d329d0e1399cffaf886adf2b95e34b9cd7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Använd StorSimple Enhetshanteraren för att hantera lagringskontouppgifter för virtuell StorSimple-matris

## <a name="overview"></a>Översikt
Den **Configuration** på StorSimple Enhetshanteraren service bladet för din virtuella StorSimple-matrisen anger tjänsten för global parametrar som kan skapas i StorSimple Manager-tjänsten. De här parametrarna kan tillämpas på alla enheter som är anslutna till tjänsten och inkluderar:

* Autentiseringsuppgifter för lagringskonto
* Access control-poster
  
  ![Enhetshanteraren Service instrumentpanelen](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Den här självstudiekursen beskrivs hur du kan lägga till, redigera eller ta bort lagringskontouppgifter för din virtuella StorSimple-matrisen. Informationen i den här kursen gäller bara för den virtuella StorSimple-matrisen. Information om hur du hanterar storage-konton i 8000-serien finns [använda StorSimple Manager-tjänsten för att hantera ditt lagringskonto](storsimple-manage-storage-accounts.md).

Lagringskontouppgifter innehåller de autentiseringsuppgifter som används av enheten för att få åtkomst till ditt lagringskonto med din molntjänstleverantör. För Microsoft Azure storage-konton är dessa autentiseringsuppgifter, till exempel namnet på kontot och den primära åtkomstnyckeln.

På den **lagringskontouppgifter** bladet, alla lagringskontouppgifter som har skapats för fakturering prenumerationen visas i tabellformat som innehåller följande information:

* **Namnet** – det unika namnet som tilldelas kontot när den skapades.
* **SSL aktiverat** – om SSL är aktiverat och enhet till moln kommunikation via den säkra kanalen.
  
  ![Konfigurationsavsnittet](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

De vanligaste uppgifterna som rör lagringskontouppgifter som kan utföras på den **lagringskontouppgifter** bladet är:

* Lägg till autentiseringsuppgift för lagringskonto
* Redigera en autentiseringsuppgift för storage-konto
* Ta bort en autentiseringsuppgift för storage-konto

## <a name="types-of-storage-account-credentials"></a>Typer av autentiseringsuppgifterna för lagringskonto
Det finns tre typer av lagring kontoautentiseringsuppgifter som kan användas med din StorSimple-enhet.

* **Automatiskt genererade lagringskontouppgifter** – som namnet antyder den här typen av lagring kontoautentisering genereras automatiskt när tjänsten skapas. Läs mer om hur dessa autentiseringsuppgifter för storage-konto har skapats i [skapa en ny tjänst](storsimple-virtual-array-manage-service.md#create-a-service).
* **lagringskontouppgifter i prenumerationen på tjänsten** – är dessa autentiseringsuppgifter för Azure storage-konto som är associerade med samma prenumeration som för tjänsten. Läs mer om hur dessa lagring autentiseringsuppgifter skapas i [om Azure Lagringskonton](../storage/common/storage-create-storage-account.md).
* **lagringskontouppgifter utanför prenumerationen på tjänsten** – det är dessa autentiseringsuppgifter för Azure storage-konto som inte tillhör med din tjänst och troligtvis fanns innan tjänsten skapades.

## <a name="add-a-storage-account-credential"></a>Lägg till autentiseringsuppgift för lagringskonto
Du kan lägga till en autentiseringsuppgift för storage-konto i tjänstimplementeringen StorSimple Enhetshanteraren genom att ange ett unikt namn och autentiseringsuppgifter som är länkade till lagringskontot. Du har också alternativet att aktivera secure sockets layer (SSL)-läge för att skapa en säker kanal för nätverkskommunikation mellan din enhet och molnet.

Du kan skapa flera konton för en viss molntjänstleverantören. Tjänsten försöker kommunicera med din molntjänstleverantör medan kontoautentisering lagring sparas. Autentiseringsuppgifterna och åtkomst-material som du har angett autentiseras just nu. Storage-konto autentiseringsuppgifter skapas endast om autentiseringen lyckas. Om autentiseringen misslyckas visas ett felmeddelande visas.

Använd följande procedurer för att lägga till autentiseringsuppgifter för Azure storage-konto:

* Om du vill lägga till en autentiseringsuppgift för storage-konto har som samma Azure-prenumerationen som tjänsten Device Manager
* Att lägga till ett Azure storage-kontoautentisering som ligger utanför tjänstprenumeration Enhetshanteraren

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Om du vill lägga till en autentiseringsuppgift för storage-konto har som samma Azure-prenumerationen som tjänsten Device Manager

1. Gå till Enhetshanteraren tjänsten, väljer och dubbelklicka på den. Då öppnas den **översikt** bladet.
2. Välj **lagringskontouppgifter** inom den **Configuration** avsnitt.
3. Klicka på **Lägg till**.
4. I den **Lägg till ett lagringskonto** bladet gör du följande:
   
    1. För **prenumeration**väljer **aktuella**.
    2. Ange namnet på ditt Azure storage-konto.
    3. Välj **aktivera** att skapa en säker kanal för nätverkskommunikation mellan din StorSimple-enhet och molnet. Välj **inaktivera** endast om du arbetar inom ett privat moln.
    4. Klicka på **Lägg till**. Du meddelas när lagringskontot har skapats.<br></br>
   
        ![Lägg till en befintlig lagring konto autentiseringsuppgift](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Att lägga till ett Azure storage-kontoautentisering som ligger utanför tjänstprenumeration Enhetshanteraren

1. Gå till Enhetshanteraren tjänsten, väljer och dubbelklicka på den. Då öppnas den **översikt** bladet.
2. Välj **lagringskontouppgifter** inom den **Configuration** avsnitt. Här visas alla befintliga lagringskontouppgifter som är kopplade till StorSimple enheten Manager-tjänsten.
3. Klicka på **Lägg till**.
4. I den **Lägg till ett lagringskonto** bladet gör du följande:
   
    1. För **prenumeration**väljer **andra**.
   
    2. Ange namnet på dina autentiseringsuppgifter för Azure storage-konto.
   
    3. I den **åtkomstnyckeln för Lagringskontot** text anger den primära åtkomstnyckeln för dina autentiseringsuppgifter för Azure storage-konto. Gå till Azure Storage-tjänsten, Välj lagring-kontoautentisering och klicka på för att få den här nyckeln **hantera nycklar**. Nu kan du kopiera den primära åtkomstnyckeln.
   
    4. Aktivera SSL genom att klicka på den **aktivera** för att skapa en säker kanal för nätverkskommunikation mellan din StorSimple Device Manager-tjänst och molnet. Klicka på den **inaktivera** knappen bara om du arbetar inom ett privat moln.
   
    5. Klicka på **Lägg till**. Du meddelas när inloggningsuppgifterna till storage-kontot har skapats.

5. Nyligen skapade lagring kontoautentisering visas i bladet StorSimple Konfigurera Enhetshanteraren tjänsten under **lagringskontouppgifter**.
   
    ![Lägg till en lagring kontoautentisering utanför tjänstprenumeration Enhetshanteraren](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Redigera en autentiseringsuppgift för storage-konto
Du kan redigera en storage-konto autentiseringsuppgifter som används av enheten. Om du redigerar en autentiseringsuppgift för lagring kontot som används för närvarande är tillgängliga fält för att ändra snabbtangent och SSL-läge för autentiseringsuppgifter för storage-konto. Du kan ange den nya lagringsåtkomstnyckel eller ändra den **aktivera SSL-läge** val och spara de uppdaterade inställningarna.

#### <a name="to-edit-a-storage-account-credential"></a>Så här redigerar du en autentiseringsuppgift för storage-konto
1. Gå till Enhetshanteraren tjänsten, väljer och dubbelklicka på den. Då öppnas den **översikt** bladet.
2. Välj **lagringskontouppgifter** inom den **Configuration** avsnitt. Här visas alla befintliga lagringskontouppgifter som är kopplade till StorSimple enheten Manager-tjänsten.
3. Välj i listan tabular lagring av autentiseringsuppgifter för konton och dubbelklicka på det konto som du vill ändra.
4. I lagring kontoautentisering **egenskaper** bladet gör du följande:
   
   1. Om nödvändigt, kan du ändra den **aktivera SSL** läge val.
   2. Du kan välja att återskapa åtkomstnycklar för lagring konto autentiseringsuppgifter. Mer information finns i [återskapa lagringskontonycklarna](../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys). Ange den nya lagringskontonyckel autentiseringsuppgifter. Detta är den primära åtkomstnyckeln för ett Azure storage-konto.
   3. Klicka på **spara** överst i den **egenskaper** bladet för att spara inställningarna. Inställningarna har uppdaterats vid den **lagringskontouppgifter** bladet.
      
      ![Redigera en autentiseringsuppgift för storage-konto](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Ta bort en autentiseringsuppgift för storage-konto
> [!IMPORTANT]
> Du kan ta bort en autentiseringsuppgift för storage-konto endast om den inte används. Om en autentiseringsuppgift för storage-konto används, visas ett meddelande.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Ta bort en autentiseringsuppgift för storage-konto
1. Gå till Enhetshanteraren tjänsten, väljer och dubbelklicka på den. Då öppnas den **översikt** bladet.
2. Välj **lagringskontouppgifter** inom den **Configuration** avsnitt. Här visas alla befintliga lagringskontouppgifter som är kopplade till StorSimple enheten Manager-tjänsten.
3. Välj i listan tabular lagring av autentiseringsuppgifter för konton och dubbelklicka på det konto som du vill ta bort.
4. I lagring kontoautentisering **egenskaper** bladet gör du följande:
   
   1. Klicka på **ta bort** att ta bort autentiseringsuppgifterna.
   2. När du uppmanas att bekräfta, klickar du på **Ja** att ta bort. Tabular listan har uppdaterats för att återspegla ändringarna.
      
      ![Ta bort en autentiseringsuppgift för storage-konto](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synkroniserar lagringskontonycklar för autentiseringsuppgifter
Av säkerhetsskäl är viktiga rotation ofta ett krav i datacenter. En Microsoft Azure-administratör kan återskapa eller ändra de primära och sekundära nycklarna genom direkt åtkomst till lagring kontoautentisering (via Microsoft Azure Storage service). StorSimple enheten Manager-tjänsten kan inte se ändringen automatiskt.

För att informera tjänsten StorSimple Enhetshanteraren ändra behöver komma åt Enhetshanteraren för StorSimple-tjänsten åtkomst till lagring-kontoautentisering och synkronisera sedan den primära eller sekundära nyckeln (beroende på vilket som ändrats). Tjänsten sedan hämtar den senaste nyckeln krypterar nycklarna och skickar den krypterade nyckeln till enheten.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Att synkronisera nycklar för autentiseringsuppgifterna för lagringskonto i samma prenumeration som tjänst (Azure)
1. På bladet service landning väljer du din tjänst, dubbelklickar du på tjänstens namn och klicka sedan på den **Configuration** klickar du på **lagringskontouppgifter**.
2. På den **lagringskontouppgifter** bladet i listan över lagringskontouppgifter, Välj lagringskontot autentiseringsuppgifter vars nycklar som du vill synkronisera.
3. I den **egenskaper** bladet för kontoautentisering valda gör du följande:
   
    1. Klicka på **mer**, och klicka sedan på **Sync åtkomstnyckel**.
   
    2. När du uppmanas att bekräfta, klickar du på **Sync nyckeln** för synkroniseringen.
    
4. Du måste uppdatera den nyckel som tidigare har ändrats i Microsoft Azure Storage-tjänsten i Enhetshanteraren för StorSimple-tjänsten. I den **synkronisera lagringskontonyckel** bladet om den primära åtkomstnyckeln har ändrats (återskapats), klicka på primär och klicka sedan på **Sync nyckeln**. Om den sekundära nyckeln har ändrats, klickar du på **sekundära**, och klicka sedan på **Sync nyckeln**.
   
    ![Åtkomstnyckeln för synkronisering](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-acess-key.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

