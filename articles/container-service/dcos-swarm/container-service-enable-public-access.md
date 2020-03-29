---
title: (FÖRÅLDRAD) Aktivera åtkomst till Azure DC/OS-behållarapp
description: Så här aktiverar du offentlig åtkomst till DC/OS-behållare i Azure Container Service.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61457383"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(FÖRÅLDRAD) Aktivera offentlig åtkomst till ett Azure Container Service-program

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Alla DC/OS-behållare i ACS [public agent-poolen](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) exponeras automatiskt för Internet. Som standard öppnas portarna **80**, **443**, **8080** och alla (offentliga) behållarlyssning på dessa portar är tillgängliga. Den här artikeln visar hur du öppnar fler portar för dina program i Azure Container Service.

## <a name="open-a-port-portal"></a>Öppna en port (portal)
Först måste vi öppna den hamn vi vill ha.

1. Logga in på Portal.
2. Hitta resursgruppen som du distribuerade Azure Container Service till.
3. Välj agentbelastningsutjämnaren (som heter **XXXX-agent-lb-XXXX**).
   
    ![Belastningsutjämnare för Azure-behållartjänst](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Klicka på **Avsökningar** och **lägg**till .
   
    ![Belastningsutjämnaravsökningar för Azure-behållartjänst](./media/container-service-enable-public-access/add-probe.png)
5. Fyll i avsökningsformuläret och klicka på **OK**.
   
   | Field | Beskrivning |
   | --- | --- |
   | Namn |Ett beskrivande namn på sonden. |
   | Port |Hamnen i behållaren att testa. |
   | Sökväg |(I HTTP-läge) Den relativa webbplatssökvägen till avsökningen. HTTPS stöds inte. |
   | Intervall |Tiden mellan avsökningsförsök, i sekunder. |
   | Felfritt tröskelvärde |Antal på varandra följande avsökningsförsök innan du överväger att behållaren inte är fel. |
6. Tillbaka på egenskaperna för agenten belastningsutjämnare, klicka **på Belastningsutjämning regler** och sedan **Lägga till**.
   
    ![Belastningsutjämna regler för Azure-behållartjänst](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Fyll i formuläret belastningsutjämnare och klicka på **OK**.
   
   | Field | Beskrivning |
   | --- | --- |
   | Namn |Ett beskrivande namn på belastningsutjämnaren. |
   | Port |Den offentliga inkommande porten. |
   | Backend-port |Den interna-offentliga porten av behållaren som ska ruttstrafiken till. |
   | Serverdelspool |Behållarna i den här poolen kommer att vara målet för den här belastningsutjämnaren. |
   | Avsökning |Avsökningen som används för att avgöra om ett mål i **backend-poolen** är felfri. |
   | Sessionspermanens |Bestämmer hur trafik från en klient ska hanteras under hela sessionen.<br><br>**Ingen**: På varandra följande begäranden från samma klient kan hanteras av valfri behållare.<br>**Klient-IP:** På varandra följande begäranden från samma klient-IP hanteras av samma behållare.<br>**Klient-IP och protokoll:** På varandra följande begäranden från samma klient-IP- och protokollkombination hanteras av samma behållare. |
   | Tidsgränsen för inaktiv |(Endast TCP) På några minuter är det dags att hålla en TCP/HTTP-klient öppen utan att förlita sig på *keep-alive-meddelanden.* |

## <a name="add-a-security-rule-portal"></a>Lägga till en säkerhetsregel (portal)
Därefter måste vi lägga till en säkerhetsregel som dirigerar trafik från vår öppnade port genom brandväggen.

1. Logga in på Portal.
2. Hitta resursgruppen som du distribuerade Azure Container Service till.
3. Välj säkerhetsgruppen **för** public agent-nätverk (som heter **XXXX-agent-public-nsg-XXXX**).
   
    ![Säkerhetsgrupp för Azure-behållartjänstnätverk](./media/container-service-enable-public-access/agent-nsg.png)
4. Välj **Regler för inkommande inkommande och** lägg sedan **till**.
   
    ![Säkerhetsgruppsregler för Azure-behållartjänstnätverk](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Fyll i brandväggsregeln för att tillåta din offentliga port och klicka på **OK**.
   
   | Field | Beskrivning |
   | --- | --- |
   | Namn |Ett beskrivande namn på brandväggsregeln. |
   | Prioritet |Prioritetsrang för regeln. Ju lägre siffra desto högre prioritet. |
   | Källa |Begränsa det inkommande IP-adressintervallet så att det tillåts eller nekas av den här regeln. Använd **Alla** för att inte ange en begränsning. |
   | Tjänst |Välj en uppsättning fördefinierade tjänster som den här säkerhetsregeln gäller för. Annars använder **Custom** för att skapa din egen. |
   | Protokoll |Begränsa trafiken baserat på **TCP** eller **UDP**. Använd **Alla** för att inte ange en begränsning. |
   | Portintervall |När **Tjänsten** är **anpassad**anger anger det portintervall som den här regeln påverkar. Du kan använda en enda port, till exempel **80**eller ett intervall som **1024-1500**. |
   | Åtgärd |Tillåt eller neka trafik som uppfyller villkoren. |

## <a name="next-steps"></a>Nästa steg
Lär dig mer om skillnaden mellan [offentliga och privata DC/OS-agenter](container-service-dcos-agents.md).

Läs mer om [hur du hanterar DC/OS-behållare](container-service-mesos-marathon-ui.md).

