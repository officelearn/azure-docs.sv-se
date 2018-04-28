---
title: Lägga till en Java-program i Azure App Service Web Apps
description: Den här kursen visar hur du lägger till en sida eller ett program till din Azure App Service Web Apps som redan är konfigurerad för att använda Java-instans.
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: mbaldwin
ms.assetid: 9b46528b-e2d0-4f26-b8d7-af94bd8c31ef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: 71009370282fcfbd71c00b30d4ea22802035714d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Lägga till en Java-program i Azure App Service Web Apps
När du har initierat Java-webbappen i [Azure App Service] [ Azure App Service] som beskrivs i [skapar en Java-webbapp i Azure App Service](app-service-web-get-started-java.md), kan du överföra ditt program genom att placera din WAR i den **webbappar** mapp.

Navigeringssökvägen till den **webbappar** mappen skiljer sig åt beroende på hur du konfigurerar din Web Apps-instans.

* Om du har lagt upp ditt webbprogram med hjälp av Azure Marketplace sökvägen till den **webbappar** mappen är i formatet **d:\home\site\wwwroot\bin\application\_server\webapps**, där **programmet\_server** är namnet på programservern gäller för Web Apps-instans. 
* Om du har lagt upp ditt webbprogram med hjälp av Azure Konfigurationsgränssnittet, sökvägen till den **webbappar** mappen är i formatet **d:\home\site\wwwroot\webapps**. 

Observera att du kan använda källkontrollen för att överföra programmet eller webbsidor, inklusive [kontinuerlig integrationsscenarier](app-service-continuous-deployment.md). FTP är också ett alternativ för uppladdning av ditt program eller en webbsida. Mer information om hur du distribuerar ditt program via FTP finns [distribuera din app använder FTP](app-service-deploy-ftp.md).

Obs för Tomcat web apps: när du har överfört WAR-filen till den **webbappar** mappen Tomcat-programservern känner av att du har lagt till den och kommer automatiskt att läsa in den. Observera att om du kopierar filer (andra än WAR-filer) till rotkatalogen programservern måste startas om innan de filerna som används. Funktionen autoload för Tomcat Java-webbprogram som körs på Azure baseras på en ny WAR-fil som läggs till, eller lägga till nya filer eller kataloger i **webbappar** mapp. 

<a name="see-also"></a>

## <a name="see-also"></a>Se även
Mer information om hur du använder Java i Azure finns i [Java-utvecklingscenter].

[Application-Insights-App-Insights-Java-Get-Started](../application-insights/app-insights-java-get-started.md)

<!-- URL List -->

[Java-utvecklingscenter]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
