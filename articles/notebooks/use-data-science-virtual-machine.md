---
title: Använda Azure virtuella datorer för datavetenskap
description: Anslut till en Azure Science VM DSVM (Data) att utöka databearbetningskraft som är tillgängliga till Azure-datorer.
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: getroyer
ms.openlocfilehash: ab3b742d50cc141420f9bffa1961a6e170b99d2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234355"
---
# <a name="use-azure-data-science-virtual-machines"></a>Använda Azure virtuella datorer för datavetenskap

Projekt körs som standard den **kostnadsfria Compute** åtkomstnivå, vilket är begränsad till 4 GB minne och 1 GB data för att förhindra missbruk. Du kan kringgå dessa begränsningar med hjälp av en annan virtuell dator som du har etablerat i en Azure-prenumeration. För detta ändamål, det bästa valet är en Azure Data Science Virtual Machine (DSVM) med hjälp av den **Data Science Virtual Machine för Linux (Ubuntu)** bild. Sådana DSVM är förkonfigurerad med allt du behöver för Azure-anteckningsböcker och visas automatiskt på den **kör** listrutan i Azure-anteckningsböcker.

> [!Note]
> Azure-datorer stöds endast på Dsvm som skapats med on Linux Ubuntu-avbildningen. Anteckningsböcker stöds inte på Windows 2012, Windows 2016 eller CentOS Linux-avbildningar.

## <a name="create-a-dsvm-instance"></a>Skapa en DSVM-instans

Följ anvisningarna för att skapa en ny instans av DSVM på [skapa en Ubuntu virtuell dator för datavetenskap](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Mer information, inklusive information om priser finns i [virtuella datorer för datavetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Ansluta till DSVM

När du har skapat DSVM, Välj den **kör** listrutan i Azure-anteckningsböcker projektet instrumentpanelen och välj lämplig DSVM-instans. Den nedrullningsbara listan visar DSVM instanser om följande villkor är uppfyllda:

- Du har loggat in Azure-anteckningsböcker med ett konto som använder Azure Active Directory (AAD), till exempel ett företagskonto.
- Ditt konto är ansluten till en Azure-prenumeration.
- Du har en eller flera virtuella datorer i prenumerationen, med minst läsåtkomst som använder den virtuella datorn för datavetenskap för Linux (Ubuntu)-avbildning.)

![Data Science Virtual Machine-instanser i listrutan på instrumentpanelen för projektet](media/project-compute-tier-dsvm.png)

När du väljer en DSVM-instans, kan Azure-datorer efterfrågas specifik dator-autentiseringsuppgifterna som används när du skapade den virtuella datorn.

Om något av villkoren inte uppfylls, kan du fortfarande ansluta till DSVM. I listrutan, väljer den **direkt Compute** alternativet, där du uppmanas ange ett namn (som visas i listan), den Virtuella datorns IP-adress och port (vanligtvis 8000, standardporten till vilken JupyterHub lyssnar) och VM-autentiseringsuppgifter:

![Uppmaning om att samla in informationen för direkta beräkningsalternativet i server](media/project-compute-tier-direct.png)

Du hämtar dessa värden från sidan DSVM i Azure-portalen.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Komma åt Azure anteckningsböcker filer från DSVM

Att bevara paritet av sökvägar med den **kostnadsfria Compute** nivå, du kan bara öppna ett projekt i taget på en DSVM. Om du vill öppna ett nytt projekt, måste du stänga av projektet är öppet först.

![Avsluta-knappen i Azure-anteckningsböcker](media/shutdown.png)

När ett projekt körs på en virtuell dator, filerna är monterade på rotkatalogen för Jupyter-server (den katalog som visas i JupyterHub), ersätter standardfiler för Azure-anteckningsböcker. När du stänger en virtuell dator med hjälp av den **avstängning** knappen i anteckningsboken Användargränssnittet för Azure-anteckningsböcker återställer standardfiler som.

## <a name="next-steps"></a>Nästa steg

Läs mer om Dsvm på [introduktion till Azure Data Science Virtual Machines](/azure/machine-learning/data-science-virtual-machine/overview).
