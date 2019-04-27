---
title: Kontrollera inställningarna för Azure Traffic Manager
description: Den här artikeln hjälper dig att verifiera Traffic Manager-inställningar.
services: traffic-manager
author: rockboyfor
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 03/16/2017
ms.date: 02/18/2019
ms.author: v-yeche
ms.openlocfilehash: 1e954e3c4ebba245d91cfb84ab583b314150e5b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771625"
---
# <a name="verify-traffic-manager-settings"></a>Verifiera Traffic Manager-inställningar

Om du vill testa dina Traffic Manager-inställningar som du behöver ha flera klienter på olika platser, där du kan köra dina tester. Importera sedan slutpunkter i Traffic Manager-profilen bort en i taget.

* Ange DNS-TTL-värdet med låg så att ändringar att spridas snabbt (till exempel 30 sekunder).
* Vet IP-adresserna för dina Azure-molntjänster och webbplatser i den profil som du vill testa.
* Använd verktyg som låter dig omvandla ett DNS-namn till en IP-adress och visa den här adressen.

Du kontrollerar att det matcha DNS-namn till IP-adresserna för slutpunkterna i din profil. Namnen ska matcha i enlighet med trafikroutningsmetod som definierats i Traffic Manager-profilen. Du kan använda verktyg som **nslookup** eller **fördjupa** för DNS-namnmatchning.

I följande exempel kan du testa Traffic Manager-profilen.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Kontrollera Traffic Manager-profil med hjälp av nslookup och ipconfig i Windows

1. Öppna ett kommando eller en Windows PowerShell-Kommandotolken som administratör.
2. Typ `ipconfig /flushdns` att rensa cacheminnet för DNS-matchare.
3. Skriv `nslookup <your Traffic Manager domain name>`. Till exempel följande kommando kontrollerar domännamnet med prefixet *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.cn

    Ett typiskt resultat visas följande information:

    + DNS-namn och IP-adressen för DNS-server som används för att lösa det här domännamnet för Traffic Manager.
    + Traffic Manager-domännamn du skrev på kommandoraden efter ”nslookup” och IP-adressen som Traffic Manager-domänen matchar. Den andra IP-adressen är det viktigt att kontrollera. Det måste matcha en offentliga virtuella IP-adress (VIP) för molntjänster eller webbplatser i Traffic Manager-profilen som du testar.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Så här testar du trafikroutningsmetoden för redundans

1. Lämna alla slutpunkter för dig.
2. Med en enda klient kan begära DNS-matchning för företagets domännamn med hjälp av nslookup eller ett liknande verktyg.
3. Se till att matcha IP-adressen matchar den primära slutpunkten.
4. Påverkar den primära slutpunkten eller ta bort filen övervakning så att Traffic Manager tror att programmet är igång.
5. Vänta tills DNS-Time-to-Live (TTL) för Traffic Manager-profil plus ytterligare två minuters. Till exempel måste TTL för DNS är 300 sekunder (fem minuter), du vänta i sju minuter.
6. Rensa dina DNS-klienten cache och begäran DNS-zonen uppdateras dynamiskt. I Windows, kan du tömma DNS-cache med kommandot ipconfig/flushdns.
7. Se till att matcha IP-adressen matchar den sekundära slutpunkten.
8. Upprepa processen att stänga av varje slutpunkt i tur och ordning. Kontrollera att DNS returnerar IP-adressen för nästa slutpunkten i listan. När alla slutpunkter är otillgängliga bör du hämta IP-adressen för den primära slutpunkten igen.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Så här testar du viktad metod för trafikroutning

1. Lämna alla slutpunkter för dig.
2. Med en enda klient kan begära DNS-matchning för företagets domännamn med hjälp av nslookup eller ett liknande verktyg.
3. Se till att matcha IP-adressen matchar en av dina slutpunkter.
4. Tömma din DNS-klientens cacheminne och upprepa steg 2 och 3 för varje slutpunkt. Du bör se olika IP-adresser som returneras för var och en av dina slutpunkter.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Så här testar du trafikroutningsmetod för prestanda

För att effektivt testa en routningsmetod för prestanda-trafik, måste du ha kunder i olika delar av världen. Du kan skapa klienter i olika Azure-regioner som kan användas för att testa dina tjänster. Om du har ett globalt nätverk kan du via en fjärranslutning logga in på klienter i andra delar av världen och kör dina tester därifrån.

Du kan också det finns ledigt webbaserade DNS-sökning och fördjupa dig tjänster som är tillgängliga. Vissa av dessa verktyg ger dig möjligheten att kontrollera DNS-namnmatchningen från olika platser runt om i världen. Gör en sökning på ”DNS-sökning” exempel. Tjänster från tredje part som Gomez eller Huvudtal kan användas för att bekräfta att dina profiler distribuerar trafik som förväntat.

## <a name="next-steps"></a>Nästa steg

* [Om Traffic Manager routningsmetoder](traffic-manager-routing-methods.md)
* [Prestandaöverväganden för Traffic Manager](traffic-manager-performance-considerations.md)
* [Felsök degraderat tillstånd i Traffic Manager](traffic-manager-troubleshooting-degraded.md)

<!-- Update_Description: update meta properties -->