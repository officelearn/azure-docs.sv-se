---
title: Så här förbereder du för en inkommande IP-adressändring - Azure
description: Om din inkommande IP-adress ska ändras, lär du dig vad du gör så att din app fortsätter att fungera efter ändringen.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.openlocfilehash: 28741e858b0c938ec8b2b2ff983106c6b08e18fc
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578226"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Så här förbereder du för en inkommande IP-adressändring

Om du har fått ett meddelande om att inkommande IP-adressen för din Azure App Service-app ändras, följer du anvisningarna i den här artikeln.

## <a name="determine-if-you-have-to-do-anything"></a>Avgöra om du behöver göra något

* Alternativ 1: Om App Service-appen inte har en anpassad domän, ingen åtgärd krävs.

* Alternativ 2: om det bara en CNAME-post (DNS-post som pekar på en URI) har konfigurerats i din domän Registreringsportalen (från tredje part DNS-leverantör eller Azure DNS), ingen åtgärd krävs.

* Alternativ 3: Om en A-post (DNS-post som pekar direkt på din IP-adress) har konfigurerats i din domän Registreringsportalen (från tredje part DNS-leverantör eller Azure DNS), Ersätt den befintliga IP-adressen med den nya. Du hittar den nya IP-adressen genom att följa anvisningarna i nästa avsnitt.

* Alternativ 4: Om ditt program är bakom en belastningsutjämnare, IP-Filter eller andra IP-mekanism som kräver appens IP-adress Ersätt den befintliga IP-adressen med den nya. Du hittar den nya IP-adressen genom att följa anvisningarna i nästa avsnitt.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Hitta nya inkommande IP-adressen i Azure portal

Ny inkommande IP-adressen som ges till din app är på portalen i den **virtuell IP-adress** fält. Både den nya IP-adressen och den gamla servern är ansluten till din app nu och senare den gamla servern kommer att kopplas från.

1.  Öppna [Azure-portalen](https://portal.azure.com).

2.  Välj i den vänstra navigeringsmenyn **Apptjänster**.

3.  Välj din App Service-app i listan.

4.  Om appen är en funktionsapp, se [funktionsapp inkommande IP-adress](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  Under den **inställningar** rubrik, klickar du på **egenskaper** i det vänstra navigeringsfönstret och hitta avsnittet märkta **virtuell IP-adress**.

5. Kopiera IP-adressen och konfigurera om din domän eller de IP-mekanism.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs hur du förbereder en ändring av IP-adress som initierades av Azure. Mer information om IP-adresser i Azure App Service finns i [inkommande och utgående IP-adresser i Azure App Service](app-service-ip-addresses.md).
