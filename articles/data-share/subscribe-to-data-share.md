---
title: 'Självstudie: Godkänn & ta emot data – Azure Data Share'
description: Självstudie – acceptera och ta emot data med Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: 17c3e9ee157cedd31be39f472f619a2df9ae32a6
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594189"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Självstudier: Acceptera och ta emot data med Azure Data Share  

I den här självstudien får du lära dig hur du godkänner en data delnings-inbjudan med Azure Data Share. Du får lära dig hur du tar emot data som delas med dig, samt hur du aktiverar ett vanligt uppdaterings intervall för att säkerställa att du alltid har den senaste ögonblicks bilden av de data som delas med dig. 

> [!div class="checklist"]
> * Så här godkänner du en Azure Data Share-inbjudan
> * Skapa ett Azure Data Share-konto
> * Ange ett mål för dina data
> * Skapa en prenumeration på din data resurs för schemalagd uppdatering

## <a name="prerequisites"></a>Förutsättningar
Innan du kan acceptera en inbjudan till en data resurs måste du etablera ett antal Azure-resurser som visas nedan. 

Se till att alla krav är uppfyllda innan du accepterar en inbjudan om data delning. 

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Inbjudan till en data resurs: en inbjudan från Microsoft Azure med ett ämne med rubriken "Azure Data Share-inbjudan från **<yourdataprovider@domain.com>** ".
* Registrera [resurs leverantören Microsoft. DataShare](concepts-roles-permissions.md#resource-provider-registration) i Azure-prenumerationen där du ska skapa en data resurs resurs och Azure-prenumerationen där dina Azure-datalager finns.

### <a name="receive-data-into-a-storage-account"></a>Ta emot data till ett lagrings konto

* Ett Azure Storage konto: om du inte redan har ett kan du skapa ett [Azure Storage-konto](../storage/common/storage-account-create.md). 
* Behörighet att skriva till lagrings kontot som finns i *Microsoft. Storage/storageAccounts/Write*. Den här behörigheten finns i deltagarrollen. 
* Behörighet att lägga till roll tilldelning till lagrings kontot, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den här behörigheten finns i ägarrollen.  

### <a name="receive-data-into-a-sql-based-target"></a>Ta emot data i ett SQL-baserat mål
Om du väljer att ta emot data i Azure SQL Database, är Azure Synapse Analytics nedan listan över krav. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Krav för att ta emot data till Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW)
Du kan följa stegen i steg [för steg-demonstrationen](https://youtu.be/aeGISgK1xro) för att konfigurera krav.

* En Azure SQL Database-eller Azure Synapse-analys (tidigare Azure SQL DW).
* Behörighet att skriva till databaser på SQL-servern, som finns i *Microsoft. SQL/Servers/databaser/skriva*. Den här behörigheten finns i **deltagarrollen**. 
* Behörighet för data resurs resursens hanterade identitet för att få åtkomst till Azure SQL Database-eller Azure Synapse-analys. Detta kan göras genom följande steg: 
    1. I Azure Portal går du till SQL-servern och anger dig själv som **Azure Active Directorys administratör**.
    1. Anslut till Azure SQL Database/informations lagret med hjälp av [Frågeredigeraren](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) eller SQL Server Management Studio med Azure Active Directory autentisering. 
    1. Kör följande skript för att lägga till den hanterade identiteten för data resursen som db_datareader, db_datawriter db_ddladmin. Du måste ansluta med Active Directory och inte SQL Server autentisering. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Observera att *<share_acc_name>* är namnet på din data resurs resurs. Om du inte har skapat någon data resurs resurs ännu kan du gå tillbaka till det här kravet senare.         

* SQL Server brand Väggs åtkomst. Detta kan göras genom följande steg: 
    1. I SQL Server i Azure Portal navigerar du till *brand väggar och virtuella nätverk*
    1. Klicka på **Ja** om *du vill tillåta Azure-tjänster och-resurser åtkomst till den här servern*.
    1. Klicka på **+ Lägg till klient-IP**. Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du delar SQL-data från Azure Portal. Du kan också lägga till ett IP-intervall.
    1. Klicka på **Spara**. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Krav för att ta emot data i Azure Synapse Analytics (arbets yta) SQL-pool

* En dedikerad SQL-pool för Azure Synapse Analytics (arbets yta). Det finns för närvarande inte stöd för att ta emot data i SQL-poolen utan server.
* Behörighet att skriva till SQL-poolen i Synapse-arbetsytan, som finns i *Microsoft. Synapse/arbetsytes/sqlPools/Write*. Den här behörigheten finns i **deltagarrollen**.
* Behörighet för data resurs resursens hanterade identitet för åtkomst till SQL-poolen Synapse-arbetsyta. Detta kan göras genom följande steg: 
    1. I Azure Portal navigerar du till arbets ytan Synapse. Välj SQL Active Directory admin från vänster navigering och ange dig själv som **Azure Active Directorys administratör**.
    1. Öppna Synapse Studio och välj *Hantera* från det vänstra navigerings fältet. Välj *åtkomst kontroll* under säkerhet. Tilldela rollen som administratör för **SQL-administratören** eller **arbets ytan** .
    1. I Synapse Studio väljer du *utveckla* från det vänstra navigerings fältet. Kör följande skript i SQL-poolen för att lägga till den hanterade identiteten för data resursen som en db_datareader, db_datawriter db_ddladmin. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Observera att *<share_acc_name>* är namnet på din data resurs resurs. Om du inte har skapat någon data resurs resurs ännu kan du gå tillbaka till det här kravet senare.  

* Åtkomst till brand vägg för Synapse-arbetsyta. Detta kan göras genom följande steg: 
    1. I Azure Portal navigerar du till arbets ytan Synapse. Välj *brand väggar* från vänster navigering.
    1. Klicka **på på** för *att tillåta Azure-tjänster och-resurser åtkomst till den här arbets ytan*.
    1. Klicka på **+ Lägg till klient-IP**. Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du delar SQL-data från Azure Portal. Du kan också lägga till ett IP-intervall.
    1. Klicka på **Spara**. 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Ta emot data till ett Azure Datautforskaren-kluster: 

* Ett Azure Datautforskaren-kluster i samma Azure-datacenter som data leverantörens Datautforskaren kluster: om du inte redan har ett kan du skapa ett [Azure datautforskaren-kluster](/azure/data-explorer/create-cluster-database-portal). Om du inte känner till Azure-datacenter i data leverantörens kluster kan du skapa klustret senare i processen.
* Behörighet att skriva till Azure Datautforskaren-klustret, som finns i *Microsoft. Kusto/kluster/Write*. Den här behörigheten finns i deltagarrollen. 
* Behörighet att lägga till roll tilldelning till Azure Datautforskaren-klustret, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den här behörigheten finns i ägarrollen. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="open-invitation"></a>Öppna inbjudan

1. Du kan öppna inbjudan från e-post eller direkt från Azure Portal. 

   Om du vill öppna inbjudan från e-post, kontrollerar du Inkorgen för en inbjudan från din data leverantör. Inbjudan är från Microsoft Azure, med titeln **Azure Data Share-inbjudan från <yourdataprovider@domain.com>**. Klicka på **Visa inbjudan** för att se din inbjudan i Azure. 

   Du öppnar inbjudan från Azure Portal direkt genom att söka efter **inbjudningar för data delning** i Azure Portal. Då går du till listan med data resurs inbjudningar.

   ![Lista över inbjudningar](./media/invitations.png "Lista över inbjudningar") 

1. Välj den resurs som du vill visa. 

## <a name="accept-invitation"></a>Acceptera inbjudan
1. Se till att alla fält har granskats, inklusive **användnings villkoren**. Om du samtycker till användnings villkoren måste du markera kryss rutan för att ange att du godkänner. 

   ![Villkor för användning](./media/terms-of-use.png "Villkor för användning") 

1. Under *konto för mål data resurs* väljer du den prenumeration och resurs grupp som du ska distribuera data resursen till. 

   För fältet **data resurs konto** väljer du **Skapa nytt** om du inte har ett befintligt data resurs konto. Annars väljer du ett befintligt data resurs konto som du vill acceptera data resursen till. 

   I fältet **mottaget resurs namn** kan du lämna standardvärdet som anges av data ange eller ange ett nytt namn för den mottagna resursen. 

   När du har samtyckt till användnings villkoren och angett ett data delnings konto för att hantera din mottagna resurs väljer du **Godkänn och konfigurera**. En resurs prenumeration kommer att skapas. 

   ![Godkänn alternativ](./media/accept-options.png "Godkänn alternativ") 

   Detta gör att du är den mottagna resursen i ditt data resurs konto. 

   Om du inte vill acceptera inbjudan väljer du *avvisa*. 

## <a name="configure-received-share"></a>Konfigurera mottagen resurs
Följ stegen nedan för att konfigurera var du vill ta emot data.

1. Fliken Välj **data uppsättningar** . Markera kryss rutan bredvid den data uppsättning som du vill tilldela ett mål. Välj **+ Mappa till mål** för att välja ett mål data lager. 

   ![Mappa till mål](./media/dataset-map-target.png "Mappa till mål") 

1. Välj en mål data lager typ som du vill att data ska hamna i. Alla datafiler eller tabeller i mål data lagret med samma sökväg och namn kommer att skrivas över. 

   För delning på plats väljer du ett data lager på den angivna platsen. Platsen är Azure Data Center där dataproviderns käll data lager finns. När data uppsättningen har mappats kan du följa länken i mål Sök vägen för att komma åt data.

   ![Mål lagrings konto](./media/dataset-map-target-sql.png "Mål lagring") 

1. För ögonblicks bilds-baserad delning, om dataprovidern har skapat ett ögonblicks bild schema för att tillhandahålla regelbunden uppdatering av data, kan du också aktivera schema för ögonblicks bild genom att välja fliken **ögonblicks bild schema** . Markera kryss rutan bredvid schemat för ögonblicks bilder och välj **+ Aktivera**.

   ![Aktivera schema för ögonblicks bild](./media/enable-snapshot-schedule.png "Aktivera schema för ögonblicks bild")

## <a name="trigger-a-snapshot"></a>Utlös en ögonblicks bild
De här stegen gäller endast för Snapshot-baserad delning.

1. Du kan utlösa en ögonblicks bild genom att välja fliken **information** följt av **Utlös ögonblicks bild**. Här kan du utlösa en fullständig eller stegvis ögonblicks bild av dina data. Om det är första gången du tar emot data från din dataprovider väljer du fullständig kopia. 

   ![Utlös ögonblicks bild](./media/trigger-snapshot.png "Utlös ögonblicks bild") 

1. När den senaste körnings statusen har *slutförts* går du till mål data lagret för att Visa mottagna data. Välj **data uppsättningar** och klicka på länken i mål Sök vägen. 

   ![Konsument data uppsättningar](./media/consumer-datasets.png "Mappning av konsument data uppsättning") 

## <a name="view-history"></a>Visa historik
Det här steget gäller endast för Snapshot-baserad delning. Välj fliken **Historik** för att visa historiken för dina ögonblicks bilder. Här hittar du historiken för alla ögonblicks bilder som har genererats under de senaste 30 dagarna.

## <a name="clean-up-resources"></a>Rensa resurser

När resursen inte längre behövs går du till sidan **Översikt över data resurs** och väljer **ta bort** för att ta bort den.

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du godkänner och tar emot en Azure-Dataresurs. Om du vill veta mer om Azure Data Share-koncept kan du fortsätta till Azure Data Share-terminologi.

> [!div class="nextstepaction"]
> [Azure Data Share-koncept](terminology.md)