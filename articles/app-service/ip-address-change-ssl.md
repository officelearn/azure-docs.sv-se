---
title: Förbered för ändring av SSL-IP-adress
description: Om din SSL-IP-adress ska ändras kan du läsa om vad du ska göra så att appen fortsätter att fungera efter ändringen.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: dcfe11bcab25f6267a557de5faf7befab467bc29
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535731"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Så här förbereder du dig för en SSL-IP-adressändring

Om du har fått ett meddelande om att SSL-IP-adressen för din Azure App Service-app ändras följer du instruktionerna i den här artikeln för att släppa befintlig SSL-IP-adress och tilldela en ny.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Släpp SSL IP-adresser och tilldela nya

1.  Öppna [Azure-portalen](https://portal.azure.com).

2.  Välj **Apptjänster**på menyn för vänster navigering .

3.  Välj appen App Service i listan.

4.  Klicka på **SSL-inställningar** i vänster navigering under rubriken **Inställningar.**

1. Välj värdnamnsposten i avsnittet TLS/SSL-bindningar. I redigeraren som öppnas väljer du **SNI SSL** på den nedrullningsvänliga menyn **SSL-typ** och klickar på **Lägg till bindning**. När meddelandet om åtgärden lyckas har den befintliga IP-adressen släppts.

6.  I avsnittet **SSL-bindningar** väljer du återigen samma värdnamnspost med certifikatet. I redigeraren som öppnas väljer du **IP-baserad SSL** på den nedrullningsvänliga menyn **SSL-typ** och klickar på **Lägg till bindning**. När du ser meddelandet om åtgärdens lyckade meddelande har du fått en ny IP-adress.

7.  Om en A-post (DNS-post som pekar direkt på din IP-adress) är konfigurerad i domänregistreringsportalen (tredjeparts-DNS-provider eller Azure DNS) ersätter du den befintliga IP-adressen med den nyligen genererade. Du hittar den nya IP-adressen genom att följa instruktionerna i nästa avsnitt.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Hitta den nya SSL-IP-adressen i Azure Portal

1.  Vänta några minuter och öppna sedan [Azure-portalen](https://portal.azure.com).

2.  Välj **Apptjänster**på menyn för vänster navigering .

3.  Välj appen App Service i listan.

4.  Klicka på **Egenskaper** i den vänstra navigeringen under **rubriken Inställningar** och leta reda på avsnittet **Virtuell IP-adress**.

5. Kopiera IP-adressen och konfigurera om domänposten eller IP-mekanismen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du förbereder en IP-adressändring som initierades av Azure. Mer information om IP-adresser i Azure App Service finns [i Inkommande och utgående IP-adresser i Azure App Service](overview-inbound-outbound-ips.md).
