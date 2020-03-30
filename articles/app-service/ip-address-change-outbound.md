---
title: Förbereda för ändringar av utgående IP-adress
description: Om din utgående IP-adress kommer att ändras kan du läsa om vad du ska göra så att appen fortsätter att fungera efter ändringen.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671673"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Så här förbereder du dig för en utgående IP-adressändring

Om du har fått ett meddelande om att de utgående IP-adresserna för din Azure App Service-app ändras följer du instruktionerna i den här artikeln.

## <a name="determine-if-you-have-to-do-anything"></a>Ta reda på om du måste göra något

* Alternativ 1: Om apptjänstappen inte använder IP-filtrering, en explicit inkluderingslista eller särskild hantering av utgående trafik som routning eller brandvägg krävs ingen åtgärd.

* Alternativ 2: Om din app har särskild hantering för utgående IP-adresser (se exempel nedan), lägg till de nya utgående IP-adresserna där de befintliga visas. Ersätt inte de befintliga IP-adresserna. Du hittar de nya utgående IP-adresserna genom att följa instruktionerna i nästa avsnitt.

  En utgående IP-adress kan till exempel uttryckligen inkluderas i en brandvägg utanför appen, eller så kan en extern betalningstjänst ha en tillåten lista som innehåller den utgående IP-adressen för din app. Om din utgående adress är konfigurerad i en lista var som helst utanför appen måste den ändras.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Hitta de utgående IP-adresserna i Azure-portalen

De nya utgående IP-adresserna visas i portalen innan de börjar gälla. När Azure börjar använda de nya används inte längre de gamla. Endast en uppsättning i taget används, så poster i inkluderingslistor måste ha både gamla och nya IP-adresser för att förhindra ett avbrott när växeln sker. 

1.  Öppna [Azure-portalen](https://portal.azure.com).

2.  Välj **Apptjänster**på menyn för vänster navigering .

3.  Välj appen App Service i listan.

1.  Om appen är en funktionsapp läser du [Funktionsapp utgående IP-adresser](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  Klicka på **Egenskaper** i den vänstra navigeringen under **rubriken Inställningar** och leta reda på avsnittet **Utgående IP-adresser**.

5. Kopiera IP-adresserna och lägg till dem i din speciella hantering av utgående trafik, till exempel ett filter eller en tillåten lista. Ta inte bort de befintliga IP-adresserna i listan.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du förbereder en IP-adressändring som initierades av Azure. Mer information om IP-adresser i Azure App Service finns [i Inkommande och utgående IP-adresser i Azure App Service](overview-inbound-outbound-ips.md).
