---
title: Distribuera Tjänsten StorSimple Device Manager | Microsoft-dokument
description: I artikeln beskrivs hur du skapar och tar bort Tjänsten StorSimple Device Manager i Azure-portalen och beskriver hur du hanterar nyckeln till tjänstregistrering.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 2ff987448e3900c2a533789b2f38fd3ced71ce72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267461"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Distribuera Tjänsten StorSimple Device Manager för StorSimple Virtual Array

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Tjänsten StorSimple Device Manager körs i Microsoft Azure och ansluter till flera StorSimple-enheter. När du har skapat tjänsten kan du använda den för att hantera enheter från Microsoft Azure-portalen som körs i en webbläsare. På så sätt kan du övervaka alla enheter som är anslutna till Tjänsten StorSimple Device Manager från en enda central plats och därigenom minimera den administrativa bördan.

De vanliga uppgifterna som är relaterade till en StorSimple Enhetshanteraren-tjänst är:

* Skapa en tjänst
* Ta bort en tjänst
* Hämta nyckel för tjänstregistrering
* Återskapa nyckeln för tjänstregistrering

I den här självstudien beskrivs hur du utför var och en av de föregående uppgifterna. Informationen i den här artikeln gäller endast StorSimple Virtual Arrays. Mer information om StorSimple 8000-serien finns i [distribuera en StorSimple Manager-tjänst](storsimple-manage-service.md).

## <a name="create-a-service"></a>Skapa en tjänst

Om du vill skapa en tjänst måste du ha:

* En prenumeration med ett enterprise-avtal
* Ett aktivt Microsoft Azure-lagringskonto
* Faktureringsinformationen som används för åtkomsthantering

Du kan också välja att generera ett lagringskonto när du skapar tjänsten.

En enda tjänst kan hantera flera enheter. En enhet kan dock inte sträcka sig över flera tjänster. Ett stort företag kan ha flera tjänstinstanser för att arbeta med olika prenumerationer, organisationer eller till och med distributionsplatser.

> [!NOTE]
> Du behöver separata instanser av StorSimple Device Manager-tjänsten för att hantera StorSimple 8000-serieenheter och StorSimple Virtual Arrays.


Gör följande för att skapa en tjänst.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Ta bort en tjänst

Innan du tar bort en tjänst kontrollerar du att inga anslutna enheter använder den. Om tjänsten används inaktiverar du de anslutna enheterna. Avaktiveringsåtgärden avbryter anslutningen mellan enheten och tjänsten, men bevarar enhetsdata i molnet.

> [!IMPORTANT]
> När en tjänst har tagits bort kan åtgärden inte återföras. Alla enheter som använde tjänsten måste fabriksåterställas innan den kan användas med en annan tjänst. I det här fallet kommer de lokala data på enheten, liksom konfigurationen, att gå förlorade.
 

Gör följande för att ta bort en tjänst.

#### <a name="to-delete-a-service"></a>Så här tar du bort en tjänst

1. Gå till **Alla resurser**. Sök efter tjänsten StorSimple Device Manager. Välj den tjänst som du vill ta bort.
   
    ![Välj tjänst som ska tas bort](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Gå till instrumentpanelen för tjänsten för att säkerställa att det inte finns några enheter anslutna till tjänsten. Om det inte finns några enheter registrerade med den här tjänsten visas även ett bannermeddelande om detta. Klicka på **Ta bort**.
   
    ![Ta bort tjänsten](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Klicka på **Ja** i bekräftelsemeddelandet när du uppmanas att bekräfta. 
   
    ![Bekräfta borttagning av tjänsten](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Det kan ta några minuter innan tjänsten tas bort. När tjänsten har tagits bort meddelas du.
   
    ![Borttagning av tjänsten har slutförts](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Listan över tjänster uppdateras.

 ![Uppdaterad lista över tjänster](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Hämta nyckel för tjänstregistrering
När du har skapat en tjänst måste du registrera din StorSimple-enhet med tjänsten. För att registrera din första StorSimple-enhet behöver du tjänstens registreringsnyckel. Om du vill registrera ytterligare enheter med en befintlig StorSimple-tjänst behöver du både registreringsnyckeln och krypteringsnyckeln för tjänstdata (som genereras på den första enheten under registreringen). Mer information om krypteringsnyckeln för tjänstdata finns i [StorSimple security](storsimple-security.md). Du kan få registreringsnyckeln genom att öppna **bladet Nycklar** för din tjänst.

Utför följande steg för att hämta nyckeln för tjänstregistrering.

#### <a name="to-get-the-service-registration-key"></a>Så här hämtar du nyckeln för tjänstregistrering
1. Gå till **Hanteringsnycklar**i **bladet StorSimple** **Enhetshanteraren &gt; ** .
   
   ![Bladet Nycklar](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. I bladet **Nycklar** visas en tjänstregistreringsnyckel. Kopiera registreringsnyckeln med hjälp av kopieringsikonen. 

Förvara tjänstens registreringsnyckel på en säker plats. Du behöver den här nyckeln, liksom krypteringsnyckeln för tjänstdata, för att registrera ytterligare enheter med den här tjänsten. När du har hämtat tjänsten registreringsnyckeln måste du konfigurera enheten via Windows PowerShell för StorSimple-gränssnittet.

## <a name="regenerate-the-service-registration-key"></a>Återskapa nyckeln för tjänstregistrering
Du måste återskapa en tjänstregistreringsnyckel om du måste utföra nyckelrotation eller om listan över tjänstadministratörer har ändrats. När du återskapar nyckeln används den nya nyckeln endast för att registrera efterföljande enheter. De enheter som redan har registrerats påverkas inte av den här processen.

Utför följande steg för att återskapa en tjänstregistreringsnyckel.

#### <a name="to-regenerate-the-service-registration-key"></a>Så här återskapar du tjänstregistreringsnyckeln
1. Gå till **Hanteringsnycklar**i **bladet StorSimple** **Enhetshanteraren &gt; ** .
   
   ![Bladet Nycklar](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Klicka på **Återskapa**i bladet **Nycklar.**
   
   ![Klicka på återskapa](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. I **bladet Regenerera tjänstregistreringsnyckel** granskar du den åtgärd som krävs när nycklarna återskapas. Alla efterföljande enheter som är registrerade hos den här tjänsten använder den nya registreringsnyckeln. Klicka på **Återskapa** för att bekräfta. Du kommer att meddelas när registreringen är klar.
   
   ![Bekräfta återskapa nyckel](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. En ny tjänstregistreringsnyckel visas.
   
    ![Bekräfta återskapa nyckel](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Kopiera den här nyckeln och spara den för att registrera nya enheter med den här tjänsten.

## <a name="next-steps"></a>Nästa steg
* Läs om hur du [kommer igång](storsimple-virtual-array-deploy1-portal-prep.md) med en StorSimple Virtual Array.
* Läs om hur du [administrerar din StorSimple-enhet](storsimple-ova-web-ui-admin.md).

