---
title: Interaktiv kartsökning med Azure Maps | Microsoft Docs
description: Azure-snabbstart – Skapa en demonstration av en interaktiv kartsökning med Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 3482a5516e0a044716ac38e7a60a9a8484f73245
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056222"
---
# <a name="create-an-interactive-search-map-by-using-azure-maps"></a>Skapa en interaktiv sökkarta med hjälp av Azure Maps

Den här artikeln visar funktionerna i Azure Maps för att skapa en karta som ger användarna en interaktiv sökupplevelse. Vi går igenom de grundläggande stegen:
* Skapa ditt eget Azure Maps-konto.
* Hämta din kontonyckel, som du ska använda i demonstrationswebbappen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-an-account-and-get-your-key"></a>Skapa ett konto och hämta din nyckel

1. Välj **Skapa en resurs** i det övre vänstra hörnet av [Azure-portalen](https://portal.azure.com).
2. Ange **Maps** i rutan **Sök på Marketplace**.
3. Bland **resultaten** väljer du **Maps**. Välj knappen **Skapa** som visas nedanför kartan.
4. Ange följande värden på sidan **Skapa Azure Maps-konto**:
   - **Namn** för ditt nya konto.
   - Den **Prenumeration** som ska användas för det här kontot.
   - **Resursgrupp** för kontot. Du kan välja att **skapa en ny** eller att **använda en befintlig** resursgrupp.
   - Välj önskad **prisnivå**.
   - Läs **licensinformationen** och **sekretesspolicyn**. Markera kryssrutan för att godkänna villkoren.
   - Välj sedan knappen **Skapa**.

     ![Skapa ett Azure Maps-konto på portalen](./media/quick-demo-map-app/create-account.png)

5. När kontot har skapats öppnar du det och går till inställningarna på kontomenyn. Välj **Nycklar** för att granska de primära och sekundära prenumerationsnycklarna för ditt Azure Maps-konto. Kopiera värdet för **Primär nyckel** till din lokala Urklipp för användning i följande avsnitt.

## <a name="download-the-application"></a>Hämta programmet

1. Ladda ned eller kopiera innehållet i filen [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html).
2. Spara innehållet i den här filen lokalt som **AzureMapDemo.html**. Öppna den i en textredigerare.
3. Sök efter strängen `<Your Azure Maps Key>`. Ersätt den med **primärnyckelvärdet** från föregående avsnitt.

## <a name="open-the-application"></a>Öppna programmet

1. Öppna filen **AzureMapDemo.html** i en webbläsare.
2. Observera kartan som visas för staden Los Angeles. Zooma in och ut om du vill se hur kartan automatiskt återges med mer eller mindre information beroende på zoomnivån. 
3. Ändra kartans standardmittpunkt. I filen **AzureMapDemo.html** söker du efter variabeln med namnet **center**. Ersätt variabelns parvärde för longitud, latitud med de nya värdena **[-74.0060, 40.7128]**. Spara filen och uppdatera webbläsaren.
4. Prova den interaktiva sökupplevelsen. I sökrutan i det övre vänstra hörnet i demowebbappen söker du efter **restauranger**.
5. Flytta musen över listan med adresser och platser som visas under sökrutan. Observera hur motsvarande nål på kartan visar information om den platsen. Observera att namnen och adresserna som visas är fiktiva för att skydda privata företag.

    ![Interaktiv sökning i webbapp](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Rensa resurser

Självstudierna förklarar hur du använder och konfigurerar Azure Maps med ditt konto. Rensa inte resurserna som du skapar i den här snabbstarten om du planerar att fortsätta med självstudierna. Om du inte planerar att fortsätta rensar du resurserna genom att utföra dessa steg:

1. Stäng webbläsaren som kör webbprogrammet **AzureMapDemo.html**.
2. Välj **Alla resurser** på menyn längst till vänster på Azure-portalen. Välj sedan ditt Azure Maps-konto. Välj **Ta bort** överst på bladet **Alla resurser**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ditt Azure Maps-konto och en demonstrationsapp. Ta en titt på följande självstudiekurser och lär dig mer om Azure Maps:

> [!div class="nextstepaction"]
> [Söka efter Orienteringspunkter i närheten med hjälp av Azure Maps](./tutorial-search-location.md)

Fler kodexempel och en interaktiv kodningsupplevelse finns i dessa guider:

> [!div class="nextstepaction"]
> [Hitta en adress med hjälp av Azure Maps search-tjänsten](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Använda Kartkontrollen Azure Maps](./how-to-use-map-control.md)
