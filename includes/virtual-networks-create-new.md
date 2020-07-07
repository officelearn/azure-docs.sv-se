---
title: inkludera fil
description: inkludera fil
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "78244984"
---
## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs > nätverk > virtuellt nätverk** eller söker efter **virtuellt nätverk** i sökrutan.

2. I **Skapa virtuellt nätverk**anger eller väljer du den här informationen på fliken **grundläggande** :

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt information**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **Skapa ny**, ange **\<resource-group-name>** och välj sedan OK, eller Välj en befintlig **\<resource-group-name>** baserad på parametrar. |
    | **Instans information** |                                                                 |
    | Name             | Går**\<virtual-network-name>**                                    |
    | Region           | Select**\<region-name>** |

3. Välj fliken **IP-adresser** eller Välj **Nästa: knappen Nästa: IP-adress** längst ned på sidan.

4. På fliken **IP-adresser** anger du den här informationen:

    | Inställningen            | Värde                      |
    |--------------------|----------------------------|
    | IPv4-adress utrymme | Går**\<IPv4-address-space>** |

5. Under **under näts namn**väljer du ordet **standard**.

6. I **Redigera undernät**anger du den här informationen:

    | Inställningen            | Värde                      |
    |--------------------|----------------------------|
    | Namn på undernät | Går**\<subnet-name>** |
    | Adressintervall för undernätet | Går**\<subnet-address-range>**

7. Välj **Spara**.

8. Välj fliken **Granska + skapa** eller Välj knappen **Granska + skapa** .

9. Välj **Skapa**.