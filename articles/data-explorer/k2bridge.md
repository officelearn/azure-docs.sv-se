---
title: Visualisera data från Azure Datautforskaren med Kibana
description: I den här artikeln får du lära dig hur du konfigurerar Azure Datautforskaren som en data källa för Kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 30d74f36c6462d1fba039595d2ed6fe722b742e8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164818"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Visualisera data från Azure Datautforskaren i Kibana med K2Bridge-anslutningen med öppen källkod

K2Bridge (Kibana-Kusto Bridge) gör att du kan använda Azure Datautforskaren som data källa och visualisera dessa data i Kibana. K2Bridge är ett behållar program med [öppen källkod](https://github.com/microsoft/K2Bridge) som fungerar som en proxy mellan en Kibana-instans och ett Azure datautforskaren-kluster. Den här artikeln beskriver hur du använder K2Bridge för att skapa anslutningen.

K2Bridge översätter Kibana-frågor till Kusto frågespråket (KQL) och skickar tillbaka Azure-Datautforskaren tillbaka till Kibana. 

   ![diagram](media/k2bridge/k2bridge-chart.png)

K2Bridge stöder Kibana-fliken identifiering där du kan:
* Sök och utforska data
* Filtrera resultat
* Lägga till eller ta bort fält i resultat rutnätet
* Visa post innehåll
* Spara och dela sökningar

Bilden nedan visar en Kibana-instans som är kopplad till Azure Datautforskaren av K2Bridge. Användar upplevelsen i Kibana har inte ändrats.

   [Sidan ![Kibana](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Förutsättningar

Innan du kan visualisera data från Azure Datautforskaren i Kibana, har du följande klart:

* [Helm v3](https://github.com/helm/helm#install), Kubernetes Package Manager
* Azure Kubernetes service-kluster (AKS) eller något annat Kubernetes-kluster (version 1,14 till version 1,16 har testats och verifierats). Om du behöver ett AKS-kluster kan du läsa distribuera ett AKS-kluster [med hjälp av Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) eller [genom att använda Azure Portal](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* Ett [Azure datautforskaren-kluster](create-cluster-database-portal.md), inklusive:
    * Azure Datautforskaren-klustrets URL 
    * Databas namnet
    
* Ett huvud namn för Azure AD-tjänsten som har behörighet att visa data i Azure Datautforskaren, inklusive:
    * Klient-ID 
    * Klient hemlighet

    Ett huvud namn för tjänsten med visnings behörighet rekommenderas. Det rekommenderas inte att använda högre behörigheter.

    * [Ange klustrets visnings behörigheter för Azure AD-tjänstens huvud namn](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Mer information om Azure AD-tjänstens huvud namn finns i [skapa ett tjänst huvud namn för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Köra K2Bridge på Azure Kubernetes service (AKS)

Som standard refererar K2Bridges's Helm-diagrammet till en offentligt tillgänglig bild som finns på Microsofts Container Registry (MCR). MCR kräver inte några autentiseringsuppgifter och fungerar direkt.

1. Ladda ned de Helm diagram som krävs.

1. Lägg till ElasticSearch-beroendet i Helm. 
    Orsaken till ElasticSearch-beroendet är att K2Bridge använder en intern liten ElasticSearch-instans för att betjäna metadata-relaterade begär Anden (till exempel index-mönster och sparade frågor). Inga affärs data sparas i den här interna instansen och kan betraktas som en implementerings information. 

    1. Så här lägger du till ElasticSearch-beroendet till Helm:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Så här hämtar du K2Bridge-diagrammet från katalogen Charts i GitHub-lagringsplatsen:
        1. Klona lagrings platsen från [GitHub](https://github.com/microsoft/K2Bridge).
        1. Gå till K2Bridges rot databas katalog.
        1. Kör:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Distribuera K2Bridge:

    1. Ange variablerna med rätt värden för din miljö:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. Valfritt Aktivera Azure Application Insights-telemetri. 
        Om det är första gången du använder Azure Application insikter bör du först [skapa en Application Insights resurs](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). Du måste [Kopiera Instrumentation-nyckeln](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) till en variabel: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Installera K2Bridge-diagrammet:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        I [konfigurationen](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) kan du hitta en fullständig uppsättning konfigurations alternativ.

    1. Kommandots utdata kommer att föreslå nästa Helm-kommando som ska köras för att distribuera Kibana. Du kan också köra:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
    1. Använd port vidarebefordring för att få åtkomst till Kibana på localhost: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
    1. Anslut till Kibana genom att bläddra till http://127.0.0.1:5601.

    1. Exponera Kibana för slutanvändarna. Det finns flera metoder att göra det. Vilken metod du använder är i stort sett beroende av ditt användnings fall.

        Exempel:

        Exponera tjänsten som en LoadBalancer-tjänst. Det gör du genom att lägga till följande parameter i K2Bridge Helm install-kommandot ([ovan](#install-k2bridge-chart)):

        `--set service.type=LoadBalancer`
    
        Kör sedan:

           ```bash
           kubectl get service -w -n k2bridge
           ```   
        Utdata bör se ut så här: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
        Du kan sedan använda den genererade externa IP-adressen som visas och använda den för att få åtkomst till Kibana genom att öppna en webbläsare för att: `\<EXTERNAL-IP>:5601`.

1. Konfigurera index mönster för att få åtkomst till dina data:  
I en ny Kibana-instans:
     1. Öppna Kibana.
     1. Navigera till hantering.
     1. Välj **index mönster**. 
     1. Skapa ett index mönster.
Namnet på indexet måste exakt matcha tabell namnet eller funktions namnet, utan en asterisk. Du kan kopiera den relevanta raden från listan.

> [!Note]
> Om du vill köra andra Kubernetes-leverantörer ändrar du ElasticSearch-storageClassName i `values.yaml` så att den passar den som föreslås av providern.

## <a name="visualize-data"></a>Visualisera data

När Azure Datautforskaren har kon figurer ATS som en data källa för Kibana kan du använda Kibana för att utforska data. 

1. I Kibana väljer du fliken **identifiera** på den vänstra menyn.

1. I den vänstra List rutan väljer du ett index mönster (i det här fallet en Azure Datautforskaren-tabell), som definierar den data källa som du vill utforska.
    
   ![Välj ett index mönster](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Om dina data har ett tids filter fält kan du ange tidsintervallet. Ange ett tids filter längst upp till höger på sidan. Som standard visar identifieringen data under de senaste 15 minuterna.

   ![Tids filter](media/k2bridge/k2bridge-time-filter.png)
    
1. I resultat tabellen visas de första 500 posterna. Du kan expandera ett dokument för att undersöka dess fält data i JSON-eller tabell format.

   ![Expandera en post](media/k2bridge/k2bridge-expand-record.png)

1. Tabellen Results innehåller som standard kolumner för dokumentet _source och fältet Time (om det finns). Du kan välja vilka kolumner som ska läggas till i resultat tabellen genom att välja **Lägg till** bredvid fält namnet i den vänstra sid panelen.

   ![Vissa kolumner](media/k2bridge/k2bridge-specific-columns.png)
    
1. I frågefönstret kan du söka i data efter:
    * Ange en sökterm
    * Använder frågesyntaxen för Lucene. 
    Exempel:
        * Sök efter "fel" för att hitta alla poster som innehåller det här värdet. 
        * Sök efter "status: 200" för att hämta alla poster med status-värdet 200. 
    * Använda logiska operatorer (och, eller, inte)
    * Använda jokertecken (asterisk "\*" eller frågetecken "?") Exempel:
        * Frågan `"destination_city: L*"` matchar poster där målets Orts värde börjar med "l" (K2Bridge är inte Skift läges känsligt).

    ![Kör frågan](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > I [sökningen](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)kan du hitta fler Sök regler och logik.

1. Om du vill filtrera Sök resultaten använder du **fält listan** i den högra sid List sidan. 
    Fält listan är där du kan se:
    * De fem översta värdena för fältet
    * Antalet poster som innehåller fältet
    * Procent andelen poster som innehåller varje värde. 
    
    >[!Tip]
    > Använd skärm förstorarens ikon (+) för att hitta alla poster som har ett angivet värde.
    
    ![Fält lista](media/k2bridge/k2bridge-field-list.png)
   
    Du kan också filtrera resultaten med hjälp av (+) skärm förstoraren i vyn resultat tabell format för varje post i resultat tabellen.
    
     ![Tabell lista](media/k2bridge/k2bridge-table-list.png)
    
1. Välj antingen för att **Spara** eller **dela** din sökning.

     ![Spara sökning](media/k2bridge/k2bridge-save-search.png)
