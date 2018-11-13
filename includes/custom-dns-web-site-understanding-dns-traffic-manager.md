---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: 73e95f6259c916b06fe61cb47fd36beac4c7a427
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572790"
---
Domain Name System (DNS) används för att hitta saker på internet. Till exempel när du anger en adress i din webbläsare eller på en länk på en webbsida, används DNS översätta domänen till en IP-adress. IP-adressen är till exempel en gatuadress, men det är inte mycket mänskliga eget. Till exempel är det mycket enklare att komma ihåg ett DNS-namn som liknar **contoso.com** än att spara en IP-adress, till exempel 192.168.1.88 eller 2001:0:4137:1f67:24a2:3888:9cce:fea3.

DNS-systemet är baserad på *poster*. Poster associera en specifik *namn*, till exempel **contoso.com**, med en IP-adress eller ett annat DNS-namn. När ett program, till exempel en webbläsare, letar upp ett namn i DNS, den söker efter post och använder det den pekar på som adress. Om värdet som den pekar på är en IP-adress, använder webbläsaren detta värde. Om den pekar på en annan DNS-namn har programmet göra upplösning igen. Slutligen går alla namnmatchning ut en IP-adress.

När du skapar en Azure-webbplats kan tilldelas automatiskt ett DNS-namn till webbplatsen. Det här namnet har formen av  **&lt;yoursitename&gt;. azurewebsites.net**. När du lägger till webbplatsen som en Azure Traffic Manager-slutpunkt kan din webbplats är sedan tillgängliga via den  **&lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** domän.

> [!NOTE]
> När din webbplats är konfigurerad som en Traffic Manager-slutpunkt, använder du den **. trafficmanager.net** adressen när du skapar DNS-poster.
> 
> Du kan bara använda CNAME-poster med Traffic Manager
> 
> 

Det finns flera typer av poster, var och en med sin egen funktioner och begränsningar för webbplatser som är konfigurerade att som Traffic Manager-slutpunkter, vi bara det bra, men om en; *CNAME* poster.

### <a name="cname-or-alias-record"></a>CNAME-post eller aliaspost
En CNAME-post som mappar en *specifika* DNS-namn, till exempel **mail.contoso.com** eller **www.contoso.com**, till en annan (canonical) domännamn. När det gäller Azure Websites med Traffic Manager, canonical domännamnet är den  **&lt;myapp >. trafficmanager.net** domännamnet för Traffic Manager-profilen. När du skapat CNAME skapar ett alias för den  **&lt;myapp >. trafficmanager.net** domännamn. CNAME-posten matchar med IP-adressen för din  **&lt;myapp >. trafficmanager.net** domännamn automatiskt, så om IP-adressen för webbplatsen ändras kan du inte behöver vidta några åtgärder.

När trafik anländer på Traffic Manager dirigerar sedan trafiken till din webbplats, med vilken belastningsutjämningsmetod som den är konfigurerad för. Det här är helt transparent för besökare till din webbplats. De ser bara det anpassade domännamnet i webbläsaren.

> [!NOTE]
> Vissa domän-registratorer kan du mappa underdomäner när du använder en CNAME-post som bara **www.contoso.com**, och inte rot namn, till exempel **contoso.com**. Mer information om CNAME-poster finns i dokumentationen som tillhandahålls av din registrator <a href="http://en.wikipedia.org/wiki/CNAME_record">Wikipedia-transaktionen på CNAME-post</a>, eller <a href="http://tools.ietf.org/html/rfc1035">IETF domännamn - implementering och specifikation</a> dokumentet.
> 
> 

