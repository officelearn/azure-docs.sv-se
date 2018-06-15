---
title: Integrera din ILB Apptjänst-miljö med Azure Programgateway
description: Genomgång av hur du integrerar en app i din ILB Apptjänst-miljö med en Programgateway
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
ms.openlocfilehash: 31aea1d19ed6da856bb5fc634a919819513cb6b2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "30833592"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrera din ILB Apptjänst-miljö med Azure Programgateway #

Den [Apptjänstmiljö](./intro.md) är en distribution av Azure App Service i undernätet för kundens virtuella Azure-nätverket. Den kan användas med en offentlig eller privat slutpunkt för åtkomst till appen. Distributionen av Apptjänst-miljön med en privat slutpunkt (det vill säga en intern belastningsutjämnare) kallas en ILB Apptjänst-miljö.  

Webbprogrammet brandväggar hjälp säkra webbprogram genom att kontrollera inkommande webbtrafik att blockera SQL injektionerna globala webbplatsskript, skadlig kod överföringar & DDoS-program och andra attacker. Den kontrollerar också svar från backend-webbservrar för Data går förlorade förebyggande (DLP). Du kan hämta en Brandvägg enhet från Azure marketplace eller kan du använda den [Azure Programgateway][appgw].

Azure Application Gateway är en virtuell installation som tillhandahåller layer 7 belastningsutjämning, SSL-avlastning och web application firewall (Brandvägg) skydd. Det kan lyssna på en offentlig IP-adress och vidarebefordra trafik till programslutpunkten. Följande information beskriver hur du integrerar en konfigurerad Brandvägg Programgateway med en app i en ILB Apptjänst-miljö.  

Integrering av programgatewayen med ILB Apptjänst-miljön är på en appnivå. När du konfigurerar programgatewayen med din ILB Apptjänst-miljö kan vill du göra det för specifika appar i din ILB Apptjänst-miljö. Den här tekniken kan värd säkra flera program i en enda ILB Apptjänst-miljö.  

![Programgateway pekar till appen på en ILB Apptjänst-miljö][1]

I den här genomgången kommer du att:

* Skapa en Gateway för Azure-program.
* Konfigurera Programgateway peka till en app i din ILB Apptjänst-miljö.
* Konfigurera appen för att respektera det anpassade domännamnet.
* Redigera den offentliga DNS-värdnamn som pekar på din Programgateway.

## <a name="prerequisites"></a>Krav

Om du vill integrera din Programgateway med din ILB Apptjänst-miljö, behöver du:

* En ILB Apptjänst-miljö.
* En app som körs i ILB Apptjänst-miljön.
* Ett dirigerbart domännamn som ska användas med din app i ILB Apptjänst-miljön.
* ILB-adressen som använder din ILB Apptjänst-miljö. Den här informationen är i Apptjänst-miljö portal under **inställningar** > **IP-adresser**:

    ![Exempel lista över IP-adresser som används av ILB Apptjänst-miljön][9]
    
* Offentliga DNS-namnet som används senare för att peka till Application Gateway. 

Mer information om hur du skapar en ILB Apptjänst-miljö finns [skapa och använda en ILB Apptjänstmiljö][ilbase].

Den här artikeln förutsätter att du vill att en Programgateway i samma virtuella Azure-nätverket där Apptjänst-miljön har distribuerats. Innan du börjar skapa Programgatewayen, Välj eller skapa ett undernät som ska användas som värd för gatewayen. 

Du bör använda ett undernät som är inte en med namnet GatewaySubnet. Om du placerar Programgatewayen i GatewaySubnet kommer du att det går inte att skapa en virtuell nätverksgateway senare. 

Du kan också placera gatewayen i undernätet som använder din ILB Apptjänst-miljö. Apptjänst-miljön är det enda som kan vara i det här undernätet.

## <a name="configuration-steps"></a>Konfigurationssteg ##

1. I Azure-portalen går du till **ny** > **nätverk** > **Programgateway**.

2. I den **grunderna** område:

   a. För **namn**, ange namnet på Programgatewayen.

   b. För **nivå**väljer **Brandvägg**.

   c. För **prenumeration**, Välj samma prenumeration som använder det virtuella nätverket för Apptjänst-miljö.

   d. För **resursgruppen**, skapa eller välja en resursgrupp.

   e. För **plats**, välj platsen för det virtuella nätverket för Apptjänst-miljö.

   ![Nya Programgateway skapa grunderna][2]

3. I den **inställningar** område:

   a. För **för virtuella nätverk**, Välj det virtuella nätverket för Apptjänst-miljö.

   b. För **undernät**, markerar du undernätet där Programgatewayen måste distribueras. Använd inte GatewaySubnet, eftersom den förhindrar att skapa VPN-gatewayer.

   c. För **IP-adresstypen**väljer **offentliga**.

   d. För **offentliga IP-adressen**, Välj en offentlig IP-adress. Om du inte har någon kan du skapa en nu.

   e. För **protokollet**väljer **HTTP** eller **HTTPS**. Om du konfigurerar för HTTPS, måste du ange ett PFX-certifikat.

   f. För **Brandvägg för webbaserade program**, du kan aktivera brandväggen och även ange antingen **identifiering** eller **förebyggande** efter önskemål.

   ![Nya inställningar för skapande av Programgateway][3]
    
4. I den **sammanfattning** , granskar du inställningarna och välj **OK**. Application Gateway kan ta lite mer än 30 minuter att slutföra installationen.  

5. Gå till portalen för Programgateway när installationen är klar i Application Gateway. Välj **serverdelspool**. Lägg till ILB-adress för din ILB Apptjänst-miljö.

   ![Konfigurera serverdelspool][4]

6. När konfigureringen av backend-adresspool har slutförts, Välj **hälsoavsökning**. Skapa en hälsoavsökningen för det domännamn som du vill använda för din app. 

   ![Konfigurera hälsotillståndsavsökningar][5]
    
7. När konfigureringen av din hälsoavsökningar har slutförts, Välj **HTTP-inställningar**. Redigera de befintliga inställningarna genom att markera **Använd anpassad avsökningen**, och välj avsökningen som du har konfigurerat.

   ![Konfigurera HTTP-inställningar][6]
    
8. Gå till den Programgateway **översikt** avsnitt och kopiera den offentliga IP-adressen som använder Application Gateway. Ange IP-adressen som en A-post för din app-domännamnet eller Använd DNS-namn för den adressen i en CNAME-post. Det är lättare att välja den offentliga IP-adressen och kopiera den från Gränssnittet för den offentliga IP-adressen i stället för att kopiera den från länken i den Programgateway **översikt** avsnitt. 

   ![Programmet Gateway-portalen][7]

9. Ange det anpassade domännamnet för din app i din ILB Apptjänst-miljö. Gå till din app i portalen och under **inställningar**väljer **anpassade domäner**.

   ![Ange namnet på domänen i appen][8]

Det finns information om hur anpassade domännamn för web apps i artikeln [definiera anpassade domännamn för din webbapp][custom-domain]. Men det finns inte någon validering på domännamnet för en app i en ILB Apptjänst-miljö. Eftersom du äger DNS som hanterar app-slutpunkter kan placera du vad du vill i dit. Det anpassade domännamnet som du lägger till i det här fallet behöver inte finnas i din DNS-Server, men fortfarande behöver konfigureras med din app. 

När installationen är klar och du har angett en kort tidsperiod för att ändringarna DNS sprids, kan du komma åt din app med hjälp av det anpassade domännamnet som du skapade. 


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
