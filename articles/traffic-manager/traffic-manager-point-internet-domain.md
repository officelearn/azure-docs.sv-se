<properties
    pageTitle="Peka företagets Internetdomän mot ett Traffic Manager-domännamn | Microsoft Azure"
    description="Den här artikeln beskriver hur du pekar ditt företags domännamn på ett Traffic Manager-domännamn."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>


# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Peka företagets Internetdomän mot en Azure Traffic Manager-domän

När du skapar en Traffic Manager-profil, tilldelar Azure automatiskt ett DNS-namn för profilen. För att använda ett namn från din DNS-zon, skapar du en CNAME DNS-post som mappar till domännamnet för din Traffic Manager-profil. Du hittar Traffic Manager-domännamnet i **Allmänt**-avsnittet på konfigurationssidan för Traffic Manager-profilen.

Om du till exempel vill peka namnet www.contoso.com mot Traffic Manager DNS-namnet contoso.trafficmanager.net så skapar du följande DNS-resurspost:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Alla trafikbegäranden för *www.contoso.com* kommer nu att dirigeras till *contoso.trafficmanager.net*.

>[AZURE.IMPORTANT] Du kan inte peka en andranivådomän, t.ex. *contoso.com*, på Traffic Manager-domänen. DNS-protokollstandarder tillåter inte CNAME-poster för andra nivåns domännamn.

## <a name="next-steps"></a>Nästa steg

- [Routningsmetoder för Traffic Manager](traffic-manager-routing-methods.md)
- [Traffic Manager – Inaktivera, aktiver eller ta bort en profil](disable-enable-or-delete-a-profile.md)
- [Traffic Manager – Inaktivera eller aktivera en slutpunkt](disable-or-enable-an-endpoint.md)



<!--HONumber=Oct16_HO3-->


