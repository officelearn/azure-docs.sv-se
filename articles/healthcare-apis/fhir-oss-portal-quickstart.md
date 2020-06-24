---
title: 'Azure Portal: Distribuera FHIR-server med öppen källkod för Azure – Azure API för FHIR'
description: Den här snabb starten förklarar hur du distribuerar Microsoft Open Source FHIR-servern med hjälp av Azure Portal.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 5109c9a7c6432e42c6841b89cc28bde3e92c74aa
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "84820188"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Snabb start: Distribuera FHIR-server med öppen källkod med Azure Portal

I den här snabb starten får du lära dig hur du distribuerar en FHIR-server med öppen källkod i Azure med hjälp av Azure Portal. Vi kommer att använda enkla distributions länkar i [databasen med öppen källkod](https://github.com/Microsoft/fhir-server)

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="github-open-source-repository"></a>GitHub-lagringsplats med öppen källkod

Gå till [distributions sidan för GitHub](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) och leta upp knapparna distribuera till Azure:

![Sidan distribution av öppen källkod](media/quickstart-oss-portal/deployment-page-oss.png)

Klicka på knappen distribution och Azure Portal öppnas.

## <a name="fill-in-deployment-parameters"></a>Fyll i distributions parametrar

Välj att skapa en ny resurs grupp och ge den ett namn. Endast annan obligatorisk parameter är ett namn för tjänsten.

![Anpassade distributions parametrar](media/quickstart-oss-portal/deployment-custom-parameters.png)

Observera att distributionen kommer att hämta käll koden direkt från databasen med öppen källkod på GitHub. Om du har förgrenat lagrings platsen kan du peka på din egen för och en viss gren.

När du har fyllt i informationen kan du starta distributionen.

## <a name="validate-fhir-server-is-running"></a>Verifiera att FHIR-servern körs

När distributionen är klar kan du peka din webbläsare för `https://SERVICENAME.azurewebsites.net/metadata` att få en kapacitets instruktion. Det tar en minut eller så att servern svarar första gången.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resurs gruppen och alla relaterade resurser. Om du vill göra det väljer du den resurs grupp som innehåller de etablerade resurserna, väljer **ta bort resurs grupp**och bekräftar sedan namnet på den resurs grupp som ska tas bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du distribuerat Microsoft FHIR-server med öppen källkod för Azure i din prenumeration. Om du vill veta mer om hur du kommer åt FHIR-API: et med Postman fortsätter du till självstudien för Postman.
 
>[!div class="nextstepaction"]
>[Åtkomst till FHIR-API med Postman](access-fhir-postman-tutorial.md)