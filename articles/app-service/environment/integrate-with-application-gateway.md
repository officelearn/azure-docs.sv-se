---
title: Integrera med Application Gateway
description: Lär dig mer om hur du integrerar en app i din ILB App Service Environment med en Application Gateway i den här end-to-end-genomgången.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e4838597c50898748eb4b33e81ff22eaeea37b30
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476901"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrera en ILB App Service-miljö med Azure Application Gateway #

[App servicemiljön](./intro.md) är en distribution av Azure App Service i undernätet i en kunds virtuella Azure-nätverk. Den kan distribueras med en offentlig eller privat slutpunkt för appåtkomst. Distributionen av App Service-miljön med en privat slutpunkt (det vill ha en intern belastningsutjämnare) kallas en ILB App Service Environment.  

Brandväggar för webbprogram hjälper till att skydda dina webbprogram genom att inspektera inkommande webbtrafik för att blockera SQL-injektioner, cross-site scripting, malware uppladdningar & ansökan DDoS och andra attacker. Den granskar också svaren från backend-webbservrar för dataförbränningsprevention (DLP). Du kan hämta en WAF-enhet från Azure marketplace eller så kan du använda [Azure Application Gateway][appgw].

Azure Application Gateway är en virtuell installation som ger lager 7 belastningsutjämning, TLS/ SSL avlastning och webbprogram brandvägg (WAF) skydd. Den kan lyssna på en offentlig IP-adress och dirigera trafik till programslutpunkten. Följande information beskriver hur du integrerar en WAF-konfigurerad programgateway med en app i en ILB App Service Environment.  

Integreringen av programgatewayen med ILB App Service Environment är på appnivå. När du konfigurerar programgatewayen med din ILB App Service Environment gör du det för specifika appar i ILB App Service Environment. Den här tekniken gör det möjligt att vara värd för säkra program med flera skikt i en enda ILB App Service Environment.  

![Programgateway som pekar på appen i en ILB-apptjänstmiljö][1]

I den här genomgången kommer du att:

* Skapa en Azure Application Gateway.
* Konfigurera programgatewayen så att den pekar på en app i ILB App Service Environment.
* Konfigurera appen så att den ärar det anpassade domännamnet.
* Redigera det offentliga DNS-värdnamnet som pekar på programgatewayen.

## <a name="prerequisites"></a>Krav

Om du vill integrera din Application Gateway med din ILB App Service Environment behöver du:

* En ILB App Service Miljö.
* En app som körs i ILB App Service Environment.
* Ett domännamn för internetrdigerbart som ska användas med din app i ILB App Service Environment.
* ILB-adressen som din ILB App Service Environment använder. Den här informationen finns i apptjänstmiljöportalen under **Inställningar** > **IP-adresser:**

    ![Exempellista över IP-adresser som används av ILB App Service Environment][9]
    
* Ett offentligt DNS-namn som används senare för att peka på programgatewayen. 

Mer information om hur du skapar en ILB App Service Environment finns i [Skapa och använda en ILB App Service Environment][ilbase].

Den här artikeln förutsätter att du vill ha en Programgateway i samma virtuella Azure-nätverk där App Service-miljön distribueras. Innan du börjar skapa programgatewayen väljer eller skapar du ett undernät som du ska använda för att vara värd för gatewayen. 

Du bör använda ett undernät som inte är det som heter GatewaySubnet. Om du placerar Application Gateway i GatewaySubnet kan du inte skapa en virtuell nätverksgateway senare. 

Du kan inte heller placera gatewayen i undernätet som din ILB App Service Environment använder. App Service-miljön är det enda som kan finnas i det här undernätet.

## <a name="configuration-steps"></a>Konfigurationssteg ##

1. Gå till **Ny** > **nätverksprogramgateway****Network** > i Azure-portalen .

2. I **området Grunderna:**

   a. För **Namn**anger du namnet på Programgatewayen.

   b. För **Nivå**väljer du **WAF**.

   c. För **Prenumeration**väljer du samma prenumeration som det virtuella nätverket apptjänstmiljö använder.

   d. Skapa eller markera resursgruppen för **resursgruppen.**

   e. För **Plats**väljer du platsen för det virtuella nätverket För App Service Environment.

   ![Grunderna i nya programgateway][2]

3. I området **Inställningar:**

   a. För **virtuellt nätverk**väljer du det virtuella nätverket App Service Environment.

   b. För **Undernät**väljer du det undernät där programgatewayen måste distribueras. Använd inte GatewaySubnet, eftersom det förhindrar skapandet av VPN-gateways.

   c. För **IP-adresstyp**väljer du **Offentlig**.

   d. För **offentlig IP-adress**väljer du en offentlig IP-adress. Om du inte har en, skapa en nu.

   e. För **Protokoll**väljer du **HTTP** eller **HTTPS**. Om du konfigurerar för HTTPS måste du ange ett PFX-certifikat.

   f. För **brandvägg för webbprogram**kan du aktivera brandväggen och även ställa in den för **identifiering** eller **förebyggande som** du vill.

   ![Inställningar för nya programgateway][3]
    
4. Granska inställningarna i avsnittet **Sammanfattning** och välj **OK**. Det kan ta lite mer än 30 minuter innan installationen är klar.  

5. När installationen av Application Gateway har slutförts går du till portalen för Application Gateway. Välj **Backend-pool**. Lägg till ILB-adressen för din ILB App Service Environment.

   ![Konfigurera serverda pool][4]

6. När processen med att konfigurera backend-poolen är klar väljer du **Hälsoavsökningar**. Skapa en hälsoavsökning för det domännamn som du vill använda för din app. 

   ![Konfigurera hälsotillståndsavsökningar][5]
    
7. När processen med att konfigurera hälsoavsökningarna är klar väljer du **HTTP-inställningar**. Redigera de befintliga inställningarna, välj **Använd anpassad avsökning**och välj den avsökning som du konfigurerade.

   ![Konfigurera HTTP-inställningar][6]
    
8. Gå till avsnittet Översikt **för programgateway** och kopiera den offentliga IP-adressen som programgatewayen använder. Ange IP-adressen som en A-post för appens domännamn eller använd DNS-namnet för den adressen i en CNAME-post. Det är enklare att välja den offentliga IP-adressen och kopiera den från den offentliga IP-adressens användargränssnitt i stället för att kopiera den från länken i avsnittet Översikt **för Programgateway.** 

   ![Portal för programgateway][7]

9. Ange det anpassade domännamnet för din app i din ILB App Service Environment. Gå till din app i portalen och välj **Anpassade domäner**under **Inställningar**.

   ![Ange anpassat domännamn i appen][8]

Det finns information om hur du anger anpassade domännamn för dina webbappar i artikeln [Ange anpassade domännamn för webbappen][custom-domain]. Men för en app i en ILB App Service Environment finns det ingen validering på domännamnet. Eftersom du äger DNS som hanterar appslutpunkterna kan du placera vad du vill där. Det anpassade domännamnet som du lägger till i det här fallet behöver inte finnas i DNS-datorn, men det måste fortfarande konfigureras med appen. 

När installationen är klar och du har tillåtit en kort tid för dns-ändringarna att spridas kan du komma åt din app med hjälp av det anpassade domännamnet som du skapade. 


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
