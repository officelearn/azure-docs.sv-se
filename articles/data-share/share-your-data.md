---
title: 'Självstudiekurs: Dela utanför din organisation - Azure Data Share'
description: Självstudiekurs - Dela data med kunder och partner med Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: a8265680f74b2d5679d1ebfbb2873dd096f498a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77083058"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Självstudiekurs: Dela data med Azure Data Share  

I den här självstudien får du lära dig hur du konfigurerar en ny Azure Data Share och börjar dela dina data med kunder och partner utanför din Azure-organisation. 

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en dataresurs.
> * Lägg till datauppsättningar i dataresursen.
> * Aktivera ett schema för ögonblicksbilder för din dataresurs. 
> * Lägg till mottagare i dataresursen. 

## <a name="prerequisites"></a>Krav

* Azure-prenumeration: Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Mottagarens Azure-inloggningsadress (med hjälp av deras e-postal alias fungerar inte).
* Om käll-Azure-datalagret finns i en annan Azure-prenumeration än den du använder för att skapa datadelningsresurs registrerar du [Microsoft.DataShare-resursleverantören](concepts-roles-permissions.md#resource-provider-registration) i prenumerationen där Azure-datalagringen finns. 

### <a name="share-from-a-storage-account"></a>Dela från ett lagringskonto:

* Ett Azure Storage-konto: Om du inte redan har ett kan du skapa ett [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Behörighet att skriva till lagringskontot, som finns i *Microsoft.Storage/storageAccounts/write*. Den här behörigheten finns i rollen Deltagare.
* Behörighet att lägga till rolltilldelning i lagringskontot, som finns i *Microsoft.Authorization/rolltilldelningar/skriva*. Den här behörigheten finns i rollen Ägare. 


### <a name="share-from-a-sql-based-source"></a>Dela från en SQL-baserad källa:

* En Azure SQL-databas eller Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) med tabeller och vyer som du vill dela.
* Behörighet att skriva till databaserna på SQL-servern, som finns i *Microsoft.Sql/servers/databases/write*. Den här behörigheten finns i rollen Deltagare.
* Behörighet för dataresursen att komma åt informationslagret. Detta kan göras genom följande steg: 
    1. Ange dig själv som Azure Active Directory Admin för SQL-servern.
    1. Anslut till Azure SQL Database/Data Warehouse med Azure Active Directory.
    1. Använd Frågeredigeraren (förhandsgranskning) för att köra följande skript för att lägga till dataresursresursen Hanterad identitet som en db_datareader. Du måste ansluta med Active Directory och inte SQL Server-autentisering. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Observera att *<share_acc_name>* är namnet på din datadelningsresurs. Om du ännu inte har skapat en dataresurs kan du återkomma till den här förutsättningen senare.  

* En Azure SQL Database-användare med "db_datareader" åtkomst för att navigera och välja de tabeller och/eller vyer som du vill dela. 

* Åtkomst till SQL Server-brandväggen för klienten. Detta kan göras genom följande steg: 
    1. I SQL-servern i Azure-portalen navigerar du till *brandväggar och virtuella nätverk*
    1. Klicka **på** växlingsknappen för att tillåta åtkomst till Azure Services.
    1. Klicka på **+Lägg till klient-IP** och klicka på **Spara**. Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du delar SQL-data från Azure-portalen. Du kan också lägga till ett IP-intervall. 

### <a name="share-from-azure-data-explorer"></a>Dela från Azure Data Explorer
* Ett Azure Data Explorer-kluster med databaser som du vill dela.
* Behörighet att skriva till Azure Data Explorer-kluster, som finns i *Microsoft.Kusto/clusters/write*. Den här behörigheten finns i rollen Deltagare.
* Behörighet att lägga till rolltilldelning i Azure Data Explorer-klustret, som finns i *Microsoft.Authorization/role assignments/write*. Den här behörigheten finns i rollen Ägare.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Skapa ett datadelningskonto

Skapa en Azure Data Share-resurs i en Azure-resursgrupp.

1. Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet i portalen.

1. Sök efter *datadelning*.

1. Välj Datadelning och Välj **Skapa**.

1. Fyll i grundläggande information om din Azure Data Share-resurs med följande information. 

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Namn | *datashareacount* | Ange ett namn för ditt datadelningskonto. |
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som du vill använda för ditt datadelningskonto.|
    | Resursgrupp | *testresursgrupp* | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
    | Location | *USA, östra 2* | Välj en region för ditt datadelningskonto.
    | | |

1. Välj **Skapa** för att etablera ditt datadelningskonto. Det tar vanligtvis cirka 2 minuter eller mindre att etablera ett nytt datadelningskonto. 

1. När distributionen är klar väljer du **Gå till resurs**.

## <a name="create-a-data-share"></a>Skapa en dataresurs

1. Navigera till sidan Översikt över datadelning.

    ![Dela dina data](./media/share-receive-data.png "Dela dina data") 

1. Välj **Börja dela dina data**.

1. Välj **Skapa**.   

1. Fyll i information om din dataresurs. Ange ett namn, en resurstyp, en beskrivning av delningsinnehåll och användningsvillkor (valfritt). 

    ![EnterShareDetails](./media/enter-share-details.png "Ange dela-information") 

1. Välj **Fortsätt**

1. Om du vill lägga till datauppsättningar i datadelningen väljer du **Lägg till datauppsättningar**. 

    ![Datamängder](./media/datasets.png "Datauppsättningar")

1. Välj den datauppsättningstyp som du vill lägga till. Du kommer att se en annan lista över datauppsättningstyper beroende på vilken resurstyp (ögonblicksbild eller på plats) som du har valt i föregående steg. Om du delar från en Azure SQL-databas eller Azure SQL Data Warehouse uppmanas du att ange vissa SQL-autentiseringsuppgifter. Autentisera med den användare som du skapade som en del av förutsättningarna.

    ![AddDatasets](./media/add-datasets.png "Lägg till datauppsättningar")    

1. Navigera till det objekt som du vill dela och välj Lägg till datauppsättningar. 

    ![SelectDatasets](./media/select-datasets.png "Välj datauppsättningar")    

1. På fliken Mottagare anger du dina datakonsumentadresser genom att välja +Lägg till mottagare. 

    ![AddRecipients](./media/add-recipient.png "Lägga till mottagare") 

1. Välj **Fortsätt**

1. Om du har valt ögonblicksbilddelningstyp kan du konfigurera ögonblicksbildschema för att tillhandahålla uppdateringar av dina data till din datakonsument. 

    ![AktiveraSnapshots](./media/enable-snapshots.png "Aktivera ögonblicksbilder") 

1. Välj en starttid och återkommande intervall. 

1. Välj **Fortsätt**

1. På fliken Granska + Skapa granskar du inställningarna för paketinnehåll, inställningar, mottagare och synkronisering. Välj **Skapa**

Din Azure-dataresurs har nu skapats och mottagaren av din dataresurs är nu redo att acceptera din inbjudan. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en Azure Data Share och bjuda in mottagare. Om du vill veta mer om hur en datakonsument kan acceptera och ta emot en dataresurs fortsätter du till [självstudien](subscribe-to-data-share.md) för att acceptera och ta emot data. 
