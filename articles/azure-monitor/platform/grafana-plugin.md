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
ms.subservice: ''
ms.openlocfilehash: fc963987b45751aab33035a83b2b477129e9a756
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730908"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Övervaka dina Azure-tjänster i Grafana
Du kan nu övervaka Azure-tjänster och program från [Grafana](https://grafana.com/) med hjälp av den [Azure Monitor-plugin-programmet för datakällans](https://grafana.com/plugins/grafana-azure-monitor-datasource). Plugin-programmet samlar in program-prestandadata som samlats in från Azure Monitor, inklusive olika loggar och mått. Du kan sedan visa dessa data på din Grafana-instrumentpanel.

Plugin-programmet är för närvarande i förhandsversion.

Använd följande steg för att ställa in en Grafana-server och skapa instrumentpaneler för mått och loggar från Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Konfigurera en server med Grafana

### <a name="set-up-grafana-locally"></a>Konfigurera Grafana lokalt
Du ställer in en lokal Grafana server [ladda ned och installera Grafana i din lokala miljö](https://grafana.com/grafana/download). Om du vill använda plugin-programmets Log Analytics-integrering, installera Grafana version 5.3 eller senare.
### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Konfigurera Grafana på Azure via Azure Marketplace
1. Gå till Azure Marketplace och välja Grafana av Grafana Labs.

2. Fyll i namn och information. Skapa en ny resursgrupp. Håll koll på den värden du anger för den virtuella datorns användarnamn, VM-lösenord och Grafana lösenord för serveradministratören.  

3. Välj VM-storlek och ett lagringskonto.

4. Konfigurera nätverksinställningar för konfigurationen.

5. Visa sammanfattningen och välj **skapa** efter att du godkänt användningsvillkoren.

6. När distributionen är klar väljer du **går du till resursgruppen**. Du kan se en lista över nyligen skapade resurser.

    ![Grafana resource gruppobjekt](media/grafana-plugin/grafana1.png)

    Om du väljer den nya nätverkssäkerhetsgruppen (*grafana-nsg* i det här fallet), ser du att port 3000 används för att få åtkomst till Grafana server.

7. Hämta offentliga IP-adressen för servern Grafana - gå tillbaka till listan över resurser och välj **offentliga IP-adressen**.

## <a name="log-in-to-grafana"></a>Logga in på Grafana

1. Med IP-adressen för din server, öppna inloggningssidan i *http://\<IP-adress\>: 3000* eller  *\<DNSName >\:3000* i webbläsaren. 3000 är standardporten, du Observera att du kan ha valt en annan port under installationen. Du bör se en inloggningssida för Grafana-server som du skapat.

    ![Grafana-inloggningsskärmen](./media/grafana-plugin/grafana-login-screen.png)

2. Logga in med användarnamnet *admin* och Grafana lösenordet för serveradministratören du skapade tidigare. Om du använder en lokal konfiguration, standardlösenordet blir *admin*, och du kan uppmanas att ändra den loggar in första gången.

## <a name="configure-data-source-plugin"></a>Konfigurera plugin-programmet för data källan

När du har loggat in kan bör du se att plugin-programmet Azure Monitor data källan redan ingår.

![Grafana innehåller Azure Monitor-plugin-programmet](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Välj **Lägg till datakälla** att lägga till och konfigurera Azure Monitor-datakälla.

2. Välj ett namn för datakällan och välj **Azure Monitor** som typ i listrutan.

3. Skapa ett huvudnamn för tjänsten - Grafana använder ett huvudnamn för tjänsten Azure Active Directory för att ansluta till API: er för Azure-övervaka och samla in data. Du måste skapa eller använda ett befintligt huvudnamn för tjänsten för att hantera åtkomst till dina Azure-resurser.
    * Se [instruktionerna](../../azure-resource-manager/resource-group-create-service-principal-portal.md) att skapa ett huvudnamn för tjänsten. Kopiera och spara din klient-ID (katalog-ID), klient-ID (program-ID) och klienthemlighet (programmet nyckelvärde).
    * Se [tilldela program till roll](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) om du vill tilldela rollen läsare i Azure Active Directory-programmet för prenumerationen, resursgruppen eller resursen du vill övervaka. 
    Log Analytics-API kräver den [Log Analytics Reader rollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader), som innehåller behörigheter som rollen Läsare och lägger till den.

4. Ange anslutningsinformationen till API: er som du vill använda. Du kan ansluta till alla eller vissa av dem. 
    * Om du ansluter till både Azure Monitor (för att samla in mått) och Azure Log Analytics (för loggdata) kan du återanvända samma autentiseringsuppgifter genom att välja **samma information som Azure Monitor API**.
    * När du konfigurerar plugin-programmet kan du ange vilket Azure-moln som plugin-programmet ska övervakas (offentligt, Azure US Government, Azure Tyskland eller Azure Kina).
    * Du kan även inkludera din Application Insights API och program-ID för att samla in Application Insights baserat mått om du använder Application Insights. Mer information finns i [få din API-nyckel och en program-ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Vissa data källfält namnges annorlunda än sina korrelerade inställningar för Azure:
        >     * Klient-ID är Azure-katalog-ID
        >     * Klient-ID är Azure Active Directory-program-ID
        >     * Klienthemlighet är nyckelvärdet för Azure Active Directory-program

5. Du kan även inkludera din Application Insights API och program-ID för att samla in Application Insights baserat mått om du använder Application Insights. Mer information finns i [få din API-nyckel och en program-ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Välj **spara**, och Grafana kommer att testa autentiseringsuppgifterna för varje API. Du bör se ett meddelande som liknar det följande.  
    ![Grafana config som godkänts av datakälla](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Skapa en Grafana-instrumentpanel

1. Gå till sidan med Grafana och välj **ny instrumentpanel**.

2. I den nya instrumentpanelen väljer du den **Graph**. Du kan prova andra diagram alternativ men den här artikeln använder *Graph* som exempel.

3. Ett tomt diagram som visas på instrumentpanelen. Klicka på rubriken panelen och välj **redigera** att ange information om de data som du vill rita i den här graph-diagram.
    ![Grafana nytt diagram](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Välj Azure Monitor-datakälla som du har konfigurerat.
    * Samla in Azure Monitor metrics - Välj **Azure Monitor** i listrutan för tjänsten. En lista över väljare visar upp, där du kan välja resurser och mått som ska övervakas i det här diagrammet. Om du vill samla in statistik från en virtuell dator att använda namnområdet **Microsoft.Compute/VirtualMachines**. När du har valt virtuella datorer och mått, kan du visa sina data i instrumentpanelen.
    ![Grafana graph-konfiguration för Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
    * Samla in Azure Log Analytics-data – Välj **Azure Log Analytics** i listrutan för tjänsten. Välj den arbetsyta som du vill fråga och ställa in frågetexten. Du kan kopiera här en Log Analytics-fråga som du redan har eller skapa en ny. När du skriver i din fråga visas IntelliSense och föreslår att du alternativ för automatisk komplettering. Välj typen av visualisering **Time series** **tabell**, och kör frågan.
    
    > [!NOTE]
    >
    > Standardfrågan medföljer plugin-programmet använder två makron: ”$__timeFilter() och $__interval. 
    > Dessa makron Tillåt Grafana att dynamiskt beräkna tidsintervall och tidsintervallet, när du zoomar in på en del av ett diagram. Du kan ta bort dessa makron och använda ett normaltid filter som *TimeGenerated > ago(1)*, men som innebär att diagrammet inte stöder zoomning i funktionen.
    
    ![Grafana graph-konfiguration för Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Följande är en enkel instrumentpanel med två diagram. En vänster visar CPU-procent två virtuella datorer. Diagrammet till höger visar transaktioner i ett Azure Storage-konto per transaktion API-typen.
    ![Grafana två diagram-exempel](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Valfritt: Övervaka din anpassade mått på samma server Grafana

Du kan också installera Telegraf och InfluxDB för att samla in och rita anpassade såväl agentbaserad mått samma Grafana-instans. Det finns många data källan plugin-program som du kan använda för att samla de här måtten på en instrumentpanel.

Du kan även återanvända den här konfigurationen att inkludera mått från Prometheus-servern. Använda plugin-programmet Prometheus datakälla i Grafanas plugin-programmet-galleriet.

Här är bra referensartiklar om hur du använder Telegraf, InfluxDB, Prometheus och Docker
 - [Så här övervakar du Systemmått med SKALSTRECK-Stack på Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Övervaka Docker Resursmått med Grafana, InfluxDB och Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [En övervakningslösning för Docker, behållare och tjänster i behållare](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Här är en avbildning av en fullständig Grafana-instrumentpanel med mått från Azure Monitor och Application Insights.
![Grafana exempel mått](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Avancerade funktioner för Grafana

### <a name="variables"></a>Variabler
Vissa frågevärden kan väljs via Användargränssnittet listrutor och uppdateras i frågan. Överväg följande fråga som ett exempel:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Du kan konfigurera en variabel som visar en lista över alla tillgängliga **lösning** standardvärden och uppdatera din fråga för att använda den.
Om du vill skapa en ny variabel, klicka på knappen för instrumentpanelens inställningar i den övre högra delen, Välj **variabler**, och sedan **New**.
Definiera datakälla och fråga för att hämta listan över värden på sidan variabeln.
![Grafana konfigurera variabeln](./media/grafana-plugin/grafana-configure-variable-dark.png)

När du skapat justera frågan så att de valda värdena och dina diagram svarar i enlighet med detta:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana Använd variabler](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Skapa instrumentpanel spelningslistor

En av de många användbara funktionerna för Grafana är spellistan för instrumentpanelen. Du kan skapa flera instrumentpaneler och lägga till dem i en spellista konfigurera ett intervall för varje instrumentpanelen att visa. Välj **spela upp** att se instrumentpaneler gå igenom. Du kanske vill visa dem på en stor wall-Övervakare för att tillhandahålla en tavla för status för din grupp.

![Grafana spellistan exempel](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du har ställt in en Grafana-miljö på Azure, debiteras du när virtuella datorer körs om du använder dem eller inte. Rensa resursgruppen som skapades i den här artikeln för att undvika extra kostnader.

1. I den vänstra menyn i Azure portal, klickar du på **resursgrupper** och klicka sedan på **Grafana**.
2. På sidan med resursgrupper, klickar du på **ta bort**, typ **Grafana** i textrutan och klicka sedan på **ta bort**.

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Monitor-mått](../../azure-monitor/platform/data-collection.md)

