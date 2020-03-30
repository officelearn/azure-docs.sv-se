---
title: Övervaka Azure-tjänster och -program med Grafana
description: Dirigera Azure Monitor- och Application Insights-data så att du kan visa dem i Grafana.
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 142e3e19c13710963d239a75bc237b63713c29cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672216"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Övervaka dina Azure-tjänster i Grafana
Du kan nu övervaka Azure-tjänster och -program från [Grafana](https://grafana.com/) med hjälp av [insticksprogrammet för Azure Monitor-datakällan](https://grafana.com/plugins/grafana-azure-monitor-datasource). Insticksprogrammet samlar in programprestandadata som samlats in av Azure Monitor, inklusive olika loggar och mått. Du kan sedan visa dessa data på grafana-instrumentpanelen.

Använd följande steg för att konfigurera en Grafana-server och skapa instrumentpaneler för mått och loggar från Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Konfigurera en Grafana-server

### <a name="set-up-grafana-locally"></a>Ställ in Grafana lokalt
Om du vill konfigurera en lokal Grafana-server [hämtar och installerar du Grafana i din lokala miljö](https://grafana.com/grafana/download). Om du vill använda insticksprogrammets Azure Monitor-integrering installerar du Grafana version 5.3 eller senare.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Konfigurera Grafana på Azure via Azure Marketplace
1. Gå till Azure Marketplace och välj Grafana by Grafana Labs.

2. Fyll i namn och detaljer. Skapa en ny resursgrupp. Håll reda på de värden du väljer för vm-användarnamnet, vm-lösenordet och administratörslösenordet för Grafana-servern.  

3. Välj VM-storlek och ett lagringskonto.

4. Konfigurera nätverkskonfigurationsinställningarna.

5. Visa sammanfattningen och välj **Skapa** när du har accepterat användningsvillkoren.

6. När distributionen är klar väljer du **Gå till resursgrupp**. Du ser en lista över nyskapade resurser.

    ![Grafana resursgrupp objekt](media/grafana-plugin/grafana1.png)

    Om du väljer*nätverkssäkerhetsgruppen (grafana-nsg* i det här fallet) kan du se att port 3000 används för att komma åt Grafana-servern.

7. Hämta den offentliga IP-adressen för din Grafana-server - gå tillbaka till listan över resurser och välj **Offentlig IP-adress**.

## <a name="sign-in-to-grafana"></a>Logga in på Grafana

1. Öppna inloggningssidan på *http://\<IP-adress\>:3000* eller * \<DNSName>\:3000* i webbläsaren med hjälp av IP-adressen på din server. 3 000 är standardporten, men du kanske har valt en annan port under installationen. Du bör se en inloggningssida för Grafana-servern du byggt.

    ![Grafana inloggningsskärm](./media/grafana-plugin/grafana-login-screen.png)

2. Logga in med *användarnamnsadministratören* och grafana-serveradministratörens lösenord som du skapade tidigare. Om du använder en lokal installation är standardlösenordet *administratör*och du skulle bli ombedd att ändra det vid din första inloggning.

## <a name="configure-data-source-plugin"></a>Konfigurera plugin-program för datakälla

När du har loggat in bör du se att azure monitor-datakällinsticksprogrammet redan ingår.

![Grafana innehåller Plugin-programmet Azure Monitor](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Välj **Lägg till datakälla** för att lägga till och konfigurera Azure Monitor-datakällan.

2. Välj ett namn för datakällan och välj **Azure Monitor** som typ i listrutan.

3. Skapa ett huvudnamn för tjänsten - Grafana använder ett huvudnamn för Tjänsten Azure Active Directory för att ansluta till Azure Monitor API:er och samla in data. Du måste skapa eller använda ett befintligt tjänsthuvudnamn för att hantera åtkomst till dina Azure-resurser.
    * Se [de här instruktionerna](../../azure-resource-manager/resource-group-create-service-principal-portal.md) för att skapa ett huvudnamn för tjänsten. Kopiera och spara ditt klient-ID (katalog-ID), klient-ID (Application ID) och klienthemlighet (programnyckelvärde).
    * Se [Tilldela program till roll](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) för att tilldela rollen Läsare till Azure Active Directory-programmet för den prenumeration, resursgrupp eller resurs som du vill övervaka. 
    Log Analytics API kräver [rollen Log Analytics Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader), som innehåller reader-rollens behörigheter och lägger till den.

4. Ange anslutningsinformation till de API:er som du vill använda. Du kan ansluta till alla eller till några av dem. 
    * Om du ansluter till både mått och loggar i Azure Monitor kan du återanvända samma autentiseringsuppgifter genom att välja **Samma information som Azure Monitor API**.
    * När du konfigurerar plugin-programmet kan du ange vilket Azure Cloud du vill att plugin-programmet ska övervaka (Public, Azure US Government, Azure Germany eller Azure China).
    * Om du använder Application Insights kan du även inkludera ditt API för Application Insights och program-ID för att samla in Application Insights-baserade mått. Mer information finns i [Hämta API-nyckel och program-ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Vissa datakällfält namnges på ett annat sätt än de korrelerade Azure-inställningarna:
        > * Klient-ID är Azure Directory ID
        > * Klient-ID är Azure Active Directory Application ID
        > * Klienthemlighet är nyckelvärdet för Azure Active Directory-program

5. Om du använder Application Insights kan du även inkludera ditt API för Application Insights och program-ID för att samla in Application Insights-baserade mått. Mer information finns i [Hämta API-nyckel och program-ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Välj **Spara**och Grafana testar autentiseringsuppgifterna för varje API. Du bör se ett meddelande som liknar följande.  
    ![Grafana datakälla config godkänd](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Skapa en Grafana-instrumentpanel

1. Gå till Grafanas startsida och välj **Ny instrumentpanel**.

2. Välj **diagram**på den nya instrumentpanelen . Du kan prova andra diagramalternativ, men den här artikeln använder *Graph* som exempel.

3. Ett tomt diagram visas på instrumentpanelen. Klicka på panelens rubrik och välj **Redigera** för att ange information om de data som du vill rita i det här diagramdiagrammet.
    ![Grafana ny graf](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Välj den Azure Monitor-datakälla som du har konfigurerat.
   * Samla in Azure Monitor-mått – välj **Azure Monitor** i listrutan för tjänsten. En lista med väljare visas, där du kan välja de resurser och mått som ska övervakas i det här diagrammet. Om du vill samla in mått från en virtuell dator använder du namnområdet **Microsoft.Compute/VirtualMachines**. När du har valt virtuella datorer och mått kan du börja visa deras data i instrumentpanelen.
     ![Grafana graf config för Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Samla in Azure Monitor-loggdata – välj **Azure Log Analytics** i listrutan för tjänsten. Markera den arbetsyta som du vill fråga efter och ange frågetexten. Du kan kopiera en loggfråga som du redan har eller skapa en ny. När du skriver i frågan visas intelliSense och föreslår alternativ för automatisk komplettering. Välj visualiseringstyp, **Tidsserietabell** **Table**och kör frågan.
    
     > [!NOTE]
     >
     > Standardfrågan som medföljer insticksprogrammet använder två makron: "$__timeFilter() och $__interval. 
     > Dessa makron gör det möjligt för Grafana att dynamiskt beräkna tidsintervallet och tidskornet när du zoomar in på en del av ett diagram. Du kan ta bort dessa makron och använda ett standardtidsfilter, till exempel *TimeGenerated > sedan(1h),* men det betyder att diagrammet inte skulle stödja zoomfunktionen.
    
     ![Grafana diagram config för Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Följande är en enkel instrumentpanel med två diagram. Den till vänster visar CPU-procentandelen för två virtuella datorer. Diagrammet till höger visar transaktionerna i ett Azure Storage-konto uppdelade efter transaktions-API-typen.
    ![Exempel på grafana-två diagram](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Valfritt: Övervaka dina anpassade mätvärden på samma Grafana-server

Du kan också installera Telegraf och InfluxDB för att samla in och rita både anpassade och agentbaserade mått samma Grafana-förekomst. Det finns många plugins för datakälla som du kan använda för att föra samman dessa mått i en instrumentpanel.

Du kan också återanvända den här uppsättningen för att inkludera mått från din Prometheus-server. Använd Prometheus datakälla plugin i Grafana plugin galleri.

Här är bra referensartiklar om hur du använder Telegraf, InfluxDB, Prometheus och Docker
 - [Så här övervakar du systemmått med TICK Stack på Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [En övervakningslösning för Docker-värdar, behållare och containertjänster](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Här är en bild av en fullständig Grafana-instrumentpanel som har mått från Azure Monitor och Application Insights.
![Grafana Exempel Mått](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Avancerade Grafana-funktioner

### <a name="variables"></a>Variabler
Vissa frågevärden kan väljas via listrutan användargränssnitt och uppdateras i frågan. Tänk på följande fråga som ett exempel:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Du kan konfigurera en variabel som listar alla tillgängliga **lösningsvärden** och sedan uppdaterar frågan så att den används.
Om du vill skapa en ny variabel klickar du på knappen Inställningar på instrumentpanelen längst upp till höger, väljer **Variabler**och sedan **Nya**.
På variabelsidan definierar du den datakälla och fråga som ska köras för att få listan med värden.
![Grafana konfigurera variabel](./media/grafana-plugin/grafana-configure-variable-dark.png)

När du har skapat den justerar du frågan så att den använder de valda värdena och diagrammen kommer att svara därefter:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana använder variabler](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Skapa instrumentpanelsspellistor

En av de många användbara funktionerna i Grafana är instrumentpanelen spellista. Du kan skapa flera instrumentpaneler och lägga till dem i en spellista som konfigurerar ett intervall som varje instrumentpanel ska visas. Välj **Spela upp** om du vill se instrumentpanelerna gå igenom. Du kanske vill visa dem på en stor väggskärm för att ge en statustavla för din grupp.

![Exempel på spellista i Grafana](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du har konfigurerat en Grafana-miljö på Azure debiteras du när virtuella datorer körs oavsett om du använder dem eller inte. Rensa resursgruppen som skapas i den här artikeln om du vill undvika att ådra dig ytterligare avgifter.

1. Klicka på **Resursgrupper** på menyn till vänster i Azure-portalen och klicka sedan på **Grafana**.
2. Klicka på **Ta bort**på resursgruppssidan, skriv **Grafana** i textrutan och klicka sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Monitor-mått](data-platform.md)

