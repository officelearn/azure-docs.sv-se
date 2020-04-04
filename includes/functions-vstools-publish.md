---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 0e69a11ab81406f650049e3c7d4d446b6628b29b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657585"
---
1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

2. I **Välj ett publiceringsmål**använder du de publiceringsalternativ som anges i följande tabell: 

    | Alternativ      | Beskrivning                                |
    | ------------ |  -------------------------------------------------- |
    | **Förbrukningsplan för Azure-funktioner** | Skapa en funktionsapp i en Azure-molnmiljö som körs i en [förbrukningsplan](../articles/azure-functions/functions-scale.md#consumption-plan). När du använder en förbrukningsplan betalar du bara för körningar av din funktionsapp. Andra hosting planer medför högre kostnader. Om du kör i en annan plan än en förbrukningsplan måste du hantera [skalningen av funktionsappen](../articles/azure-functions/functions-scale.md).| 
    | **Skapa nytt** | En ny funktionsapp, med relaterade resurser, skapas i Azure. <br/>Om du väljer **Välj befintlig**skrivs alla filer i den befintliga funktionsappen i Azure över av filer från det lokala projektet. Använd det här alternativet endast när du publicerar om uppdateringar till en befintlig funktionsapp. |
    | **Kör från paketfil** | Din funktionsapp distribueras med [Zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) med [kör-från-paket-läge](../articles/azure-functions/run-functions-from-deployment-package.md) aktiverat. Den här distributionen, som resulterar i bättre prestanda, är det rekommenderade sättet att köra dina funktioner. <br/>Om du inte använder det här alternativet måste du stoppa ditt funktionsappprojekt från att köras lokalt innan du publicerar till Azure. |

    ![Välj ett publiceringsmål](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)


3. Välj **Skapa profil**. Om du inte redan har loggat in på ditt Azure-konto från Visual Studio väljer du **Logga in**. Du kan också skapa ett kostnadsfritt Azure-konto.

4. I **Apptjänst: Skapa nya**använder du de värden som anges i följande tabell:

    | Inställning      | Värde  | Beskrivning                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Globalt unikt namn | Namn som unikt identifierar din nya funktionsapp. Acceptera det här namnet eller ange ett nytt namn. Giltiga tecken `a-z`är: `0-9` `-`, och . |
    | **Prenumeration** | Din prenumeration | Den Azure-prenumeration som ska användas. Acceptera den här prenumerationen eller välj en ny i listrutan. |
    | **[Resursgrupp](../articles/azure-resource-manager/management/overview.md)** | Namn på resursgruppen |  Resursgruppen där funktionsappen ska skapas. Välj en befintlig resursgrupp i listrutan eller välj **Nytt** för att skapa en ny resursgrupp.|
    | **[Hosting Plan](../articles/azure-functions/functions-scale.md)** | Namn på din värdplan | Välj **Ny** om du vill konfigurera en serverlös plan. Se till att välja **Förbrukning** under **Storlek**. När du publicerar projektet i en funktionsapp som körs i en [förbrukningsplan](../articles/azure-functions/functions-scale.md#consumption-plan)betalar du bara för körningar av din funktionsapp. Andra hosting planer medför högre kostnader. Om du kör i en annan plan än **Förbrukning**måste du hantera [skalningen av funktionsappen](../articles/azure-functions/functions-scale.md).  |
    | **Location** | Plats för apptjänsten | Välj en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller andra tjänster som dina funktioner har åtkomst till. |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Allmänt lagringskonto | Ett Azure Storage-konto krävs av funktionskörningen. Välj **Ny** om du vill konfigurera ett allmänt lagringskonto. Du kan också välja ett befintligt konto som uppfyller kraven för [lagringskonto](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Dialogrutan Skapa App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

5. Välj **Skapa** om du vill skapa en funktionsapp och dess relaterade resurser i Azure med dessa inställningar och distribuera din funktionsprojektkod. 

6. Välj Publicera och när distributionen är klar, anteckna **värdet för url-platsen,** som är adressen till din funktionsapp i Azure.

    ![Meddelande som anger att publiceringen lyckades](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
