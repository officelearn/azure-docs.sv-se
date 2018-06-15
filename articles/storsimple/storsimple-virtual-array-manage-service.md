---
title: Distribuera StorSimple enheten Manager-tjänsten | Microsoft Docs
description: Beskriver hur du skapar och tar bort StorSimple enheten Manager-tjänsten i Azure-portalen och beskriver hur du hanterar nyckeln för tjänstregistrering.
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
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876105"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Distribuera StorSimple enheten Manager-tjänsten för virtuell StorSimple-matris
## <a name="overview"></a>Översikt

StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera StorSimple-enheter. När du har skapat tjänsten kan du använda den för att hantera enheter från Microsoft Azure-portalen som körs i en webbläsare. På så sätt kan du övervaka alla enheter som är anslutna till StorSimple enheten Manager-tjänsten från en enda central plats, vilket minimerar administrativa belastningen.

Vanliga uppgifter som rör en StorSimple Device Manager-tjänst är:

* Skapa en tjänst
* Ta bort en tjänst
* Hämta nyckel för tjänstregistrering
* Återskapa nyckeln för tjänstregistrering

Den här självstudiekursen beskriver hur du utför alla föregående aktiviteter. Informationen i den här artikeln gäller endast virtuella StorSimple-matriser. Mer information om StorSimple 8000-serien, gå till [distribuera StorSimple Manager-tjänsten](storsimple-manage-service.md).

## <a name="create-a-service"></a>Skapa en tjänst

Om du vill skapa en tjänst måste du ha:

* En prenumeration med ett Enterprise-avtal
* Ett aktivt Microsoft Azure storage-konto
* Faktureringsinformation som används för hantering

Du kan också välja att generera ett storage-konto när du skapar tjänsten.

En enskild tjänst kan hantera flera enheter. Men en enhet kan sträcka sig över flera tjänster. Stora företag kan ha flera instanser av tjänsten ska fungera med olika prenumerationer, organisationer eller även distribution platser.

> [!NOTE]
> Du behöver separata instanser av Enhetshanteraren för StorSimple-tjänsten för att hantera StorSimple 8000-serien enheter och virtuella StorSimple-matriser.


Utför följande steg för att skapa en tjänst.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Ta bort en tjänst

Innan du tar bort en tjänst måste du kontrollera att inga anslutna enheter använder den. Om tjänsten inte används, inaktivera anslutna enheter. Åtgärden inaktivera sever anslutningen mellan enheten och tjänsten, men behålla enhetsdata i molnet.

> [!IMPORTANT]
> När en tjänst har tagits bort, kan åtgärden inte ångras. Alla enheter som använder tjänsten måste vara fabriksåterställning innan den kan användas med en annan tjänst. I det här scenariot går lokala data på enheten, samt konfigurationen, förlorade.
 

Utför följande steg för att ta bort en tjänst.

#### <a name="to-delete-a-service"></a>Ta bort en tjänst

1. Gå till **alla resurser**. Sök efter StorSimple Device Manager-tjänsten. Markera den tjänst som du vill ta bort.
   
    ![Välj tjänsten att ta bort](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Gå till instrumentpanelen tjänsten så det finns inga enheter som är ansluten till tjänsten. Om det finns inga enheter som registrerats med den här tjänsten kan visas du dessutom en Banderollmeddelande om att. Klicka på **Ta bort**.
   
    ![Ta bort tjänsten](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. När du uppmanas att bekräfta, klickar du på **Ja** i meddelande om bekräftelse. 
   
    ![Bekräfta borttagning av tjänsten](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Det kan ta några minuter för tjänsten som ska tas bort. När tjänsten har har tagits bort, du får ett meddelande.
   
    ![Lyckad tjänsten tas bort](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Listan över tjänster kommer att uppdateras.

 ![Uppdaterade listan över tjänster](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Hämta nyckel för tjänstregistrering
När du har skapat en tjänst måste registrera din StorSimple-enhet med tjänsten. Om du vill registrera din första StorSimple-enhet, måste nyckeln för tjänstregistrering. Om du vill registrera ytterligare enheter med en befintlig StorSimple-tjänst, måste både Registreringsnyckeln och den krypteringsnyckel för tjänstdata (som genereras på den första enheten under registreringen). Läs mer om krypteringsnyckeln för tjänstdata [StorSimple-säkerhet](storsimple-security.md). Du kan hämta nyckel för tjänstregistrering genom att öppna den **nycklar** bladet för din tjänst.

Utför följande steg för att hämta nyckel för tjänstregistrering.

#### <a name="to-get-the-service-registration-key"></a>Att hämta nyckel för tjänstregistrering
1. I den **StorSimple Enhetshanteraren** gå till bladet **Management &gt;**  **nycklar**.
   
   ![Bladet Nycklar](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. I den **nycklar** bladet en nyckel för tjänstregistrering visas. Kopiera registreringsnyckel med hjälp av kopiera-ikonen. 

Behåll nyckel för tjänstregistrering på en säker plats. Du behöver den här nyckeln, samt krypteringsnyckeln för tjänstdata, att registrera ytterligare enheter med den här tjänsten. När du har fått nyckeln för tjänstregistrering, behöver du konfigurera din enhet via Windows PowerShell för StorSimple-gränssnittet.

## <a name="regenerate-the-service-registration-key"></a>Återskapa nyckeln för tjänstregistrering
Du måste återskapa en nyckel för tjänstregistrering om du behöver utföra viktiga rotation eller om listan över tjänstadministratörer har ändrats. När du återskapar nyckeln används den nya nyckeln bara för att registrera följande enheter. De enheter som redan har registrerats påverkas inte av den här processen.

Utför följande steg för att återskapa en nyckel för tjänstregistrering.

#### <a name="to-regenerate-the-service-registration-key"></a>Återskapa nyckeln för tjänstregistrering
1. I den **StorSimple Enhetshanteraren** gå till bladet **Management &gt;**  **nycklar**.
   
   ![Bladet Nycklar](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. I den **nycklar** bladet, klickar du på **återskapa**.
   
   ![Klicka på Återskapa](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. I den **Generera nyckel för tjänstregistrering** bladet, granska åtgärden krävs när nycklar genereras om. Alla efterföljande enheter som registreras med tjänsten ska använda nya Registreringsnyckeln. Klicka på **återskapa** att bekräfta. Du meddelas när registreringen är klar.
   
   ![Bekräfta Generera nyckel](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. En ny nyckel för tjänstregistrering visas.
   
    ![Bekräfta Generera nyckel](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Kopiera den här nyckeln och spara den för att registrera nya enheter med den här tjänsten.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [Kom igång](storsimple-virtual-array-deploy1-portal-prep.md) med en virtuell StorSimple-matris.
* Lär dig hur du [administrera din StorSimple-enhet](storsimple-ova-web-ui-admin.md).

