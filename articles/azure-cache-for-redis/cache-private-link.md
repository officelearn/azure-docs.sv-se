---
title: Azure cache för Redis med Azure Private Link (för hands version)
description: Den privata Azure-slutpunkten är ett nätverks gränssnitt som ansluter privat och säkert till Azure cache för Redis som drivs av en privat Azure-länk. I den här artikeln får du lära dig hur du skapar en Azure-cache, ett virtuellt Azure-nätverk och en privat slut punkt med hjälp av Azure Portal.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 59d5c6c4a9c3af70eb08e9d5a05d516f4b8b8bdd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100583"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Azure cache för Redis med Azure Private Link (för hands version)
Den privata Azure-slutpunkten är ett nätverks gränssnitt som ansluter privat och säkert till Azure cache för Redis som drivs av en privat Azure-länk. 

I den här artikeln får du lära dig hur du skapar en Azure-cache, ett virtuellt Azure-nätverk och en privat slut punkt med hjälp av Azure Portal.  

## <a name="prerequisites"></a>Förutsättningar
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)

> [!NOTE]
  > Den här funktionen är för närvarande i för hands version – [kontakta oss](mailto:azurecache@microsoft.com) om du är intresse rad av.
  >


## <a name="create-a-cache"></a>Skapa en cache
1. Om du vill skapa en cache loggar du in på [Azure Portal](https://portal.azure.com) och väljer **skapa en resurs**. 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Välj Skapa en resurs.":::
   
1. Välj **databaser** på sidan **nytt** och välj sedan **Azure cache för Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Välj Azure-cache för Redis.":::
   
1. På sidan **ny Redis cache** konfigurerar du inställningarna för din nya cache.
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* är * \<DNS name> . Redis.cache.Windows.net*. | 
   | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **Plats** | List rutan och välj en plats. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda din cache. |
   | **Prisnivå** | List rutan och välj en [pris nivå](https://azure.microsoft.com/pricing/details/cache/). |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Cache for Redis](cache-overview.md). |
   
1. Välj **Skapa**. 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="Skapa Azure-cache för Redis.":::
   
   Det tar en stund innan cacheminnet skulle skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång**är cacheminnet redo att användas.
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="Azure cache för Redis har skapats.":::

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät.

1. Välj **Skapa en resurs**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Välj Skapa en resurs.":::

2. Välj **nätverk** på sidan **nytt** och välj sedan **virtuellt nätverk**.

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="Skapa ett virtuellt nätverk.":::

3. I **Skapa virtuellt nätverk**anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | List rutan och välj din prenumeration.                                  |
    | Resursgrupp   | List rutan och välj en resurs grupp. |
    | **Instans information** |                                                                 |
    | Namn             | Går**\<virtual-network-name>**                                    |
    | Region           | Select**\<region-name>** |

4. Välj fliken **IP-adresser** eller Välj **Nästa: knappen Nästa: IP-adress** längst ned på sidan.

5. På fliken **IP-adresser** anger du den här informationen:

    | Inställningen            | Värde                      |
    |--------------------|----------------------------|
    | IPv4-adress utrymme | Går**\<IPv4-address-space>** |

6. Under **under näts namn**väljer du ordet **standard**.

7. I **Redigera undernät**anger du den här informationen:

    | Inställningen            | Värde                      |
    |--------------------|----------------------------|
    | Namn på undernät | Går**\<subnet-name>** |
    | Adressintervall för undernätet | Går**\<subnet-address-range>**

8. Välj **Spara**.

9. Välj fliken **Granska + skapa** eller Välj knappen **Granska + skapa** .

10. Välj **Skapa**.


## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt 

I det här avsnittet ska du skapa en privat slut punkt och ansluta den till cachen som du skapade tidigare.

1. Sök efter **privat länk** och tryck på RETUR eller Välj den från Sök förslagen.

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="Sök efter en privat länk.":::

2. Välj **privata slut punkter**på vänster sida av skärmen.

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="Välj privat länk.":::

3. Klicka på knappen **+ Lägg till** för att skapa din privata slut punkt. 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="Lägg till en privat länk.":::

4. På **sidan Skapa en privat slut punkt**konfigurerar du inställningarna för din privata slut punkt.

    | Inställningen | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | List rutan och välj din prenumeration. |
    | Resursgrupp | List rutan och välj en resurs grupp. |
    | **INSTANSINFORMATION** |  |
    | Namn |Ange ett namn för din privata slut punkt.  |
    | Region |List rutan och välj en plats. |
    |||

5. Välj **Nästa: resurs** knappen längst ned på sidan.

6. På fliken **resurs** väljer du din prenumeration, väljer resurs typ som Microsoft. cache/Redis och väljer sedan den cache som du gjorde i föregående avsnitt.

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="Resurser för privat länk.":::

7. Välj **Nästa: konfigurations** knappen längst ned på sidan.

8. På fliken **konfiguration** väljer du det virtuella nätverk och undernät som du skapade i föregående avsnitt.

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="Konfiguration för privat länk.":::

9. Välj knappen **Nästa: Taggar** längst ned på sidan.

10. På fliken **taggar** anger du namn och värde om du vill kategorisera resursen. Det här steget är valfritt.

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="Taggar för privat länk.":::

11. Välj **Granska + skapa**. Du kommer till fliken **Granska + skapa**   där Azure verifierar konfigurationen.

12. När meddelandet grön **verifiering har skickats** visas väljer du **skapa**.
