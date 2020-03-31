---
title: Skapa IP-grupper i Azure-brandväggen
description: Med IP-grupper kan du gruppera och hantera IP-adresser för Azure Firewall-regler.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444542"
---
# <a name="create-ip-groups-preview"></a>Skapa IP-grupper (förhandsgranskning)

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med IP-grupper kan du gruppera och hantera IP-adresser för Azure Firewall-regler. De kan ha en enda IP-adress, flera IP-adresser eller ett eller flera IP-adressintervall.

## <a name="create-an-ip-group"></a>Skapa en IP-grupp

1. På startsidan för Azure-portalen väljer du **Skapa en resurs**.
2. Skriv **IP-grupper** i söktextrutan och välj sedan **IP-grupper**.
3. Välj **Skapa**.
4. Välj din prenumeration.
5. Välj en resursgrupp eller skapa en ny.
6. Skriv ett unikt namn för dig IP-grupp och välj sedan en region.

6. Välj **Nästa: IP-adresser**.
7. Ange en IP-adress, flera IP-adresser eller IP-adressintervall.

   Det finns två sätt att ange IP-adresser:
   - Du kan ange dem manuellt
   - Du kan importera dem från en fil

   Om du vill importera från en fil väljer du **Importera från en fil**. Du kan antingen dra filen till rutan eller välja **Bläddra efter filer**. Om det behövs kan du granska och redigera dina uppladdade IP-adresser.

   När du skriver en IP-adress validerar portalen den för att söka efter överlappande, dubbletter och formateringsproblem.

5. När du är klar väljer du **Granska + Skapa**.
6. Välj **Skapa**.


## <a name="next-steps"></a>Nästa steg

- [Läs mer om IP-grupper](ip-groups.md)