---
title: "Verifiera Azure Traffic Manager-inställningar | Microsoft Docs"
description: "Den här artikeln hjälper dig att kontrollera din Traffic Manager-inställningar"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 2180b640-596e-4fb2-be59-23a38d606d12
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: aadff1806a7cb22347283143563467366e857569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="verify-traffic-manager-settings"></a>Kontrollera inställningarna för Traffic Manager

Om du vill testa din Traffic Manager-inställningar som du behöver ha flera klienter på olika platser, från vilken du kör dina tester. Sedan sätta slutpunkterna i Traffic Manager-profilen ned en i taget.

* Ange DNS-TTL-värdet låg så att ändringar sprida snabbt (till exempel 30 sekunder).
* Vet IP-adresserna för dina Azure-molntjänster och webbplatser i den profil som du vill testa.
* Använd verktyg som kan matcha DNS-namnet till en IP-adress och visa den adressen.

Du kontrollerar att matcha DNS-namn till IP-adresser för slutpunkter i din profil. Namnet ska matcha i överensstämmelse med trafikroutningsmetod som definierats i Traffic Manager-profilen. Du kan använda verktyg som **nslookup** eller **gräva** för DNS-namnmatchning.

I följande exempel kan du testa Traffic Manager-profilen.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Kontrollera Traffic Manager-profil med nslookup och ipconfig i Windows

1. Öppna ett kommando eller en Windows PowerShell-Kommandotolken som administratör.
2. Typen `ipconfig /flushdns` att rensa cacheminnet för DNS-matchare.
3. Skriv `nslookup <your Traffic Manager domain name>`. Till exempel följande kommando kontrollerar domännamn med prefixet *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Ett typiskt resultat visas följande information:

    + DNS-namn och IP-adressen för DNS-server som används för att lösa det här domännamnet för Traffic Manager.
    + Traffic Manager-domännamn du angav på kommandoraden efter ”nslookup” och IP-adressen som löser Traffic Manager-domän. Den andra IP-adressen är det viktigt att kontrollera. Den måste matcha en offentlig virtuella IP-adresser (VIP)-adress för molntjänster eller webbplatser i Traffic Manager-profilen som du vill testa.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Hur du testar redundans trafikroutningsmetod

1. Lämna alla slutpunkter upp.
2. Med en enda klient begär DNS-matchning för företagets domännamn med hjälp av nslookup eller en liknande funktion.
3. Se till att matcha IP-adressen matchar slutpunkten som primär.
4. Avslutar primära slutpunkten eller ta bort filen övervakning så att trafik Manager tror att programmet inte är igång.
5. Vänta tills DNS-Time-to-Live (TTL) av trafikhanterarprofilen plus en ytterligare två minuter. Till exempel måste din DNS-TTL är 300 sekunder (fem minuter), du vänta tills sju minuter.
6. Rensa din DNS-klienten cache och begäran DNS-matchning med nslookup. I Windows kan tömma du DNS-cache med kommandot ipconfig/flushdns.
7. Se till att matcha IP-adressen matchar din sekundära slutpunkt.
8. Upprepa processen att stänga av varje slutpunkt i sin tur. Kontrollera att DNS-servern returnerar IP-adressen för nästa slutpunkt i listan. När alla slutpunkter är nere, bör du skaffa IP-adressen för primär slutpunkten igen.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Så här testar du viktad trafikroutningsmetod

1. Lämna alla slutpunkter upp.
2. Med en enda klient begär DNS-matchning för företagets domännamn med hjälp av nslookup eller en liknande funktion.
3. Se till att matcha IP-adressen matchar ett av dina slutpunkter.
4. Rensa din DNS-klientens cacheminne och upprepa steg 2 och 3 för varje slutpunkt. Du bör se olika IP-adresser som returneras för var och en av dina slutpunkter.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Hur du testar prestanda trafikroutningsmetod

För att effektivt testa en routningsmetoden för prestanda-trafik, måste du ha klienter som finns i olika delar av världen. Du kan skapa klienter i olika Azure-regioner som kan användas för att testa dina tjänster. Om du har ett globalt nätverk du logga in på klienter i andra delar av världen via fjärranslutning och kör dina tester därifrån.

Du kan också finns kostnadsfria webbaserade DNS-sökning och gräva tjänster som är tillgängliga. Vissa av dessa verktyg ger dig möjligheten att kontrollera DNS-namnmatchningen från olika platser i världen. Gör en sökning efter ”DNS-sökning” exempel. Tjänster från tredje part som Gomez eller presentation kan användas för att bekräfta att dina profiler distribuerar trafik som förväntat.

## <a name="next-steps"></a>Nästa steg

* [Om Traffic Manager-trafikroutningsmetoder](traffic-manager-routing-methods.md)
* [Prestandaöverväganden för Traffic Manager](traffic-manager-performance-considerations.md)
* [Felsök degraderat tillstånd i Traffic Manager](traffic-manager-troubleshooting-degraded.md)
