---
title: 'Snabb start: interaktiv kart sökning med Azure Maps | Microsoft Azure Maps'
description: Lär dig hur du skapar ett demonstrations webb program för interaktiv kart sökning genom att använda Microsoft Azure Maps-webbsdk.
author: farah-alyasari
ms.author: v-faalya
ms.date: 1/14/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 74539a7ed74d0216f1a4bb2d719983895d8b320f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359731"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>Snabb start: skapa en interaktiv Sök karta genom att använda Azure Maps

Den här artikeln visar funktionerna i Azure Maps för att skapa en karta som ger användarna en interaktiv sökupplevelse. Vi går igenom de grundläggande stegen:
* Skapa ditt eget Azure Maps-konto.
* Hämta din kontonyckel, som du ska använda i demonstrationswebbappen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Skapa ett konto med Azure Maps

Skapa ett nytt Maps-konto med följande steg:

1. Klicka på [Skapa en resurs](https://portal.azure.com) längst upp till vänster i **Azure Portal**.
2. Skriv *Maps* i rutan **Sök på Marketplace**.
3. Bland *resultaten* väljer du **Maps**. Klicka på knappen **Skapa** som visas nedanför kartan.
4. Ange följande värden på sidan **Skapa Maps-konto**:
    * Den *Prenumeration* som ska användas för det här kontot.
    * Namnet på *Resursgrupp* för kontot. Du kan välja att *skapa ny* eller *använda befintlig* resursgrupp.
    * *Namn* för ditt nya konto.
    * *Pris nivån* för det här kontot.
    * Läs *licensen* och *sekretesspolicyn* och markera kryssrutan för att godkänna villkoren.
    * Klicka på knappen **Skapa**.

![Skapa Maps-konto i portalen](./media/quick-demo-map-app/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Hitta primärnyckeln för ditt konto

När ditt Maps-konto har skapats hämtar du nyckeln som gör att du kan fråga API:et Kartkontroll. Vi rekommenderar att du använder ditt kontos primär nyckel som prenumerations nyckel när du anropar Azure Maps Services.

1. Öppna ditt Maps-konto i portalen.
2. I avsnittet Inställningar väljer du **autentisering**.
3. Kopiera **Primärnyckel** till Urklipp. Spara den lokalt för senare användning i den här självstudien.

![Hämta primär nyckel Azure Maps nyckel i Azure Portal](./media/quick-demo-map-app/get-key.png)

## <a name="download-the-application"></a>Hämta programmet

1. Gå till [interactiveSearch. html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) och klicka på den för att visa innehållet i GitHub-användargränssnittet. Högerklicka på den **råa** knappen och kopiera innehållet i filen eller Spara som för att ladda ned filen.
2. Spara innehållet i den här filen lokalt som **AzureMapDemo.html**. Öppna den i en textredigerare.
3. Sök efter strängen `<Your Azure Maps Key>`. Ersätt den med **primärnyckelvärdet** från föregående avsnitt.

## <a name="open-the-application"></a>Öppna programmet

1. Öppna filen **AzureMapDemo.html** i en webbläsare.
2. Observera kartan som visas för staden Los Angeles. Zooma in och ut om du vill se hur kartan automatiskt återges med mer eller mindre information beroende på zoomnivån. 
3. Ändra kartans standardmittpunkt. I filen **AzureMapDemo.html** söker du efter variabeln med namnet **center**. Ersätt variabelns parvärde för longitud, latitud med de nya värdena **[-74.0060, 40.7128]** . Spara filen och uppdatera webbläsaren.
4. Prova den interaktiva sökupplevelsen. I sökrutan i det övre vänstra hörnet i demowebbappen söker du efter **restauranger**.
5. Flytta musen över listan med adresser och platser som visas under sökrutan. Observera hur motsvarande nål på kartan visar information om den platsen. Observera att namnen och adresserna som visas är fiktiva för att skydda privata företag.

    ![Webb program för interaktiv kart sökning](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Rensa resurser

Självstudierna förklarar hur du använder och konfigurerar Azure Maps med ditt konto. Rensa inte resurserna som du skapar i den här snabbstarten om du planerar att fortsätta med självstudierna. Om du inte planerar att fortsätta rensar du resurserna genom att utföra dessa steg:

1. Stäng webbläsaren som kör webbprogrammet **AzureMapDemo.html**.
2. Välj **Alla resurser** på menyn längst till vänster på Azure-portalen. Välj sedan ditt Azure Maps-konto. Välj **Ta bort** överst på bladet **Alla resurser**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ditt Azure Maps-konto och en demonstrationsapp. Ta en titt på följande självstudier om du vill lära dig mer om Azure Maps:

> [!div class="nextstepaction"]
> [Söka efter orienteringspunkter i närheten med hjälp av Azure Maps](tutorial-search-location.md)

Fler kodexempel och en interaktiv kodningsupplevelse finns i dessa guider:

> [!div class="nextstepaction"]
> [Hitta en adress med hjälp av söktjänsten i Azure Maps](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Använda kartkontrollen i Azure Maps](how-to-use-map-control.md)
