---
title: Distribuera StorSimple Device Manager-tjänsten | Microsoft Docs
description: Beskriver hur du skapar och tar bort StorSimple Device Manager-tjänsten i Azure-portalen och beskriver hur du hanterar Registreringsnyckeln för tjänsten.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: alkohli
ms.openlocfilehash: 1881a0625b107ae1a90e5b772f5296a4d728973d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38688161"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Distribuera StorSimple Device Manager-tjänsten för StorSimple Virtual Array
## <a name="overview"></a>Översikt

StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera StorSimple-enheter. När du har skapat tjänsten kan du använda det för att hantera enheter i Microsoft Azure-portalen som körs i en webbläsare. På så sätt kan du övervaka alla enheter som är anslutna till StorSimple Device Manager-tjänsten från en gemensam, central plats, vilket minimerar administrativa arbetet.

Vanliga uppgifter som rör en StorSimple Device Manager-tjänsten är:

* Skapa en tjänst
* Ta bort en tjänst
* Hämta nyckel för tjänstregistrering
* Återskapa nyckel för tjänstregistrering

Den här självstudien beskrivs hur du utför alla föregående aktiviteter. Informationen i den här artikeln gäller bara för StorSimple Virtual Array. Mer information om StorSimple 8000-serien, går du till [distribuera en StorSimple Manager-tjänsten](storsimple-manage-service.md).

## <a name="create-a-service"></a>Skapa en tjänst

Om du vill skapa en tjänst, måste du ha:

* En prenumeration med ett Enterprise-avtal
* Ett aktivt Microsoft Azure storage-konto
* Faktureringsinformation som används för access management

Du kan också välja att generera ett storage-konto när du har skapat tjänsten.

En enskild tjänst kan hantera flera enheter. Men kan inte en enhet omfatta flera tjänster. Ett stort företag kan ha flera instanser av tjänsten att arbeta med olika prenumerationer, organisationer eller även distribution platser.

> [!NOTE]
> Du behöver separata instanser av StorSimple Device Manager-tjänsten för att hantera enheter i StorSimple 8000-serien och StorSimple Virtual Array.


Utför följande steg för att skapa en tjänst.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Ta bort en tjänst

Innan du tar bort en tjänst kan du kontrollera att inga anslutna enheter använder den. Om tjänsten används, inaktiverar du de anslutna enheterna. Åtgärden inaktivera Server anslutningen mellan enheten och tjänsten, men bevara enhetsdata i molnet.

> [!IMPORTANT]
> När en tjänst har tagits bort, kan inte åtgärden ångras. Alla enheter som med hjälp av tjänsten måste vara fabriksåterställning innan den kan användas med en annan tjänst. I det här scenariot går lokala data på enheten, samt konfiguration, förlorade.
 

Utför följande steg för att ta bort en tjänst.

#### <a name="to-delete-a-service"></a>Att ta bort en tjänst

1. Gå till **alla resurser**. Sök efter din StorSimple Device Manager-tjänsten. Välj den tjänst som du vill ta bort.
   
    ![Välj tjänsten för att ta bort](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Gå till instrumentpanelen så det finns inga enheter är anslutna till tjänsten. Om det finns inga enheter registrerade med den här tjänsten kan visas du dessutom en Banderollmeddelande om att. Klicka på **Ta bort**.
   
    ![Ta bort tjänst](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. När du uppmanas att bekräfta klickar du på **Ja** i meddelande om bekräftelse. 
   
    ![Bekräfta borttagning av](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Det kan ta några minuter för tjänsten som ska tas bort. När tjänsten har tagits bort, kommer du att meddelas.
   
    ![Lyckad borttagning av](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

I listan över tjänster kommer att uppdateras.

 ![Uppdaterade listan över tjänster](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Hämta nyckel för tjänstregistrering
När du har skapat en tjänst, kommer du behöva registrera din StorSimple-enhet med tjänsten. Om du vill registrera din första StorSimple-enhet, måste nyckeln för tjänstregistrering. För att registrera ytterligare enheter med en befintlig StorSimple-tjänst, behöver du både Registreringsnyckeln och tjänstdatakrypteringsnyckel (som genereras på den första enheten under registreringen). Läs mer om krypteringsnyckeln för tjänstdata [StorSimple-säkerhet](storsimple-security.md). Du kan hämta nyckel för tjänstregistrering genom att öppna den **nycklar** bladet för din tjänst.

Utför följande steg för att hämta Registreringsnyckeln för tjänsten.

#### <a name="to-get-the-service-registration-key"></a>Att hämta tjänstregistreringsnyckeln
1. I den **StorSimple Device Manager** gå till bladet **Management &gt;**  **nycklar**.
   
   ![Bladet Nycklar](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. I den **nycklar** bladet en tjänstregistreringsnyckel visas. Kopiera Registreringsnyckeln med hjälp av kopieringsikonen. 

Behåll nyckeln för tjänstregistrering på en säker plats. Du behöver den här nyckeln samt tjänstdatakrypteringsnyckel, att registrera ytterligare enheter med den här tjänsten. När du har fått nyckeln för tjänstregistrering, måste du konfigurera din enhet via Windows PowerShell för StorSimple-gränssnittet.

## <a name="regenerate-the-service-registration-key"></a>Återskapa nyckel för tjänstregistrering
Du måste återskapa en nyckel för tjänstregistrering om du behöver utföra nyckelrotation eller om listan över tjänstadministratörer har ändrats. När du återskapar en nyckel används den nya nyckeln endast för att registrera följande enheter. De enheter som redan har registrerats påverkas inte av den här processen.

Utför följande steg för att återskapa en nyckel för tjänstregistrering.

#### <a name="to-regenerate-the-service-registration-key"></a>Återskapa nyckel för tjänstregistrering
1. I den **StorSimple Device Manager** gå till bladet **Management &gt;**  **nycklar**.
   
   ![Bladet Nycklar](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. I den **nycklar** bladet klickar du på **återskapa**.
   
   ![Klicka på Återskapa](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. I den **återskapa tjänstregistreringsnyckeln** bladet, granska åtgärden krävs när nycklarna har återskapats. Alla efterföljande enheter som registreras med den här tjänsten använder den nya Registreringsnyckeln. Klicka på **återskapa** att bekräfta. Du kommer att meddelas när registreringen är klar.
   
   ![Bekräfta att återskapa nyckeln](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. En ny nyckel för tjänstregistrering visas.
   
    ![Bekräfta att återskapa nyckeln](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Kopiera den här nyckeln och spara den för att registrera nya enheter med den här tjänsten.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [börjar](storsimple-virtual-array-deploy1-portal-prep.md) med en StorSimple Virtual Array.
* Lär dig hur du [administrera din StorSimple-enhet](storsimple-ova-web-ui-admin.md).

