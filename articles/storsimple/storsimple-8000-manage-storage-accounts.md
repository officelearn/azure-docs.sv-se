---
title: Hantera lagringskontouppgifter, StorSimple 8000-seriens enhet
description: I artikeln beskrivs hur du kan använda sidan Konfigurera StorSimple-enhetshanteraren för att lägga till, redigera, ta bort eller rotera säkerhetsnycklarna för ett lagringskonto.
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
ms.openlocfilehash: 65aa83731be97b59a36a5baf60ea308690a0dcf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297746"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Använda Tjänsten StorSimple Device Manager för att hantera dina lagringskontouppgifter

## <a name="overview"></a>Översikt

Avsnittet **Konfiguration** i tjänstbladet StorSimple Device Manager visar alla globala tjänstparametrar som kan skapas i Tjänsten StorSimple Device Manager. Dessa parametrar kan tillämpas på alla enheter som är anslutna till tjänsten och inkluderar:

* Autentiseringsuppgifter för lagringskonto
* Bandbreddsmallar 
* Åtkomstkontrollposter 

I den här självstudien beskrivs hur du lägger till, redigerar eller tar bort autentiseringsuppgifter för lagringskontot eller roterar säkerhetsnycklarna för ett lagringskonto.

 ![Lista över autentiseringsuppgifter för lagringskonton](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Lagringskonton innehåller de autentiseringsuppgifter som StorSimple-enheten använder för att komma åt ditt lagringskonto med din molntjänstleverantör. För Microsoft Azure-lagringskonton är dessa autentiseringsuppgifter som kontonamnet och den primära åtkomstnyckeln. 

På **autentiseringsbladet för lagringskonton** visas alla lagringskonton som skapas för faktureringsprenumerationen i tabellformat som innehåller följande information:

* **Namn** – Det unika namn som tilldelats kontot när det skapades.
* **SSL aktiverat** – Om TLS är aktiverat och kommunikation mellan enheter och moln är över den säkra kanalen.
* **Används av** – Antalet volymer som använder lagringskontot.

De vanligaste uppgifterna som är relaterade till lagringskonton som kan utföras är:

* Lägga till ett lagringskonto 
* Redigera ett lagringskonto 
* Ta bort ett lagringskonto 
* Nyckelrotation av lagringskonton 

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

Det finns tre typer av lagringskonton som kan användas med din StorSimple-enhet.

* **Automatiskt genererade lagringskonton** – Som namnet antyder genereras den här typen av lagringskonto automatiskt när tjänsten först skapas. Mer information om hur det här lagringskontot skapas finns i [steg 1: Skapa en ny tjänst](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) i Distribuera din lokala [StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md). 
* **Lagringskonton i tjänstprenumerationen** – Det här är Azure-lagringskonton som är associerade med samma prenumeration som tjänstens. Mer information om hur dessa lagringskonton skapas finns i [Om Azure Storage Accounts](../storage/common/storage-create-storage-account.md). 
* **Lagringskonton utanför tjänstprenumerationen** – Det här är Azure-lagringskonton som inte är associerade med din tjänst och som troligen fanns innan tjänsten skapades.

## <a name="add-a-storage-account"></a>Lägga till ett lagringskonto

Du kan lägga till ett lagringskonto genom att ange ett unikt eget namn och åtkomstautentiseringsuppgifter som är länkade till lagringskontot (med den angivna molntjänstleverantören). Du kan också aktivera TLS-läget (Transport Layer Security), som tidigare kallades SSL-läge (Secure Sockets Layer), för att skapa en säker kanal för nätverkskommunikation mellan enheten och molnet.

Du kan skapa flera konton för en viss molntjänstleverantör. Tänk dock på att när ett lagringskonto har skapats kan du inte ändra molntjänstleverantören.

Medan lagringskontot sparas försöker tjänsten kommunicera med din molntjänstleverantör. Autentiseringsuppgifterna och åtkomstmaterialet som du har angett autentiseras just nu. Ett lagringskonto skapas endast om autentiseringen lyckas. Om autentiseringen misslyckas visas ett lämpligt felmeddelande.

Använd följande procedurer för att lägga till autentiseringsuppgifter för Azure-lagringskonto:

* Så här lägger du till en lagringskontoautentiseringsinformation som har samma Azure-prenumeration som Tjänsten Enhetshanteraren
* Så här lägger du till en Azure-lagringskontoautentisering som ligger utanför Tjänsten Enhetshanterarens tjänstprenumeration

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Så här lägger du till en Azure-lagringskontoautentisering utanför StorSimple Device Manager-tjänstprenumerationen

1. Navigera till tjänsten StorSimple Device Manager, välj och dubbelklicka på den. Då öppnas **bladet Översikt.**
2. Välj **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration.** Här visas alla befintliga autentiseringsuppgifter för lagringskonto som är associerade med Tjänsten StorSimple Device Manager.
3. Klicka på **Lägg till**.
4. Gör följande i **bladet Lägg till ett lagringskonto:**
   
    1. För **Prenumeration**väljer du **Annat**.
   
    2. Ange namnet på din Azure-lagringskontoautentiseringsuppgifter.
   
    3. I textrutan **Åtkomst till lagringskonto** anger du den primära åtkomstnyckeln för din Azure-lagringskontoautentiseringsuppgifter. Om du vill hämta den här nyckeln går du till Azure Storage-tjänsten, väljer autentiseringsuppgifter för lagringskonto och klickar på **Hantera kontonycklar**. Nu kan du kopiera den primära åtkomstnyckeln.
   
    4. Om du vill aktivera TLS klickar du på knappen **Aktivera** för att skapa en säker kanal för nätverkskommunikation mellan Tjänsten StorSimple Device Manager och molnet. Klicka bara på knappen **Inaktivera** om du arbetar i ett privat moln.
   
    5. Klicka på **Lägg till**. Du meddelas när lagringskontoautentiseringsuppgifterna har skapats.

5. Den nyligen skapade lagringskontoautentiseringen visas på tjänstbladet Konfigurera Enhetshanteraren i StorSimple under **Lagringskontoautentiseringsuppgifter**.
   


## <a name="edit-a-storage-account"></a>Redigera ett lagringskonto

Du kan redigera ett lagringskonto som används av en volymbehållare. Om du redigerar ett lagringskonto som används för närvarande är det enda fält som är tillgängligt för att ändra åtkomstnyckeln för lagringskontot. Du kan ange den nya åtkomstnyckeln för lagring och spara de uppdaterade inställningarna.

#### <a name="to-edit-a-storage-account"></a>Så här redigerar du ett lagringskonto

1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.

    ![Autentiseringsuppgifter för lagringskonto](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. Välj och klicka på den som du vill redigera i listan över lagringskontouppgifter i bladet **Lagringskontouppgifter.** 

3. Du kan ändra alternativet **Aktivera SSL.** Du kan också klicka på **Mer...** och sedan välja **Länkknapp för Synkronisera för att rotera** åtkomstnycklarna för lagringskontot. Gå till [Nyckelrotation av lagringskonton](#key-rotation-of-storage-accounts) för mer information om hur du utför nyckelrotation. När du har ändrat inställningarna klickar du på **Spara**. 

    ![Spara redigerade autentiseringsuppgifter för lagringskonto](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. 

    ![Bekräfta ändringar](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Inställningarna uppdateras och sparas för ditt lagringskonto. 

## <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto

> [!IMPORTANT]
> Du kan bara ta bort ett lagringskonto om det inte används av en volymbehållare. Om ett lagringskonto används av en volymbehållare tar du först bort volymbehållaren och tar sedan bort det associerade lagringskontot.

#### <a name="to-delete-a-storage-account"></a>Så här tar du bort ett lagringskonto

1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.

2. Hovra över kontot som du vill ta bort i tabelllistan över lagringskonton. Högerklicka om du vill anropa snabbmenyn och klicka på **Ta bort**.

    ![Ta bort autentiseringsuppgifter för lagringskonto](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. När du uppmanas att bekräfta klickar du på **Ja** för att fortsätta med borttagningen. Tabelllistan uppdateras för att återspegla ändringarna.

    ![Bekräfta borttagning](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Nyckelrotation av lagringskonton

Av säkerhetsskäl är nyckelrotation ofta ett krav i datacenter. Varje Microsoft Azure-prenumeration kan ha ett eller flera associerade lagringskonton. Åtkomsten till dessa konton styrs av prenumerations- och åtkomstnycklarna för varje lagringskonto. 

När du skapar ett lagringskonto genererar Microsoft Azure två 512-bitars lagringsåtkomstnycklar som används för autentisering när lagringskontot används. Med två lagringsåtkomstnycklar kan du återskapa nycklarna utan avbrott i din lagringstjänst eller åtkomst till den tjänsten. Nyckeln som för närvarande används *primary* är primärnyckeln och säkerhetskopieringsnyckeln kallas *sekundärnyckel.* En av dessa två nycklar måste anges när din Microsoft Azure StorSimple-enhet kommer åt din molnlagringstjänstleverantör.

## <a name="what-is-key-rotation"></a>Vad är nyckelrotation?

Vanligtvis använder program bara en av nycklarna för att komma åt dina data. Efter en viss tid kan du låta dina program växla över till att använda den andra nyckeln. När du har växlat dina program till den sekundära nyckeln kan du dra tillbaka den första nyckeln och sedan generera en ny nyckel. Genom att använda de två nycklarna på det här sättet kan dina program komma åt data utan att drabbas av några driftstopp.

Lagringskontonycklarna lagras alltid i tjänsten i krypterad form. Dessa kan dock återställas via StorSimple Device Manager-tjänsten. Tjänsten kan hämta primärnyckeln och den sekundära nyckeln för alla lagringskonton i samma prenumeration, inklusive konton som skapats i lagringstjänsten samt standardlagringskonton som genererades när Tjänsten StorSimple Device Manager var först Skapad. StorSimple Device Manager-tjänsten hämtar alltid dessa nycklar från den klassiska Azure-portalen och lagrar dem sedan på ett krypterat sätt.

## <a name="rotation-workflow"></a>Arbetsflöde för rotation

En Microsoft Azure-administratör kan återskapa eller ändra den primära eller sekundära nyckeln genom att direkt komma åt lagringskontot (via Microsoft Azure Storage-tjänsten). Tjänsten StorSimple Device Manager ser inte den här ändringen automatiskt.

Om du vill informera StorSimple Device Manager-tjänsten om ändringen måste du komma åt Tjänsten StorSimple Device Manager, komma åt lagringskontot och sedan synkronisera den primära eller sekundära nyckeln (beroende på vilken som har ändrats). Tjänsten får sedan den senaste nyckeln, krypterar nycklarna och skickar den krypterade nyckeln till enheten.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Så här synkroniserar du nycklar för lagringskonton i samma prenumeration som tjänsten 
1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.
2. Klicka på den som du vill ändra i tabelllistan över lagringskonton. 

    ![synkronisera tangenter](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Klicka på **... Mer** och välj sedan **Länkknapp för Synkronisera åtkomst för att rotera**.   

    ![synkronisera tangenter](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. I Tjänsten StorSimple Device Manager måste du uppdatera nyckeln som tidigare ändrats i Microsoft Azure Storage-tjänsten. Om den primära åtkomstnyckeln har ändrats (återskapats) väljer du **primärnyckel.** Om den sekundära nyckeln ändrades väljer du **sekundärnyckel.** Klicka på **Synkroniseringsnyckel**.
      
      ![synkronisera tangenter](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Du meddelas när nyckeln har synkroniserats.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Så här synkroniserar du nycklar för lagringskonton utanför tjänstprenumerationen
1. Klicka på fliken **Konfigurera** på sidan **Tjänster.**
2. Klicka på **Lägg till/redigera lagringskonton**.
3. Gör följande i dialogrutan:
   
   1. Välj lagringskontot med den åtkomstnyckel som du vill uppdatera.
   2. Du måste uppdatera lagringsåtkomstnyckeln i StorSimple Device Manager-tjänsten. I det här fallet kan du se åtkomstnyckeln för lagring. Ange den nya nyckeln i rutan **Åtkomstnyckel för lagringskonto.** 
   3. Spara ändringarna. Åtkomstnyckeln för ditt lagringskonto bör nu uppdateras.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [StorSimple-säkerhet](storsimple-8000-security.md).
* Läs mer om [hur du använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

