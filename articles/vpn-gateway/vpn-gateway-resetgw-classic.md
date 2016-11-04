---
title: Återställa en Azure VPN Gateway | Microsoft Docs
description: Den här artikeln visar hur du återställer din Azure VPN Gateway. Artikeln gäller VPN-gatewayer som har skapats med den klassiska distributionsmodellen.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-service-management

ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: cherylmc

---
# Återställa en Azure VPN Gateway med PowerShell
Den här artikeln visar hur du återställer din Azure VPN Gateway med PowerShell-cmdletar. Dessa anvisningar gäller den klassiska distributionsmodellen. För närvarande kan inte virtuella nätverks-gatewayar som skapas i Resource Manager-distributionsmodellen återställas.

Att återställa Azures VPN-gateway är användbart om du tappar VPN-anslutningen mellan flera platser i en eller flera S2S VPN-tunnlar. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer. När du använder cmdleten *Reset-AzureVNetGateway* startar den om gatewayen. Därefter tillämpar den konfigurationerna för flera platser igen. Gatewayen behåller den offentliga IP-adress den redan har. Det innebär att du inte behöver uppdatera VPN-routerkonfigurationen med en ny offentlig IP-adress för Azure VPN-gatewayen.  

Verifiera nyckelobjekten nedan innan du återställer din gateway för varje IPSec VPN-tunnel (S2S) från plats till plats. Eventuella matchningsfel i objekten resulterar i att S2S VPN-tunnlarna kopplas från. Att kontrollera och korrigera konfigurationerna för dina lokala och Azures VPN-gatewayer besparar dig onödiga omstarter och avbrott i andra fungerande anslutningar på gatewayerna.

Kontrollera följande innan du återställer din gateway.

* Internets IP-adresser (VIP) för både Azures VPN-gateway och den lokala VPN-gatewayen har konfigurerats korrekt i både Azures och lokala VPN-principer.
* Den i förväg delade nyckeln måste vara samma på både Azures och de lokala VPN-gatewayerna.
* Om du använder specifik IPsec-/IKE-konfiguration, till exempel kryptering, hash-algoritmer och PFS (Perfect Forward Secrecy), bör du kontrollera att både Azures och lokala VPN-gatewayer har samma konfigurationer.

## Återställa en VPN Gateway med PowerShell
PowerShell-cmdleten för att återställa Azures VPN-gateway är *Reset-AzureVNetGateway*. Varje Azure VPN-gateway består av två VM-instanser som körs i en aktiv/vänteläges-konfiguration. När kommandot ges startas den aktuella aktiva instansen av Azures VPN-gateway om omedelbart. Det blir en kort paus under redundansen från den aktiva instansen (som startas om) till vänteläges-instansen. Pausen bör vara kortare än en minut. 

I följande exempel återställs Azures VPN-gateway för det virtuella nätverk som kallas ”ContosoVNet”.

        Reset-AzureVNetGateway –VnetName “ContosoVNet” 

        Error          :
        HttpStatusCode : OK
        Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
        Status         : Successful
        RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
        StatusCode     : OK


Om anslutningen inte har återställts efter den första omstarten, utfärdar du samma kommando igen för att starta om den andra VM-instansen (den nya aktiva gatewayen). Om de två omstarterna görs direkt efter varandra, uppstår en något längre paus där båda VM-instanserna (aktiv och vänteläge) startas om. Detta kommer att medföra ett längre uppehåll i VPN-anslutningen, cirka 2–4 minuter, för att de virtuella datorerna ska slutföra omstarterna.

Om du fortfarande har problem med nätverksanslutningen mellan flera platser efter två omstarter, öppnar du ett supportärende från den klassiska Azure-portalen och kontaktar Microsoft Azure-supporten.

## Nästa steg
Se [PowerShell-referens](https://msdn.microsoft.com/library/azure/mt270366.aspx) för mer information om denna cmdlet.

<!--HONumber=sep16_HO1-->


