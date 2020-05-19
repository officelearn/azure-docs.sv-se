---
title: Azure CDN slut punkt multi-Origin
description: Kom igång med Azure CDN slut punkt flera ursprung.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 5/8/2020
ms.author: allensu
ms.openlocfilehash: 628a284335b7285a2ee989511b130fb7d4d3cde6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597879"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN slut punkt multi-Origin

Med stöd för flera ursprung kan du upprätta global redundans och eliminera stillestånd genom att välja flera ursprung i en Azure CDN slut punkt. Redundansen som tillhandahålls av multi-Origin-risk genom att probinga hälso tillståndet för varje ursprung och redundansväxla vid behov. Konfigurera en eller flera ursprungs grupper om du vill konfigurera flera ursprung. Varje ursprungs grupp är en samling av ett eller flera ursprung som kan ta liknande arbets belastningar.

> [!NOTE]
> Den här funktionen är för närvarande endast tillgänglig från Azure CDN från Microsoft. 

## <a name="create-the-origin-group"></a>Skapa ursprungs gruppen

1. Logga in på [Azure Portal](https://portal.azure.com)

2. Välj din Azure CDN profil och välj sedan den slut punkt som ska konfigureras för flera ursprung.

3. Välj **ursprung** under **Inställningar** i slut punkts konfigurationen:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="Azure CDN flera ursprung" border="true":::

4. Om du vill aktivera flera ursprung behöver du minst en ursprungs grupp. Välj **skapa ursprungs grupp**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Azure CDN flera ursprung" border="true":::

5. I **Lägg till konfiguration av ursprungs grupp** anger eller väljer du följande information:

   | Inställningen           | Värde                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Namn på ursprungs grupp | Ange ett namn för din ursprungs grupp.                                   |
   | Sökväg för avsökning        | Sökvägen i ursprunget som används för att fastställa hälso tillståndet. |
   | Avsöknings intervall    | Välj ett avsöknings intervall på 1, 2 eller 4 minuter.                        |
   | Avsöknings protokoll    | Välj **http** eller **https**.                                         |
   | Avsöknings metod      | Välj **huvud** eller **Hämta**.                                           |
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Azure CDN flera ursprung" border="true":::

6. Välj **Lägg till**.

7. Om du vill välja standard ursprungs gruppen väljer du **Konfigurera ursprungs grupp**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-4.png" alt-text="Azure CDN flera ursprung" border="true":::

8. Välj din ursprungs grupp i den nedrullningsbara rutan och välj **OK**.

## <a name="add-multiple-origins"></a>Lägg till flera ursprung

1. I ursprungs inställningarna för din slut punkt väljer du **+ skapa ursprung**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Azure CDN flera ursprung" border="true":::

2. Ange eller Välj följande information i **Lägg till ursprung** -konfiguration:

   | Inställningen           | Värde                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Typ av ursprung | Välj **lagring**, **moln tjänst**, **webbapp**eller **anpassat ursprung**.                                   |
   | Ursprungets värdnamn        | Välj eller ange ditt ursprungs namn.  I list rutan visas alla tillgängliga ursprung av den typ som du angav i föregående inställning. Om du valde **anpassat ursprung** som typ av ursprung anger du domänen för din kund ursprungs Server. |
   | Ursprungsvärdadress    | Ange värd huvudet som du vill Azure CDN skicka med varje begäran, eller lämna standardvärdet.                        |
   | HTTP-port   | Ange HTTP-porten.                                         |
   | HTTPS-port     | Ange HTTPS-porten.                                           |
   | Prioritet    | Ange ett tal mellan 1 och 5.       |
   | Vikt      | Ange ett tal mellan 1 och 1000.   |

    > [!NOTE]
    > När ett ursprung skapas i en ursprungs grupp måste det tilldelas en prioritet och vikt. Om en ursprungs grupp endast har ett ursprung anges standard prioritet och vikt som 1. Trafiken dirigeras till de högsta prioritets ursprungen om ursprunget är felfritt. Om ett ursprung bedöms vara ohälsosamt, överförs anslutningarna till ett annat ursprung i prioritetsordning. Om två ursprung har samma prioritet, distribueras trafiken per vikt som anges för ursprunget 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Azure CDN flera ursprung" border="true":::

3. Välj **Lägg till**.

4. Välj **Konfigurera ursprung** för att ange ursprungs Sök väg för alla ursprung:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Azure CDN flera ursprung" border="true":::

5. Välj **OK**.

## <a name="configure-origins-and-origin-group-settings"></a>Konfigurera inställningar för ursprung och ursprungs grupp

När du har flera ursprung och en ursprungs grupp kan du lägga till eller ta bort ursprungen i olika grupper. Ursprung inom samma grupp bör fungera som liknande arbets belastningar. Trafiken fördelas i dessa ursprung utifrån deras friska status, prioritet och vikt värde. 

1. I ursprungs inställningarna för Azure CDN slut punkten väljer du **Konfigurera ursprungs grupp**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Azure CDN flera ursprung" border="true":::

2. Välj den ursprungs grupp som du vill konfigurera i den nedrullningsbara List rutan och välj **OK**.

3. I **gruppen uppdatera ursprungs grupp**väljer du **+ Lägg till ursprung**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Azure CDN flera ursprung" border="true":::

4. Välj det ursprung som ska läggas till i gruppen i den nedrullningsbara List rutan och välj **OK**.

5. Kontrol lera att ursprunget har lagts till i gruppen och välj sedan **Spara**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Azure CDN flera ursprung" border="true":::

## <a name="remove-origin-from-origin-group"></a>Ta bort ursprung från gruppen ursprung

1. I ursprungs inställningarna för Azure CDN slut punkten väljer du **Konfigurera ursprungs grupp**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Azure CDN flera ursprung" border="true":::

2. Välj den ursprungs grupp som du vill konfigurera i den nedrullningsbara List rutan och välj **OK**.

3. Om du vill ta bort ett ursprung från gruppen original, väljer du pappers korgs ikonen bredvid ursprunget och väljer **Spara**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Azure CDN flera ursprung" border="true":::

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du aktiverat Azure CDN slut punkt multi-Origin.

Mer information om Azure CDN och de andra Azure-tjänsterna som nämns i den här artikeln finns i:

* [Azure CDN](./cdn-overview.md)
* [Jämför Azure CDN produkt funktion](./cdn-features.md)
