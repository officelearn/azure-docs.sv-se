---
title: "Interaktiv kartsökning med Azure Location Based Services | Microsoft Docs"
description: "Snabbstart för Azure – Starta en interaktiv kartsökning som demonstration med Azure Location Based Services (förhandsversion)"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: quickstart
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 0edca9e5eb709eb3e1b42b45e5a41ac7a5ba1e75
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="launch-a-demo-interactive-map-search-using-azure-location-based-services-preview"></a>Starta en interaktiv kartsökning som demonstration med Azure Location Based Services (förhandsversion)

Den här artikeln visar funktionerna i Azure Location Based Services (förhandsversion) eller det kortare LBS, i en interaktiv sökning med hjälp av Azure Maps. Här beskrivs också de grundläggande stegen för att skapa ditt eget LBS-konto och hur du får kontots nyckeln att använda i webbappen i demonstrationen. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-location-based-services-account-and-get-account-key"></a>Skapa ett Location Based Services-konto och hämta kontonyckeln

1. Klicka på **Skapa en resurs** längst upp till vänster i [Azure Portal](https://portal.azure.com).
2. Skriv **location based services** i rutan *Search the Marketplace* (sök på marketplace).
3. Klicka på **Location Based Services (förhandsversion)** i *Resultat*. Klicka på knappen **Skapa** som visas nedanför kartan. 
4. På sidan **Skapa Location Based Services-konto** anger du *Namn* för ditt nya konto, väljer vilken *Prenumeration* som ska användas och anger namnet på en ny eller befintlig  *Resursgrupp*. Välj plats för resursgruppen, acceptera *Förhandsversionsvillkoren* och klicka på **skapa**.

    ![Skapa Location Based Services-konto i portalen](./media/quick-demo-map-app/create-lbs-account.png)

5. Öppna kontot när det har skapats och navigera till kontots **INSTÄLLNINGAR**. Klicka på **Nycklar** för att hämta primära och sekundära prenumerationsnycklar för ditt konto. Kopiera värdet för **Primär nyckel** till din lokala Urklipp för användning i följande avsnitt. 

## <a name="download-the-demo-application-for-azure-maps"></a>Ladda ned demonstrationsprogrammet för Azure Maps

1. Ladda ned eller kopiera innehållet i filen [interactiveSearch.html](https://github.com/Azure-Samples/location-based-services-samples/blob/master/src/interactiveSearch.html).
2. Spara innehållet i filen lokalt som **AzureMapDemo.html** och öppna den i ett textredigeringsprogram.
3. Sök efter strängen **&#60;insert-key&#62;** och ersätt den med värdet för **Primär nyckel** som hämtades i föregående avsnitt. 


## <a name="launch-the-demo-application-for-azure-maps"></a>Starta demonstrationsprogrammet för Azure Maps

1. Öppna filen **AzureMapDemo.html** i en webbläsare.
2. Observera att kartan visar staden Los Angeles. Staden bestäms av värdet för `[longitude, latitude]`-paret som anges för JavaScript-variabeln med namnet **center** i *AzureMapDemo.html*. Du kan ändra dessa koordinater till en annan stad. Koordinaterna för New York City är till exempel *[-74.0060, 40.7128]*.
3. Ange eventuell platstyp eller en adress som du vill söka efter i sökrutan i det övre vänstra hörnet av demonstrationswebbappen. 
4. Flytta musen över listan med adresser/platser som visas under sökrutan och observera hur motsvarande nål på kartan visar information om den platsen. Till exempel får vi följande om vi startar webbappen och söker efter *restaurants*. Observera att för skydda privata företag visas fiktiva namn och adresser här. 

    ![Interaktiv sökning i webbapp](./media/quick-demo-map-app/lbs-interactive-search.png)


## <a name="clean-up-resources"></a>Rensa resurser

Självstudierna visar i detalj hur du använder och konfigurerar Azure Location Based Services för ditt konto. Om du planerar att fortsätta arbeta med självstudierna ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten.

1. Stäng webbläsaren där webbappen **AzureMapDemo.html** körs.
2. Klicka på **Alla resurser** på menyn till vänster på Azure Portal och välj sedan ditt LBS-konto. Klicka på **Ta bort** överst på bladet **Alla resurser**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett Azure LBS-konto och startat en demonstrationsapp med ditt konto. Om du vill lära dig hur du skapar egna program med API:erna för Azure Location Based Services kan du fortsätta med nästa självstudie.

> [!div class="nextstepaction"]
> [Självstudiekurs om att använda Azure Map och Search](./tutorial-search-location.md)
