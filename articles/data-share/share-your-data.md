---
title: 'Självstudie: dela utanför din organisation – Azure-Dataresurs'
description: Självstudie – dela data med kunder och partner med Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 4ef9256404b0d0d4d6379e4f5a76c0d41a38c7cd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499334"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Självstudie: dela data med Azure Data Share  

I den här självstudien får du lära dig hur du konfigurerar en ny Azure-Dataresurs och börjar dela dina data med kunder och partner utanför Azure-organisationen. 

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en dataresurs.
> * Lägg till datauppsättningar i dataresursen.
> * Aktivera ett synkroniseringsschema för dataresursen. 
> * Lägg till mottagare i dataresursen. 

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Mottagarens e-postadress för Azure-inloggning (med deras e-postalias fungerar inte).

### <a name="share-from-a-storage-account"></a>Dela från ett lagrings konto:

* Ett Azure Storage konto: om du inte redan har ett kan du skapa ett [Azure Storage konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Behörighet att lägga till roll tilldelning till lagrings kontot, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv* behörighet. Den här behörigheten finns i ägar rollen. 

### <a name="share-from-a-sql-based-source"></a>Dela från en SQL-baserad Källa:

* En Azure SQL Database eller Azure SQL Data Warehouse med tabeller och vyer som du vill dela.
* Behörighet för data resursen för åtkomst till data lagret. Detta kan göras genom följande steg: 
    1. Ange själv som Azure Active Directory administratör för-servern.
    1. Anslut till Azure SQL Database/informations lagret med hjälp av Azure Active Directory.
    1. Använd Frågeredigeraren (för hands version) för att köra följande skript för att lägga till data resursen MSI som db_owner. Du måste ansluta med Active Directory och inte SQL Server autentisering. 
    
```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```                   
Observera att *< share_acc_name >* är namnet på ditt data resurs konto. Om du inte har skapat ett data resurs konto ännu kan du komma tillbaka till det här kravet senare.  

* Klientens IP SQL Server brand Väggs åtkomst: Detta kan göras via följande steg: 1. Navigera till *brand väggar och virtuella nätverk* 1. Klicka på **Växla för** att tillåta åtkomst till Azure-tjänster. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Skapa ett data resurs konto

Skapa en Azure Data Share-resurs i en Azure-resurs grupp.

1. Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet i portalen.

1. Sök efter *data resurs*.

1. Välj data resurs och välj **skapa**.

1. Fyll i grundläggande information om din Azure Data Share-resurs med följande information. 

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Namn | *datashareacount* | Ange ett namn för ditt data resurs konto. |
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som du vill använda för ditt data resurs konto.|
    | Resursgrupp | *test-resource-group* | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
    | Plats | *USA, östra 2* | Välj en region för ditt data resurs konto.
    | | |

1. Välj **skapa** för att etablera ditt data resurs konto. Det tar vanligt vis ungefär 2 minuter att tillhandahålla ett nytt data resurs konto. 

1. När distributionen är klar väljer du **Gå till resurs**.

## <a name="create-a-data-share"></a>Skapa en data resurs

1. Gå till översikts sidan för data delning.

    ![Dela dina data](./media/share-receive-data.png "Dela dina data") 

1. Välj **börja dela dina data**.

1. Välj **Skapa**.   

1. Fyll i informationen för din data resurs. Ange ett namn, en beskrivning av delnings innehåll och användnings villkor (valfritt). 

    ![EnterShareDetails](./media/enter-share-details.png "Ange resurs information") 

1. Välj **Fortsätt**

1. Om du vill lägga till data uppsättningar till din data resurs väljer du **Lägg till data uppsättningar**. 

    ![Datauppsättningar](./media/datasets.png "Datauppsättningar")

1. Välj den data uppsättnings typ som du vill lägga till. 

    ![AddDatasets](./media/add-datasets.png "Lägg till data uppsättningar")    

1. Navigera till det objekt som du vill dela och välj Lägg till data uppsättningar. 

    ![SelectDatasets](./media/select-datasets.png "Välj data uppsättningar")    

1. På fliken mottagare anger du e-postadresserna till din data konsument genom att välja "+ Lägg till mottagare". 

    ![AddRecipients](./media/add-recipient.png "Lägga till mottagare") 

1. Välj **Fortsätt**

1. Om du vill att din data konsument ska kunna hämta stegvisa uppdateringar av dina data aktiverar du schemat för ögonblicks bilder. 

    ![EnableSnapshots](./media/enable-snapshots.png "Aktivera ögonblicks bilder") 

1. Välj en start tid och upprepnings intervall. 

1. Välj **Fortsätt**

1. På fliken Granska + skapa granskar du paket innehåll, inställningar, mottagare och synkroniseringsinställningar. Välj **Skapa**

Din Azure Data-resurs har nu skapats och mottagaren av din data resurs är nu klar att acceptera din inbjudan. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du lära dig hur du skapar en Azure-Dataresurs och bjuder in mottagare. Om du vill veta mer om hur en data konsument kan godkänna och ta emot en data resurs fortsätter du till kursen för att [godkänna och ta emot data](subscribe-to-data-share.md) . 
