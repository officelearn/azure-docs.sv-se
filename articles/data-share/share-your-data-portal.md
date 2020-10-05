---
title: Dela utanför organisationen (Azure Portal) – snabb start för Azure Data Share
description: Lär dig hur du delar data med kunder och partner med Azure Data share i den här snabb starten.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 08/19/2020
ms.openlocfilehash: 41598c04af78d4366435259357d8f897ac178942
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "89489943"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>Snabb start: dela data med Azure Data share i Azure Portal

I den här snabb starten får du lära dig hur du konfigurerar en ny Azure-Dataresurs med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.


## <a name="create-a-data-share-account"></a>Skapa ett data resurs konto

Skapa en Azure Data Share-resurs i en Azure-resurs grupp.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj knappen **skapa en resurs** (+) i det övre vänstra hörnet i portalen.

1. Sök efter *data resurs*.

1. Välj **data resurs** och välj **skapa**.

1. Fyll i grundläggande information om din Azure Data Share-resurs med följande information. 

   **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
   |---|---|---|
   | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som du vill använda för ditt data resurs konto.|
   | Resursgrupp | *test-resurs-grupp* | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
   | Plats | *USA, östra 2* | Välj en region för ditt data resurs konto.
   | Namn | *datashareaccount* | Ange ett namn för ditt data resurs konto. |

1. Välj **Granska + skapa**och sedan **skapa** för att etablera ditt data resurs konto. Det tar vanligt vis ungefär 2 minuter att tillhandahålla ett nytt data resurs konto.

1. När distributionen är klar väljer du **Gå till resurs**.

## <a name="create-a-share"></a>Skapa en resurs

1. Gå till översikts sidan för data delning.

   ![Dela dina data](./media/share-receive-data.png "Dela dina data") 

1. Välj **börja dela dina data**.

1. Välj **Skapa**.

1. Fyll i informationen för din resurs. Ange ett namn, resurs typ, en beskrivning av delnings innehåll och användnings villkor (valfritt). 

   ![EnterShareDetails](./media/enter-share-details.png "Ange resurs information") 

1. Välj **Fortsätt**.

1. Om du vill lägga till data uppsättningar i din resurs väljer du **Lägg till data uppsättningar**. 

   ![Lägg till data uppsättningar till din resurs](./media/datasets.png "Datauppsättningar")

1. Välj den data uppsättnings typ som du vill lägga till. Du ser en annan lista över data uppsättnings typer beroende på vilken resurs typ (ögonblicks bild eller på plats) som du har valt i föregående steg. Om du delar från en Azure SQL Database eller Azure Synapse Analytics uppmanas du att ange vissa SQL-autentiseringsuppgifter. Autentisera med den användare som du skapade som en del av förutsättningarna.

   ![AddDatasets](./media/add-datasets.png "Lägg till data uppsättningar")    

1. Navigera till det objekt som du vill dela och välj Lägg till data uppsättningar. 

   ![SelectDatasets](./media/select-datasets.png "Välj data uppsättningar")    

1. På fliken mottagare anger du e-postadresserna till din data konsument genom att välja "+ Lägg till mottagare".

   ![AddRecipients](./media/add-recipient.png "Lägga till mottagare") 

1. Välj **Fortsätt**.

1. Om du har valt typ av ögonblicks bilds resurs kan du konfigurera ögonblicks bilds schema för att tillhandahålla uppdateringar av dina data till din data konsument. 

   ![EnableSnapshots](./media/enable-snapshots.png "Aktivera ögonblicks bilder") 

1. Välj en start tid och upprepnings intervall. 

1. Välj **Fortsätt**.

1. På fliken Granska + skapa granskar du paket innehåll, inställningar, mottagare och synkroniseringsinställningar. Välj **Skapa**.

Din Azure Data-resurs har nu skapats och mottagaren av din data resurs är nu klar att acceptera din inbjudan.

## <a name="clean-up-resources"></a>Rensa resurser

När resursen inte längre behövs går du till sidan **Översikt över data resurs** och väljer **ta bort** för att ta bort den.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en Azure-Dataresurs. Om du vill veta mer om hur en data konsument kan godkänna och ta emot en data resurs fortsätter du till kursen för att [godkänna och ta emot data](subscribe-to-data-share.md) . 
