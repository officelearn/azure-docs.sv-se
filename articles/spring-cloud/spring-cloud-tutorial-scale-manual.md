---
title: 'Självstudie: skala ett program i Azure våren Cloud | Microsoft Docs'
description: I den här självstudien får du lära dig hur du skalar ett program med Azure våren Cloud i Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: f08a3b5d0a03b0e898457bbb783dd5031c4b0f27
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "76277483"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Skala ett program i Azure våren Cloud

Den här självstudien visar hur du skalar ett mikrotjänstprogram med hjälp av moln instrument panelen för Azure våren i Azure Portal.

Skala ditt program uppåt och nedåt genom att ändra antalet virtuella processorer (virtuella processorer) och mängden minne. Skala ditt program i och ut genom att ändra antalet program instanser.

När du är klar vet du hur du ska göra snabba manuella ändringar i varje program i din tjänst. Skalningen börjar gälla om några sekunder och kräver inga kod ändringar eller omdistributioner.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 
* En distribuerad Azure våren Cloud Service-instans.  Följ vår [snabb start för att distribuera en app via Azure CLI](spring-cloud-quickstart-launch-app-cli.md) för att komma igång.
* Minst ett program som redan har skapats i din tjänst instans.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Gå till sidan skala i Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till **översikts** sidan för Azure våren-molnet.

1. Välj den resurs grupp som innehåller din tjänst.

1. Välj fliken **appar** under **Inställningar** på menyn på vänster sida av sidan.

1. Välj det program som du vill skala. I det här exemplet väljer du appen med namnet **Account-service**. Du bör sedan se programmets **översikts** sida.

1. Gå till fliken **Scale (skala** ) under **Inställningar** på menyn på vänster sida av sidan. Du bör se alternativ för skalning av de attribut som visas i följande avsnitt.

## <a name="scale-your-application"></a>Skala ditt program

Tänk på följande när du ändrar skalnings attributen:

* **Processorer**: det maximala antalet processorer per program instans är fyra. Det totala antalet processorer för ett program är det värde som anges här multiplicerat med antalet program instanser.

* **Minne/GB**: den maximala mängden minne per program instans är 8 GB. Den totala mängden minne för ett program är det värde som anges här multiplicerat med antalet program instanser.

* **Antal instanser av app**: på standard-nivån kan du skala ut till maximalt 20 instanser. Det här värdet ändrar antalet separata instanser av mikrotjänst programmet som körs.

Se till att välja **Spara** för att tillämpa skalnings inställningarna.

![Skalnings tjänsten i Azure Portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Efter några sekunder visas ändringarna du gjort på sidan **Översikt** med mer information på fliken **program instanser** . skalning kräver inte kod ändringar eller omdistribution.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du manuellt skalar dina Azure våren Cloud-program. Fortsätt till nästa självstudie om du vill veta mer om hur du övervakar ditt program.

> [!div class="nextstepaction"]
> [Lär dig hur du övervakar ett program](spring-cloud-tutorial-distributed-tracing.md)
