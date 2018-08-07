---
title: Så här förbereder du för en IP SSL-adressändring - Azure
description: Om din SSL IP-adress ska ändras, lär du dig vad du gör så att din app fortsätter att fungera efter ändringen.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.openlocfilehash: e8558b4c3c7dafca8d4fff7e2aae0597a66c031d
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576550"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Så här förbereder du för en ändring för SSL IP-adress

Om du har fått ett meddelande om att SSL-IP-adressen för din Azure App Service-app ändras, följer du anvisningarna i den här artikeln för att släppa befintlig SSL IP-adress och tilldela en ny.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Viktig SSL-IP-adresser och tilldela nya

1.  Öppna [Azure-portalen](https://portal.azure.com).

2.  Välj i den vänstra navigeringsmenyn **Apptjänster**.

3.  Välj din App Service-app i listan.

4.  Under den **inställningar** rubrik, klickar du på **SSL-inställningar** i det vänstra navigeringsfönstret.

5. Välj namn värdpost i avsnittet SSL-bindningar. I redigeraren som öppnas, Välj **SNI SSL** på den **SSL-typ** nedrullningsbara menyn och klicka på **Lägg till bindning för**. När du ser ett meddelande för åtgärden, har den befintliga IP-adressen släppts.

6.  I den **SSL-bindningar** igen väljer du samma värd-namnpost med certifikatet. I redigeringsprogrammet som öppnas väljer du den här gången **IP-baserad SSL** på den **SSL-typ** nedrullningsbara menyn och klicka på **Lägg till bindning för**. När du ser ett meddelande för åtgärden, har du skaffat en ny IP-adress.

7.  Om en A-post (DNS-post som pekar direkt på din IP-adress) har konfigurerats i din domän Registreringsportalen (från tredje part DNS-leverantör eller Azure DNS), Ersätt den befintliga IP-adressen med den nyligen skapade. Du hittar den nya IP-adressen genom att följa anvisningarna i nästa avsnitt.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Hitta nya SSL-IP-adressen i Azure Portal

1.  Vänta några minuter och öppna sedan den [Azure-portalen](https://portal.azure.com).

2.  Välj i den vänstra navigeringsmenyn **Apptjänster**.

3.  Välj din App Service-app i listan.

4.  Under den **inställningar** rubrik, klickar du på **egenskaper** i det vänstra navigeringsfönstret och hitta avsnittet märkta **virtuell IP-adress**.

5. Kopiera IP-adressen och konfigurera om din domän eller de IP-mekanism.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs hur du förbereder en ändring av IP-adress som initierades av Azure. Mer information om IP-adresser i Azure App Service finns i [SSL och SSL-IP-adresser i Azure App Service](app-service-ip-addresses.md).
