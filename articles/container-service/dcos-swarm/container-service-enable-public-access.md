---
title: "Aktivera åtkomst till Azure DC/OS behållaren app"
description: "Så här aktiverar du offentlig åtkomst till DC/OS-behållare i Azure Container Service."
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: aedc97335a0b9ad00cf653477b62bf530b556900
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="enable-public-access-to-an-azure-container-service-application"></a>Aktivera offentlig åtkomst till ett Azure Container Service-program

Alla DC/OS-behållare i ACS [offentlig agent poolen](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) automatiskt exponerad mot internet. Som standard portarna **80**, **443**, **8080** öppnas, och (offentlig)-behållare som lyssnar på de portarna som är tillgängliga. Den här artikeln visar hur du öppnar flera portar för dina program i Azure Container Service.

## <a name="open-a-port-portal"></a>Öppna en port (portal)
Vi måste först öppna porten som vi vill.

1. Logga in på portalen.
2. Hitta den resursgrupp som du har distribuerat Azure Container Service till.
3. Välj agentens belastningsutjämnare (som heter liknar **XXXX-agent-lb-XXXX**).
   
    ![Azure container service-belastningsutjämnare](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Klicka på **avsökningar** och sedan **lägga till**.
   
    ![Azure container service-belastningsutjämnaren avsökningar](./media/container-service-enable-public-access/add-probe.png)
5. Fyll i formuläret avsökning och klicka på **OK**.
   
   | Fält | Beskrivning |
   | --- | --- |
   | Namn |Ett beskrivande namn på avsökningen. |
   | Port |Porten för att testa behållaren. |
   | Sökväg |(När i HTTP-läge) Relativa webbplats sökvägen till avsökning. HTTPS stöds inte. |
   | Intervall |Hur lång tid mellan avsökningen försök i sekunder. |
   | Tröskelvärde för ohälsosamt värde |Antal på varandra följande avsökningen försöker innan behållaren feltillstånd. |
6. Tillbaka på egenskaperna för agentens belastningsutjämnare, klickar du på **belastningsutjämningsregler** och sedan **Lägg till**.
   
    ![Azure container service regler för inläsning av belastningsutjämnare](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Fyll i formuläret belastningen belastningsutjämnaren och klicka på **OK**.
   
   | Fält | Beskrivning |
   | --- | --- |
   | Namn |Ett beskrivande namn på belastningsutjämnaren. |
   | Port |Den offentliga porten för inkommande. |
   | Backend-port |Internt offentliga porten för att dirigera trafik till behållaren. |
   | Serverdelspool |Behållare i den här poolen är målet för denna belastningsutjämning. |
   | Avsökningen |Avsökningen används för att avgöra om ett mål i den **serverdelspool** är felfri. |
   | Persistence för session |Anger hur trafik från en klient ska hanteras för hela sessionen.<br><br>**Ingen**: efterföljande förfrågningar från samma klient kan hanteras av en behållare.<br>**Klienten IP**: efterföljande förfrågningar från samma klient-IP hanteras av samma behållare.<br>**Klienten IP och protokoll**: efterföljande förfrågningar från samma klient IP och protokoll kombination hanteras av samma behållare. |
   | Tidsgränsen för inaktivitet |(TCP) Tid att behålla en TCP/HTTP-klient öppna i minuter, utan *keep-alive* meddelanden. |

## <a name="add-a-security-rule-portal"></a>Lägg till en säkerhetsregel (portal)
Vi måste sedan lägga till en anslutningssäkerhetsregel som dirigerar trafik från våra öppna port i brandväggen.

1. Logga in på portalen.
2. Hitta den resursgrupp som du har distribuerat Azure Container Service till.
3. Välj den **offentliga** agent nätverkssäkerhetsgruppen (som heter liknar **XXXX-agent-offentliga-nsg-XXXX**).
   
    ![Azure container service-nätverkssäkerhetsgrupp](./media/container-service-enable-public-access/agent-nsg.png)
4. Välj **inkommande säkerhetsregler** och sedan **Lägg till**.
   
    ![Azure container service regler för nätverkssäkerhetsgrupper](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Fyll i brandväggsregeln till att tillåta den offentliga porten och klickar på **OK**.
   
   | Fält | Beskrivning |
   | --- | --- |
   | Namn |Ett beskrivande namn för brandväggsregeln. |
   | Prioritet |Prioritet rangordningen för regeln. Ju lägre det nummer desto högre prioritet. |
   | Källa |Begränsa inkommande IP-adressintervall om du vill ska tillåtas eller nekas av den här regeln. Använd **alla** inte ange en begränsning. |
   | Tjänst |Välj en uppsättning fördefinierade tjänster som denna regel gäller. Använd annars **anpassad** att skapa en egen. |
   | Protokoll |Begränsa trafik utifrån **TCP** eller **UDP**. Använd **alla** inte ange en begränsning. |
   | Portintervall |När **Service** är **anpassade**, anger intervallet för de portar som påverkar den här regeln. Du kan använda en enskild port, till exempel **80**, eller ett intervall som **1024 1500**. |
   | Åtgärd |Tillåt eller neka trafik som uppfyller villkoren. |

## <a name="next-steps"></a>Nästa steg
Lär dig mer om skillnaden mellan [offentliga och privata DC/OS-agenterna](container-service-dcos-agents.md).

Läs mer om [hantera DC/OS-behållare](container-service-mesos-marathon-ui.md).

