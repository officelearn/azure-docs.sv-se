---
title: Övervaka Azure-tjänster och program med Grafana
description: Dirigera Azure Monitor och Application Insights data så att du kan visa dem i Grafana.
services: azure-monitor
keywords: ''
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: conceptual
ms.service: azure-monitor
ms.component: ''
ms.openlocfilehash: 533fbfc42939299f99699a13513cd3b55c89cabc
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957292"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Övervaka dina Azure-tjänster i Grafana
Du kan nu också övervaka Azure-tjänster och program från [Grafana](https://grafana.com/) med hjälp av den [Azure Monitor-plugin-programmet för datakällans](https://grafana.com/plugins/grafana-azure-monitor-datasource). Plugin-programmet samlar in prestandadata för program som samlas in av Application Insights SDK samt infrastrukturdata tillhandahålls av Azure Monitor. Du kan sedan visa dessa data på din Grafana-instrumentpanel.

Plugin-programmet är för närvarande i förhandsversion.

Använd följande steg för att ställa in en Grafana-server från Azure Marketplace och skapa instrumentpaneler för mått från Azure Monitor och Application Insights.

## <a name="set-up-a-grafana-instance"></a>Konfigurera en Grafana-instans
1. Gå till Azure Marketplace och välja Grafana av Grafana Labs.

2. Fyll i namn och information. Skapa en ny resursgrupp. Håll koll på den värden du anger för den virtuella datorns användarnamn, VM-lösenord och Grafana lösenord för serveradministratören.  

3. Välj VM-storlek och ett lagringskonto.

4. Konfigurera nätverksinställningar för konfigurationen.

5. Visa sammanfattningen och välj **skapa** efter att du godkänt användningsvillkoren.

## <a name="log-in-to-grafana"></a>Logga in på Grafana
1. När distributionen är klar väljer du **går du till resursgruppen**. Du kan se en lista över nyligen skapade resurser.

    ![Grafana resource gruppobjekt](media/monitor-send-to-grafana/grafana1.png)

    Om du väljer den nya nätverkssäkerhetsgruppen (*grafana-nsg* i det här fallet), ser du att port 3000 används för att få åtkomst till Grafana server.

2. Gå tillbaka till listan över resurser och välj **offentliga IP-adressen**. Med hjälp av de värden som finns på den här skärmen, skriver *http://<IP address>: 3000* eller  *<DNSName>: 3000* i webbläsaren. Du bör se en inloggningssida för Grafana-server som du precis skapat.

    ![Grafana-inloggningsskärmen](media/monitor-send-to-grafana/grafana2.png)

3. Logga in med användarnamnet som *admin* och Grafana lösenordet för serveradministratören du skapade tidigare.

## <a name="configure-data-source-plugin"></a>Konfigurera plugin-programmet för data källan

När du har loggat in kan bör du se att plugin-programmet Azure Monitor data källan redan ingår.

![Grafana visar Azure Monitor-plugin-programmet](media/monitor-send-to-grafana/grafana3.png)

1. Välj **Lägg till datakälla** att konfigurera Azure Monitor och Application Insights.

2. Välj ett namn för datakällan och välj **Azure Monitor** som datakälla i listrutan.


## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Grafana använder ett Azure Active Directory-tjänstobjekt för att ansluta till API: er för Azure Monitor och samla in mätdata. Du måste skapa ett tjänstobjekt att hantera åtkomst till dina Azure-resurser.

1. Se [instruktionerna](../active-directory/develop/howto-create-service-principal-portal.md) att skapa ett huvudnamn för tjänsten. Kopiera och spara din klient-ID, klient-ID och en klienthemlighet.

2. Se [tilldela program till roll](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) att tilldela läsarrollen till Azure Active Directory-programmet.     

3. Du kan även inkludera din Application Insights API och program-ID för att samla in Application Insights baserat mått om du använder Application Insights. Mer information finns i [få din API-nyckel och en program-ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

4. När du har angett alla den här informationen väljer **spara** och Grafana testar API: et. Du bör se ett meddelande som liknar det följande.  

    ![Grafana visar Azure Monitor-plugin-programmet](media/monitor-send-to-grafana/grafana4-1.png)

> [!NOTE]
> När du konfigurerar plugin-programmet kan du ange vilket Azure-moln (offentligt, Azure US Government, Azure Tyskland eller Azure Kina) som plugin-programmet konfigureras mot.
>
>

## <a name="build-a-grafana-dashboard"></a>Skapa en Grafana-instrumentpanel

1. Gå till startsidan och välj **ny instrumentpanel**.

2. I den nya instrumentpanelen väljer du den **Graph**. Du kan prova andra diagram alternativ men den här artikeln använder *Graph* som exempel.

    ![Ny Grafana-instrumentpanel](media/monitor-send-to-grafana/grafana5.png)

3. Ett tomt diagram som visas på instrumentpanelen.

4. Klicka på rubriken panelen och välj **redigera** att ange information om de data som du vill rita i den här graph-diagram.

5. När du har valt alla rätt virtuella datorer, kan du visa måtten i instrumentpanelen.

Följande är en enkel instrumentpanel med två diagram. En vänster visar CPU-procent två virtuella datorer. Diagrammet till höger visar transaktioner i ett Azure Storage-konto per transaktion API-typen.

![Grafana två diagram-exempel](media/monitor-send-to-grafana/grafana6.png)


## <a name="optional-create-dashboard-playlists"></a>Valfritt: Skapa instrumentpanelen spelningslistor

En av de många användbara funktionerna för Grafana är spellistan för instrumentpanelen. Du kan skapa flera instrumentpaneler och lägga till dem i en spellista konfigurera ett intervall för varje instrumentpanelen att visa. Välj **spela upp** att se instrumentpaneler gå igenom. Du kanske vill visa dem på en stor wall-Övervakare för att tillhandahålla en ”status tavla” för din grupp.

![Grafana spellistan exempel](media/monitor-send-to-grafana/grafana7.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Valfritt: Övervaka dina anpassade mått på samma server Grafana

Du kan också installera Telegraf och InfluxDB för att samla in och rita anpassade såväl agentbaserad mått i samma Grafana-instans. Det finns många data källan plugin-program som du kan använda för att samla de här måtten på en instrumentpanel.

Du kan även återanvända den här konfigurationen att inkludera mått från Prometheus-servern. Använda plugin-programmet Prometheus datakälla i Grafanas plugin-programmet-galleriet.

Här är bra referensartiklar om hur du använder Telegraf, InfluxDB, Prometheus och Docker
 - [Så här övervakar du Systemmått med SKALSTRECK-Stack på Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Övervaka Docker Resursmått med Grafana, InfluxDB och Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [En övervakningslösning för Docker, behållare och tjänster i behållare](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Här är en avbildning av en fullständig Grafana-instrumentpanel med mått från Azure Monitor och Application Insights.
![Grafana exempel mått](media/monitor-send-to-grafana/grafana8.png)


## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras när virtuella datorer körs om du använder dem eller inte. Rensa resursgruppen som skapades i den här artikeln för att undvika extra kostnader.

1. I den vänstra menyn i Azure portal, klickar du på **resursgrupper** och klicka sedan på **Grafana**.
2. På sidan med resursgrupper, klickar du på **ta bort**, typ **Grafana** i textrutan och klicka sedan på **ta bort**.

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Monitor-mått](../monitoring/monitoring-data-collection.md)
