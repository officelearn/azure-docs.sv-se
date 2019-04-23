---
title: Azure CDN aviseringar i realtid | Microsoft Docs
description: Aviseringar i realtid i Microsoft Azure CDN. Aviseringar i realtid ger aviseringar om prestanda för slutpunkter i din CDN-profil.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e20161147aa16456e31aff2bd3cc6337c3690e89
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789931"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Aviseringar i realtid i Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
Det här dokumentet beskriver aviseringar i realtid i Microsoft Azure CDN. Den här funktionen ger aviseringar i realtid om prestanda för slutpunkter i din CDN-profil.  Du kan ställa in e-post eller HTTP-aviseringar baserat på:

* Bandbredd
* Statuskoder
* Status för cachelagring
* Anslutningar

## <a name="creating-a-real-time-alert"></a>Skapa en avisering om i realtid
1. I den [Azure-portalen](https://portal.azure.com), bläddra till din CDN-profil.
   
    ![CDN-profil](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. Från bladet för CDN-profil klickar du på den **hantera** knappen.
   
    ![CDN-profil hantera knappen](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
3. Hovra över den **Analytics** och sedan hovra över den **statistik i realtid** utfällbar meny.  Klicka på **aviseringar i realtid**.
   
    ![CDN-hanteringsportalen](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Listan över befintliga aviseringen konfigurationer (i förekommande fall) visas.
4. Klicka på den **Lägg signal** knappen.
   
    ![Lägg till avisering knapp](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Ett formulär för att skapa en ny avisering visas.
   
    ![Nytt avisering formulär](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Om du vill att den här aviseringen ska aktiveras när du klickar på **spara**, kontrollera den **avisering aktiverad** kryssrutan.
6. Ange ett beskrivande namn för aviseringen i den **namn** fält.
7. I den **medietyp** listrutan **LOB-HTTP-**.
   
    ![Medietyp med HTTP stora objekt som valts](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Du måste välja **LOB-HTTP-** som den **medietyp**.  De andra alternativen används inte av **Azure CDN från Verizon**.  Det gick inte att välja **LOB-HTTP-** orsakar aviseringen aldrig utlöses.
   > 
   > 
8. Skapa en **uttryck** att övervaka genom att välja en **mått**, **operatorn**, och **utlösa värdet**.
   
   * För **mått**, Välj typ av villkor som du vill att övervakade.  **Mbit/s bandbredd** avser mängden användning av nätverksbandbredd i megabit per sekund.  **Totalt antal anslutningar** är antalet samtidiga HTTP-anslutningar till vår edge-servrar.  Definitioner av de olika för cachelagring och statuskoder finns [statuskoder för Azure CDN-cacheminnet](/previous-versions/azure/mt759237(v=azure.100)) och [Azure CDN HTTP-statuskoder](/previous-versions/azure/mt759238(v=azure.100))
   * **Operatorn** är den matematiska operatör som upprättar relationen mellan måttet och värdet för utlösaren.
   * **Utlösa värdet** är tröskelvärdet som måste uppfyllas innan en avisering skickas.
     
     I följande exempel anger uttrycket som skapade att skickas ett meddelande när antalet 404 statuskoder är större än 25.
     
     ![I realtid aviseringsexempel uttryck](./media/cdn-real-time-alerts/cdn-expression.png)
9. För **intervall**, ange hur ofta du vill att uttrycket utvärderas.
10. I den **meddela på** listrutan när du vill meddelas när uttrycket är sant.
    
    * **Ange ett villkor för Start** anger att ett meddelande skickas när villkoret först har identifierats.
    * **Ange ett villkor för End** anger att ett meddelande skickas när villkoret kan inte längre hittas. Det här meddelandet kan bara aktiveras när våra system för nätverksövervakning upptäckte att det angivna villkoret uppstod.
    * **Kontinuerlig** anger att ett meddelande skickas varje gång att system för nätverksövervakning identifierar det angivna villkoret. Tänk på att systemet för nätverksövervakning kontrollerar bara en gång per intervall för det angivna villkoret.
    * **Villkoret Start- och** anger att ett meddelande skickas första gången, att den angivna villkoren har identifierats och igen när villkoret inte längre har upptäckts.
1. Om du vill ta emot meddelanden via e-post, kontrollera den **meddela via e-post** kryssrutan.  
    
    ![Meddela via e-formulär](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    I den **till** fältet, anger du den e-postadress som du var du vill att meddelanden skickas. För **ämne** och **brödtext**, kan du lämna standardvärdet eller du kan anpassa meddelandet med hjälp av den **tillgängliga nyckelord** listan för att dynamiskt infoga aviseringsdata när meddelandet skickas.
    
    > [!NOTE]
    > Du kan testa e-postmeddelande genom att klicka på den **testmeddelande** knappen, men endast när aviseringskonfigurationen har sparats.
    > 
    > 
12. Om du vill att meddelanden ska publiceras till en webbserver, kontrollerar du den **meddela av HTTP Post** kryssrutan.
    
    ![Meddela via HTTP Post-formulär](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    I den **Url** fältet, anger du URL: en du där du vill att HTTP-meddelande har publicerats. I den **rubriker** textrutan Ange HTTP-huvuden skickas i begäran.  För **brödtext**, du kan anpassa meddelandet med hjälp av den **tillgängliga nyckelord** listan om du vill infoga aviseringsdata dynamiskt när meddelandet har skickats.  **Rubriker** och **brödtext** standard till en XML-nyttolast som liknar följande exempel:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Du kan testa HTTP Post-meddelande genom att klicka på den **testmeddelande** knappen, men endast när aviseringskonfigurationen har sparats.
    > 
    > 
13. Klicka på den **spara** för att spara din aviseringskonfigurationen.  När du har markerat **avisering aktiverad** i steg 5, aviseringen är aktiv.

## <a name="next-steps"></a>Nästa steg
* Analysera [realtidsstatistik i Azure CDN](cdn-real-time-stats.md)
* Gräv djupare med [avancerade HTTP-rapporter](cdn-advanced-http-reports.md)
* Analysera [användningsmönster](cdn-analyze-usage-patterns.md)

