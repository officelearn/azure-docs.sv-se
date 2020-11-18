---
title: Använd Azure Data Science Virtual Machines
description: Lär dig hur du ansluter till en Azure-Data Science Virtual Machine (DSVM) för att utöka beräknings kraften som är tillgänglig för Azure Notebooks för hands versionen.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: 7d65a39c1a1d302e565b59db1644af2c08befb6b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843170"
---
# <a name="use-azure-data-science-virtual-machines"></a>Använd Azure Data Science Virtual Machines

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Som standard körs projekt på den **kostnads fria beräknings** nivån, som är begränsad till 4 GB minne och 1 GB data för att förhindra missbruk. Du kan kringgå de här begränsningarna genom att använda en annan virtuell dator som du har etablerad i en Azure-prenumeration. Det bästa valet är ett Azure-Data Science Virtual Machine (DSVM) med **data science Virtual Machine för Linux-avbildningen (Ubuntu)** . En sådan DSVM är förkonfigurerad med allt du behöver för Azure Notebooks och visas automatiskt i den nedrullningsbara List rutan **Kör** i Azure Notebooks.

> [!Note]
> Azure Notebooks stöds endast på Dsvm som skapats med på Linux Ubuntu-avbildningen. Antecknings böcker stöds inte på Windows 2012-, Windows 2016-eller Linux CentOS-avbildningar.

## <a name="create-a-dsvm-instance"></a>Skapa en DSVM-instans

Följ instruktionerna för att [skapa en Ubuntu data science VM](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md)för att skapa en ny DSVM-instans. Mer information, inklusive pris information, finns i [Virtual Machines för data vetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Anslut till DSVM

När du har skapat DSVM väljer du List rutan **Kör** på instrument panelen för Azure Notebooks-projektet och väljer lämplig DSVM-instans. Den nedrullningsbara listan visar DSVM-instanser om följande villkor är uppfyllda:

- Du är inloggad på Azure Notebooks med ett konto som använder Azure Active Directory (AAD), till exempel ett företags konto.
- Ditt konto är anslutet till en Azure-prenumeration.
- Du har en eller flera virtuella datorer i den prenumerationen, med minst läsar åtkomst, som använder Data Science Virtual Machine för Linux-avbildningen (Ubuntu).)

![Data Science Virtual Machine instanser i list rutan på instrument panelen för projektet](media/project-compute-tier-dsvm.png)

När du väljer en DSVM-instans kan Azure Notebooks fråga dig om de autentiseringsuppgifter som ska användas när du skapade den virtuella datorn.

> [!Important]
> Användar namnet måste vara i gemener för att kunna användas med JupyterHub.

Om något av villkoren inte uppfylls kan du fortfarande ansluta till DSVM. I list rutan väljer du alternativet **direkt beräkning** , som uppmanar dig att ange ett namn (som ska visas i listan), den virtuella datorns IP-adress och port (vanligt vis 8000, standard porten som JupyterHub lyssnar) och de virtuella datorernas autentiseringsuppgifter:

![Uppmana att samla in Server information för det direkta beräknings alternativet](media/project-compute-tier-direct.png)

Du hämtar dessa värden från sidan DSVM i Azure Portal.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Komma åt Azure Notebooks-filer från DSVM

Åtkomst till fil systemet stöds för DSVM-versioner 19.06.15 eller senare. För att kontrol lera versionen ansluter du först till din DSVM via SSH och kör sedan följande kommando: `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (du måste använda den exakta IP-adressen som visas här). Versions numret visas i utdata för "version".

Om du vill bevara paritet av fil Sök vägar med den **kostnads fria beräknings** nivån kan du bara öppna ett projekt i taget på en DSVM. Om du vill öppna ett nytt projekt måste du först stänga det öppna projektet.

När ett projekt körs på en virtuell dator monteras filerna på Jupyter-serverns rot Katalog (den katalog som visas i JupyterHub) och ersätter standard Azure Notebooks-filerna. När du stänger av den virtuella datorn med knappen **Stäng** av i anteckningsbok-gränssnittet, Azure Notebooks återställer standardfilerna.

![Knappen Stäng av i Azure Notebooks](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Skapa nya DSVM-användare

Om flera användare delar en DSVM kan du undvika att blockera varandra genom att skapa och använda en DSVM-användare för varje Notebook-användare:

1. På [Azure Portal](https://portal.azure.com)navigerar du till den virtuella datorn.
1. Välj **Återställ lösen ord** under **support + fel sökning** i vänstermarginalen.
1. Ange ett nytt **användar namn**. Användar namnet måste vara i gemener för att kunna användas med JupyterHub. Ange ett lösen ord. Välj sedan **Uppdatera**. (Befintliga användar namn påverkas inte.)
1. Upprepa föregående steg för ytterligare användare.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om Dsvm för [Introduktion till Azure Data Science Virtual Machines](../machine-learning/data-science-virtual-machine/overview.md).