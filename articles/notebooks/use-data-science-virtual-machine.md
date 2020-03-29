---
title: Använda virtuella Azure Data Science-datorer
description: Lär dig hur du ansluter till en virtuell Azure Data Science-dator (DSVM) för att utöka beräkningskraften som är tillgänglig för förhandsversionen av Azure Notebooks.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: b4da63b7b2a6da4316215b85a09ca7420745251c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898410"
---
# <a name="use-azure-data-science-virtual-machines"></a>Använda virtuella Azure Data Science-datorer

Som standard körs projekt på den **kostnadsfria** beräkningsnivån, som är begränsad till 4 GB minne och 1 GB data för att förhindra missbruk. Du kan kringgå dessa begränsningar med hjälp av en annan virtuell dator som du har etablerat i en Azure-prenumeration. För detta ändamål är det bästa valet en Azure Data Science Virtual Machine (DSVM) med hjälp av **Data Science Virtual Machine för Linux (Ubuntu)** avbildning. En sådan DSVM levereras förkonfigurerad med allt du behöver för Azure-anteckningsböcker och visas automatiskt i listrutan **Kör** i Azure Notebooks.

> [!Note]
> Azure Notebooks stöds endast på DSVMs som skapats med linux-Ubuntu-avbildningen. Anteckningsböcker stöds inte på Windows 2012-, Windows 2016- eller Linux CentOS-avbildningar.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-dsvm-instance"></a>Skapa en DSVM-instans

Om du vill skapa en ny DSVM-instans följer du instruktionerna för [Skapa en Ubuntu Data Science VM](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Mer information, inklusive prisinformation, finns i [Virtuella datavetenskapdatorer](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Anslut till DSVM

När du har skapat DSVM väljer du listrutan **Kör** på azure notebooks-projektinstrumentpanelen och väljer lämplig DSVM-instans. Listrutan visar DSVM-instanser om följande villkor är uppfyllda:

- Du är inloggad på Azure-anteckningsböcker med ett konto som använder Azure Active Directory (AAD), till exempel ett företagskonto.
- Ditt konto är anslutet till en Azure-prenumeration.
- Du har en eller flera virtuella datorer i den prenumerationen, med minst läsaråtkomst, som använder avbildningen Data Science Virtual Machine för Linux (Ubuntu).

![Data Science Virtual Machine-instanser i listrutan på instrumentpanelen för projektet](media/project-compute-tier-dsvm.png)

När du väljer en DSVM-instans kan Azure Notebooks fråga dig om de specifika datorautentiseringsuppgifter som användes när du skapade den virtuella datorn.

> [!Important]
> Användarnamnet måste vara gemener för att kunna använda det med JupyterHub.

Om något av villkoren inte uppfylls kan du fortfarande ansluta till DSVM. I listrutan väljer du alternativet **Direkt beräkning,** som uppmanar dig att ange ett namn (att visa i listan), den virtuella datorns IP-adress och port (vanligtvis 8000, standardporten som JupyterHub lyssnar på) och autentiseringsuppgifterna för den virtuella datorn:

![Fråga om att samla in serverinformation för alternativet Direkt beräkning](media/project-compute-tier-direct.png)

Du hämtar dessa värden från DSVM-sidan i Azure-portalen.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Komma åt Azure Notebooks-filer från DSVM

Filsystemåtkomst stöds för DSVM-versioner 19.06.15 eller senare. För att kontrollera versionen, först ansluta till din DSVM via `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` SSH, kör sedan följande kommando: (du måste använda den exakta IP-adressen som visas här). Versionsnumret visas i utdata för "version".

Om du vill bevara pariteten för filsökvägar med den **kostnadsfria beräkningsnivån** kan du bara öppna ett projekt i taget på en DSVM. Om du vill öppna ett nytt projekt måste du först stänga av det öppna projektet.

När ett projekt körs på en virtuell dator monteras filerna på jupyterserverns rotkatalog (katalogen som visas i JupyterHub) och ersätter standardfilerna för Azure Notebooks. När du stänger av den virtuella datorn med hjälp av **knappen Avstängning** i användargränssnittet för bärbara datorer återställer Azure Notebooks standardfilerna.

![Knappen Avstängning i Azure-anteckningsböcker](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Skapa nya DSVM-användare

Om flera användare delar en DSVM kan du undvika att blockera varandra genom att skapa och använda en DSVM-användare för varje användare av bärbara datorer:

1. På [Azure-portalen](https://portal.azure.com)navigerar du till din virtuella dator.
1. Under **Support + felsökning** i vänstermarginalen väljer du Återställ **lösenord**.
1. Ange ett nytt **användarnamn**. Användarnamnet måste vara gemener för att kunna använda det med JupyterHub. Ange ett lösenord. Välj sedan **Uppdatera**. (Befintliga användarnamn påverkas inte.)
1. Upprepa föregående steg för eventuella ytterligare användare.

## <a name="next-steps"></a>Nästa steg

Läs mer om DSVM:er vid [introduktion till virtuella Azure Data Science-datorer](/azure/machine-learning/data-science-virtual-machine/overview).
