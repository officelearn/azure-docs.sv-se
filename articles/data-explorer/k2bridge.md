---
title: Visualisera data från Azure Data Explorer med Kibana
description: I den här artikeln får du lära dig hur du konfigurerar Azure Data Explorer som datakälla för Kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065602"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Visualisera data från Azure Data Explorer i Kibana med anslutningsappen för öppen källkod till K2Bridge

Med K2Bridge (Kibana-Kusto Bridge) kan du använda Azure Data Explorer som datakälla och visualisera dessa data i Kibana. K2Bridge är ett containerprogram med öppen källkod som fungerar som en proxy mellan en Kibana-instans och ett Azure Data [Explorer-kluster.](https://github.com/microsoft/K2Bridge) I den här artikeln beskrivs hur du använder K2Bridge för att skapa den anslutningen.

K2Bridge översätter Kibana-frågor till Kusto Query Language (KQL) och skickar tillbaka Azure Data Explorer-resultaten till Kibana. 

   ![diagram](media/k2bridge/k2bridge-chart.png)

K2Bridge stöder Kibanas fliken Upptäck, där du kan:
* Sök och utforska data
* Filtrera resultat
* Lägga till eller ta bort fält i resultatrutnätet
* Visa postinnehåll
* Spara och dela sökningar

Avbildningen nedan visar en Kibana-instans bunden till Azure Data Explorer av K2Bridge. Användarupplevelsen i Kibana är oförändrad.

   [![Kibana Sida](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Krav

Innan du kan visualisera data från Azure Data Explorer i Kibana ska du ha följande redo:

* [Helm V3](https://github.com/helm/helm#install), Kubernetes pakethanterare
* Azure Kubernetes Service (AKS) kluster, eller någon annan Kubernetes kluster (version 1.14 till version 1.16 har testats och verifierats). Om du behöver ett AKS-kluster läser du Distribuera ett AKS-kluster [med Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) eller använder [Azure-portalen](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* Ett [Azure Data Explorer-kluster,](create-cluster-database-portal.md)inklusive:
    * Url:en för Azure Data Explorer-klustret 
    * Databasnamnet
    
* Ett Azure AD-tjänsthuvudnamn som auktoriserats för att visa data i Azure Data Explorer, inklusive:
    * Klient-ID:et 
    * Klienthemligheten

    Ett tjänsthuvudnamn med behörigheten Viewer rekommenderas. Det rekommenderas att använda högre behörigheter.

    * [Ange klustrets vybehörigheter för Azure AD-tjänstens huvudnamn](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Mer information om Huvudnamnen för Azure AD-tjänsten finns i [Skapa ett huvudnamn för Azure AD-tjänsten](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Kör K2Bridge på Azure Kubernetes Service (AKS)

Som standard refererar K2Bridges Helm-diagram till en allmänt tillgänglig avbildning som finns i Microsofts MCR (Container Registry). MCR kräver inga autentiseringsuppgifter och fungerar utanför boxen.

1. Ladda ner de nödvändiga Helm-diagrammen.

1. Lägg till Elasticsearch-beroendet i Helm. 
    Orsaken till Elasticsearch-beroendet är att K2Bridge använder en intern liten Elasticsearch-instans för att betjäna metadatarelaterade begäranden (till exempel indexmönster och sparade frågor). Inga affärsdata sparas i den här interna instansen och kan betraktas som en implementeringsdetalj. 

    1. Så här lägger du till Elasticsearch-beroendet i Helm:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Så här hämtar du K2Bridge-diagrammet från diagramkatalogen i GitHub-databasen:
        1. Klona databasen från [GitHub](https://github.com/microsoft/K2Bridge).
        1. Gå till katalogen K2Bridges-rotdatabas.
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

    1. (Valfritt) Aktivera telemetri för Azure Application Insights. 
        Om det är första gången du använder Azure Application Insights bör du först [skapa en Application Insights-resurs](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). Du måste [kopiera instrumenteringsnyckeln](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) till en variabel: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Installera K2Bridge-diagrammet:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        I [Konfiguration](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) hittar du den fullständiga uppsättningen konfigurationsalternativ.

    1. Kommandoutdata föreslår nästa Helm-kommando som ska köras för att distribuera Kibana. Du kan också köra:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. Använd portsedering för att komma åt Kibana på localhost: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. Anslut till Kibana genom http://127.0.0.1:5601att bläddra till .

    1. Exponera Kibana för slutanvändarna. Det finns flera metoder för att göra det. Vilken metod du använder beror till stor del på ditt användningsfall.

        Ett exempel:

        Exponera tjänsten som en LoadBalancer-tjänst. För att göra `--set service.type=LoadBalancer` det, lägga till parametern i K2Bridge Helm installationskommandot ([ovan](#install-k2bridge-chart)).        
    
        Kör sedan:
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        Utdata ska se ut så här: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        Du kan sedan använda den genererade EXTERN-IP som visas och använda `<EXTERNAL-IP>:5601`den för att komma åt Kibana genom att öppna en webbläsare för .

1. Konfigurera indexmönster för att komma åt dina data:  
I en ny Kibana-instans:
     1. Öppna Kibana.
     1. Navigera till hantering.
     1. Välj **Indexmönster**. 
     1. Skapa ett indexmönster.
Namnet på indexet måste exakt matcha tabellnamnet eller funktionsnamnet, utan en asterisk. Du kan kopiera den relevanta raden från listan.

> [!Note]
> Om du vill köra på andra Kubernetes-providers `values.yaml` ändrar du Elasticsearch storageClassName så att den passar den som föreslås av providern.

## <a name="visualize-data"></a>Visualisera data

När Azure Data Explorer har konfigurerats som en datakälla för Kibana kan du använda Kibana för att utforska data. 

1. Välj fliken **Upptäck** på den vänstra menyn i Kibana.

1. Välj ett indexmönster (i det här fallet en Azure Data Explorer-tabell) i den vänstra listrutan som definierar den datakälla som du vill utforska.
    
   ![Välj ett indexmönster](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Om dina data har ett tidsfilterfält kan du ange tidsintervallet. Ställ in ett tidsfilter längst upp till höger på sidan. Som standard visar Upptäck data för de senaste 15 minuterna.

   ![Tidsfilter](media/k2bridge/k2bridge-time-filter.png)
    
1. Resultattabellen visar de första 500 posterna. Du kan expandera ett dokument för att granska dess fältdata i antingen JSON- eller tabellformat.

   ![Expandera en post](media/k2bridge/k2bridge-expand-record.png)

1. Som standard innehåller resultattabellen kolumner för dokumentet _source och tidsfältet (om det finns). Du kan välja specifika kolumner som ska läggas till i resultattabellen genom att välja **Lägg** till bredvid fältnamnet i det vänstra sidofältet.

   ![Specifika kolumner](media/k2bridge/k2bridge-specific-columns.png)
    
1. I frågefältet kan du söka efter data genom att:
    * Ange en sökterm
    * Använda syntaxen för Lucene-frågan. 
    Ett exempel:
        * Sök "fel" för att hitta alla poster som innehåller det här värdet. 
        * Sök efter "status: 200", för att få alla poster med ett statusvärde på 200. 
    * Använda logiska operatorer (OCH, ELLER, INTE)
    * Använda jokertecken (asterisk \* " " eller frågetecken "?") Till exempel:
        * Frågan `"destination_city: L*"` matchar poster där målstadsvärdet börjar med "l" (K2Bridge är inte skiftlägeskänslig).

    ![Kör frågan](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > I [Söka](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)hittar du fler sökregler och logik.

1. Om du vill filtrera sökresultaten använder du **fältlistan** till höger på sidan. 
    Fältlistan är där du kan se:
    * De fem översta värdena för fältet
    * Antalet poster som innehåller fältet
    * Procentandelen poster som innehåller varje värde. 
    
    >[!Tip]
    > Använd ikonen (+) förstoringsglas för att hitta alla poster som har ett visst värde.
    
    ![Fältlista](media/k2bridge/k2bridge-field-list.png)
   
    Du kan också filtrera resultaten med hjälp av ikonen (+) förstoringsglas i resultattabellformatvyn för varje post i resultattabellen.
    
     ![Tabelllista](media/k2bridge/k2bridge-table-list.png)
    
1. Välj antingen om du vill **spara** eller **dela** din sökning.

     ![Spara sökning](media/k2bridge/k2bridge-save-search.png)
