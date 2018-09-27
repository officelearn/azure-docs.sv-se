---
title: Hantera din StorSimple-lagringskontouppgifter för enheter i Microsoft Azure StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du kan använda konfigurationssidan för StorSimple Device Manager för att lägga till, redigera, ta bort eller rotera säkerhetsnycklarna för ett lagringskonto.
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
ms.openlocfilehash: afaa4c090508ee5a2306c2be1515076e6bd4f100
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225733"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Använda StorSimple Device Manager-tjänsten för att hantera dina autentiseringsuppgifter för lagringskonto

## <a name="overview"></a>Översikt

Den **Configuration** avsnittet i bladet för StorSimple Device Manager-tjänsten visas alla tjänsten för global-parametrar som kan skapas i StorSimple Device Manager-tjänsten. Dessa parametrar kan tillämpas på alla enheter som är anslutna till tjänsten och inkluderar:

* Autentiseringsuppgifter för lagringskonto
* Bandbreddsmallar 
* Åtkomstkontrollposter 

Den här självstudien beskrivs hur du lägger till, redigera, ta bort autentiseringsuppgifterna för lagringskontot eller rotera säkerhetsnycklarna för ett lagringskonto.

 ![Lista över autentiseringsuppgifter för lagringskonton](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Storage-konton innehåller de autentiseringsuppgifter som StorSimple-enheten använder för att få åtkomst till ditt lagringskonto med din molntjänstleverantör. För Microsoft Azure storage-konton är dessa autentiseringsuppgifter, till exempel namnet på kontot och den primära åtkomstnyckeln. 

På den **lagringskontouppgifter** bladet, alla lagringskonton som har skapats för faktureringsprenumerationen visas i tabellformat som innehåller följande information:

* **Namn på** – det unika namnet som tilldelas kontot när den skapades.
* **SSL aktiverat** – om SSL är aktiverat och enhet till molnet kommunikation via den säkra kanalen.
* **Används av** – antalet volymer med storage-konto.

De vanligaste uppgifterna som rör lagringskonton som kan utföras är:

* Lägg till ett lagringskonto 
* Redigera ett storage-konto 
* Ta bort ett lagringskonto 
* Rotation av storage-konton 

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

Det finns tre typer av lagringskonton som kan användas med din StorSimple-enhet.

* **Automatiskt genererade lagringskonton** – som namnet antyder, den här typen av lagringskonto skapas automatiskt när tjänsten skapas. Läs mer om hur det här lagringskontot har skapats i [steg 1: skapa en ny tjänst](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md). 
* **Storage-konton i tjänstprenumeration** – dessa är Azure storage-konton som är associerade med samma prenumeration som för tjänsten. Läs mer om hur dessa storage-konton har skapats i [om Azure Storage-konton](../storage/common/storage-create-storage-account.md). 
* **Storage-konton utanför tjänstprenumeration** – dessa är Azure storage-konton som inte är kopplad till din tjänst och sannolikt fanns innan tjänsten har skapats.

## <a name="add-a-storage-account"></a>Lägg till ett lagringskonto

Du kan lägga till ett lagringskonto genom att ange ett unikt namn och autentiseringsuppgifter som är länkade till storage-konto (med angivna molntjänstleverantören). Du har också alternativet för aktivering av secure sockets layer (SSL)-läge för att skapa en säker kanal för nätverkskommunikation mellan enheten och molnet.

Du kan skapa flera konton för en viss molntjänstleverantör. Tänk dock på att du inte kan ändra molntjänstleverantören när du har skapat ett lagringskonto.

När lagringskontot sparas, försöker tjänsten kommunicerar med din molntjänstleverantör. Autentiseringsuppgifterna och åtkomst-material som du har angett autentiseras just nu. Ett lagringskonto skapas endast om autentiseringen lyckas. Om autentiseringen misslyckas visas ett felmeddelande visas.

Använd följande procedurer för att lägga till autentiseringsuppgifter för Azure lagringskonto:

* Lägg till autentiseringsuppgift för lagringskonto har som samma Azure-prenumeration som Device Manager-tjänsten
* Att lägga till en autentiseringsuppgift för Azure storage-konto som ligger utanför den Device Manager-tjänstprenumeration

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Att lägga till ett Azure storage-kontouppgiften utanför den StorSimple Device Manager-tjänstprenumeration

1. Navigera till StorSimple Device Manager-tjänsten, väljer och dubbelklicka på den. Då öppnas det **översikt** bladet.
2. Välj **lagringskontouppgifter** inom den **Configuration** avsnittet. Här visas alla befintliga autentiseringsuppgifter för lagringskonto som är associerade med StorSimple Device Manager-tjänsten.
3. Klicka på **Lägg till**.
4. I den **Lägg till autentiseringsuppgift för lagringskonto** bladet gör du följande:
   
    1. För **prenumeration**väljer **andra**.
   
    2. Ange namnet på dina autentiseringsuppgifter för Azure storage-konto.
   
    3. I den **åtkomstnyckel för Lagringskontot** text anger du den primära åtkomstnyckeln för dina autentiseringsuppgifter för Azure storage-konto. Den här nyckeln, gå till Azure Storage-tjänsten väljer du din autentiseringsuppgiften för lagringskontot och klicka på **hantera nycklar**. Nu kan du kopiera den primära åtkomstnyckeln.
   
    4. Aktivera SSL genom att klicka på den **aktivera** för att skapa en säker kanal för nätverkskommunikation mellan din StorSimple Device Manager-tjänst och molnet. Klicka på den **inaktivera** endast om du arbetar i ett privat moln.
   
    5. Klicka på **Lägg till**. Du meddelas när autentiseringsuppgifterna för lagringskontot har skapats.

5. Den nyligen skapade autentiseringsuppgiften för lagringskontot visas på bladet för StorSimple konfigurera Device Manager-tjänsten under **lagringskontouppgifter**.
   


## <a name="edit-a-storage-account"></a>Redigera ett storage-konto

Du kan redigera ett storage-konto som används av en volymbehållare. Om du redigerar ett lagringskonto som används för närvarande är det enda fältet användas för att ändra åtkomstnyckeln för lagringskontot. Du kan ange nya lagringsåtkomstnyckel och spara de uppdaterade inställningarna.

#### <a name="to-edit-a-storage-account"></a>Så här redigerar du ett storage-konto

1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.

    ![Autentiseringsuppgifter för lagringskonto](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. I den **lagringskontouppgifter** bladet från listan med autentiseringsuppgifterna för lagringskontot, Välj och klicka på det som du vill redigera. 

3. Du kan ändra den **aktivera SSL** val. Du kan också klicka på **mer...**  och välj sedan **synkronisera åtkomstnyckel för att rotera** dina åtkomstnycklar för lagringskontot. Gå till [nyckeln rotation av storage-konton](#key-rotation-of-storage-accounts) mer information om hur du utför rotation av. När du har ändrat inställningarna klickar du på **spara**. 

    ![Spara autentiseringsuppgifterna för redigerade lagringskontot](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. 

    ![Bekräfta ändringar](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Inställningarna uppdateras och sparas för ditt lagringskonto. 

## <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto

> [!IMPORTANT]
> Du kan ta bort ett lagringskonto endast om den inte används av en volymbehållare. Om ett lagringskonto som används av en volymbehållare, först ta bort volymcontainern och ta sedan bort det associerade lagringskontot.

#### <a name="to-delete-a-storage-account"></a>Ta bort ett lagringskonto

1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.

2. I listan med lagringskonton, hovrar du över det konto som du vill ta bort. Högerklicka för att öppna snabbmenyn och klickar på **ta bort**.

    ![Ta bort autentiseringsuppgifter för lagringskonto](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. När du uppmanas att bekräfta klickar du på **Ja** att ta bort. Listan uppdateras för att återspegla ändringarna.

    ![Bekräfta borttagning](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Rotation av storage-konton

Av säkerhetsskäl är nyckelrotation ofta ett krav i datacenter. Varje Microsoft Azure-prenumeration kan ha en eller flera associerade storage-konton. Åtkomst till dessa konton styrs av prenumerationen och åtkomstnycklar för varje lagringskonto. 

När du skapar ett lagringskonto genererar Microsoft Azure två 512-bitars lagringsåtkomstnycklar som används för autentisering när lagringskontot används. Har två åtkomstnycklar för lagring kan du återskapa nycklarna utan avbrott i lagringstjänsten eller åtkomst till tjänsten. Den nyckel som används för närvarande är den *primära* och den säkerhetskopiera nyckeln kallas den *sekundära* nyckel. En av dessa två nycklar måste anges när Microsoft Azure StorSimple-enheten ansluter till din molntjänstleverantör för lagring.

## <a name="what-is-key-rotation"></a>Vad är nyckelrotation?

Normalt kan använda program endast en av nycklarna komma åt dina data. Efter en viss tidsperiod, kan du ha dina program växla till den andra nyckeln. När du har växlat dina program till den sekundära nyckeln kan du dra tillbaka den första nyckeln och sedan skapa en ny nyckel. Med hjälp av två nycklar på så sätt kan dina program åtkomst till data utan driftstopp.

Storage-kontonycklar lagras alltid i tjänsten i krypterad form. Dessa kan dock återställas via StorSimple Device Manager-tjänsten. Tjänsten kan hämta primärnyckeln och sekundärnyckeln för alla lagringskonton i samma prenumeration, inklusive konton som skapats i lagringstjänsten samt standard storage-konton som skapades när StorSimple Device Manager-tjänsten kunde första Skapa. StorSimple Device Manager-tjänsten alltid hämta dessa nycklar från den klassiska Azure-portalen och lagra dem i en krypterad.

## <a name="rotation-workflow"></a>Arbetsflöde för rotation

En Microsoft Azure-administratör kan återskapa eller ändra den primära eller sekundära nyckeln genom att gå direkt till storage-konto (via Microsoft Azure Storage-tjänsten). StorSimple Device Manager-tjänsten finns inte den här ändringen automatiskt.

För att informera StorSimple Device Manager-tjänsten av en ändring, behöver du åtkomst till StorSimple Device Manager-tjänsten åtkomst till lagringskontot, och synkronisera sedan den primära eller sekundära nyckeln (beroende på vilket som ändrats). Tjänsten sedan hämtar den senaste nyckeln, krypterar nycklarna och skickar den krypterade nyckeln till enheten.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Synkronisera nycklar för lagringskonton i samma prenumeration som tjänsten 
1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.
2. Klicka på det som du vill ändra i tabellistan med lagringskonton. 

    ![Synkronisera nycklar](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Klicka på **... Mer** och välj sedan **synkronisera åtkomstnyckel för att rotera**.   

    ![Synkronisera nycklar](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. Du måste uppdatera den nyckel som tidigare har ändrats i Microsoft Azure Storage-tjänsten i StorSimple Device Manager-tjänsten. Om den primära åtkomstnyckeln har ändrats (återskapade), Välj **primära** nyckel. Om den sekundära nyckeln har ändrats, Välj **sekundära** nyckel. Klicka på **synkronisera nyckel**.
      
      ![Synkronisera nycklar](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Du kommer att meddelas när nyckeln är har sycnhronized.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Synkronisera nycklar för storage-konton utanför tjänstprenumeration
1. På den **Services** klickar du på den **konfigurera** fliken.
2. Klicka på **Lägg till/redigera Lagringskonton**.
3. Gör följande i dialogrutan:
   
   1. Välj lagringskontot med åtkomstnyckeln som du vill uppdatera.
   2. Behöver du uppdatera lagringsåtkomstnyckel i StorSimple Device Manager-tjänsten. I det här fallet kan du se lagringsåtkomstnyckeln. Ange den nya nyckeln i den **åtkomstnyckel för Lagringskontot** box. 
   3. Spara ändringarna. Din åtkomstnyckel för lagringskontot ska nu uppdateras.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [StorSimple-säkerhet](storsimple-8000-security.md).
* Läs mer om [med StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

