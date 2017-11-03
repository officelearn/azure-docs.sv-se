---
title: "Aviseringar i realtid för Azure CDN | Microsoft Docs"
description: Aviseringar i realtid i Microsoft Azure CDN. Meddelanden om resultat av slutpunkterna i CDN-profilen med realtid aviseringar.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 51dce1680be5f5f4387c2ba02827195bcdbe9b48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Aviseringar i realtid i Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
Det här dokumentet förklarar aviseringar i realtid i Microsoft Azure CDN. Den här funktionen innehåller meddelanden i realtid om resultat av slutpunkterna i CDN-profilen.  Du kan konfigurera e-post eller HTTP-varningar baserat på:

* Bandbredd
* Statuskoder
* Cache-status
* Anslutningar

## <a name="creating-a-real-time-alert"></a>Skapa en avisering om realtid
1. I den [Azure-portalen](https://portal.azure.com), bläddra till CDN-profilen.
   
    ![CDN-profil](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. CDN-profilbladet klickar du på den **hantera** knappen.
   
    ![CDN-profilen hantera knappen](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
3. Hovra över den **Analytics** och klicka sedan hovra över den **realtid Stats** utfällbar.  Klicka på **Aviserinar**.
   
    ![CDN-hanteringsportalen](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Listan med befintliga aviseringen konfigurationer (eventuella) visas.
4. Klicka på den **Lägg till avisering** knappen.
   
    ![Aviseringen knappen Lägg till](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Ett formulär för att skapa en ny avisering visas.
   
    ![Ny avisering formulär](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Om du vill att den här aviseringen är aktiv när du klickar på **spara**, kontrollera den **avisering aktiverad** kryssrutan.
6. Ange ett beskrivande namn för aviseringen i den **namn** fältet.
7. I den **medietyp** listrutan **HTTP stort objekt**.
   
    ![Medietyp med HTTP-stort objekt som valts](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Du måste välja **HTTP stort objekt** som den **medietyp**.  De andra alternativen används inte av **Azure CDN från Verizon**.  Det gick inte att markera **HTTP stort objekt** orsakar aviseringen som aldrig utlöses.
   > 
   > 
8. Skapa en **uttryck** till övervakning genom att välja en **mått**, **operatorn**, och **utlösa värdet**.
   
   * För **mått**, Välj typ av villkor som önskade övervakade.  **Mbit/s bandbredd** avser mängden bandbreddsanvändning i megabit per sekund.  **Totalt antal anslutningar** är antalet HTTP-anslutningar till vår edge-servrar.  Definitioner av de olika cache status och statuskoder finns [Azure CDN Cache-statuskoder](https://msdn.microsoft.com/library/mt759237.aspx) och [Azure CDN HTTP-statuskoder](https://msdn.microsoft.com/library/mt759238.aspx)
   * **Operatorn** är operatorn matematiska som upprättar relationen mellan måttet och värdet för utlösare.
   * **Utlösa värdet** är tröskelvärdet som måste vara uppfyllda innan ett meddelande skickas.
     
     I följande exempel anger skapas uttrycket att en avisering skickas när antalet 404 statuskoder är större än 25.
     
     ![Realtid avisering exempeluttryck](./media/cdn-real-time-alerts/cdn-expression.png)
9. För **intervall**, ange hur ofta du vill att uttrycket utvärderas.
10. I den **meddela på** listrutan när du vill meddelas när uttrycket är uppfyllt.
    
    * **Condition-Start** anger att en avisering skickas när villkoret identifieras först.
    * **Condition-End** anger att en avisering skickas när villkoret kan inte längre hittas. Det här meddelandet kan bara aktiveras när våra nätverksövervakning systemet upptäckte att det angivna villkoret har inträffat.
    * **Kontinuerlig** anger att ett meddelande skickas varje gång att nätverksövervakning systemet identifierar det angivna villkoret. Tänk på att systemet för nätverksövervakning kontrollerar bara en gång per intervall för det angivna villkoret.
    * **Villkoret Start- och slutdatum** anger att ett meddelande skickas första gången, att det angivna villkoret har identifierats och igen när villkoret kan inte längre hittas.
1. Om du vill ta emot meddelanden via e-post, kontrollerar den **Avisera via e-post** kryssrutan.  
    
    ![Meddela via e-formulär](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    I den **till** anger du den e-postadress du där du vill att meddelanden skickas. För **ämne** och **brödtext**, kan du lämna standardinställningen och du kan anpassa meddelandet med hjälp av **tillgängliga nyckelord** listan om du vill infoga aviseringsdata dynamiskt när meddelandet har skickats.
    
    > [!NOTE]
    > Du kan testa e-postmeddelande genom att klicka på den **testmeddelande** knappen, men endast när aviseringen konfigurationen har sparats.
    > 
    > 
12. Om du vill att meddelanden ska publiceras till en webbserver i **Avisera av HTTP Post** kryssrutan.
    
    ![Meddela via HTTP Post-formulär](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    I den **Url** anger den URL som du där du vill att HTTP-meddelande skickat. I den **huvuden** textruta ange HTTP-huvuden skickas i begäran.  För **brödtext**, du kan anpassa meddelandet med hjälp av den **tillgängliga nyckelord** listan om du vill infoga aviseringsdata dynamiskt när meddelandet har skickats.  **Huvuden** och **brödtext** standard till en XML-nyttolast som liknar följande exempel:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Du kan testa HTTP Post-meddelandet genom att klicka på den **testmeddelande** knappen, men endast när aviseringen konfigurationen har sparats.
    > 
    > 
13. Klicka på den **spara** för att spara konfigurationen av aviseringen.  När du har markerat **avisering aktiverad** i steg 5 aviseringen är aktiv.

## <a name="next-steps"></a>Nästa steg
* Analysera [realtid statistik i Azure CDN](cdn-real-time-stats.md)
* Gräva djupare med [avancerade http-rapporter](cdn-advanced-http-reports.md)
* Analysera [användningsmönster](cdn-analyze-usage-patterns.md)

