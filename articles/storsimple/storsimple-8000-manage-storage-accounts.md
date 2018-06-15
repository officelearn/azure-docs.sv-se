---
title: Hantera StorSimple autentiseringsuppgifterna för ditt lagringskonto för Microsoft Azure StorSimple 8000-serien enheter | Microsoft Docs
description: Beskriver hur du kan använda sidan StorSimple Enhetshanteraren konfigurera för att lägga till, redigera, ta bort eller rotera tangenterna säkerhet för ett lagringskonto.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 36058ad69ea670998b50cf9038741c294a5b79ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875132"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att hantera autentiseringsuppgifterna för ditt lagringskonto

## <a name="overview"></a>Översikt

Den **Configuration** avsnittet i bladet StorSimple Enhetshanteraren tjänsten visas alla tjänsten för global-parametrar som kan skapas i StorSimple enheten Manager-tjänsten. De här parametrarna kan tillämpas på alla enheter som är anslutna till tjänsten och inkluderar:

* Autentiseringsuppgifter för lagringskonto
* Bandbredd-mallar 
* Access control-poster 

Den här självstudiekursen beskrivs hur du lägger till, redigera, ta bort lagringskontouppgifter eller rotera tangenterna säkerhet för ett lagringskonto.

 ![Lista över autentiseringsuppgifter för lagringskonton](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Storage-konton innehåller de autentiseringsuppgifter som StorSimple-enheten använder för att få åtkomst till ditt lagringskonto med din molntjänstleverantör. För Microsoft Azure storage-konton är dessa autentiseringsuppgifter, till exempel namnet på kontot och den primära åtkomstnyckeln. 

På den **lagringskontouppgifter** bladet, alla lagringskonton som skapats för fakturering prenumerationen visas i tabellformat som innehåller följande information:

* **Namnet** – det unika namnet som tilldelas kontot när den skapades.
* **SSL aktiverat** – om SSL är aktiverat och enhet till moln kommunikation via den säkra kanalen.
* **Används av** – antal volymer med storage-konto.

De vanligaste uppgifterna som rör storage-konton som du kan utföra är:

* Lägg till ett lagringskonto 
* Redigera ett lagringskonto 
* Ta bort ett lagringskonto 
* Viktiga rotation av storage-konton 

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

Det finns tre typer av lagringskonton som kan användas med din StorSimple-enhet.

* **Automatiskt genererade lagringskonton** – som namnet antyder den här typen av lagringskonto genereras automatiskt när tjänsten skapas. Läs mer om hur det här lagringskontot har skapats i [steg 1: skapa en ny tjänst](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md). 
* **Storage-konton i prenumerationen på tjänsten** – dessa är Azure storage-konton som är associerade med samma prenumeration som för tjänsten. Läs mer om hur dessa lagring konton skapas i [om Azure Lagringskonton](../storage/common/storage-create-storage-account.md). 
* **Storage-konton utanför prenumerationen på tjänsten** – dessa är Azure storage-konton som inte tillhör med din tjänst och troligtvis fanns innan tjänsten skapades.

## <a name="add-a-storage-account"></a>Lägg till ett lagringskonto

Du kan lägga till ett lagringskonto genom att ange ett unikt namn och autentiseringsuppgifter som är kopplade till storage-konto (med angivna molntjänstleverantören). Du har också alternativet att aktivera secure sockets layer (SSL)-läge för att skapa en säker kanal för nätverkskommunikation mellan din enhet och molnet.

Du kan skapa flera konton för en viss molntjänstleverantören. Tänk dock på att du inte kan ändra molntjänstleverantören efter ett lagringskonto skapas.

Tjänsten försöker kommunicera med din molntjänstleverantör medan lagringskontot sparas. Autentiseringsuppgifterna och åtkomst-material som du har angett autentiseras just nu. Ett lagringskonto skapas endast om autentiseringen lyckas. Om autentiseringen misslyckas visas ett felmeddelande visas.

Använd följande procedurer för att lägga till autentiseringsuppgifter för Azure storage-konto:

* Om du vill lägga till en autentiseringsuppgift för storage-konto har som samma Azure-prenumerationen som tjänsten Device Manager
* Att lägga till ett Azure storage-kontoautentisering som ligger utanför tjänstprenumeration Enhetshanteraren

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Att lägga till ett Azure storage-kontoautentisering utanför prenumerationen på StorSimple Device Manager-tjänsten

1. Navigera till din StorSimple Enhetshanteraren tjänst, väljer och dubbelklicka på den. Då öppnas den **översikt** bladet.
2. Välj **lagringskontouppgifter** inom den **Configuration** avsnitt. Här visas alla befintliga lagringskontouppgifter som är kopplade till StorSimple enheten Manager-tjänsten.
3. Klicka på **Lägg till**.
4. I den **lägga till en lagring kontoautentisering** bladet gör du följande:
   
    1. För **prenumeration**väljer **andra**.
   
    2. Ange namnet på dina autentiseringsuppgifter för Azure storage-konto.
   
    3. I den **åtkomstnyckeln för Lagringskontot** text anger den primära åtkomstnyckeln för dina autentiseringsuppgifter för Azure storage-konto. Gå till Azure Storage-tjänsten, Välj lagring-kontoautentisering och klicka på för att få den här nyckeln **hantera nycklar**. Nu kan du kopiera den primära åtkomstnyckeln.
   
    4. Aktivera SSL genom att klicka på den **aktivera** för att skapa en säker kanal för nätverkskommunikation mellan din StorSimple Device Manager-tjänst och molnet. Klicka på den **inaktivera** knappen bara om du arbetar inom ett privat moln.
   
    5. Klicka på **Lägg till**. Du meddelas när inloggningsuppgifterna till storage-kontot har skapats.

5. Nyligen skapade lagring kontoautentisering visas i bladet StorSimple Konfigurera Enhetshanteraren tjänsten under **lagringskontouppgifter**.
   


## <a name="edit-a-storage-account"></a>Redigera ett lagringskonto

Du kan redigera ett lagringskonto som används av en volymbehållare. Om du redigerar ett lagringskonto som används för närvarande är endast användas för att ändra fältet åtkomstnyckeln för lagringskontot. Du kan ange den nya lagringsåtkomstnyckel och spara de uppdaterade inställningarna.

#### <a name="to-edit-a-storage-account"></a>Så här redigerar du ett storage-konto

1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.

    ![Autentiseringsuppgifter för lagringskonto](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. I den **lagringskontouppgifter** bladet från listan över lagringskontouppgifter, markera och klicka på den enhet som du vill redigera. 

3. Du kan ändra den **aktivera SSL** val. Du kan också klicka på **mer...**  och välj sedan **Sync snabbtangenten för att rotera** åtkomstnycklar för lagring konto. Gå till [nyckeln rotation av lagringskonton](#key-rotation-of-storage-accounts) mer information om hur du utför viktiga rotation. När du har ändrat inställningarna klickar du på **spara**. 

    ![Spara autentiseringsuppgifterna redigerade lagringskonto](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. 

    ![Bekräfta ändringar](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Inställningarna uppdateras och sparas för ditt lagringskonto. 

## <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto

> [!IMPORTANT]
> Du kan ta bort ett lagringskonto endast om den inte används av en volymbehållare. Om ett lagringskonto används av en volymbehållare, först ta bort volymbehållaren och ta sedan bort det associerade lagringskontot.

#### <a name="to-delete-a-storage-account"></a>Ta bort ett lagringskonto

1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.

2. Hovra över det konto som du vill ta bort i listan tabular för storage-konton. Högerklicka på att anropa snabbmenyn och klicka på **ta bort**.

    ![Ta bort lagringskontouppgifter](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. När du uppmanas att bekräfta, klickar du på **Ja** att ta bort. Tabell listan kommer att uppdateras för att återspegla ändringarna.

    ![Bekräfta borttagning](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Viktiga rotation av storage-konton

Av säkerhetsskäl är viktiga rotation ofta ett krav i datacenter. Varje Microsoft Azure-prenumeration kan ha en eller flera associerade storage-konton. Åtkomst till dessa konton styrs av prenumeration och åtkomst nycklarna för varje lagringskonto. 

När du skapar ett lagringskonto genererar Microsoft Azure två 512-bitars lagring åtkomstnycklar som används för autentisering när lagringskontot används. Har två åtkomstnycklar för lagring kan du återskapa nycklarna utan avbrott i lagringstjänsten eller åtkomst till tjänsten. Den nyckel som används för närvarande är den *primära* och den säkerhetskopiera nyckeln kallas den *sekundära* nyckel. En av dessa två nycklar måste anges när Microsoft Azure StorSimple-enheten ansluter till din molntjänstleverantör för lagring.

## <a name="what-is-key-rotation"></a>Vad är viktiga rotation?

Normalt använder program endast en av nycklarna åtkomst till data. Efter en viss tidsperiod, kan du ha dina program växla till med hjälp av den andra nyckeln. När du har växlat dina program till den sekundära nyckeln kan du dra tillbaka den första nyckeln och sedan skapa en ny nyckel. Med hjälp av två sätt kan ditt program åtkomst till data utan att det medför några driftavbrott.

Lagringskontonycklar lagras alltid i tjänsten i krypterad form. Dessa kan dock återställas via StorSimple enheten Manager-tjänsten. Tjänsten kan hämta primärnyckeln och sekundärnyckeln för alla lagringskonton i samma prenumeration, inklusive konton som skapats i lagringstjänsten samt lagringsplatsen standard som genereras när tjänsten StorSimple Enhetshanteraren först skapas. Tjänsten StorSimple Enhetshanteraren alltid hämta nycklarna från den klassiska Azure-portalen och lagra dem på ett sätt som är krypterade.

## <a name="rotation-workflow"></a>Rotation arbetsflöde

En Microsoft Azure-administratör kan återskapa eller ändra de primära och sekundära nycklarna genom direkt åtkomst till lagringskontot (via Microsoft Azure Storage service). StorSimple enheten Manager-tjänsten kan inte se ändringen automatiskt.

För att informera tjänsten StorSimple Enhetshanteraren ändra behöver du komma åt Enhetshanteraren för StorSimple-tjänsten åtkomst till lagringskontot, och synkronisera primär eller sekundär nyckeln (beroende på vilket som ändrats). Tjänsten sedan hämtar den senaste nyckeln krypterar nycklarna och skickar den krypterade nyckeln till enheten.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Att synkronisera nycklar för lagringskonton med samma prenumeration som för tjänsten 
1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.
2. Klicka på det som du vill ändra tabular listan med lagringskonton. 

    ![Synkronisera nycklar](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Klicka på **... Flera** och välj sedan **Sync snabbtangenten för att rotera**.   

    ![Synkronisera nycklar](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. Du måste uppdatera den nyckel som tidigare har ändrats i Microsoft Azure Storage-tjänsten i Enhetshanteraren för StorSimple-tjänsten. Om den primära åtkomstnyckeln har ändrats (återskapats), väljer **primära** nyckel. Om den sekundära nyckeln har ändrats, väljer **sekundära** nyckel. Klicka på **Sync nyckeln**.
      
      ![Synkronisera nycklar](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Du meddelas när nyckeln är har sycnhronized.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Att synkronisera nycklar för lagringskonton utanför prenumerationen på tjänsten
1. På den **Services** klickar du på den **konfigurera** fliken.
2. Klicka på **lägga till eller redigera Lagringskonton**.
3. I dialogrutan för att göra följande:
   
   1. Välj lagringskontot med åtkomstnyckel som du vill uppdatera.
   2. Du behöver uppdatera lagringsåtkomstnyckel i Enhetshanteraren för StorSimple-tjänsten. I det här fallet kan du se lagringsåtkomstnyckel. Ange den nya nyckeln i den **åtkomstnyckeln för Lagringskontot** rutan. 
   3. Spara ändringarna. Din lagringsåtkomstnyckel för kontot ska nu uppdateras.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [StorSimple-säkerhet](storsimple-8000-security.md).
* Lär dig mer om [använder Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

