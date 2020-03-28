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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244984"
---
## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa ett virtuellt nätverk och undernät.

1. På skärmens övre vänstra sida väljer du **Skapa en resurs > Nätverk > Virtuellt nätverk** eller sök efter **virtuellt nätverk** i sökrutan.

2. Ange eller välj den här informationen på fliken **Grunderna** i **Skapa virtuellt nätverk:**

    | **Inställning**          | **Värde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektinformation**  |                                                                 |
    | Prenumeration     | Välj din Azure-prenumeration                                  |
    | Resursgrupp   | Välj **Skapa nytt**, ange ** \<resursgruppnamn>**, välj sedan OK eller välj ett befintligt ** \<resursgruppnamn>** baserat på parametrar. |
    | **Information om instans** |                                                                 |
    | Namn             | Ange ** \<>för virtuella nätverk och nätverk**                                    |
    | Region           | Välj ** \<regionnamn>** |

3. Välj fliken **IP-adresser** eller välj knappen **Nästa: IP-adresser** längst ned på sidan.

4. Ange den här informationen på fliken **IP-adresser:**

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | IPv4-adressutrymme | Ange ** \<>för IPv4-adressutrymme** |

5. Under **Undernätsnamn**väljer du ordet **standard**.

6. Ange den här informationen i **Redigera undernät:**

    | Inställning            | Värde                      |
    |--------------------|----------------------------|
    | Namn på undernät | Ange ** \<>** |
    | Adressintervall för undernätet | Ange ** \<>undernät-adressintervall**

7. Välj **Spara**.

8. Välj fliken **Granska + skapa** eller välj knappen Granska + **skapa.**

9. Välj **Skapa**.