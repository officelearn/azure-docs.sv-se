---
title: Översikt över aliasposter - Azure DNS
description: I den här artikeln får du lära dig mer om stöd för aliasposter i Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 271770935cf4cb83d4abc6e82a4f4b13ffe865b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295493"
---
# <a name="azure-dns-alias-records-overview"></a>Översikt över Azure DNS-aliasposter

Azure DNS-aliasposter är kvalifikationer för en DNS-postuppsättning. De kan referera till andra Azure-resurser från din DNS-zon. Du kan till exempel skapa en aliaspostuppsättning som refererar till en offentlig Azure-IP-adress i stället för en A-post. Din aliaspostuppsättning pekar dynamiskt på en azure public IP-adresstjänstinstans dynamiskt. Det innebär att aliasposten sömlöst uppdaterar sig själv under DNS-matchningen.

En aliaspostuppsättning stöds för följande posttyper i en Azure DNS-zon: 

- A
- AAAA
- CNAME

> [!NOTE]
> Om du tänker använda en aliaspost för A- eller AAAA-posttyperna för att peka på en [Azure Traffic Manager-profil](../traffic-manager/quickstart-create-traffic-manager-profile.md) måste du se till att Traffic Manager-profilen bara har [externa slutpunkter](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Du måste ange IPv4- eller IPv6-adressen för externa slutpunkter i Traffic Manager. Du kan inte använda fullständigt kvalificerade domännamn (FQDN) i slutpunkter. Använd helst statiska IP-adresser.

## <a name="capabilities"></a>Funktioner

- **Peka på en offentlig IP-resurs från en DNS A/AAAA-postuppsättning.** Du kan skapa en A/AAAA-postuppsättning och göra den till en aliaspostuppsättning som pekar på en offentlig IP-resurs (standard eller grundläggande). DNS-postuppsättningen ändras automatiskt om den offentliga IP-adressen ändras eller tas bort. Dinglande DNS-poster som pekar på felaktiga IP-adresser undviks.

   Det finns en aktuell gräns på 20 aliasposter per resurs.

- **Peka på en Traffic Manager-profil från en DNS A/AAAA/CNAME-postuppsättning.** Du kan skapa en A/AAAA- eller CNAME-postuppsättning och använda aliasposter för att peka den på en Traffic Manager-profil. Det är särskilt användbart när du behöver dirigera trafik vid en zonsponsa, eftersom traditionella CNAME-poster inte stöds för en zonsponsa. Anta till exempel att din Traffic Manager-profil är myprofile.trafficmanager.net och att din affärs-DNS-zon är contoso.com. Du kan skapa en aliaspostuppsättning av typen A/AAAA för contoso.com (zonsponsan) och peka på myprofile.trafficmanager.net.
- **Peka på en CDN-slutpunkt (Azure Content Delivery Network).** Detta är användbart när du skapar statiska webbplatser med Azure storage och Azure CDN.
- **Peka på en annan DNS-postuppsättning inom samma zon.** Aliasposter kan referera till andra postuppsättningar av samma typ. En DNS CNAME-postuppsättning kan till exempel vara ett alias till en annan CNAME-postuppsättning. Det här arrangemanget är användbart om du vill att vissa postuppsättningar ska vara alias och vissa icke-alias.

## <a name="scenarios"></a>Scenarier

Det finns några vanliga scenarier för Alias-poster.

### <a name="prevent-dangling-dns-records"></a>Förhindra dinglande DNS-poster

Ett vanligt problem med traditionella DNS-poster är dinglande poster. DNS-poster som inte har uppdaterats för att återspegla ändringar av IP-adresser. Problemet uppstår särskilt med A/AAAA- eller CNAME-posttyper.

Om mål-IP- eller CNAME inte längre finns med en traditionell DNS-zonpost måste DNS-posten som är associerad med den uppdateras manuellt. I vissa organisationer kan det hända att en manuell uppdatering inte sker i tid på grund av processproblem eller separation av roller och associerade behörighetsnivåer. En roll kan till exempel ha behörighet att ta bort en CNAME- eller IP-adress som tillhör ett program. Men den har inte tillräcklig behörighet för att uppdatera DNS-posten som pekar på dessa mål. En fördröjning i uppdateringen av DNS-posten kan orsaka ett avbrott för användarna.

Aliasposter förhindrar dinglande referenser genom att koppla samman livscykeln för en DNS-post med en Azure-resurs. Tänk dig till exempel en DNS-post som är kvalificerad som en aliaspost för att peka på en offentlig IP-adress eller en Traffic Manager-profil. Om du tar bort dessa underliggande resurser blir DNS-aliasposten en tom postuppsättning. Den refererar inte längre till den borttagna resursen.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Uppdatera DNS-postuppsättning automatiskt när programmets IP-adresser ändras

Det här scenariot liknar det föregående. Kanske ett program flyttas eller den underliggande virtuella datorn startas om. En aliaspost uppdateras sedan automatiskt när IP-adressen ändras för den underliggande offentliga IP-resursen. På så sätt undviker du potentiella säkerhetsrisker med att dirigera användarna till ett annat program som har tilldelats den gamla offentliga IP-adressen.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Värdbelastningsbalanserade program vid zonsponsen

DNS-protokollet förhindrar tilldelning av CNAME-poster vid zonapexen. Till exempel om din domän är contoso.com; Du kan skapa CNAME-poster för somelabel.contoso.com; men du kan inte skapa CNAME för contoso.com sig själv.
Den här begränsningen utgör ett problem för programägare som har belastningsbalanserade program bakom [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Eftersom det krävs att en CNAME-post skapas med hjälp av en Traffic Manager-profil går det inte att peka på Traffic Manager-profilen från zonsponsan.

Det här problemet löses med hjälp av aliasposter. Till skillnad från CNAME-poster skapas aliasposter i zonens spets och programägare kan använda den för att peka sin zonapexpost till en Traffic Manager-profil som har externa slutpunkter. Programägare pekar på samma Traffic Manager-profil som används för en annan domän i DNS-zonen.

Till exempel kan contoso.com och\.www contoso.com peka på samma Traffic Manager-profil. Mer information om hur du använder aliasposter med Azure Traffic Manager-profiler finns i avsnittet Nästa steg.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Punktzonsapex till Azure CDN-slutpunkter

Precis som en Traffic Manager-profil kan du också använda aliasposter för att peka din DNS-zon apex till Azure CDN-slutpunkter. Detta är användbart när du skapar statiska webbplatser med Azure storage och Azure CDN. Du kan sedan komma åt webbplatsen utan att försöka "www" till ditt DNS-namn.

Om din statiska webbplats `www.contoso.com, your users can access your site using contoso.com` till exempel namnges utan att du behöver förbereda www till DNS-namnet.

Som tidigare beskrivits stöds inte CNAME-poster i zonapexen. Du kan därför inte använda en CNAME-post för att peka contoso.com till CDN-slutpunkten. I stället kan du använda en aliaspost för att peka zonapexen till en CDN-slutpunkt direkt.

> [!NOTE]
> Det går inte att peka en zonapex till CDN-slutpunkter för Azure CDN från Akamai.

## <a name="next-steps"></a>Nästa steg

Mer information om aliasposter finns i följande artiklar:

- [Självstudiekurs: Konfigurera en aliaspost för att referera till en offentlig Azure-IP-adress](tutorial-alias-pip.md)
- [Självstudie: Konfigurera en aliaspost för att hantera apex-domännamn med Traffic Manager](tutorial-alias-tm.md)
- [Vanliga frågor och svar om DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
