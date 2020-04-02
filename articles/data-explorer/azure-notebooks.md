---
title: Använda Azure-anteckningsböcker för att analysera data i Azure Data Explorer
description: I det här avsnittet visas hur du skapar en fråga med en Azure Notebook
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522412"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Använda Azure-anteckningsböcker för att analysera data i Azure Data Explorer

[Azure Notebooks](https://notebooks.azure.com/) är en Azure-molntjänst som förenklar skapandet och delningen [av Jupyter-anteckningsböcker](https://jupyter.org/). Azure Notebooks gör det enkelt att kombinera dokumentation, kod och resultatet av att köra koden.

> [!Note]
> * Följande procedur använder Python-klienten i Azure Notebooks-miljön för att fråga Azure Data Explorer. Du kan dock också använda [KQLmagic](https://docs.microsoft.com/azure/data-explorer/kqlmagic) för att fråga Azure Data Explorer.
> * Vissa användare rapporterade problem med att autentisera med Edge. tills sådana problem är lösta, använd en annan webbläsare.

## <a name="create-a-project"></a>Skapa ett projekt

1. Öppna [Azure-anteckningsböcker](https://notebooks.azure.com/) i webbläsaren och logga in.

1. Välj fliken **Mina projekt** i sidhuvudet. 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. Välj **+ Nya projekt**.
    
1. I dialogrutan **Skapa nytt projekt:**
    1. Ange **projektnamnet**.
    1. Avmarkera kryssrutan **Offentlig.**
        >[!Important]
        > Om du inte avmarkerar kryssrutan Offentlig visas projektet på det öppna Internet.
    1. Välj **Skapa**.
    
    ![Skapa ett nytt projekt](media/azurenotebooks/an-create-new-project-blank.png)

    Projekt-ID:n skapas automatiskt.

## <a name="create-a-notebook"></a>Skapa en notebook-fil

1. Skapa en anteckningsbok i det nya projektet. Anteckningsboken bör använda ett [språk som stöds](https://github.com/Azure/azure-kusto-python#minimum-requirements).
Om du vill skapa en anteckningsbok väljer du **+ Ny** och väljer **Anteckningsbok**.

    ![Skapa ny anteckningsbok](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. Ange anteckningsboksnamnet i dialogrutan **Skapa ny anteckningsbok.**

1. Välj **Python 3.6** och välj **Nytt**.
    
    ![Skapa ny anteckningsbok](media/azurenotebooks/an-create-new-notebook.png) 
    
1. Välj den nya anteckningsboken i projektet.

    ![Välj ny anteckningsbok](media/azurenotebooks/an-select-notebook.png)

    Vänta tills pythonkärnan initieras. När den fyllda cirkelikonen ändras till en ikon för ihålig cirkel kan du fortsätta.

    ![Kärnan initierar](media/azurenotebooks/an-python-init-icon.png)

1. Om du vill importera systemmodulen anger du följande kommandon och väljer **Kör:**
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > Om du vill köra en cell kan du också trycka på Skift+Retur.

1.  Om du vill importera SDK-klasser anger du följande kommandon och väljer **Kör:**
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  Om du vill skapa anslutningssträngen och starta Kusto-klienten anger du följande kommandon och väljer **Kör:**  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. Öppna en ny webbläsarflik [https://aka.ms/devicelogin](https://aka.ms/devicelogin)i prompten till . 
   
1. Ange auktoriseringskoden och logga@microsoft.comin ditt AAD -konto. Kusto-klienten `kc` kan nu autentisera till Azure Data Explorer med din identitet.

1. Gå tillbaka till anteckningsboken för att se resultatet av autentiseringen. 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>Köra en Kusto-fråga

1. Ange din Kusto-fråga och välj **Kör**. Ett exempel:

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* [Kusto-python GitHub repo](https://github.com/Azure/azure-kusto-python)
