---
title: Självstudie – dela data med kunder och partner som använder förhandsversionen av Azure Data resurs
description: Självstudie – dela data med kunder och partner som använder förhandsversionen av Azure Data resurs
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 01888f3656765b922c1b646e7ca8e07d81e799f3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838426"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Självstudier: Dela dina data med hjälp av Azure Data resurs Preview

I den här självstudiekursen kommer du lära dig hur du ställer in en ny resurs i Azure Data och börja dela dina data med kunder och partner utanför organisationen Azure. 

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en Dataresurs.
> * Lägga till datauppsättningar i dina Data-resurs.
> * Aktivera ett synkroniseringsschema för din Data-resurs. 
> * Lägga till mottagarna att dela dina Data. 

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Ett Azure Storage-konto: Om du inte redan har ett kan du skapa en [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Behörighet att lägga till rolltilldelning till lagringskontot, som finns i den *Microsoft.Authorization/role tilldelningar/skrivning* behörighet. Den här behörigheten finns i rollen ägare. 
* Dina mottagare Azure-inloggning e-postadress (med sina e-postalias inte fungerar).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Skapa ett konto för Data-resurs

Skapa en resurs i Azure Data i en Azure-resursgrupp.

1. Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet i portalen.

1. Sök efter *Dataresurs*.

1. Välj Dataresurs (förhandsgranskning) och välj **skapa**.

1. Fyll i grundläggande information om din Azure-Dataresursen resursen med följande information. 

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Namn | *datashareacount* | Ange ett namn för ditt konto för data-resursen. |
    | Subscription | Din prenumeration | Välj den prenumeration som du vill använda för ditt konto för data-resursen.|
    | Resource group | *test-resource-group* | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
    | Location | *Östra USA 2* | Välj en region för ditt konto för data-resursen.
    | | |

1. Välj **skapa** att etablera din data resurs-konto. Etablera en ny data resurs konto vanligtvis tar ungefär två minuter eller mindre. 

1. När distributionen är klar väljer du **Gå till resurs**.

## <a name="create-a-data-share"></a>Skapa en Dataresurs

1. Gå till sidan Översikt över dela Data.

    ![Dela dina data](./media/share-receive-data.png "dela dina data") 

1. Välj **börja dela dina data**.

1. Välj **Skapa**.   

1. Fyll i informationen för din Data-resurs. Ange ett namn, beskrivning av Resursinnehåll och villkor för användning (valfritt). 

    ![EnterShareDetails](./media/enter-share-details.png "ange resursinformation") 

1. Välj **fortsätta**

1. Om du vill lägga till datauppsättningar i dina Data-resurs, Välj **lägga till datauppsättningar**. 

    ![Datauppsättningar](./media/datasets.png "datauppsättningar")

1. Välj den typ av datauppsättning som du vill lägga till. 

    ![AddDatasets](./media/add-datasets.png "lägga till datauppsättningar")    

1. Navigera till det objekt som du vill dela och väljer Lägg till datauppsättningar. 

    ![SelectDatasets](./media/select-datasets.png "Välj datauppsättningar")    

1. Ange i e-postadresserna för dina Data-Consumer genom att välja ”+ Lägg till mottagare” på fliken mottagare. 

    ![AddRecipients](./media/add-recipient.png "lägga till mottagarna") 

1. Välj **fortsätta**

1. Om du vill att din datakonsument för att kunna hämta inkrementella uppdateringar för dina data, aktivera schema för ögonblicksbilder. 

    ![EnableSnapshots](./media/enable-snapshots.png "aktivera ögonblicksbilder") 

1. Välj ett start-tid och återkommande intervall. 

1. Välj **fortsätta**

1. I granskningen + skapa fliken kan du granska paketinnehållet, inställningar, mottagare och inställningar för synkronisering. Välj **Skapa**

Din Azure Data filresurs har skapats och mottagaren av din Data filresurs är nu redo att acceptera din inbjudan. 

## <a name="next-steps"></a>Nästa steg

I de här självstudierna upptäckt hur du skapar en Azure-filresurs för Data och bjuda in mottagare. Mer information om hur en Data-Consumer kan acceptera och ta emot en Dataresurs, fortsätter du att den [acceptera och ta emot data](subscribe-to-data-share.md) självstudien. 