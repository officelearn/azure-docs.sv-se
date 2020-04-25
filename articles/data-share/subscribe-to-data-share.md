---
title: 'Självstudie: Godkänn & ta emot data – Azure Data Share'
description: Självstudie – acceptera och ta emot data med Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 4dff48f909cd3febbbb7e92dcf96070020b8f57c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145142"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Självstudie: Godkänn och ta emot data med Azure Data Share  

I den här självstudien får du lära dig hur du godkänner en data delnings-inbjudan med Azure Data Share. Du får lära dig hur du tar emot data som delas med dig, samt hur du aktiverar ett vanligt uppdaterings intervall för att säkerställa att du alltid har den senaste ögonblicks bilden av de data som delas med dig. 

> [!div class="checklist"]
> * Så här godkänner du en Azure Data Share-inbjudan
> * Skapa ett Azure Data Share-konto
> * Ange ett mål för dina data
> * Skapa en prenumeration på din data resurs för schemalagd uppdatering

## <a name="prerequisites"></a>Krav
Innan du kan acceptera en inbjudan till en data resurs måste du etablera ett antal Azure-resurser som visas nedan. 

Se till att alla krav är uppfyllda innan du accepterar en inbjudan om data delning. 

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Inbjudan till en data resurs: en inbjudan från Microsoft Azure med ett ämne med rubriken "Azure Data Share- **<yourdataprovider@domain.com>** inbjudan från".
* Registrera [resurs leverantören Microsoft. DataShare](concepts-roles-permissions.md#resource-provider-registration) i Azure-prenumerationen där du ska skapa en data resurs resurs och Azure-prenumerationen där dina Azure-datalager finns.

### <a name="receive-data-into-a-storage-account"></a>Ta emot data till ett lagrings konto: 

* Ett Azure Storage konto: om du inte redan har ett kan du skapa ett [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Behörighet att skriva till lagrings kontot som finns i *Microsoft. Storage/storageAccounts/Write*. Den här behörigheten finns i deltagar rollen. 
* Behörighet att lägga till roll tilldelning till lagrings kontot, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den här behörigheten finns i ägar rollen.  

### <a name="receive-data-into-a-sql-based-source"></a>Ta emot data i en SQL-baserad Källa:

* Behörighet att skriva till databaser på SQL-servern, som finns i *Microsoft. SQL/Servers/databaser/skriva*. Den här behörigheten finns i deltagar rollen. 
* Behörighet för data resurs resursens hanterade identitet för att komma åt Azure SQL Database eller Azure SQL Data Warehouse. Detta kan göras genom följande steg: 
    1. Ange själv som Azure Active Directory administratör för SQL Server.
    1. Anslut till Azure SQL Database/informations lagret med hjälp av Azure Active Directory.
    1. Använd Frågeredigeraren (för hands version) för att köra följande skript för att lägga till den hanterade identiteten för data resursen som db_datareader, db_datawriter db_ddladmin. Du måste ansluta med Active Directory och inte SQL Server autentisering. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Observera att *<share_acc_name>* är namnet på din data resurs resurs. Om du inte har skapat någon data resurs resurs ännu kan du gå tillbaka till det här kravet senare.         

* Åtkomst till klient-IP SQL Server-brandvägg. Detta kan göras genom följande steg: 
    1. I SQL Server i Azure Portal navigerar du till *brand väggar och virtuella nätverk*
    1. Klicka på **Växla för** att tillåta åtkomst till Azure-tjänster.
    1. Klicka på **+ Lägg till klient-IP** och klicka på **Spara**. Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du tar emot data i ett SQL-mål från Azure Portal. Du kan också lägga till ett IP-intervall. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Ta emot data till ett Azure Datautforskaren-kluster: 

* Ett Azure Datautforskaren-kluster i samma Azure-datacenter som data leverantörens Datautforskaren kluster: om du inte redan har ett kan du skapa ett [Azure datautforskaren-kluster](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal). Om du inte känner till Azure-datacenter i data leverantörens kluster kan du skapa klustret senare i processen.
* Behörighet att skriva till Azure Datautforskaren-klustret, som finns i *Microsoft. Kusto/kluster/Write*. Den här behörigheten finns i deltagar rollen. 
* Behörighet att lägga till roll tilldelning till Azure Datautforskaren-klustret, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den här behörigheten finns i ägar rollen. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="open-invitation"></a>Öppna inbjudan

1. Du kan öppna inbjudan från e-post eller direkt från Azure Portal. 

   Om du vill öppna inbjudan från e-post, kontrollerar du Inkorgen för en inbjudan från din data leverantör. Inbjudan är från Microsoft Azure, med titeln **Azure Data Share-inbjudan från <yourdataprovider@domain.com> **. Klicka på **Visa inbjudan** för att se din inbjudan i Azure. 

   Du öppnar inbjudan från Azure Portal direkt genom att söka efter **inbjudningar för data delning** i Azure Portal. Då går du till listan med data resurs inbjudningar.

   ![Inbjudningar](./media/invitations.png "Lista över inbjudningar") 

1. Välj den resurs som du vill visa. 

## <a name="accept-invitation"></a>Acceptera inbjudan
1. Se till att alla fält har granskats, inklusive **användnings villkoren**. Om du samtycker till användnings villkoren måste du markera kryss rutan för att ange att du godkänner. 

   ![Villkor för användning](./media/terms-of-use.png "Användningsvillkor") 

1. Under *konto för mål data resurs*väljer du den prenumeration och resurs grupp som du ska distribuera data resursen till. 

   För fältet **data resurs konto** väljer du **Skapa nytt** om du inte har ett befintligt data resurs konto. Annars väljer du ett befintligt data resurs konto som du vill acceptera data resursen till. 

   I fältet **mottaget resurs namn** kan du lämna standardvärdet som anges av data ange eller ange ett nytt namn för den mottagna resursen. 

   ![Mål data resurs konto](./media/target-data-share.png "Mål data resurs konto") 

1. När du har samtyckt till användnings villkoren och angett en plats för din resurs väljer du *Godkänn och konfigurera*. En resurs prenumeration kommer att skapas.

   För ögonblicks bilds-baserad delning uppmanas du på nästa skärm välja ett mål lagrings konto för dina data som ska kopieras till. 

   ![Godkänn alternativ](./media/accept-options.png "Godkänn alternativ") 

   Om du vill acceptera inbjudan nu men konfigurera mål data lagret vid ett senare tillfälle väljer du *Godkänn och konfigurera senare*. Om du vill fortsätta att konfigurera lagringen senare går du till sidan [Konfigurera data uppsättnings mappningar](how-to-configure-mapping.md) för detaljerade anvisningar om hur du återupptar konfigurationen av data resurser. 

   Mer information om hur du återupptar konfigurationen av data resurser finns på sidan [Konfigurera mappningar](how-to-configure-mapping.md) för data uppsättningar. 

   Om du inte vill acceptera inbjudan väljer du *avvisa*. 

## <a name="configure-storage"></a>Konfigurera lagring
1. Under *mål lagrings inställningar*väljer du den prenumeration, resurs grupp och det lagrings konto som du vill att data ska skickas till. 

   ![Mål lagrings inställningar](./media/target-storage-settings.png "Mål lagring") 

1. Se till att aktivera inställningarna för ögonblicks bilder för att få en regelbunden uppdatering av dina data. Observera att du bara ser ett schema för ögonblicks bilds inställningar om data leverantören har inkluderat den i data resursen. 

   ![Inställningar för ögonblicks bild](./media/snapshot-settings.png "Inställningar för ögonblicks bild") 

1. Välj *Spara*. 

> [!IMPORTANT]
> Om du tar emot SQL-baserade data och vill ta emot dessa data till en SQL-baserad källa, kan du gå till [Konfigurera en uppsättning för data uppsättnings mappning](how-to-configure-mapping.md) för att lära dig hur du konfigurerar en SQL Server som mål för din data uppsättning. 

## <a name="trigger-a-snapshot"></a>Utlös en ögonblicks bild
De här stegen gäller endast för Snapshot-baserad delning.

1. Du kan utlösa en ögonblicks bild på fliken mottagna resurser – > information genom att välja **Utlös ögonblicks bild**. Här kan du utlösa en fullständig eller stegvis ögonblicks bild av dina data. Om det är första gången du tar emot data från din dataprovider väljer du fullständig kopia. 

   ![Utlös ögonblicks bild](./media/trigger-snapshot.png "Utlös ögonblicks bild") 

1. När den senaste körnings statusen har *slutförts*går du till mål data lagret för att Visa mottagna data. Välj **data uppsättningar**och klicka på länken i mål Sök vägen. 

   ![Konsument data uppsättningar](./media/consumer-datasets.png "Mappning av konsument data uppsättning") 

## <a name="view-history"></a>Visa historik
Om du vill visa en historik över dina ögonblicks bilder navigerar du till mottagna resurser-> historik. Här hittar du en historik över alla ögonblicks bilder som har genererats under de senaste 60 dagarna. 

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du godkänner och tar emot en Azure-Dataresurs. Om du vill veta mer om Azure Data Share-koncept fortsätter du till [koncept: Azure Data Share-terminologi](terminology.md).