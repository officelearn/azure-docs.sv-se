---
title: 'Självstudier: Dela utanför din organisation – för hands version av Azure Data-resurs'
description: Självstudie – dela data med kunder och partner med hjälp av Azure Data Share Preview
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: f7df46a6a6f149ef0228fda8c967469a25dc3d50
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327421"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Självstudier: Dela dina data med hjälp av Azure Data Share Preview

I den här självstudien får du lära dig hur du konfigurerar en ny Azure-Dataresurs och börjar dela dina data med kunder och partner utanför Azure-organisationen. 

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en data resurs.
> * Lägg till data uppsättningar till data resursen.
> * Aktivera ett synkroniseringsschema för data resursen. 
> * Lägg till mottagare i data resursen. 

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Ett Azure Storage konto: Om du inte redan har en, kan du skapa ett [Azure Storage konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Behörighet att lägga till roll tilldelning till lagrings kontot, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv* behörighet. Den här behörigheten finns i ägar rollen. 
* Mottagarnas e-postadress för Azure-inloggning (med deras e-postalias fungerar inte).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Skapa ett data resurs konto

Skapa en Azure Data Share-resurs i en Azure-resurs grupp.

1. Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet i portalen.

1. Sök efter *data resurs*.

1. Välj data resurs (för hands version) och välj **skapa**.

1. Fyll i grundläggande information om din Azure Data Share-resurs med följande information. 

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Name | *datashareacount* | Ange ett namn för ditt data resurs konto. |
    | Subscription | Din prenumeration | Välj den Azure-prenumeration som du vill använda för ditt data resurs konto.|
    | Resource group | *test-resource-group* | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
    | Location | *USA, östra 2* | Välj en region för ditt data resurs konto.
    | | |

1. Välj **skapa** för att etablera ditt data resurs konto. Det tar vanligt vis ungefär 2 minuter att tillhandahålla ett nytt data resurs konto. 

1. När distributionen är klar väljer du **Gå till resurs**.

## <a name="create-a-data-share"></a>Skapa en data resurs

1. Gå till översikts sidan för data delning.

    ![Dela dina]data genom att dela dina(./media/share-receive-data.png "data") 

1. Välj **börja dela dina data**.

1. Välj **Skapa**.   

1. Fyll i informationen för din data resurs. Ange ett namn, en beskrivning av delnings innehåll och användnings villkor (valfritt). 

    ![EnterShareDetails](./media/enter-share-details.png "Ange resurs information") 

1. Välj **Fortsätt**

1. Om du vill lägga till data uppsättningar till din data resurs väljer du **Lägg till data uppsättningar**. 

    (./media/datasets.png "Data uppsättningar") för ![data uppsättningar]

1. Välj den data uppsättnings typ som du vill lägga till. 

    ![AddDatasets](./media/add-datasets.png "Lägg till data uppsättningar")    

1. Navigera till det objekt som du vill dela och välj Lägg till data uppsättningar. 

    ![SelectDatasets](./media/select-datasets.png "Välj data uppsättningar")    

1. På fliken mottagare anger du e-postadresserna till din data konsument genom att välja "+ Lägg till mottagare". 

    ![AddRecipients](./media/add-recipient.png "Lägg till mottagare") 

1. Välj **Fortsätt**

1. Om du vill att din data konsument ska kunna hämta stegvisa uppdateringar av dina data aktiverar du schemat för ögonblicks bilder. 

    ![EnableSnapshots](./media/enable-snapshots.png "Aktivera ögonblicks bilder") 

1. Välj en start tid och upprepnings intervall. 

1. Välj **Fortsätt**

1. På fliken Granska + skapa granskar du paket innehåll, inställningar, mottagare och synkroniseringsinställningar. Välj **Skapa**

Din Azure Data-resurs har nu skapats och mottagaren av din data resurs är nu klar att acceptera din inbjudan. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du lära dig hur du skapar en Azure-Dataresurs och bjuder in mottagare. Om du vill veta mer om hur en data konsument kan godkänna och ta emot en data resurs fortsätter du till kursen för att [godkänna och ta emot data](subscribe-to-data-share.md) . 
