---
title: 'Självstudie: kom igång med Azure Synapse Analytics – visualisera arbets ytans data med Power BI'
description: I den här självstudien får du lära dig hur du skapar en Power BI arbets yta, länkar din Azure Synapse-arbetsyta och skapar en Power BI data uppsättning som använder data i Azure Synapse-arbetsytan.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 64a87df4e4fea9fb29e787ab3420f1a62f41323d
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592421"
---
# <a name="visualize-data-with-power-bi"></a>Visualisera data med Power BI

I den här självstudien får du lära dig hur du skapar en Power BI arbets yta, länkar din Azure Synapse-arbetsyta och skapar en Power BI data uppsättning som använder data i din Azure Synapse-arbetsyta. 

## <a name="overview"></a>Översikt

Från NYC taxi-data skapade vi sammanställda data uppsättningar i två tabeller:
- **nyctaxi.passengercountstats**
- **SQLDB1. dbo. PassengerCountStats**

Du kan länka en Power BI arbets yta till din Azure Synapse-arbetsyta. Med den här funktionen kan du enkelt hämta data till din Power BI-arbetsyta. Du kan redigera dina Power BI rapporter direkt i din Azure Synapse-arbetsyta.

### <a name="create-a-power-bi-workspace"></a>Skapa en Power BI arbets yta

1. Logga in på [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
1. Skapa en ny Power BI arbets yta med namnet **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Länka din Azure Synapse-arbetsyta till din nya Power BI-arbetsyta

1. I Synapse Studio går du till **Hantera**  >  **länkade tjänster**.
1. Välj **ny**  >  **Anslut till Power BI**
1. Ange **namnet** till **NYCTaxiWorkspace1**
1. Ange **arbets ytans namn** till **NYCTaxiWorkspace1**
1. Välj **Skapa**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Skapa en Power BI-datauppsättning som använder data i din Azure Synapse-arbetsyta

1. I Synapse Studio går du till **utveckla**  >  **Power BI**.
1. Gå till **NYCTaxiWorkspace1**  >  **Power BI data uppsättningar** och välj **ny Power BI data uppsättning**.
1. Hovra över **SQLDB1** -databasen och välj **Hämta. pbids-fil**.
1. Öppna den nedladdade **. pbids** -filen. Power BI Skriv bordet öppnas och ansluter automatiskt till **SQLDB1** i din Azure Synapse-arbetsyta.
1. Om en dialog ruta visas som kallas **SQL Server-databas** :
    1. Välj **Microsoft-konto**.
    1. Välj **Logga** in och logga in på ditt konto.
    1. Välj **Anslut**.
1. När dialog rutan **navigatör** öppnas kontrollerar du tabellen **PassengerCountStats** och väljer **load**.
1. När dialog rutan **anslutnings inställningar** visas väljer du **DirectQuery**  >  **OK**.
1. Välj **rapport** knappen på vänster sida.
1. Lägg till **linje diagram** i rapporten.
    1. Dra kolumnen **PassengerCount** till **visualiserings**  >  **axeln**.
    1. Dra kolumnerna **SumTripDistance** och **AvgTripDistance** till **visualiserings**  >  **värden**.
1. På fliken **Start** väljer du **publicera**.
1. Välj **Spara** för att spara ändringarna.
1. Välj fil namnet **PassengerAnalysis. pbix** och välj sedan **Spara**.
1. I **Välj ett mål** väljer du **NYCTaxiWorkspace1** och klickar sedan på **Välj**.
1. Vänta tills publiceringen har slutförts.

### <a name="configure-authentication-for-your-dataset"></a>Konfigurera autentisering för din data uppsättning

1. Öppna [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) och **Logga** in.
1. På den vänstra sidan, under **arbets ytor** , väljer du arbets ytan **NYCTaxiWorkspace1** .
1. På arbets ytan letar du reda på en data uppsättning som kallas **passagerar analys** och en rapport som kallas **passagerar analys**.
1. Hovra över **PassengerAnalysis** -datauppsättningen, Välj knappen med tre punkter (...) och välj sedan **Inställningar**.
1. I **autentiseringsuppgifter för data källa** anger du **autentiseringsmetoden** till **OAuth2** och väljer sedan **Logga** in.

### <a name="edit-a-report-in-synapse-studio"></a>Redigera en rapport i Synapse Studio

1. Gå tillbaka till Synapse Studio och välj **Stäng och uppdatera**.
1. Gå till **utveckla** hubben.
1. Hovra över **Power BI** och välj noden uppdatera **Power BI rapporter** .
1. Under **Power BI** bör du se:
    * Under **NYCTaxiWorkspace1**  >  **Power BI data uppsättningar** , en ny data uppsättning som kallas **PassengerAnalysis**.
    * Under **NYCTaxiWorkspace1**  >  **Power BI rapporter** kallas en ny rapport som heter **PassengerAnalysis**.
1. Välj **PassengerAnalysis** -rapporten. Rapporten öppnas och du kan redigera den direkt i Synapse Studio.

## <a name="monitor-activities"></a>Övervaka aktiviteter

1. Gå till **Monitor** Hub i Synapse Studio.
1. På den här platsen kan du se en historik över alla aktiviteter som sker i arbets ytan och vilka som är aktiva nu.
1. Utforska **pipeline-körningar** , **Apache Spark program** och **SQL-begäranden** för att se vad du redan har gjort i arbets ytan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka](get-started-monitor.md)
                                 

