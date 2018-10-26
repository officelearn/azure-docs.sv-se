---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d001d76bca5b9a0837349b6e05b3b0a271ea3a73
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132998"
---
> [!NOTE]
> Du kan använda Azure DNS för att konfigurera ett anpassat DNS-namn för Azure Web Apps. Mer information finns i [Använda Azure DNS för att skapa inställningar för anpassad domän för en Azure-tjänst](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

Logga in på webbplatsen till din domänleverantör.

Sök upp sidan för hantering av DNS-poster. Leverantören för varje domän har sitt eget DNS-postgränssnitt, så läs leverantörens dokumentation. Leta efter områden på webbplatsen med namnet **Domännamn**, **DNS**, eller **Namnserverhantering**. 

Du kan ofta hitta sidan med DNS-poster genom att visa din kontoinformation och sedan söka efter en länk som heter exempelvis **Mina domäner**. Gå till sidan och sök upp efter en länk som heter något i stil med **Zonfil**, **DNS-poster** eller **Avancerad konfiguration**.

Skärmbilden nedan är ett exempel på en sida med DNS-poster:

![Exempelsida för DNS-poster](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

På exempelskärmbilden väljer du **Lägg till** för att skapa en post. Vissa providrar har olika länkar för att lägga till olika posttyper. Se leverantörens dokumentation.

> [!NOTE]
> För vissa leverantörer, till exempel GoDaddy, börjar ändringar i DNS-posterna inte att gälla förrän du väljer en separat **Spara ändringar**-länk. 
