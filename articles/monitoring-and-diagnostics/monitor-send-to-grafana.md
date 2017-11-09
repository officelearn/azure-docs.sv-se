---
title: "Övervaka Azure-tjänster och program med hjälp av Grafana | Microsoft Docs"
description: "Väg Azure-Monitor och Application Insights data så att du kan visa dem i Grafana."
services: monitoring-and-diagnostics
keywords: 
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: article
ms.service: monitoring-and-diagnostics
ms.openlocfilehash: 709a98f8bcdb75962f8e41de348ca7a41c677610
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="monitor-your-azure-services-in-grafana"></a>Övervaka Azure-tjänster i Grafana
Du kan nu också övervaka Azure-tjänster och program från [Grafana](https://grafana.com/) med hjälp av den [plugin-program för Azure-Monitor datakälla](https://grafana.com/plugins/grafana-azure-monitor-datasource). Plugin-programmet samlar in prestandadata för program som samlas in av Application Insights SDK samt infrastrukturdata som tillhandahålls av Azure-Monitor. Du kan sedan visa dessa data på instrumentpanelen Grafana.

Plugin-programmet är för närvarande under förhandsgranskning.

Använd följande steg för att ställa in en Grafana server från Azure Marketplace och skapa instrumentpaneler för mätvärden från Azure-Monitor och Application Insights.

## <a name="set-up-a-grafana-instance"></a>Konfigurera en Grafana-instans
1. Gå till Azure Marketplace och välj Grafana av Grafana Labs.

2. Fylla i namn och information. Skapa en ny resursgrupp. Hålla reda på de värden som du väljer för VM-användarnamn, lösenord för virtuell dator och Grafana lösenord för serveradministratören.  

3. Välj VM-storlek och ett lagringskonto.

4. Konfigurera nätverksinställningar för konfigurationen.

5. Visa sammanfattningen och välj **skapa** efter att acceptera användningsvillkoren.

## <a name="log-in-to-grafana"></a>Logga in på Grafana
1. När distributionen är klar väljer du **gå till resursgruppen**. Du kan se en lista över nyligen skapade resurser. 

    ![Grafana resurs gruppobjekt](.\media\monitor-how-to-grafana\grafana1.png) 

    Om du väljer nätverkssäkerhetsgruppen (*grafana nsg* i det här fallet), ser du att port 3000 används för att komma åt Grafana server. 

2. Gå tillbaka till listan över resurser och välj **offentliga IP-adressen**. Med hjälp av värdena som finns på den här skärmen, skriver *http://<IP address>: 3000* eller  *<DNSName>: 3000* i webbläsaren. Du bör se en inloggningssida för Grafana-server som du precis har skapat.
    
    ![Grafana inloggningsskärm](.\media\monitor-how-to-grafana\grafana2.png) 

3. Logga in med användarnamnet som *admin* och Grafana lösenord för serveradministratören du skapade tidigare. 

## <a name="configure-data-source-plugin"></a>Konfigurera plugin-programmet för datakällan

När du har loggat in kan bör du se till att plugin-programmet för Azure-Monitor datakälla ingår redan.

![Grafana visar Azure-Monitor-plugin-programmet](.\media\monitor-how-to-grafana\grafana3.png) 

1. Välj **Lägg till datakälla** att konfigurera Azure-Monitor och Application Insights. 
    
2. Välj ett namn för datakällan och välj **Azure-Monitor** som datakälla i listrutan.
    
    
## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten 

Grafana använder ett Azure Active Directory-tjänstens huvudnamn för att ansluta till Azure-Monitor API: er och samla in data för mått. Du måste skapa en tjänst huvudnamn för att hantera åtkomst till resurserna i Azure.

1. Se [instruktionerna](../azure-resource-manager/resource-group-create-service-principal-portal.md) att skapa ett huvudnamn för tjänsten. Kopiera och spara din klient-ID, klient-ID och en klienthemlighet.

2. Se [tilldela program till rollen](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) tilldela läsarrollen till Azure Active Directory-programmet.   

3. Du kan även inkludera dina Application Insights API och program-ID för att samla in Application Insights baserat mått om du använder Application Insights. Mer information finns i [komma din API-nyckel och en program-ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

4. När du har angett alla informationen, Välj **spara** och Grafana testar API: et. Du bör se ett meddelande som liknar följande.  

    ![Grafana visar Azure-Monitor-plugin-programmet](.\media\monitor-how-to-grafana\grafana4.png) 
    
## <a name="build-a-grafana-dashboard"></a>Skapa en Grafana instrumentpanel

1. Gå till startsidan och välj **ny instrumentpanel**.

2. I den nya instrumentpanelen, väljer du den **diagram**. Du kan försöka diagram alternativ men den här artikeln använder *diagram* som exempel. 

    ![Grafana ny instrumentpanel](.\media\monitor-how-to-grafana\grafana5.png) 

3. Ett tomt diagram visas på instrumentpanelen. 

4. Klicka på rubriken panelen och välj **redigera** att ange information om de data du vill rita i det här diagrammet i diagrammet.
    
5. När du har valt alla rätt virtuella datorer kan starta du visa måtten i instrumentpanelen. 

Följande är en enkel instrumentpanel med två diagram. På vänster visas två virtuella datorer CPU-procent. Diagrammet till höger innehåller transaktioner i ett Azure Storage-konto per transaktion API-typen.
    
![Grafana två diagram exempel](.\media\monitor-how-to-grafana\grafana6.png) 
    

## <a name="optional-create-dashboard-playlists"></a>Valfritt: Skapa instrumentpanelen spelningslistor

En av de många användbara funktionerna för Grafana är instrumentpanelen spelningslista. Du kan skapa flera instrumentpaneler och lägga till dem i en spellista konfigurera ett intervall för varje instrumentpanelen för att visa. Välj **spela upp** att visa instrumentpaneler gå igenom. Du kanske vill visa dem på en stor brandvägg Övervakare att tillhandahålla en ”status board” för din grupp. 
    
![Grafana spelningslista exempel](.\media\monitor-how-to-grafana\grafana7.png) 


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Valfritt: Övervaka din anpassade mått på samma server Grafana

Du kan också installera Telegraf och InfluxDB för att samla in och rita både anpassade och agent-baserade mått i samma Grafana-instans. Det finns många datakälla plugin-program som du kan använda för att sammanställa de här måtten på en instrumentpanel. 
    
Du kan också återanvända det ställts in för att inkluderar mått från Prometheus-servern. Använd Prometheus datakälla plugin-programmet i Grafana's plugin-galleriet.
    
Här är bra referensartiklar om hur du använder Telegraf, InfluxDB, Prometheus och Docker
 - [Så här övervakar du System mått med TICK-stacken på Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Övervaka Docker resursen med Grafana, InfluxDB och Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [En övervakningslösning för Docker-värdar, behållare och av tjänster](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Här är en avbildning av en fullständig Grafana instrumentpanel med mått från Azure-Monitor och Application Insights.
![Grafana exempel mått](.\media\monitor-how-to-grafana\grafana8.png) 


## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras när virtuella datorer som körs om du använder dem eller inte. Rensa för att undvika ytterligare avgifter, den resursgrupp som skapats i den här artikeln. 

1. I den vänstra menyn i Azure-portalen klickar du på **resursgrupper** och klicka sedan på **Grafana**. 
2. På din resurs gruppen klickar du på **ta bort**, typen **Grafana** i textrutan och klicka sedan på **ta bort**.

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure övervakaren mått](monitoring-overview-metrics.md)


