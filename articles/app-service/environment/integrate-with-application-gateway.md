---
title: Integrera din ILB ASE med en Azure Programgateway
description: "Genomgång av hur du integrerar en app i ILB-ASE med din Azure Programgateway"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: ccompy
ms.openlocfilehash: eedad8824add7fe425d34975dab640fbee82c2bc
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2017
---
# <a name="integrating-your-ilb-ase-with-an-application-gateway"></a>Integrera din ILB ASE med en Programgateway #

Den [Azure App Service miljö (ASE)](./intro.md) är en distribution av Azure App Service i undernätet för en kund Azure Virtual Network. Den kan användas med en offentlig eller privat slutpunkt för åtkomst till appen. Distributionen av ASE med en privat slutpunkt kallas en ILB ASE.  
Azure Application Gateway är en virtuell installation som tillhandahåller layer 7 belastningsutjämning, SSL-avlastning och Brandvägg skydd. Det kan lyssna på en offentlig IP-adress och vidarebefordra trafik till programslutpunkten. Följande information beskriver hur du integrerar en Brandvägg konfigurerad Programgateway med en app på en ILB ASE.  

Integrering av Programgatewayen med ILB ASE är på en appnivå.  När du konfigurerar Programgatewayen med ILB-ASE vill du göra det för specifika appar i ILB-ASE. Detta gör att värd säker program med flera klienter i en enda ILB ASE.  

![Programgateway pekar till appen på en ILB ASE][1]

I den här genomgången kommer du att:

* Skapa en Programgateway
* Konfigurera Programgateway att peka till en app på ILB-ASE
* Konfigurera appen för att respektera det anpassade domännamnet
* Redigera din offentliga DNS-värdnamn som pekar på en Programgateway

Om du vill integrera din Programgateway med ILB-ASE, behöver du:

* en ILB ASE
* en app som körs i ILB ASE
* ett dirigerbart domännamn som ska användas med din app i ILB ASE
* ILB-adress som används av ILB-ASE (detta är i ASE portal under **Inställningar -> IP-adresser**)

    ![IP-adresser som används av ILB-ASE][9]
    
* offentliga DNS-namnet som används senare för att peka på din Programgateway 

För information om hur du skapar en ILB ASE läsa dokumentet [skapa och använda en ILB ASE][ilbase]

Den här handboken förutsätts att du vill använda en Programgateway i samma Azure virtuella nätverk som ASE distribueras till. Välj eller skapa ett undernät som ska användas som värd för Programgatewayen innan du börjar skapa Application Gateway. Du bör använda ett undernät som är inte en med namnet GatewaySubnet eller det undernät som används av ILB ASE.
Om du placerar Programgatewayen i GatewaySubnet kommer sedan du inte att skapa en virtuell nätverksgateway senare. Du också kan inte försätta den i det undernät som används av ILB-ASE eftersom ASE är det enda som kan vara i undernätet.

## <a name="steps-to-configure"></a>Steg för att konfigurera ##

1. Från inom Azure-portalen går du till **New > nätverk > Programgateway** 
    1. Ange:
        1. Namnet på programmet-Gateway
        1. Välj Brandvägg
        1. Välj samma prenumeration som används för VNet ASE
        1. Skapa eller välja en resursgrupp
        1. Välj den plats där ASE VNet i

    ![Nya program gateway skapa grunderna][2]   
    1. I uppsättningen inställningar området:
        1. ASE VNet
        1. Undernätet Programgatewayen måste distribueras till. Göra något GatewaySubnet som den kommer att skapa VPN-gatewayer
        1. Välj offentlig
        1. Välj en offentlig IP-adress. Om du inte har något sedan skapa en just nu
        1. Konfigurera för HTTP eller HTTPS. Om du konfigurerar för HTTPS måste du ange ett PFX-certifikat
        1. Välj inställningar för webbprogram fireway. Här du kan aktivera brandväggen och även ange för antingen identifiering eller förebyggande som du ser får plats.

    ![Ny gateway skapa programinställningar][3]
    
    1. I sammanfattningen-granskningen väljer **Ok**. Det kan ta lite mer än 30 minuter för din Programgateway att slutföra installationen.  

2. När installationen är klar i Application Gateway går du till Application Gateway-portalen. Välj **serverdelspool**.  Lägg till ILB-adress för ILB-ASE.

    ![Konfigurera serverdelspool][4]

3. När bearbetningen är klar för att konfigurera din serverdelspool Välj **hälsoavsökning**. Skapa en hälsoavsökningen för domännamnet som du vill använda för din app. 

    ![Konfigurera hälsotillståndsavsökningar][5]
    
4. När bearbetningen är klar för att konfigurera din hälsoavsökningar, Välj **HTTP-inställningar**.  Redigera den befintliga inställningen det genom att markera **Använd anpassad avsökningen**, och välj avsökningen som du har konfigurerat.

    ![Konfigurera HTTP-inställningar][6]
    
5. Gå till Programgatewayen **översikt**, och kopiera den offentliga IP-adressen som används för Application Gateway.  Ange IP-adressen som en A-post för din app-domännamnet eller Använd DNS-namn för den adressen i en CNAME-post.  Det är lättare att välja den offentliga IP-adressen och kopiera den från den offentliga IP-adressen Användargränssnittet i stället för att kopiera den från länken på avsnittet Programöversikt för Gateway. 

    ![Programmet Gateway-portalen][7]

6. Ange det anpassade domännamnet för din app i ILB-ASE.  Gå till din app i portalen och under Inställningar väljer **anpassade domäner**

![Ange namnet på domänen i appen][8]

Det finns information om hur anpassade domännamn för web apps här [definiera anpassade domännamn för din webbapp][custom-domain]. Skillnaden med en app i en ASE ILB och dokument, är att det inte finns någon validering på domännamnet.  Eftersom du äger DNS som hanterar app-slutpunkter kan placera du vad du vill i dit. Det anpassade domännamnet som du lägger till i det här fallet behöver inte finnas i din DNS-Server, men fortfarande behöver konfigureras med din app. 

När installationen är klar och du har angett en kort tidsperiod för att ändringarna DNS ska spridas, och du kan komma åt din app av det anpassade domännamnet som du skapade. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
