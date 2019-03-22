---
title: Azure DNS alias poster översikt
description: Översikt över stöd för alias-poster i Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 87ca7cae8e9170c8c437d0961cb1acb2e0dd0eb1
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337654"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS alias poster översikt

Azure DNS alias poster är kvalifikationerna på en DNS-postuppsättning. De kan referera till andra Azure-resurser från DNS-zonen. Du kan till exempel skapa en postuppsättning för alias som refererar till en Azure offentlig IP-adress i stället för en A-post. Ditt alias set pekar på en Azure offentlig IP-adress tjänstinstans dynamiskt. Därför uppdateras alias postuppsättningen sömlöst under DNS-matchning.

En alias-postuppsättning stöds för följande typer av poster i en Azure DNS-zon: 

- A
- AAAA
- CNAME

> [!NOTE]
> Om du planerar att använda en aliasresurspost för posttyper A eller AAAA för att peka mot en [Azure Traffic Manager-profil](../traffic-manager/quickstart-create-traffic-manager-profile.md) måste du se till att Traffic Manager-profilen har bara [externa slutpunkter](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Du måste ange IPv4 eller IPv6-adress för externa slutpunkter i Traffic Manager. Vi rekommenderar använda statiska IP-adresser.

## <a name="capabilities"></a>Funktioner

- **Peka på en offentlig IP-adressresurs från DNS A/AAAA-postuppsättning.** Du kan skapa en A/AAAA-postuppsättning och gör det till ett alias postuppsättningen så att den pekar till en offentlig IP-resurs. DNS-postuppsättning är automatiskt om den offentliga IP-adressen ändras eller har tagits bort. Överflödiga DNS undviks-poster som pekar på felaktig IP-adresser.

- **Peka på en Traffic Manager-profil från en CNAME-DNS A/AAAA-postuppsättning.** Du kan skapa en A/AAAA eller CNAME-post och använda alias poster för att peka på en Traffic Manager-profil. Det är särskilt användbart när du vill dirigera trafik på en basdomänen som traditionella CNAME-poster inte stöds för en zonens apex. Anta exempelvis att Traffic Manager-profilen är myprofile.trafficmanager.net och företag DNS-zonen contoso.com. Du kan skapa ett alias postuppsättningen av typen A/AAAA för contoso.com (basdomänen) och pekar på myprofile.trafficmanager.net.
- **Peka på en Azure Content Delivery Network (CDN) slutpunkt**. Detta är användbart när du skapar statiska webbplatser som använder Azure storage och Azure CDN.
- **Peka på en annan DNS-postuppsättning i samma zon.** Aliasposter kan referera till andra postuppsättningar av samma typ. En DNS CNAME-postuppsättning kan till exempel vara ett alias till en annan CNAME-postuppsättning. Det här är användbart om du vill att vissa postuppsättningar vara alias och vissa alias.

## <a name="scenarios"></a>Scenarier

Det finns några vanliga scenarier för Alias poster.

### <a name="prevent-dangling-dns-records"></a>Förhindra överflödiga DNS-poster

Ett vanligt problem med traditionella DNS-poster överflödiga poster. Till exempel DNS-poster som inte har uppdaterats för att återspegla ändringar till IP-adresser. Problemet uppstår särskilt med A/AAAA- eller CNAME posttyper.

Med en traditionell DNS-zon post, om det finns inte längre mål-IP- eller CNAME-post, måste DNS-posten som associeras med den uppdateras manuellt. I vissa organisationer kan en manuell uppdatering inte ske i tid på grund av problem med processen eller på grund av åtskillnad av roller och associerade behörighetsnivåer. En roll kan till exempel ha behörighet att ta bort en CNAME-post eller IP-adress som hör till ett program. Men som har inte tillräcklig behörighet att uppdatera DNS-post som pekar på dessa mål. En fördröjning vid uppdatering av DNS-posten kan eventuellt medföra ett avbrott för användare.

Alias-poster förhindrar överflödiga referenser nära att koppla livscykeln för en DNS-post med en Azure-resurs. Anta exempelvis att en DNS-post som är kvalificerad som ett aliaspost så att den pekar till en offentlig IP-adress eller en Traffic Manager-profil. Om de underliggande resurserna tas bort tas DNS-aliaspost bort samtidigt.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Uppdatera DNS-postuppsättning automatiskt när programmet IP-adresser ändras

Det här scenariot liknar föregående. Kanske ett program har flyttats, eller den underliggande virtuella datorn har startats om. En aliaspost uppdaterar sedan automatiskt när IP-adressen ändras för den underliggande offentliga IP-adressresursen. På så sätt undviker potentiella säkerhetsriskerna med att dirigera användarna till ett annat program som har tilldelats den gamla offentliga IP-adressen.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Värdprogram för Utjämning av nätverksbelastning i basdomänen

Tilldelningen av CNAME-poster i basdomänen förhindrar att DNS-protokollet. Till exempel om domänen är contoso.com; Du kan skapa CNAME-poster för somelable.contoso.com; men du kan inte skapa CNAME-post för contoso.com själva.
Den här begränsningen utgör ett problem för programägare som har belastningsutjämnade program bakom [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Eftersom du använder en Traffic Manager-profil kräver en CNAME-post skapas, går det inte att den pekar på Traffic Manager-profilen från zonens apex.

Det här problemet kan lösas med hjälp av alias poster. Till skillnad från CNAME-poster, alias poster kan skapas i basdomänen och programägare kan använda den för att rikta sin zon apex-post till en Traffic Manager-profil som har externa slutpunkter. Programägare kan peka på samma Traffic Manager-profilen som används för andra domäner i sina DNS-zonen.

Till exempel contoso.com och www\.contoso.com kan peka på samma Traffic Manager-profilen. Om du vill lära dig mer om att använda alias poster med Azure Traffic Manager-profiler finns i avsnittet nästa steg.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Peka zonens apex på Azure CDN-slutpunkter

Precis som en Traffic Manager-profil kan du också använda alias poster för att peka DNS-basdomänen på Azure CDN-slutpunkter. Detta är användbart när du skapar statiska webbplatser som använder Azure storage och Azure CDN. Du kan sedan komma åt webbplatsen utan prepending ”www” till din DNS-namnet.

Till exempel om din statiska webbplats har namnet www.contoso.com, dina användare kan komma åt din webbplats med hjälp av contoso.com utan att behöva lägga till åtkomstgruppen för www till DNS-namn.

Som tidigare nämnts, stöds inte CNAME-poster i basdomänen. Så det går inte att du använder en CNAME-post för att peka contoso.com till CDN-slutpunkten. Använd istället en aliaspost för att peka zonens apex direkt på en CDN-slutpunkt.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om alias poster, finns i följande artiklar:

- [Självstudier: Konfigurera en aliaspost för att referera till en Azure offentlig IP-adress](tutorial-alias-pip.md)
- [Självstudier: Konfigurera en aliaspost för att stödja apex-domännamn med Traffic Manager](tutorial-alias-tm.md)
- [Vanliga frågor och svar om DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
