---
title: Diagnostisera fel - Azure Logic Apps | Microsoft Docs
description: "Vanliga sätt att förstå där logikappar misslyckas"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 814e6f93088cdd96b0a663d2a7494b5a11470d99
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="diagnose-logic-app-failures"></a>Diagnostisera logik app fel
Om det uppstår problem eller fel med logic apps kan det inte finns några metoder kan hjälpa dig att bättre förstå var felen kommer från.  

## <a name="azure-portal-tools"></a>Azure portal-verktyg
Azure-portalen innehåller många verktyg för att diagnostisera varje logikapp i varje steg.

### <a name="trigger-history"></a>Utlösaren historik

Varje logikapp har minst en utlösare. Om du märker att appar inte startar, leta i utlösaren historiken för mer information. Du kan komma åt logik app'ss huvudbladet utlösaren historik.

![Hitta trigger-historik][1]

Utlösaren historiken visas alla utlösare försök som görs i din logikapp. Du kan klicka på varje utlösare försök att visa detaljer, särskilt, några indata eller utdata som genererats av utlösare försöket. Om du hittar misslyckade utlösare Markera försöket utlösare och välj den **utdata** länken för att granska alla genererade felmeddelanden, till exempel för FTP-autentiseringsuppgifter som inte är giltigt.

De olika statuslägen som kan visas är:

* **Hoppade över**. Slutpunkten har avsökts om du vill söka efter data och tog emot ett svar som att det fanns inga data.
* **Lyckades**. Utlösaren tog emot ett svar var data tillgängliga. Den här statusen kan bero på en manuell utlösare, en upprepning utlösare eller en avsökning utlösare. Denna status är oftast den **Fired** status, men kanske inte om du har ett villkor eller SplitOn kommandot i kodvy som inte uppfylls.
* **Det gick inte**. Ett fel har genererats.

#### <a name="start-a-trigger-manually"></a>Starta en utlösare manuellt

Om du vill att söka efter en tillgänglig utlösare omedelbart utan att vänta på nästa återkommande logikappen **Välj utlösare** på huvudbladet att tvinga en kontroll. Till exempel gör klickar på länken med en Dropbox-utlösare att arbetsflödet att genast söka Dropbox för nya filer.

### <a name="run-history"></a>Kör historik

Varje Eldad utlösare resulterar i en körning. Du kan komma åt information om körning från huvudblad som innehåller många detaljer som hjälper dig att förstå vad som hände under arbetsflödet.

![Hitta körningshistoriken][2]

Körs visas något av följande status:

* **Lyckades**. Alla åtgärder har slutförts. Om ett fel uppstod har felet hanterats av en åtgärd som inträffat senare i arbetsflödet. Det vill säga hanterades felet av en åtgärd som har angetts ska köras efter en misslyckad åtgärd.
* **Det gick inte**. Minst en åtgärd hade fel som inte hanterades av en åtgärd senare i arbetsflödet.
* **Avbröt**. Arbetsflödet kördes, men tog emot en begäran om avbrytning.
* **Kör**. Arbetsflödet körs. Den här statusen kan uppstå för begränsad arbetsflöden eller på grund av den aktuella prisavtal. Mer information finns i [åtgärd gränser för prissättningssidan](https://azure.microsoft.com/pricing/details/app-service/plans/). Konfigurera diagnostik (diagrammen som visas under körningshistoriken) kan också ge information om begränsning av händelser som inträffar.

När du tittar på ett körningshistorik detaljer mer information.  

#### <a name="trigger-outputs"></a>Utlösaren utdata

Utlösaren utdata visar data som kommer från utlösaren. Dessa utdata kan hjälpa dig att avgöra om alla egenskaper returneras som förväntat.

> [!NOTE]
> Om du ser allt innehåll som du inte förstår lär du dig hur Azure Logic Apps [hanterar olika typer av innehåll](../logic-apps/logic-apps-content-type.md).
> 

![Utlösaren utdata-exempel][3]

#### <a name="action-inputs-and-outputs"></a>Åtgärden indata och utdata

Du kan gå till in- och utgångar som en åtgärd togs emot. Informationen är användbar för att förstå storlek och form av utdata och för att söka efter eventuella felmeddelanden som kan ha genererats.

![Åtgärden indata och utdata][4]

## <a name="debug-workflow-runtime"></a>Felsöka arbetsflödets körtid

Du kan lägga till några steg i ett arbetsflöde som hjälp med felsökning tillsammans med övervakning indata, utdata och utlösare av en körning. 
[RequestBin](http://requestb.in) är ett kraftfullt verktyg som du kan lägga till som ett steg i ett arbetsflöde. Genom att använda RequestBin kan ställa du in en HTTP-begäran inspector att fastställa exakt storlek, form och format för en HTTP-begäran. Du kan skapa en RequestBin och klistra in Webbadressen i en logikapp HTTP POST-åtgärd med brödtextinnehåll som du vill testa, till exempel, ett uttryck eller en annan steg utdata. När du kör logikappen kan uppdatera du din RequestBin om du vill se hur begäran bildades när genererats från Logic Apps-motorn.

<!-- image references -->
[1]: ./media/logic-apps-diagnosing-failures/triggerhistory.png
[2]: ./media/logic-apps-diagnosing-failures/runhistory.png
[3]: ./media/logic-apps-diagnosing-failures/triggeroutputslink.png
[4]: ./media/logic-apps-diagnosing-failures/actionoutputs.png
