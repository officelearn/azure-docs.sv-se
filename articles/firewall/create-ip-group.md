---
title: Skapa IP-grupper i Azure-brandväggen
description: Med IP-grupper kan du gruppera och hantera IP-adresser för Azure brand Väggs regler.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c3ae62bf5b4f0b4796cac2e7079c8a09116d4895
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85602541"
---
# <a name="create-ip-groups"></a>Skapa IP-grupper

Med IP-grupper kan du gruppera och hantera IP-adresser för Azure brand Väggs regler. De kan ha en enskild IP-adress, flera IP-adresser eller ett eller flera IP-adressintervall.

## <a name="create-an-ip-group"></a>Skapa en IP-grupp

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Skriv **IP-grupper** i Sök text rutan och välj sedan **IP-grupper**.
3. Välj **Skapa**.
4. Välj din prenumeration.
5. Välj en resurs grupp eller skapa en ny.
6. Ange ett unikt namn för IP-gruppen och välj sedan en region.

6. Välj **Nästa: IP-adresser**.
7. Ange en IP-adress, flera IP-adresser eller IP-adressintervall.

   Det finns två sätt att ange IP-adresser:
   - Du kan ange dem manuellt
   - Du kan importera dem från en fil

   Om du vill importera från en fil väljer du **Importera från en fil**. Du kan antingen dra filen till rutan eller välja **Bläddra efter filer**. Om det behövs kan du granska och redigera dina överförda IP-adresser.

   När du anger en IP-adress validerar portalen den för att söka efter överlappande, dubbletter och formateringsfel.

5. När du är färdig väljer du **Granska + skapa**.
6. Välj **Skapa**.


## <a name="next-steps"></a>Nästa steg

- [Läs mer om IP-grupper](ip-groups.md)