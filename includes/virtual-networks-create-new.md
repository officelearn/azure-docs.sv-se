---
title: ta med fil
description: ta med fil
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
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
    | Resursgrupp   | Välj **Skapa ny**, ange ** \<resurs grupp-namn>** och välj sedan OK, eller Välj ett befintligt ** \<resurs grupp namn>** baserat på parametrar. |
    | **Instans information** |                                                                 |
    | Name             | Ange ** \<Virtual-Network-Name->**                                    |
    | Region           | Välj ** \<region – namn>** |

3. Välj fliken **IP-adresser** eller Välj **Nästa: knappen Nästa: IP-adress** längst ned på sidan.

4. På fliken **IP-adresser** anger du den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | IPv4-adress utrymme | Ange ** \<IPv4-Address-Space->** |

5. Under **under näts namn**väljer du ordet **standard**.

6. I **Redigera undernät**anger du den här informationen:

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | Namn på undernät | Ange ** \<undernät – namn>** |
    | Adressintervall för undernätet | Ange ** \<under nätets adress intervall>**

7. Välj **Spara**.

8. Välj fliken **Granska + skapa** eller Välj knappen **Granska + skapa** .

9. Välj **Skapa**.