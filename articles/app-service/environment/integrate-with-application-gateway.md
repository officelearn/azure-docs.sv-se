---
title: Integrera ILB App Service-miljö med Application Gateway – Azure
description: Genomgång av hur du integrerar en app i din ILB App Service Environment med en Application Gateway
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
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ea46b5e57e4e508a3311de8633ae61d346b574eb
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114203"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrera din ILB App Service-miljö med Azure Application Gateway #

Den [App Service Environment](./intro.md) är en distribution av Azure App Service i undernätet för en kunds Azure-nätverk. Den kan distribueras med en offentlig eller privat slutpunkt för åtkomst till appen. Distribution av App Service Environment med en privat slutpunkt (det vill säga en intern belastningsutjämnare) kallas för en ILB App Service Environment.  

Hjälper till att säkra brandväggar för webbaserade program dina webbprogram genom att kontrollera ingående webbtrafik för att blockera SQL-inmatningar, skriptkörning över flera webbplatser, överföring av skadlig kod & program-DDoS och andra attacker. Den kontrollerar även svar från backend-webbservrar för Data skydd mot dataförlust (DLP). Du kan hämta en WAF-enhet från Azure marketplace eller använda den [Azure Application Gateway][appgw].

Azure Application Gateway är en virtuell installation som tillhandahåller layer 7 belastningsutjämning, SSL-avlastning och web application firewall (WAF) skydd. Det kan lyssna på en offentlig IP-adressen och dirigera trafiken till programslutpunkten för ditt. Följande information beskriver hur du integrerar en WAF-konfigurerat application gateway med en app i en ILB App Service Environment.  

Integrering av application gateway med ILB App Service Environment är en app-nivå. När du konfigurerar programgatewayen med ILB App Service Environment kan gör du det för specifika appar i din ILB App Service Environment. Den här tekniken möjliggör som är värd för säkra program i en enda ILB App Service-miljö för flera innehavare.  

![Application gateway som pekar på appen på en ILB App Service Environment][1]

I den här genomgången kommer du att:

* Skapa en Programgateway i Azure.
* Konfigurera Programgatewayen så att den pekar till en app i din ILB App Service Environment.
* Konfigurera din app för att respektera det anpassade domännamnet.
* Redigera den offentliga DNS-värdnamn som pekar på din application gateway.

## <a name="prerequisites"></a>Förutsättningar

För att integrera din Application Gateway med ILB App Service Environment, behöver du:

* En ILB App Service Environment.
* En app som körs i ILB App Service Environment.
* Ett internet dirigerbar domännamn som ska användas med din app i ILB App Service Environment.
* ILB-adressen som använder din ILB App Service Environment. Den här informationen är i App Service Environment-portalen under **inställningar** > **IP-adresser**:

    ![Exempellistan över IP-adresser som används av ILB App Service Environment][9]
    
* En offentlig DNS-namn som används senare för att peka på Application Gateway. 

Mer information om hur du skapar en ILB App Service Environment finns i [skapa och använda en ILB App Service Environment][ilbase].

Den här artikeln förutsätter att du vill att en Application Gateway i samma virtuella Azure-nätverket där App Service Environment har distribuerats. Innan du börjar skapa Application Gateway, Välj eller skapa ett undernät som använder du som värd för gatewayen. 

Du bör använda ett undernät som är inte en med namnet GatewaySubnet. Om du placerar Application Gateway i GatewaySubnet, kommer du att det går inte att skapa en virtuell nätverksgateway senare. 

Du kan också placera gatewayen i det undernät som använder din ILB App Service Environment. App Service Environment är det enda som kan finnas i det här undernätet.

## <a name="configuration-steps"></a>Konfigurationssteg ##

1. I Azure-portalen går du till **New** > **nätverk** > **Application Gateway**.

2. I den **grunderna** området:

   a. För **namnet**, anger du namnet på Application Gateway.

   b. För **nivå**väljer **WAF**.

   c. För **prenumeration**, Välj samma prenumeration som det virtuella nätverket för App Service Environment.

   d. För **resursgrupp**, skapa eller välj resursgruppen.

   e. För **plats**, välj platsen för det virtuella nätverket för App Service Environment.

   ![Ny grunderna för skapande av Application Gateway][2]

3. I den **inställningar** området:

   a. För **virtuellt nätverk**, Välj det virtuella nätverket för App Service Environment.

   b. För **undernät**, Välj det undernät där Programgatewayen måste distribueras. Använd inte GatewaySubnet, eftersom den förhindrar du att VPN-gatewayer.

   c. För **IP-adresstyp**väljer **offentliga**.

   d. För **offentliga IP-adressen**, Välj en offentlig IP-adress. Om du inte har någon kan du skapa en nu.

   e. För **protokollet**väljer **HTTP** eller **HTTPS**. Om du konfigurerar för HTTPS, måste du ange ett PFX-certifikat.

   f. För **Brandvägg för webbaserade program**, du kan aktivera brandväggen och också ange den för antingen **identifiering** eller **Dataförlustskydd** som du vill.

   ![Nya inställningar för skapande av Application Gateway][3]
    
4. I den **sammanfattning** , granskar du inställningarna och väljer **OK**. Application Gateway kan ta lite mer än 30 minuter att slutföra installationen.  

5. När Application Gateway är klar installationen går du till din Application Gateway-portal. Välj **serverdelspool**. Lägg till ILB-adressen för din ILB App Service Environment.

   ![Konfigurera serverdelspoolen][4]

6. När konfigureringen av backend-poolen är klar väljer **hälsoavsökningar**. Skapa en hälsoavsökning för det domännamn som du vill använda för din app. 

   ![Konfigurera hälsotillståndsavsökningar][5]
    
7. När processen med att konfigurera din hälsokontroller av slutpunkter är klar väljer **HTTP-inställningar**. Redigera de befintliga inställningarna genom att markera **Använd anpassad avsökning**, och välj avsökningen som du har konfigurerat.

   ![Konfigurera HTTP-inställningar][6]
    
8. Gå till Application Gateway **översikt** avsnitt och kopiera den offentliga IP-adressen som Application Gateway använder. Ange IP-adress som en A-post för din app-domännamnet eller Använd DNS-namnet för den här adressen i en CNAME-post. Det är lättare att välja den offentliga IP-adressen och kopiera den offentliga IP-adressens användargränssnittet i stället för att kopiera den från länken i Application Gateway **översikt** avsnittet. 

   ![Application Gateway-portalen][7]

9. Ange det anpassade domännamnet för din app i din ILB App Service Environment. Gå till din app i portalen och under **inställningar**väljer **anpassade domäner**.

   ![Ange anpassat domännamn för appen][8]

Det finns information om hur anpassade domännamn för dina webbprogram i artikeln [ställa in anpassade domännamn för din webbapp][custom-domain]. Men det finns inte någon verifiering på domännamnet för en app i en ILB App Service Environment. Du kan ange vad du vill där eftersom du äger DNS som hanterar app-slutpunkter. Det anpassade domännamnet som du lägger till i det här fallet behöver inte finnas i din DNS-Server, men den fortfarande behöver konfigureras med din app. 

När installationen är klar och du har tillåtit en kort tidsperiod för din DNS-ändringarna att spridas, kan du komma åt din app med hjälp av det anpassade domännamnet som du skapade. 


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
[appgw]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
