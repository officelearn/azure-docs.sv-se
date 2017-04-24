---
title: "Ansluta en webbapp till ett nätverk för innehållsleverans | Microsoft Docs"
description: "Anslut en webbapp till ett nätverk för innehållsleverans för att leverera statiska filer från kantnoder."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 8194e669f943a6c47f02ae0d2a55e0e720420489
ms.lasthandoff: 04/18/2017

---
# <a name="connect-a-web-app-to-a-content-delivery-network"></a>Ansluta en webbapp till ett nätverk för innehållsleverans

I den här kursen får du skapa en Azure CDN-profil och en Azure CDN-slutpunkt för att hantera statiska filer från webbappen via Azure CDN POP-platser.

> [!TIP]
> Se en uppdaterad lista över [Azure CDN POP-platser](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="step-1---login-to-azure-portal"></a>Steg 1 – Logga in på Azure Portal

Öppna webbläsaren och gå till Azure [Portal](https://portal.azure.com).

## <a name="step-2---create-a-cdn-profile"></a>Steg 2 – Skapa en CDN-profil

Klicka på knappen **+ Ny** i det vänstra navigeringsfältet och klicka på **Webb + mobilt**. Välj **CDN** under kategorin Webb + mobilt.

Ange **namn**, **plats**, **resursgrupp**, **prisnivå** och klicka sedan på **Skapa**.

Öppna resursgruppshubben i det vänstra navigeringsfältet och välj **myResourceGroup**. Välj **myCDNProfile** i resurslistan.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Steg 3 – Skapa en CDN-slutpunkt

Klicka på **+ Slutpunkt** bland kommandona bredvid sökrutan så att bladet för att skapa slutpunkten öppnas.

Ange **namn**, **typ av ursprung**, **ursprungets värdnamn** och klicka sedan på **Lägg till**.

Slutpunkten skapas. När CDN-slutpunkten har skapats uppdateras statusen till **körs**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---leveraging-cdn"></a>Steg 4 – Utnyttja CDN

Nu när slutpunkten körs kan vi kontrollera att innehållet är tillgängligt på POP-servern genom att gå till en statisk fil på webbservern och ändra värdnamnet från `http://<app_name>.azurewebsites.net/path/to-static-file` till `http://<endpoint_name>.azureedge.net/path/to-static-file`.

![app-service-web-url-to-cdn-endpoint-url](media/app-service-web-tutorial-content-delivery-network/app-service-web-url-to-cdn-endpoint-url.png)

## <a name="next-steps"></a>Nästa steg


