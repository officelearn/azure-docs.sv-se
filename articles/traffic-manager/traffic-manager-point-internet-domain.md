<properties
   pageTitle="Peka företagets Internetdomän på en Traffic Manager-domän | Microsoft Azure"
   description="Den här artikeln beskriver hur du pekar ditt företags domännamn på ett Traffic Manager-domännamn."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# Peka företagets Internetdomän på en Azure Traffic Manager-domän

Om du vill peka företagets domännamn på ett Traffic Manager-domännamn ändrar du DNS-resursposten på din Internet-DNS-server så att den använder CNAME-posttypen, som mappar företagets domännamn till domännamnet för din Traffic Manager-profil. Du kan se Traffic Manager-domännamnet i avsnittet **Allmänt** på konfigurationssidan för Traffic Manager-profilen.

Om du till exempel vill peka företagsdomännamnet contoso.trafficmanager.net på Traffic Manager-domännamnet www.contoso.com uppdaterar du DNS-resursposten enligt följande:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Alla trafikförfrågningar till *www.contoso.com* kommer nu att dirigeras till *contoso.trafficmanager.net*.

>[AZURE.IMPORTANT] Du kan inte peka en andranivådomän, t.ex. *contoso.com*, på Traffic Manager-domänen. Det här är en begränsning i DNS-protokollet, som inte tillåter CNAME-poster för domännamn på den andra nivån.

## Nästa steg

[Traffic Manager-dirigeringsmetoder](traffic-manager-routing-methods.md)

[Traffic Manager – Inaktivera, aktivera eller ta bort en profil](disable-enable-or-delete-a-profile.md)

[Traffic Manager – Inaktivera eller aktivera en slutpunkt](disable-or-enable-an-endpoint.md)



<!--HONumber=sep16_HO1-->


