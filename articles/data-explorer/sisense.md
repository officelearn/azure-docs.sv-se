---
title: Visualisera data från Azure Data Explorer med hjälp av Sisense
description: I den här artikeln lär du dig hur du konfigurerar Datautforskaren i Azure som en datakälla för Sisense och visualisera data.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358190"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Visualisera data från Azure Data Explorer i Sisense

Sisense är en business intelligence analysplattform som gör det möjligt för dig att skapa analytics-appar som levererar interaktiva användarupplevelser. Business intelligence och instrumentpanelen reporting programvara kan du komma åt och kombinera data med några få klick. Du kan ansluta till strukturerade och Ostrukturerade data källor, ansluter till tabeller från flera källor med minimal skript och kodning och skapa interaktiva webb-instrumentpaneler och rapporter. I den här artikeln lär du dig att konfigurera Datautforskaren i Azure som en datakälla för Sisense och visualisera data från en exempel-klustret.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att slutföra den här artikeln:

* [Ladda ned och installera Sisense app](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Skapa ett kluster och databasen som innehåller exempeldata StormEvents. Mer information finns i [ Snabbstart: Skapa ett Azure Data Explorer-kluster och databasen](create-cluster-database-portal.md) och [mata in exempeldata i Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Ansluta till Sisense instrumentpaneler med hjälp av Azure Data Explorer JDBC-anslutning

1. Ladda ned och kopiera de senaste versionerna av de följande jar-filerna till *... \Sisense\DataConnectors\jdbcdrivers\adx* 

    * activation-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * e-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Öppna **Sisense** app.
1. Välj **Data** fliken och markera **+ ElastiCube** att skapa en ny ElastiCube modell.
    
    ![Select ElastiCube](media/sisense/data-select-elasticube.png)

1. I **lägga till ny ElastiCube modell**, namnge ElastiCube modellen och **spara**.
   
    ![Lägg till ny ElastiCube modell](media/sisense/add-new-elasticube-model.png)

1. Välj **+ Data**.

    ![Knappen Välj data](media/sisense/select-data.png)

1. I **Välj Anslutningsappen** väljer du en **allmän JDBC** connector.

    ![Välj JDBC-anslutning](media/sisense/select-connector.png)

1. I den **Connect** fliken, Fyll i följande fält för den **allmän JDBC** anslutningen och välj **nästa**.

    ![Inställningarna för JDBC-anslutningsprogrammet](media/sisense/jdbc-connector.png)

    |Fält |Beskrivning |
    |---------|---------|
    |Anslutningssträng     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC JAR: er mapp  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Drivrutinens klassnamn    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Användarnamn   |    AAD-användarnamn     |
    |Lösenord     |   AAD-användarlösenord      |

1. I den **väljer Data** fliken, Sök **Välj databas** att välja relevanta databasen som du har behörighet. I det här exemplet väljer *test1*.

    ![Välj databas](media/sisense/select-database.png)

1. I *testa* (databasnamn)-fönstret:
    1. Välj tabellnamnet att förhandsgranska tabellen och finns i kolumnen Tabellnamn. Du kan ta bort onödiga kolumner.
    1. Markera kryssrutan för den aktuella tabellen och välj tabellen. 
    1. Välj **Done** (Klar).

    ![Välj tabell](media/sisense/select-table-see-columns.png)    

1. Välj **skapa** att bygga din datauppsättning. 

    * I den **skapa** väljer **skapa**.

      ![Skapa fönster](media/sisense/build-window.png)

    * Vänta tills genereringsprocessen är klar och välj sedan **skapa lyckades**.

      ![Genereringen lyckades](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Skapa Sisense instrumentpaneler

1. I **Analytics** fliken **+**  >  **ny instrumentpanel** att skapa instrumentpaneler för den här datauppsättningen.

    ![Ny instrumentpanel](media/sisense/new-dashboard.png)

1. Välj en instrumentpanel och välj **skapa**. 

    ![Skapa instrumentpanel](media/sisense/create-dashboard.png)

1. Under **nya Widget**väljer **+ Välj Data** att skapa en ny widget. 

    ![Lägg till fält i StormEvents instrumentpanel](media/sisense/storm-dashboard-add-field.png)  

1. Välj **+ Lägg till mer Data** att lägga till fler kolumner i diagrammet. 

    ![Lägg till mer data i diagram](media/sisense/add-more-data.png)

1. Välj **+ Widget** att skapa en annan widget. Dra och släpp widgetar att ordna om din instrumentpanel.

    ![Storm-instrumentpanelen](media/sisense/final-dashboard.png)

Nu kan du utforska dina data med visual analytics, ytterligare instrumentpaneler och omvandla data till användbara insikter för att göra en inverkan på verksamheten.

## <a name="next-steps"></a>Nästa steg

* [Skriva frågor för Azure Data Explorer](write-queries.md)

