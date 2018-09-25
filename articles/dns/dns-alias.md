---
title: Azure DNS alias poster översikt
description: Översikt över stöd för alias-poster i Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 05a6a1700de2b8b334b40d9efd9b8d79e9e7241f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957592"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS alias poster översikt

Azure DNS-alias poster är kompetensen på en DNS-postuppsättning som gör det möjligt att referera till andra Azure-resurser från DNS-zonen. Du kan till exempel skapa en postuppsättning för alias som refererar till en Azure offentlig IP-adress i stället en A-post. Eftersom alias-recordset pekar på en tjänstinstans för Azures offentliga IP-adress dynamiskt, uppdateras alias postuppsättningen sömlöst under DNS-matchning.

En alias-postuppsättning stöds för följande typer av poster i en Azure DNS-zon: A, AAAA och CNAME-post. 

> [!NOTE]
> Alias-poster för A eller AAAA posttyper för Traffic Manager stöds bara för typer av externa slutpunkter. Du måste ange IPv4- eller IPv6-adressen (helst statiska IP-adresser) för externa slutpunkter i Traffic Manager.

## <a name="capabilities"></a>Funktioner

- **Peka på en offentlig IP-resurs från en DNS A/AAAA-postuppsättning**. Du kan skapa en A/AAAA-postuppsättning och göra det en alias-postuppsättningen så att den pekar till en offentlig IP-resurs.
- **Peka på en Traffic Manager-profil från en CNAME-DNS A/AAAA-postuppsättning**. Förutom att peka på CNAME för en Traffic Manager-profil (till exempel: contoso.trafficmanager.net) från en DNS CNAME-postuppsättning du kan nu också peka på en Traffic Manager-profil som har externa slutpunkter, från en A eller AAAA-postuppsättning i ditt DNS zon.
   > [!NOTE]
   > Alias-poster för A eller AAAA posttyper för Traffic Manager stöds bara för typer av externa slutpunkter. Du måste ange IPv4- eller IPv6-adressen (helst statiska IP-adresser) för externa slutpunkter i Traffic Manager.
- **Peka på en annan DNS-postuppsättning i samma zon**. Alias poster kan referera till andra postuppsättningar av samma typ. Du kan till exempel ha en DNS CNAME-postuppsättning som ett alias till en annan CNAME-postuppsättning av samma typ. Detta är användbart om du vill ha vissa postuppsättningar vara alias och andra som alias när det gäller beteende.

## <a name="scenarios"></a>Scenarier
Det finns några vanliga scenarier för Alias poster:

### <a name="prevent-dangling-dns-records"></a>Förhindra överflödiga DNS-poster
Från inom Azure DNS-zoner, kan alias poster användas för att nära hålla reda på livscykeln för Azure-resurser som en offentlig IP-adress och Traffic Manager-profil. En av de vanliga problem med traditionella DNS-poster är ”överflödiga poster”, särskilt med A/AAAA- eller CNAME posttyper. 

Med en traditionell DNS-zon post, om det finns inte längre mål-IP- eller CNAME-post, DNS-zon posten har ingen information om detta och måste uppdateras manuellt. I vissa organisationer har den här manuell uppdatering inte kan inträffa i tid eller kan vara problematiskt på grund av åtskillnad av roller och associerade behörighetsnivåer.

Den roll som har behörighet att ta bort en CNAME-post eller IP-adress som hör till ett program kan till exempel inte har tillräcklig behörighet att uppdatera DNS-post som pekar på dessa mål. Därför kan finnas det en tidsfördröjning mellan när IP- eller CNAME-post tas bort och DNS-post som pekar på den tas bort, vilket kan medföra ett avbrott för slutanvändare.

Alias poster ta bort komplexiteten som är associerade med det här scenariot och förhindra att sådana överflödiga referenser. När en DNS-post som en aliaspost är kvalificerad för att peka på en offentlig IP-adress eller en Traffic Manager-profil, och om de underliggande resurserna tas bort, tas även DNS-aliaspost på samma gång. Detta säkerställer att slutanvändarna drabbas aldrig ett avbrott.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Uppdatera DNS-zoner automatiskt när programmet IP-adresser ändras

Liknar det föregående scenariot, om ett program flyttas eller om den underliggande virtuella datorn startas om, en aliaspost uppdateras automatiskt när IP-adressen ändras för den underliggande offentliga IP-adressresursen. Du kan undvika potentiella säkerhetsrisker om slutanvändare dirigeras till ett annat program som har den gamla IP-adressen.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Värd belastningsutjämnade program på zonens apex

DNS-protokollet förhindrar tilldelningen av något annat än en A eller AAAA-post i basdomänen (till exempel: contoso.com). Detta anger hur många problem för programägare som har load balanced program bakom en Traffic Manager, eftersom den inte var möjligt att den pekar på Traffic Manager-profilen från zonen apex posten. Därför var programägare tvungen att använda en lösning. Till exempel en omdirigering på programnivå att omdirigera från basdomänen till en annan domän (från contoso.com till www.contoso.com). Detta medför en enskild felpunkt när det gäller omdirigerings-funktioner.

Det här problemet finns inte längre med alias poster. Programägare kan nu rikta sin zon apex-post till en Traffic Manager-profil som har externa slutpunkter. Med den här funktionen kan programägare peka på samma Traffic Manager-profilen som används för andra domäner i sina DNS-zonen. Till exempel kan contoso.com och www.contoso.com både peka på samma Traffic Manager-profilen så länge Traffic Manager-profilen har endast externa slutpunkter som har konfigurerats.

## <a name="next-steps"></a>Nästa steg

Om du vill Läs mer om alias-poster, finns i följande artiklar:

- [Självstudie: Konfigurera en aliaspost för att referera till en Azure offentlig IP-adress](tutorial-alias-pip.md)
- [Självstudie: Konfigurera en aliaspost för att stödja apex-domännamn med Traffic Manager](tutorial-alias-tm.md)
- [Vanliga frågor och svar om DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
