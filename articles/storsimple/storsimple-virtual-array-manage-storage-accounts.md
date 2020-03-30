---
title: Hantera lagringskontouppgifter för StorSimple Virtual Array | Microsoft-dokument
description: I artikeln beskrivs hur du kan använda sidan Konfigurera StorSimple-enhetshanteraren för att lägga till, redigera, ta bort eller rotera säkerhetsnycklarna för autentiseringsuppgifter för lagringskonto som är associerade med StorSimple Virtual Array.
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
ms.openlocfilehash: 5cedde1e7daa49aaa7a2786c9ad8a65fb8e452f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297582"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Använda StorSimple Enhetshanteraren för att hantera lagringskontoautentiseringsuppgifter för StorSimple Virtual Array

## <a name="overview"></a>Översikt
Avsnittet **Konfiguration i** tjänstbladet StorSimple Device Manager i din StorSimple Virtual Array presenterar de globala tjänstparametrar som kan skapas i Tjänsten StorSimple Manager. Dessa parametrar kan tillämpas på alla enheter som är anslutna till tjänsten och inkluderar:

* Autentiseringsuppgifter för lagringskonto
* Åtkomstkontrollposter
  
  ![Instrumentpanelen för Enhetshanterarens tjänst](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

I den här självstudien beskrivs hur du kan lägga till, redigera eller ta bort autentiseringsuppgifter för lagringskonto för den virtuella storsimple-matrisen. Informationen i den här självstudien gäller endast för StorSimple Virtual Array. Information om hur du hanterar lagringskonton i 8000-serien finns i [Använda Tjänsten StorSimple Manager för att hantera ditt lagringskonto](storsimple-manage-storage-accounts.md).

Lagringskontouppgifter innehåller de autentiseringsuppgifter som enheten använder för att komma åt ditt lagringskonto med din molntjänstleverantör. För Microsoft Azure-lagringskonton är dessa autentiseringsuppgifter som kontonamnet och den primära åtkomstnyckeln.

På **autentiseringsbladet för lagringskonto** visas alla autentiseringsuppgifter för lagringskonto som har skapats för faktureringsprenumerationen i tabellformat som innehåller följande information:

* **Namn** – Det unika namn som tilldelats kontot när det skapades.
* **SSL aktiverat** – Om TLS är aktiverat och kommunikation mellan enheter och moln är över den säkra kanalen.
  
  ![Avsnittet Konfiguration](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

De vanligaste uppgifterna som är relaterade till autentiseringsuppgifter för lagringskonto som kan utföras på **autentiseringsbladet för lagringskonto** är:

* Lägg till autentiseringsuppgift för lagringskonto
* Redigera en lagringskontoautentisering
* Ta bort en lagringskontoautentisering

## <a name="types-of-storage-account-credentials"></a>Typer av autentiseringsuppgifter för lagringskonto
Det finns tre typer av lagringskontouppgifter som kan användas med din StorSimple-enhet.

* **Autentiseringsuppgifter för automatiskt genererade lagringskonto** – Som namnet antyder genereras den här typen av lagringskontoautentisering automatiskt när tjänsten först skapas. Mer information om hur den här lagringskontoautentiseringen skapas finns i [Skapa en ny tjänst](storsimple-virtual-array-manage-service.md#create-a-service).
* **lagringskontoautentiseringsuppgifter i tjänstprenumerationen** – Det här är Azure-lagringskontoautentiseringsuppgifterna som är associerade med samma prenumeration som tjänstens. Mer information om hur dessa lagringskontouppgifter skapas finns i [Om Azure Storage Accounts](../storage/common/storage-create-storage-account.md).
* **lagringskontoautentiseringsuppgifter utanför tjänstprenumerationen** – Det här är Azure-lagringskontouppgifterna som inte är associerade med din tjänst och som troligen fanns innan tjänsten skapades.

## <a name="add-a-storage-account-credential"></a>Lägg till autentiseringsuppgift för lagringskonto
Du kan lägga till en lagringskontoautentiseringsautentisering i tjänstkonfigurationen för StorSimple Enhetshanteraren genom att ange ett unikt eget namn och åtkomstautentiseringsuppgifter som är länkade till lagringskontot. Du kan också aktivera TLS-läget (Transport Layer Security), som tidigare kallades SSL-läge (Secure Sockets Layer), för att skapa en säker kanal för nätverkskommunikation mellan enheten och molnet.

Du kan skapa flera konton för en viss molntjänstleverantör. Medan lagringskontoautentiseringsuppgifterna sparas försöker tjänsten kommunicera med molntjänstleverantören. Autentiseringsuppgifterna och åtkomstmaterialet som du har angett autentiseras just nu. En lagringskontoautentisering skapas endast om autentiseringen lyckas. Om autentiseringen misslyckas visas ett lämpligt felmeddelande.

Använd följande procedurer för att lägga till autentiseringsuppgifter för Azure-lagringskonto:

* Så här lägger du till en lagringskontoautentiseringsinformation som har samma Azure-prenumeration som Tjänsten Enhetshanteraren
* Så här lägger du till en Azure-lagringskontoautentisering som ligger utanför Tjänsten Enhetshanterarens tjänstprenumeration

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Så här lägger du till en lagringskontoautentiseringsinformation som har samma Azure-prenumeration som Tjänsten Enhetshanteraren

1. Navigera till tjänsten Enhetshanteraren, välj och dubbelklicka på den. Då öppnas **bladet Översikt.**
2. Välj **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration.**
3. Klicka på **Lägg till**.
4. Gör följande i bladet **Lägg till ett lagringskonto:**
   
    1. För **Prenumeration**väljer du **Aktuell**.
    2. Ange namnet på ditt Azure-lagringskonto.
    3. Välj **Aktivera** om du vill skapa en säker kanal för nätverkskommunikation mellan StorSimple-enheten och molnet. Välj **Inaktivera** endast om du arbetar i ett privat moln.
    4. Klicka på **Lägg till**. Du meddelas när lagringskontot har skapats.<br></br>
   
        ![Lägga till en befintlig lagringskontoautentisering](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Så här lägger du till en Azure-lagringskontoautentisering som ligger utanför Tjänsten Enhetshanterarens tjänstprenumeration

1. Navigera till tjänsten Enhetshanteraren, välj och dubbelklicka på den. Då öppnas **bladet Översikt.**
2. Välj **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration.** Här visas alla befintliga autentiseringsuppgifter för lagringskonto som är associerade med Tjänsten StorSimple Device Manager.
3. Klicka på **Lägg till**.
4. Gör följande i bladet **Lägg till ett lagringskonto:**
   
    1. För **Prenumeration**väljer du **Annat**.
   
    2. Ange namnet på din Azure-lagringskontoautentiseringsuppgifter.
   
    3. I textrutan **Åtkomst till lagringskonto** anger du den primära åtkomstnyckeln för din Azure-lagringskontoautentiseringsuppgifter. Om du vill hämta den här nyckeln går du till Azure Storage-tjänsten, väljer autentiseringsuppgifter för lagringskonto och klickar på **Hantera kontonycklar**. Nu kan du kopiera den primära åtkomstnyckeln.
   
    4. Om du vill aktivera TLS klickar du på knappen **Aktivera** för att skapa en säker kanal för nätverkskommunikation mellan Tjänsten StorSimple Device Manager och molnet. Klicka bara på knappen **Inaktivera** om du arbetar i ett privat moln.
   
    5. Klicka på **Lägg till**. Du meddelas när lagringskontoautentiseringsuppgifterna har skapats.

5. Den nyligen skapade lagringskontoautentiseringen visas på tjänstbladet Konfigurera Enhetshanteraren i StorSimple under **Lagringskontoautentiseringsuppgifter**.
   
    ![Lägga till en lagringskontoautentiseringsinformation utanför tjänstprenumerationen för Enhetshanteraren](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Redigera en lagringskontoautentisering
Du kan redigera en lagringskontoautentisering som används av enheten. Om du redigerar en lagringskontoautentisering som används för närvarande är de fält som är tillgängliga för att ändra åtkomstnyckeln och TLS-läget för lagringskontoautentiseringsuppgifterna. Du kan ange den nya åtkomstnyckeln för lagring eller ändra valen **av SSL-läge** och spara de uppdaterade inställningarna.

#### <a name="to-edit-a-storage-account-credential"></a>Så här redigerar du en lagringskontoautentisering
1. Navigera till tjänsten Enhetshanteraren, välj och dubbelklicka på den. Då öppnas **bladet Översikt.**
2. Välj **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration.** Här visas alla befintliga autentiseringsuppgifter för lagringskonto som är associerade med Tjänsten StorSimple Device Manager.
3. Markera och dubbelklicka på det konto som du vill ändra i tabelllistan över autentiseringsuppgifter för lagringskonto.
4. Gör följande i bladet **Egenskaper** för lagringskontoautentiseringsuppgifter:
   
   1. Om det behövs kan du ändra valen **Aktivera SSL-läge.**
   2. Du kan välja att återskapa åtkomstnycklarna för lagringskontots autentiseringsuppgifter. Mer information finns i [Hantera åtkomstnycklar för lagringskonto](../storage/common/storage-account-keys-manage.md). Ange den nya nyckeln för lagringskontoautentiseringsuppgifter. För ett Azure-lagringskonto är detta den primära åtkomstnyckeln.
   3. Klicka på **Spara** högst upp på egenskapsbladet **Egenskaper** för att spara inställningarna. Inställningarna uppdateras på **autentiseringsbladet för lagringskontouppgifter.**
      
      ![Redigera en lagringskontoautentisering](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Ta bort en lagringskontoautentisering
> [!IMPORTANT]
> Du kan bara ta bort en lagringskontoautentisering om den inte används. Om en lagringskontoautentiseringsautentisering används meddelas du.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Så här tar du bort en lagringskontoautentisering
1. Navigera till tjänsten Enhetshanteraren, välj och dubbelklicka på den. Då öppnas **bladet Översikt.**
2. Välj **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration.** Här visas alla befintliga autentiseringsuppgifter för lagringskonto som är associerade med Tjänsten StorSimple Device Manager.
3. Markera och dubbelklicka på det konto som du vill ta bort i tabelllistan över autentiseringsuppgifter för lagringskonto.
4. Gör följande i bladet **Egenskaper** för lagringskontoautentiseringsuppgifter:
   
   1. Klicka på **Ta bort** om du vill ta bort autentiseringsuppgifterna.
   2. När du uppmanas att bekräfta klickar du på **Ja** för att fortsätta med borttagningen. Tabelllistan uppdateras för att återspegla ändringarna.
      
      ![Ta bort en lagringskontoautentisering](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synkronisera autentiseringsuppgifter för lagringskonto
Av säkerhetsskäl är nyckelrotation ofta ett krav i datacenter. En Microsoft Azure-administratör kan återskapa eller ändra den primära eller sekundära nyckeln genom att direkt komma åt lagringskontoautentiseringsuppgifterna (via Microsoft Azure Storage-tjänsten). Tjänsten StorSimple Device Manager ser inte den här ändringen automatiskt.

Om du vill informera StorSimple Device Manager-tjänsten om ändringen måste du komma åt Tjänsten StorSimple Device Manager, komma åt autentiseringsuppgifterna för lagringskontot och sedan synkronisera den primära eller sekundära nyckeln (beroende på vilken som har ändrats). Tjänsten får sedan den senaste nyckeln, krypterar nycklarna och skickar den krypterade nyckeln till enheten.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Så här synkroniserar du nycklar för lagringskontoautentiseringsuppgifter i samma prenumeration som tjänsten (endast Azure)
1. På tjänstens landningsblad väljer du din tjänst, dubbelklickar på tjänstnamnet och klickar sedan på **Autentiseringsuppgifter för lagringskonto**i avsnittet **Konfiguration** .
2. I **Storage account credentials** listan över lagringskontoautentiseringsuppgifter i listan över lagringskontoautentiseringsuppgifter väljer du den lagringskontoautentiseringsautentisering vars nycklar som du vill synkronisera.
3. Gör följande i **egenskapsbladet** för den valda lagringskontoautentiseringen:
   
    1. Klicka på **Mer**och sedan på **Synkronisera åtkomstnyckel**.
   
    2. När du uppmanas att bekräfta klickar du på **Synkroniseringsnyckel** för att slutföra synkroniseringen.
    
4. I Tjänsten StorSimple Device Manager måste du uppdatera nyckeln som tidigare ändrats i Microsoft Azure Storage-tjänsten. Om nyckelbladet för det primära åtkomstkommandot har ändrats (återskapats) i **nyckelbladet Synkronisera lagringskonto,** klickar du på Primär och sedan på **Synkroniseringsnyckel**. Om den sekundära nyckeln har ändrats klickar du på **Sekundär**och sedan på **Synkronisera nyckel**.
   
    ![Åtkomstnyckel för synkronisering](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [administrerar din Virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

