---
title: Azure DNS alias poster översikt
description: Översikt över stöd för alias-poster i Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 52b42e964e7abe207064aff49f7f8f27f8476ef4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092850"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS alias poster översikt

Azure DNS alias poster är kvalifikationerna på en DNS-postuppsättning. De kan referera till andra Azure-resurser från DNS-zonen. Du kan till exempel skapa en postuppsättning för alias som refererar till en Azure offentlig IP-adress i stället för en A-post. Ditt alias set pekar på en Azure offentlig IP-adress tjänstinstans dynamiskt. Därför uppdateras alias postuppsättningen sömlöst under DNS-matchning.

En alias-postuppsättning stöds för följande typer av poster i en Azure DNS-zon: 

- A 
- AAAA 
- CNAME 

> [!NOTE]
> Alias-poster för A eller AAAA posttyper för Azure Traffic Manager stöds bara för typer av externa slutpunkter. Du måste ange IPv4 eller IPv6-adress, efter behov för externa slutpunkter i Traffic Manager. Vi rekommenderar Använd statiska IP-adresser för adressen.

## <a name="capabilities"></a>Funktioner

- **Peka på en offentlig IP-adressresurs från DNS A/AAAA-postuppsättning.** Du kan skapa en A/AAAA-postuppsättning och gör det till ett alias postuppsättningen så att den pekar till en offentlig IP-resurs.

- **Peka på en Traffic Manager-profil från en CNAME-DNS A/AAAA-postuppsättning.** Du kan peka till CNAME för en Traffic Manager-profil från en DNS CNAME-postuppsättning. Ett exempel är contoso.trafficmanager.net. Nu kan kan du också peka på en Traffic Manager-profil som har externa slutpunkter från ett A eller AAAA-postuppsättning i din DNS-zon.

   > [!NOTE]
   > Alias-poster för A eller AAAA posttyper för Traffic Manager stöds bara för typer av externa slutpunkter. Du måste ange IPv4 eller IPv6-adress, efter behov för externa slutpunkter i Traffic Manager. Vi rekommenderar Använd statiska IP-adresser för adressen.
   
- **Peka på en annan DNS-postuppsättning i samma zon.** Aliasposter kan referera till andra postuppsättningar av samma typ. En DNS CNAME-postuppsättning kan till exempel vara ett alias till en annan CNAME-postuppsättning av samma typ. Det här är användbart om du vill att vissa postuppsättningar vara alias och vissa alias.

## <a name="scenarios"></a>Scenarier
Det finns några vanliga scenarier för Alias poster.

### <a name="prevent-dangling-dns-records"></a>Förhindra överflödiga DNS-poster
 Alias poster kan användas för nära livscykeln för Azure-resurser i Azure DNS-zoner. Resurser är en offentlig IP-adress eller en Traffic Manager-profil. Ett vanligt problem med traditionella DNS-poster överflödiga poster. Det här problemet uppstår särskilt med A/AAAA- eller CNAME posttyper. 

Med en traditionell DNS-zon post, om det finns inte längre mål-IP- eller CNAME-post, känner DNS-zon posten inte till den. Posten måste därför uppdateras manuellt. I vissa organisationer kan den här manuell uppdatering inte ske i tid. Det kan också vara problematiskt på grund av åtskillnad av roller och associerade behörighetsnivåer.

En roll kan till exempel ha behörighet att ta bort en CNAME-post eller IP-adress som hör till ett program. Men som har inte tillräcklig behörighet att uppdatera DNS-post som pekar på dessa mål. Tid fördröjning mellan när IP- eller CNAME-post tas bort och DNS-post som pekar till den tas bort. Den här tidsfördröjningen orsaka ett avbrott för användare.

Alias poster ta bort komplexiteten som är associerade med det här scenariot. De bidrar till att förhindra överflödiga referenser. Ta till exempel en DNS-post som är kvalificerad som ett aliaspost så att den pekar till en offentlig IP-adress eller en Traffic Manager-profil. Om de underliggande resurserna tas bort tas DNS-aliaspost bort samtidigt. Den här processen säkerställer att användare aldrig har drabbats av ett avbrott.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Uppdatera DNS-zoner automatiskt när programmet IP-adresser ändras

Det här scenariot liknar föregående. Kanske ett program har flyttats, eller den underliggande virtuella datorn har startats om. En aliaspost uppdaterar sedan automatiskt när IP-adressen ändras för den underliggande offentliga IP-adressresursen. Dirigera användare till ett annat program som har den gamla IP-adressen för att undvika potentiella säkerhetsrisker.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Värdprogram för Utjämning av nätverksbelastning i basdomänen

DNS-protokollet förhindrar tilldelningen av något annat än en A eller AAAA-post på zonens apex. Ett exempel är contoso.com. Den här begränsningen utgör ett problem för programägare som har belastningsutjämnade program bakom Traffic Manager. Det inte går att den pekar på Traffic Manager-profilen från zonen apex posten. Programägare måste därför använda en lösning. En omdirigering på programnivå omdirigera från basdomänen till en annan domän. Ett exempel är en omdirigering från contoso.com till www.contoso.com. Den här ordningen anger en enskild felpunkt för funktionen omdirigering.

Det här problemet finns inte längre med alias poster. Nu kan programägare peka deras zon apex-poster på en Traffic Manager-profil som har externa slutpunkter. Programägare kan peka på samma Traffic Manager-profilen som används för andra domäner i sina DNS-zonen. Till exempel kan contoso.com och www.contoso.com peka på samma Traffic Manager-profilen. Detta är fallet så länge Traffic Manager-profilen har endast externa slutpunkter som har konfigurerats.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om alias poster, finns i följande artiklar:

- [Självstudie: Konfigurera en aliaspost för att referera till en Azure offentlig IP-adress](tutorial-alias-pip.md)
- [Självstudie: Konfigurera en aliaspost för att stödja apex-domännamn med Traffic Manager](tutorial-alias-tm.md)
- [Vanliga frågor och svar om DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
