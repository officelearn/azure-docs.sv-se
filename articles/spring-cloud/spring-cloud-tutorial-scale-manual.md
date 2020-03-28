---
title: 'Självstudiekurs: Skala ett program i Azure Spring Cloud | Microsoft-dokument'
description: I den här självstudien får du lära dig hur du skalar ett program med Azure Spring Cloud i Azure-portalen
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: f08a3b5d0a03b0e898457bbb783dd5031c4b0f27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277483"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Skala ett program i Azure Spring Cloud

Den här självstudien visar hur du skalar alla mikrotjänstprogram med hjälp av Azure Spring Cloud-instrumentpanelen i Azure-portalen.

Skala upp och ned programmet genom att ändra antalet virtuella processorer (vCPUs) och mängden minne. Skala in och ut programmet genom att ändra antalet programinstanser.

När du är klar vet du hur du gör snabba manuella ändringar av varje program i tjänsten. Skalningen börjar gälla på några sekunder och kräver inga kodändringar eller omfördelningar.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 
* En distribuerad Azure Spring Cloud-tjänstinstans.  Följ vår [snabbstart när du distribuerar en app via Azure CLI](spring-cloud-quickstart-launch-app-cli.md) för att komma igång.
* Minst ett program som redan har skapats i tjänstinstansen.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Navigera till sidan Skala i Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till sidan **översikt över** Azure Spring Cloud.

1. Välj den resursgrupp som innehåller tjänsten.

1. Välj fliken **Appar** under **Inställningar** på menyn till vänster på sidan.

1. Markera det program som du vill skala. I det här exemplet väljer du programmet med namnet **kontotjänst**. Du bör då se programmets **översiktssida.**

1. Gå till fliken **Skala** under **Inställningar** i menyn till vänster på sidan. Du bör se alternativ för skalning av attribut som visas i följande avsnitt.

## <a name="scale-your-application"></a>Skala ditt program

Om du ändrar skalningsattributen bör du tänka på följande:

* **Processorer**: Det maximala antalet processorer per programinstans är fyra. Det totala antalet processorer för ett program är det värde som anges här multiplicerat med antalet programinstanser.

* **Minne/GB**: Den maximala mängden minne per programinstans är 8 GB. Den totala mängden minne för ett program är det värde som anges här multiplicerat med antalet programinstanser.

* **Antal appinstanser:** På standardnivån kan du skala ut till högst 20 instanser. Det här värdet ändrar antalet separata instanser som körs i mikrotjänstprogrammet.

Var noga med att välja **Spara** för att tillämpa skalningsinställningarna.

![Tjänsten Skala i Azure-portalen](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Efter några sekunder visas de ändringar du har gjort på sidan **Översikt,** med mer information på fliken **Programinstanser.**

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du manuellt kan skala dina Azure Spring Cloud-program. Om du vill veta hur du övervakar ditt program fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Lär dig hur du övervakar ett program](spring-cloud-tutorial-distributed-tracing.md)
