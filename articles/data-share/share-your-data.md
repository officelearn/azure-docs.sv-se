---
title: 'Självstudie: dela utanför din organisation – Azure-Dataresurs'
description: Självstudie – dela data med kunder och partner med Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 64c5d80b5a2660164b21e71f06e847d5b11e40da
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964437"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Självstudie: dela data med Azure Data Share  

I den här självstudien får du lära dig hur du konfigurerar en ny Azure-Dataresurs och börjar dela dina data med kunder och partner utanför Azure-organisationen. 

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en dataresurs.
> * Lägg till datauppsättningar i dataresursen.
> * Aktivera ett ögonblicks bild schema för data resursen. 
> * Lägg till mottagare i dataresursen. 

## <a name="prerequisites"></a>Krav

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Mottagarens e-postadress för Azure-inloggning (med deras e-postalias fungerar inte).

### <a name="share-from-a-storage-account"></a>Dela från ett lagrings konto:

* Ett Azure Storage konto: om du inte redan har ett kan du skapa ett [Azure Storage konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Behörighet att skriva till lagrings kontot som finns i *Microsoft. Storage/storageAccounts/Write*. Den här behörigheten finns i deltagar rollen.
* Behörighet att lägga till roll tilldelning till lagrings kontot, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den här behörigheten finns i ägar rollen. 


### <a name="share-from-a-sql-based-source"></a>Dela från en SQL-baserad Källa:

* En Azure SQL Database-eller Azure Synapse-analys (tidigare Azure SQL Data Warehouse) med tabeller och vyer som du vill dela.
* Behörighet att skriva till databaserna på SQL Server, som finns i *Microsoft. SQL/Servers/databaser/skriva*. Den här behörigheten finns i deltagar rollen.
* Behörighet för data resursen för åtkomst till data lagret. Detta kan göras genom följande steg: 
    1. Ange själv som Azure Active Directory administratör för SQL Server.
    1. Anslut till Azure SQL Database/informations lagret med hjälp av Azure Active Directory.
    1. Använd Frågeredigeraren (för hands version) för att köra följande skript för att lägga till den hanterade identiteten för data resurs som en db_datareader. Du måste ansluta med Active Directory och inte SQL Server autentisering. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Observera att *< share_acc_name >* är namnet på din data resurs resurs. Om du inte har skapat någon data resurs resurs ännu kan du gå tillbaka till det här kravet senare.  

* En Azure SQL Database användare med "db_datareader"-åtkomst för att navigera och välja de tabeller och/eller vyer som du vill dela. 

* Åtkomst till klient-IP SQL Server-brandvägg. Detta kan göras genom följande steg: 
    1. I SQL Server i Azure Portal navigerar du till *brand väggar och virtuella nätverk*
    1. Klicka på **Växla för** att tillåta åtkomst till Azure-tjänster.
    1. Klicka på **+ Lägg till klient-IP** och klicka på **Spara**. Klientens IP-adress kan komma att ändras. Du kan också lägga till ett IP-intervall. 

### <a name="share-from-azure-data-explorer"></a>Dela från Azure Datautforskaren
* Ett Azure Datautforskaren-kluster med databaser som du vill dela.
* Behörighet att skriva till Azure Datautforskaren-kluster, som finns i *Microsoft. Kusto/kluster/Write*. Den här behörigheten finns i deltagar rollen.
* Behörighet att lägga till roll tilldelning till Azure Datautforskaren-klustret, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den här behörigheten finns i ägar rollen.

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
    | Location | *USA, östra 2* | Välj en region för ditt data resurs konto.
    | | |

1. Välj **skapa** för att etablera ditt data resurs konto. Det tar vanligt vis ungefär 2 minuter att tillhandahålla ett nytt data resurs konto. 

1. När distributionen är klar väljer du **Gå till resurs**.

## <a name="create-a-data-share"></a>Skapa en data resurs

1. Gå till översikts sidan för data delning.

    ![Dela dina data](./media/share-receive-data.png "Dela dina data") 

1. Välj **börja dela dina data**.

1. Välj **Skapa**.   

1. Fyll i informationen för din data resurs. Ange ett namn, resurs typ, en beskrivning av delnings innehåll och användnings villkor (valfritt). 

    ![EnterShareDetails](./media/enter-share-details.png "Ange resurs information") 

1. Välj **Fortsätt**

1. Om du vill lägga till data uppsättningar till din data resurs väljer du **Lägg till data uppsättningar**. 

    ![Datauppsättningar](./media/datasets.png "Datauppsättningar")

1. Välj den data uppsättnings typ som du vill lägga till. Du ser en annan lista över data uppsättnings typer beroende på vilken resurs typ (ögonblicks bild eller på plats) som du har valt i föregående steg. Om du delar från en Azure SQL Database eller Azure SQL Data Warehouse kommer du att uppmanas att ange vissa SQL-autentiseringsuppgifter. Autentisera med den användare som du skapade som en del av förutsättningarna.

    ![AddDatasets](./media/add-datasets.png "Lägg till data uppsättningar")    

1. Navigera till det objekt som du vill dela och välj Lägg till data uppsättningar. 

    ![SelectDatasets](./media/select-datasets.png "Välj data uppsättningar")    

1. På fliken mottagare anger du e-postadresserna till din data konsument genom att välja "+ Lägg till mottagare". 

    ![AddRecipients](./media/add-recipient.png "Lägga till mottagare") 

1. Välj **Fortsätt**

1. Om du har valt typ av ögonblicks bilds resurs kan du konfigurera ögonblicks bilds schema för att tillhandahålla uppdateringar av dina data till din data konsument. 

    ![EnableSnapshots](./media/enable-snapshots.png "Aktivera ögonblicks bilder") 

1. Välj en start tid och upprepnings intervall. 

1. Välj **Fortsätt**

1. På fliken Granska + skapa granskar du paket innehåll, inställningar, mottagare och synkroniseringsinställningar. Välj **Skapa**

Din Azure Data-resurs har nu skapats och mottagaren av din data resurs är nu klar att acceptera din inbjudan. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du lära dig hur du skapar en Azure-Dataresurs och bjuder in mottagare. Om du vill veta mer om hur en data konsument kan godkänna och ta emot en data resurs fortsätter du till kursen för att [godkänna och ta emot data](subscribe-to-data-share.md) . 
