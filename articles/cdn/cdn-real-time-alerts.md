---
title: Azure CDN realtidsaviseringar | Microsoft-dokument
description: Aviseringar i realtid i Microsoft Azure CDN. Aviseringar i realtid ger meddelanden om slutpunkternas prestanda i CDN-profilen.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 4b8cbc27757cf6c321ea4b3c27720a129aa27c1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593485"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Aviseringar i realtid i Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
I det här dokumentet beskrivs aviseringar i realtid i Microsoft Azure CDN. Den här funktionen ger meddelanden i realtid om slutpunkternas prestanda i CDN-profilen.  Du kan ställa in e-post- eller HTTP-aviseringar baserat på:

* Bandbredd
* Statuskoder
* Cachestatus
* Anslutningar

## <a name="creating-a-real-time-alert"></a>Skapa en avisering i realtid
1. I [Azure-portalen](https://portal.azure.com)bläddrar du till din CDN-profil.
   
    ![CDN-profil](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. Klicka på knappen **Hantera** i CDN-profilbladet.
   
    ![Knappen Hantera CDN-profil](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
3. Håll muspekaren över fliken **Analytics** och hovra sedan över **utfällningen av statistik i realtid.**  Klicka på **realtidsvarningar**.
   
    ![CDN-hanteringsportal](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Listan över befintliga varningskonfigurationer (om sådana finns) visas.
4. Klicka på knappen **Lägg till avisering.**
   
    ![Knappen Lägg till varning](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Ett formulär för att skapa en ny avisering visas.
   
    ![Nytt aviseringsformulär](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Om du vill att den här aviseringen ska vara aktiv när du klickar på **Spara**markerar du kryssrutan **Aviseringsaktiverad.**
6. Ange ett beskrivande namn för aviseringen i fältet **Namn.**
7. Välj **HTTP Stora objekt**i listrutan **Medietyp** .
   
    ![Medietyp med HTTP Stort objekt markerat](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Du måste välja **HTTP Large Object** som **medietyp**.  De andra alternativen används inte av **Azure CDN från Verizon**.  Om du inte väljer **HTTP-stort objekt** utlöses inte aviseringen.
   > 
   > 
8. Skapa ett **uttryck** som ska övervakas genom att välja ett **mått,** **operatör**och **utlösarvärde**.
   
   * För **Mått**väljer du den typ av villkor som du vill övervaka.  **Bandbredd Mbps** är mängden bandbreddsanvändning i megabit per sekund.  **Totalt antal anslutningar** är antalet samtidiga HTTP-anslutningar till våra kantservrar.  Definitioner av de olika cachestatusarna och statuskoderna finns i [Azure CDN Cache Status Codes](/previous-versions/azure/mt759237(v=azure.100)) och Azure [CDN HTTP Status Codes](/previous-versions/azure/mt759238(v=azure.100))
   * **Operator** är den matematiska operator som upprättar relationen mellan måttet och utlösarvärdet.
   * **Utlösarvärde** är tröskelvärdet som måste uppfyllas innan ett meddelande skickas.
     
     I följande exempel anger det skapade uttrycket att ett meddelande skickas när antalet 404 statuskoder är större än 25.
     
     ![Exempeluttryck för aviseringar i realtid](./media/cdn-real-time-alerts/cdn-expression.png)
9. För **Intervall**anger du hur ofta du vill att uttrycket ska utvärderas.
10. Välj när du vill bli meddelad när uttrycket är sant i listrutan **Meddela** vid.
    
    * **Villkorsstart** anger att ett meddelande skickas när det angivna villkoret först identifieras.
    * **Villkorsslut** anger att ett meddelande skickas när det angivna villkoret inte längre identifieras. Det här meddelandet kan bara utlösas när vårt nätverk övervakningssystem upptäckt att det angivna villkoret inträffade.
    * **Kontinuerlig anger** att ett meddelande skickas varje gång nätverksövervakningssystemet upptäcker det angivna villkoret. Tänk på att nätverksövervakningssystemet bara kontrollerar ett gång per intervall efter det angivna villkoret.
    * **Villkorsstart och slut** anger att ett meddelande skickas första gången som det angivna villkoret identifieras och återigen när villkoret inte längre identifieras.
1. Om du vill få aviseringar via e-post markerar du kryssrutan **Meddela via e-post.**  
    
    ![Meddela via e-postformulär](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    I fältet **Till** anger du den e-postadress du vill ha meddelanden skickade. För **Ämne** och **Brödtext**kan du lämna standardinställningen eller anpassa meddelandet med hjälp av listan **Tillgängliga nyckelord** för att dynamiskt infoga varningsdata när meddelandet skickas.
    
    > [!NOTE]
    > Du kan testa e-postmeddelandet genom att klicka på knappen **Testa meddelanden,** men först efter att aviseringskonfigurationen har sparats.
    > 
    > 
12. Om du vill att meddelanden ska publiceras på en webbserver markerar du kryssrutan **Meddela via HTTP-inlägg.**
    
    ![Meddela via formuläret HTTP-inlägg](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    I fältet **Url** anger du den URL där du vill att HTTP-meddelandet ska publiceras. Ange de HTTP-huvuden som ska skickas i begäran i textrutan **Rubriker.**  För **Brödtext**kan du anpassa meddelandet med hjälp av listan **Tillgängliga nyckelord** för att dynamiskt infoga varningsdata när meddelandet skickas.  **Rubriker** och **brödtext** som standard till en XML-nyttolast som liknar följande exempel:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Du kan testa HTTP-postmeddelandet genom att klicka på knappen **Testa meddelanden,** men först efter att aviseringskonfigurationen har sparats.
    > 
    > 
13. Klicka på knappen **Spara** för att spara aviseringskonfigurationen.  Om du har **markerat Alert Enabled** i steg 5 är aviseringen nu aktiv.

## <a name="next-steps"></a>Efterföljande moment
* Analysera [statistik i realtid i Azure CDN](cdn-real-time-stats.md)
* Djupare med [avancerade HTTP-rapporter](cdn-advanced-http-reports.md)
* Analysera [användningsmönster](cdn-analyze-usage-patterns.md)

