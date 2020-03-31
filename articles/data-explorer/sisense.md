---
title: Visualisera data från Azure Data Explorer med Sisense
description: I den här artikeln kan du läsa om hur du konfigurerar Azure Data Explorer som en datakälla för Sisense och visualiserar data.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66358190"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Visualisera data från Azure Data Explorer i Sisense

Sisense är en plattform för analys business intelligence som gör att du kan skapa analysappar som levererar mycket interaktiva användarupplevelser. Business Intelligence och instrumentpanelen rapportering programvara kan du komma åt och kombinera data med ett par klick. Du kan ansluta till strukturerade och ostrukturerade datakällor, koppla tabeller från flera källor med minimal skriptning och kodning och skapa interaktiva webbinstrumentpaneler och rapporter. I den här artikeln får du lära dig hur du konfigurerar Azure Data Explorer som en datakälla för Sisense och visualiserar data från ett exempelkluster.

## <a name="prerequisites"></a>Krav

Du behöver följande för att slutföra den här artikeln:

* [Ladda ner och installera Sisense-appen](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Skapa ett kluster och en databas som innehåller exempeldata för StormEvents. Mer information finns i [Snabbstart: Skapa ett Azure Data Explorer-kluster och databas](create-cluster-database-portal.md) och [matar in exempeldata i Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Ansluta till Sisense-instrumentpaneler med Azure Data Explorer JDBC-anslutning

1. Hämta och kopiera de senaste versionerna av följande jar-filer till *.. \Sisense\DataConnectors\jdbcdrivers\adx* 

    * aktivering-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-samlingar4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-kommentarer-1,0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * e-post-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Öppna **Sisense-appen.**
1. Välj **fliken Data** och välj **+ElastiCube** om du vill skapa en ny ElastiCube-modell.
    
    ![Välj ElastiCube](media/sisense/data-select-elasticube.png)

1. I **Lägg till ny ElastiCube-modell**namnger du ElastiCube-modellen och **Spara**.
   
    ![Lägg till ny ElastiCube-modell](media/sisense/add-new-elasticube-model.png)

1. Välj **+ Data**.

    ![Knappen Välj data](media/sisense/select-data.png)

1. Välj **Allmän JDBC-koppling** på fliken **Välj koppling.**

    ![Välj JDBC-kontakt](media/sisense/select-connector.png)

1. Fyll i följande fält för den **allmänna JDBC-kopplingen** på fliken **Anslut** och välj **Nästa**.

    ![JDBC-anslutningsinställningar](media/sisense/jdbc-connector.png)

    |Field |Beskrivning |
    |---------|---------|
    |Anslutningssträng     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC JARs mapp  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Förarens klassnamn    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Användarnamn   |    AAD-användarnamn     |
    |lösenord     |   AAD-användarlösenord      |

1. Sök i **Välj databas** på fliken **Välj data** för att välja den relevanta databas som du har behörighet till. I det här exemplet väljer du *test1*.

    ![välj databas](media/sisense/select-database.png)

1. I *fönstret Test* (databasnamn):
    1. Markera tabellnamnet om du vill förhandsgranska tabellen och se tabellkolumnnamnen. Du kan ta bort onödiga kolumner.
    1. Markera kryssrutan i den relevanta tabellen för att markera tabellen. 
    1. Välj **Done** (Klar).

    ![välj tabell](media/sisense/select-table-see-columns.png)    

1. Välj **Skapa** för att skapa din datauppsättning. 

    * Välj **Bygg** i **Build**fönstret Bygg .

      ![Bygg fönster](media/sisense/build-window.png)

    * Vänta tills byggprocessen är klar och välj sedan **Bygg lyckades**.

      ![Build lyckades](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Skapa Sisense-instrumentpaneler

1. På **Analytics** fliken Analytics **+**  > väljer du **Ny instrumentpanel** för att skapa instrumentpaneler på den här datauppsättningen.

    ![Ny instrumentpanel](media/sisense/new-dashboard.png)

1. Välj en instrumentpanel och välj **Skapa**. 

    ![Skapa instrumentpanel](media/sisense/create-dashboard.png)

1. Under **Ny widget**väljer du + Välj **data** för att skapa en ny widget. 

    ![Lägga till fält på instrumentpanelen StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Välj **+ Lägg till mer data** om du vill lägga till ytterligare kolumner i diagrammet. 

    ![Lägga till mer data i diagram](media/sisense/add-more-data.png)

1. Välj **+ Widget** för att skapa en annan widget. Dra och släpp widgetar för att ordna om instrumentpanelen.

    ![Storm-instrumentpanelen](media/sisense/final-dashboard.png)

Du kan nu utforska dina data med visuell analys, skapa ytterligare instrumentpaneler och omvandla data till användbara insikter för att påverka ditt företag.

## <a name="next-steps"></a>Nästa steg

* [Skriva frågor för Azure Data Explorer](write-queries.md)

