---
title: Förbereda för inkommande IP-adressändring
description: Om din inkommande IP-adress kommer att ändras kan du läsa om vad du ska göra så att appen fortsätter att fungera efter ändringen.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672419"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Så här förbereder du dig för en inkommande IP-adressändring

Om du har fått ett meddelande om att den inkommande IP-adressen för din Azure App Service-app ändras följer du instruktionerna i den här artikeln.

## <a name="determine-if-you-have-to-do-anything"></a>Ta reda på om du måste göra något

* Alternativ 1: Om apptjänstappen inte har en anpassad domän krävs ingen åtgärd.

* Alternativ 2: Om endast en CNAME-post (DNS-post som pekar på en URI) är konfigurerad i domänregistreringsportalen (tredje parts DNS-leverantör eller Azure DNS), krävs ingen åtgärd.

* Alternativ 3: Om en A-post (DNS-post som pekar direkt på din IP-adress) är konfigurerad i domänregistreringsportalen (tredje parts DNS-leverantör eller Azure DNS) ersätter du den befintliga IP-adressen med den nya. Du hittar den nya IP-adressen genom att följa instruktionerna i nästa avsnitt.

* Alternativ 4: Om ditt program ligger bakom en belastningsutjämnare, IP-filter eller någon annan IP-mekanism som kräver appens IP-adress, ersätter du den befintliga IP-adressen med den nya. Du hittar den nya IP-adressen genom att följa instruktionerna i nästa avsnitt.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Hitta den nya inkommande IP-adressen i Azure-portalen

Den nya inkommande IP-adressen som ges till din app finns i portalen i fältet **Virtuell IP-adress.** Både den här nya IP-adressen och den gamla är anslutna till din app nu, och senare kommer den gamla att kopplas från.

1.  Öppna [Azure-portalen](https://portal.azure.com).

2.  Välj **Apptjänster**på menyn för vänster navigering .

3.  Välj appen App Service i listan.

1.  Om appen är en funktionsapp läser du [Funktionsappens inkommande IP-adress](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  Klicka på **Egenskaper** i den vänstra navigeringen under **rubriken Inställningar** och leta reda på avsnittet **Virtuell IP-adress**.

5. Kopiera IP-adressen och konfigurera om domänposten eller IP-mekanismen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du förbereder en IP-adressändring som initierades av Azure. Mer information om IP-adresser i Azure App Service finns [i Inkommande och utgående IP-adresser i Azure App Service](overview-inbound-outbound-ips.md).
