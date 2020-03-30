---
title: Verifiera Azure Traffic Manager-inställningar
description: I den här artikeln får du lära dig hur du verifierar trafikstyrningsinställningarna och testar trafikroutningsmetoden.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 94ab5e550f0053fa19b9b93f1d67690211543325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938386"
---
# <a name="verify-traffic-manager-settings"></a>Verifiera Traffic Manager-inställningar

Om du vill testa inställningarna för Traffic Manager måste du ha flera klienter på olika platser som du kan köra dina tester från. Sedan kan du sänka slutpunkterna i Traffic Manager-profilen en i taget.

* Ange DNS TTL-värdet lågt så att ändringarna sprids snabbt (till exempel 30 sekunder).
* Lär känna IP-adresserna för dina Azure-molntjänster och webbplatser i den profil du testar.
* Använd verktyg som gör att du kan matcha ett DNS-namn till en IP-adress och visa den adressen.

Du kontrollerar att DNS-namnen löser till IP-adresser för slutpunkterna i din profil. Namnen ska matchas på ett sätt som överensstämmer med den trafikroutningsmetod som definierats i Traffic Manager-profilen. Du kan använda verktyg som **nslookup** eller **gräva** för att matcha DNS-namn.

Följande exempel hjälper dig att testa din Traffic Manager-profil.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Kontrollera Traffic Manager-profilen med nslookup och ipconfig i Windows

1. Öppna ett kommando eller En Windows PowerShell-fråga som administratör.
2. Skriv `ipconfig /flushdns` om du vill tömma DNS-matcharens cacheminne.
3. Skriv `nslookup <your Traffic Manager domain name>`. Följande kommando kontrollerar till exempel domännamnet med prefixet *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Ett typiskt resultat visar följande information:

    + DNS-namnet och IP-adressen för den DNS-server som används för att matcha det här Traffic Manager-domännamnet.
    + Det Traffic Manager-domännamn som du skrev på kommandoraden efter "nslookup" och den IP-adress som Traffic Manager-domänen löser. Den andra IP-adressen är den viktiga att kontrollera. Den bör matcha en offentlig virtuell IP-adress (VIP) för en av molntjänsterna eller webbplatserna i Traffic Manager-profilen som du testar.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Så här testar du routningsmetoden för redundanstrafik

1. Lämna alla slutpunkter uppe.
2. Med en enda klient begär du DNS-lösning för ditt företagsdomännamn med nslookup eller ett liknande verktyg.
3. Kontrollera att den lösta IP-adressen matchar den primära slutpunkten.
4. Ta ner din primära slutpunkt eller ta bort övervakningsfilen så att Traffic Manager tror att programmet är nere.
5. Vänta på DNS Time-to-Live (TTL) för Traffic Manager-profilen plus ytterligare två minuter. Om din DNS TTL till exempel är 300 sekunder (5 minuter) måste du vänta i sju minuter.
6. Rensa DNS-klientcachen och begär DNS-upplösning med nslookup. I Windows kan du tömma DNS-cachen med kommandot ipconfig /flushdns.
7. Kontrollera att den lösta IP-adressen matchar din sekundära slutpunkt.
8. Upprepa processen, få ner varje slutpunkt i tur och ordning. Kontrollera att DNS returnerar IP-adressen för nästa slutpunkt i listan. När alla slutpunkter är nere bör du hämta IP-adressen för den primära slutpunkten igen.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Så här testar du den viktade trafikroutningsmetoden

1. Lämna alla slutpunkter uppe.
2. Med en enda klient begär du DNS-lösning för ditt företagsdomännamn med nslookup eller ett liknande verktyg.
3. Kontrollera att den lösta IP-adressen matchar en av dina slutpunkter.
4. Rensa DNS-klientcachen och upprepa steg 2 och 3 för varje slutpunkt. Du bör se olika IP-adresser returneras för var och en av dina slutpunkter.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Så här testar du routningsmetoden för prestandatrafik

Om du effektivt vill testa en routningsmetod för prestandatrafik måste du ha klienter i olika delar av världen. Du kan skapa klienter i olika Azure-regioner som kan användas för att testa dina tjänster. Om du har ett globalt nätverk kan du fjärr logga in på klienter i andra delar av världen och köra dina tester därifrån.

Alternativt finns det gratis webbaserade DNS-sökning och gräva tjänster tillgängliga. Några av dessa verktyg ger dig möjlighet att kontrollera DNS-namnmatchning från olika platser runt om i världen. Gör en sökning på "DNS-sökning" för exempel. Tjänster från tredje part som Gomez eller Keynote kan användas för att bekräfta att dina profiler distribuerar trafik som förväntat.

## <a name="next-steps"></a>Nästa steg

* [Om trafikhanterare trafikroutningsmetoder](traffic-manager-routing-methods.md)
* [Prestandaöverväganden för Traffic Manager](traffic-manager-performance-considerations.md)
* [Felsöka degraderat Traffic Manager-tillstånd](traffic-manager-troubleshooting-degraded.md)
