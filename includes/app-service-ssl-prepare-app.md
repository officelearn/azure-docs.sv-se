---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3a44dc35c27cd084e6589482f180b13b258f0b24
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470973"
---
## <a name="prepare-your-web-app"></a>Förbereda din webbapp

Om du vill binda ett anpassat SSL-certifikat (ett tredjepartscertifikat eller App Service-certifikat) till webbappen måste din [App Service plan](https://azure.microsoft.com/pricing/details/app-service/) ha nivån **Basic**, **Standard**, **Premium** eller **Isolerad**. I det här steget ser du till att webbappen har en prisnivå som stöds.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna [Azure-portalen](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navigera till din webbapp

Sök efter och välj **app Services**.

![Välj App Services](./media/app-service-ssl-prepare-app/app-services.png)

På sidan **app Services** väljer du namnet på din Azure-App.

![Portalnavigering till Azure-app](./media/app-service-ssl-prepare-app/select-app.png)

Du har kommit till hanteringssidan för din webbapp.  

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

I det vänstra navigeringsfältet på webbappsidan bläddrar du till avsnittet **Inställningar** och väljer **Skala upp (App Service-plan)** .

![Skala upp-menyn](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Kontrollera att webbappen inte är på nivån **F1** eller **D1**. Appens aktuella nivå markeras med en mörkblå ruta.

![Kontrollera prisnivå](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Anpassat SSL stöds inte på nivån **F1** eller **D1**. Om du behöver skala upp följer du stegen i nästa avsnitt. I annat fall stängs sidan **Skala upp** och hoppar över avsnittet [Skala upp App Service-planen](#scale-up-your-app-service-plan).

### <a name="scale-up-your-app-service-plan"></a>Skala upp App Service-planen

Välj någon av betalnivåerna (**B1**, **B2**, **B3** eller någon nivå i kategorin **Produktion**). Klicka på **Visa ytterligare alternativ** om du vill se fler alternativ.

Klicka på **Verkställ**.

![Välja prisnivå](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

När du ser följande meddelande har skalningsåtgärden slutförts.

![Uppskalningsmeddelande](./media/app-service-ssl-prepare-app/scale-notification.png)

