---
title: 'Självstudier: Skala ett program i Azure våren-molnet | Microsoft Docs'
description: I den här självstudien får du lära dig hur du skalar ett program i Azure våren Cloud på Azure Portal
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 10/06/2019
ms.openlocfilehash: bca88cac45e1ba8117eb4e10141e32d621434b86
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038911"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Självstudier: Skala ett program i Azure våren Cloud

Den här självstudien visar hur du skalar ett mikrotjänstprogram med hjälp av moln instrument panelen för Azure våren i Azure Portal. Skala ditt program uppåt och nedåt genom att ändra antalet virtuella processorer (virtuella processorer) och mängden minne. Skala ditt program i och ut genom att ändra antalet instanser av programmet. När du är klar vet du hur du ska göra snabba manuella ändringar i varje program i din tjänst. Skalningen börjar gälla om några sekunder och kräver inga kod ändringar eller omdistributioner.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
* En distribuerad Azure våren Cloud Service-instans.  Följ vår [snabb start](spring-cloud-quickstart-launch-app-cli.md) för att komma igång.
* Minst ett program som redan har skapats i den tjänst instansen.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Gå till sidan skala i Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till **översikts** sidan för Azure våren-molnet.

1. Gå till fliken **appar** under rubriken **Inställningar** på menyn på vänster sida.

1. Välj det program som du vill skala. I det här exemplet ska vi skala programmet med namnet "Account-service". Detta bör gå till programmets **översikts** sida.

1. Gå till fliken **Scale (skala** ) under rubriken **Inställningar** på menyn på vänster sida. Du bör se ett formulär med rader för var och en av de skalnings attribut som vi nämnde tidigare.

## <a name="scale-your-application"></a>Skala ditt program

Du kan ändra skalnings attributen. Behåll följande anteckningar i åtanke.

* **Processorer**: Det maximala antalet processorer som tillåts är 4 per program instans. Det totala antalet processorer för ett program blir det värde som anges här multiplicerat med antalet program instanser.

* **Minne/GB**: Högsta mängd minne som tillåts är 8 GB per program instans.  Den totala mängden minne för ett program blir det värde som anges här multiplicerat med antalet program instanser.

* **Antal instanser**: Du kan skala upp till 20 instanser på standard nivån. Det här värdet ändrar antalet separata instanser av mikrotjänst programmet som körs.

Se till att klicka på knappen **Spara** för att tillämpa skalnings inställningarna.

Efter några sekunder visas ändringarna du gjort på sidan **Översikt** med mer information som är tillgänglig på fliken **program instanser** . Skalning kräver inte kod ändringar eller omdistribution.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du manuellt skalar dina Azure våren Cloud-program.  Fortsätt till nästa självstudie om du vill veta mer om hur du övervakar ditt program.

> [!div class="nextstepaction"]
> [Lär dig hur du övervakar ditt program](spring-cloud-tutorial-distributed-tracing.md)
