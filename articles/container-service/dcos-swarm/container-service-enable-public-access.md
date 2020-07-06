---
title: FÖRÅLDRAD Aktivera åtkomst till appen Azure DC/OS container
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "61457383"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>FÖRÅLDRAD Aktivera offentlig åtkomst till ett Azure Container Service-program

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Alla DC/OS-behållare i den [offentliga](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) ACS-lagringspoolen exponeras automatiskt för Internet. Som standard öppnas portarna **80**, **443**, **8080** och alla (offentliga) behållare som lyssnar på dessa portar är tillgängliga. Den här artikeln visar hur du öppnar fler portar för dina program i Azure Container Service.

## <a name="open-a-port-portal"></a>Öppna en port (portal)
Först måste vi öppna den port som vi vill ha.

1. Logga in på Portal.
2. Hitta resurs gruppen som du har distribuerat Azure Container Service till.
3. Välj agent belastnings utjämning (som heter liknar **xxxx-Agent-LB-xxxx**).
   
    ![Belastnings utjämning för Azure Container Service](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Klicka på **avsökningar** och **Lägg sedan till**.
   
    ![Avsökningar för Azure Container Service Load Balancer](./media/container-service-enable-public-access/add-probe.png)
5. Fyll i avsöknings formuläret och klicka på **OK**.
   
   | Fält | Beskrivning |
   | --- | --- |
   | Name |Ett beskrivande namn på avsökningen. |
   | Port |Porten för den behållare som ska testas. |
   | Sökväg |(I HTTP-läge) Den relativa webbplats Sök vägen till avsökningen. HTTPS stöds inte. |
   | Intervall |Hur lång tid det tar mellan avsöknings försök, i sekunder. |
   | Tröskelvärde för ej felfri |Antal avsöknings försök i följd innan du överväger den felaktiga behållaren. |
6. Gå tillbaka till egenskaperna för agentens belastningsutjämnare, klicka på **belastnings Utjämnings regler** och **Lägg sedan till**.
   
    ![Regler för belastnings utjämning i Azure Container Service](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Fyll i formuläret för belastnings utjämning och klicka på **OK**.
   
   | Fält | Beskrivning |
   | --- | --- |
   | Name |Ett beskrivande namn på belastningsutjämnaren. |
   | Port |Den offentliga inkommande porten. |
   | Backend-port |Den interna offentliga porten för den behållare som trafiken ska skickas till. |
   | Serverdelspool |Behållarna i den här poolen är målet för den här belastningsutjämnaren. |
   | Avsökning |Avsökningen som används för att fastställa om ett mål i **backend-poolen** är felfritt. |
   | Sessionspermanens |Fastställer hur trafik från en klient ska hanteras under sessionens varaktighet.<br><br>**Ingen**: efterföljande förfrågningar från samma klient kan hanteras av alla behållare.<br>**Klientens IP**: efterföljande förfrågningar från samma klient-IP hanteras av samma behållare.<br>**Klientens IP och protokoll**: efterföljande förfrågningar från samma klient-IP-och protokoll kombination hanteras av samma behållare. |
   | Timeout för inaktivitet |(Endast TCP) I minuter är tiden för att hålla en TCP/HTTP-klient öppen utan att förlita dig på *Keep-Alive-* meddelanden. |

## <a name="add-a-security-rule-portal"></a>Lägg till en säkerhets regel (portal)
Därefter måste vi lägga till en säkerhets regel som dirigerar trafik från vår öppna port genom brand väggen.

1. Logga in på Portal.
2. Hitta resurs gruppen som du har distribuerat Azure Container Service till.
3. Välj den **offentliga** agentens nätverks säkerhets grupp (som heter liknar **xxxx-agent-Public-NSG-xxxx**).
   
    ![Nätverks säkerhets grupp för Azure Container Service](./media/container-service-enable-public-access/agent-nsg.png)
4. Välj **inkommande säkerhets regler** och **Lägg sedan till**.
   
    ![Regler för nätverks säkerhets grupper för Azure Container Service](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Fyll i brand Väggs regeln för att tillåta din offentliga port och klicka på **OK**.
   
   | Fält | Beskrivning |
   | --- | --- |
   | Name |Ett beskrivande namn på brand Väggs regeln. |
   | Prioritet |Regelns prioritets rang. Ju lägre siffra desto högre prioritet. |
   | Källa |Begränsa det inkommande IP-adressintervall som ska tillåtas eller nekas av den här regeln. Använd **valfri** om du inte vill ange en begränsning. |
   | Tjänst |Välj en uppsättning fördefinierade tjänster som den här säkerhets regeln gäller för. Använd annars **anpassad** för att skapa en egen. |
   | Protokoll |Begränsa trafik baserat på **TCP** eller **UDP**. Använd **valfri** om du inte vill ange en begränsning. |
   | Portintervall |När **tjänsten** är **anpassad**anger det port intervall som regeln påverkar. Du kan använda en enda port, till exempel **80**, eller ett intervall som **1024-1500**. |
   | Åtgärd |Tillåt eller neka trafik som uppfyller kriterierna. |

## <a name="next-steps"></a>Nästa steg
Lär dig mer om skillnaden mellan [offentliga och privata DC/OS-agenter](container-service-dcos-agents.md).

Läs mer information om hur [du hanterar dina DC/OS-behållare](container-service-mesos-marathon-ui.md).

