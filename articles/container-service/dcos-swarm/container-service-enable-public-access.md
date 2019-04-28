---
title: (INAKTUELL) Aktivera åtkomst till Azure DC/OS-behållarapp
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457383"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(INAKTUELL) Aktivera offentlig åtkomst till ett Azure Container Service-program

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Alla DC/OS-behållare i ACS [offentliga agentpoolen](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) automatiskt exponerade mot internet. Som standard portarna **80**, **443**, **8080** öppnas, och (offentlig)-behållare som lyssnar på de portarna som är tillgängliga. Den här artikeln visar hur du öppna flera portar för dina program i Azure Container Service.

## <a name="open-a-port-portal"></a>Öppna en port (portal)
Vi måste först öppna porten som vi vill.

1. Logga in på portalen.
2. Hitta resursgruppen som du har distribuerat Azure Container Service till.
3. Välj agentens belastningsutjämnare (som heter liknar **XXXX-agent-lb-XXXX**).
   
    ![Belastningsutjämnare för Azure container service](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Klicka på **avsökningar** och sedan **lägga till**.
   
    ![Azure container service avsökningar av belastningsutjämnare](./media/container-service-enable-public-access/add-probe.png)
5. Fyll i formuläret avsökning och klicka på **OK**.
   
   | Fält | Beskrivning |
   | --- | --- |
   | Namn |Ett beskrivande namn på avsökningen. |
   | Port |Porten för behållaren för att testa. |
   | Sökväg |(När du är i läget för HTTP) Relativa webbplats sökvägen till avsökning. HTTPS stöds inte. |
   | Interval |Tiden mellan avsökningen försök i sekunder. |
   | Tröskelvärde för Ej felfri |Antal upprepade inloggningsförsök innan behållaren feltillstånd. |
6. Tillbaka på egenskaperna för agentens belastningsutjämnare, klickar du på **belastningsutjämningsregler** och sedan **Lägg till**.
   
    ![Azure container service-belastningsutjämningsregler](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Fyll i formuläret för load balancer och klicka på **OK**.
   
   | Fält | Beskrivning |
   | --- | --- |
   | Namn |Ett beskrivande namn för belastningsutjämnaren. |
   | Port |Den offentliga inkommande porten. |
   | Serverdelsport |Intern-offentlig port på behållaren att dirigera trafik till. |
   | Serverdelspool |Behållare i den här poolen är målet för den här belastningsutjämnaren. |
   | Avsökningen |Avsökningen används för att avgöra om ett mål i den **serverdelspool** är felfri. |
   | Sessionspermanens |Anger hur trafik från en klient ska hanteras för hela sessionen.<br><br>**Ingen**: Efterföljande förfrågningar från samma klient kan hanteras av någon behållare.<br>**Klientens IP**: Efterföljande förfrågningar från samma klient-IP hanteras av samma behållare.<br>**Klientens IP och protokoll**: Efterföljande förfrågningar från samma klient IP och protokoll kombination hanteras av samma behållare. |
   | Timeout för inaktivitet |(TCP) Tid att behålla en TCP/HTTP-klient öppna i minuter, utan att behöva *keep-alive* meddelanden. |

## <a name="add-a-security-rule-portal"></a>Lägg till en säkerhetsregel (portal)
Därefter behöver vi lägga till en säkerhetsregel som dirigerar trafik från våra öppnade port genom brandväggen.

1. Logga in på portalen.
2. Hitta resursgruppen som du har distribuerat Azure Container Service till.
3. Välj den **offentliga** nätverkssäkerhetsgrupp kopplad till agenten (som heter liknar **XXXX-agent-public-nsg-XXXX**).
   
    ![Nätverkssäkerhetsgruppen för Azure container service](./media/container-service-enable-public-access/agent-nsg.png)
4. Välj **ingående säkerhetsregler** och sedan **Lägg till**.
   
    ![Azure container service reglerna för nätverkssäkerhetsgrupper](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Fyll brandväggsregel för att tillåta den offentliga porten och klicka på **OK**.
   
   | Fält | Beskrivning |
   | --- | --- |
   | Namn |Ett beskrivande namn på brandväggsregeln. |
   | Prioritet |Prioritet rangordning för regeln. Ju lägre det nummer desto högre prioritet. |
   | Källa |Begränsa inkommande IP-adressintervall för att beviljas eller nekas av den här regeln. Använd **alla** att inte ange en begränsning. |
   | Tjänst |Välj en uppsättning fördefinierade tjänster säkerhetsregelns avser. Annars använda **anpassad** att skapa dina egna. |
   | Protokoll |Begränsa trafik baserat på **TCP** eller **UDP**. Använd **alla** att inte ange en begränsning. |
   | Portintervall |När **Service** är **anpassade**, anger intervallet för de portar som påverkas av den här regeln. Du kan använda en enskild port, till exempel **80**, eller ett intervall som **1024 1500**. |
   | Åtgärd |Tillåter eller nekar trafik som uppfyller villkoren. |

## <a name="next-steps"></a>Nästa steg
Lär dig mer om skillnaden mellan [offentliga och privata DC/OS-agenter](container-service-dcos-agents.md).

Läs mer om [hantera DC/OS-behållare](container-service-mesos-marathon-ui.md).

