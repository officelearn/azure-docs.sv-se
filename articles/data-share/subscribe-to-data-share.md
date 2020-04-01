---
title: 'Självstudiekurs: Acceptera & ta emot data - Azure Data Share'
description: Självstudiekurs - Acceptera och ta emot data med Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 5b7d9cd7e7d438cf2beac76d5d8bcc78d377a8f4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77083104"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Självstudiekurs: Acceptera och ta emot data med Azure Data Share  

I den här självstudien får du lära dig hur du accepterar en inbjudan datadelning med Azure Data Share. Du får lära dig hur du tar emot data som delas med dig, samt hur du aktiverar ett regelbundet uppdateringsintervall för att säkerställa att du alltid har den senaste ögonblicksbilden av de data som delas med dig. 

> [!div class="checklist"]
> * Så här accepterar du en Azure Data Share-inbjudan
> * Skapa ett Azure Data Share-konto
> * Ange ett mål för dina data
> * Skapa en prenumeration på din dataresurs för schemalagd uppdatering

## <a name="prerequisites"></a>Krav
Innan du kan acceptera en inbjudan om datadelning måste du etablera ett antal Azure-resurser som visas nedan. 

Se till att alla förutsättningar är fullständiga innan du accepterar en inbjudan om datadelning. 

* Azure-prenumeration: Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En inbjudan till datadelning: En inbjudan från Microsoft Azure med **<yourdataprovider@domain.com>** ämnet "Azure Data Share invitation from ".
* Registrera [microsoft.DataShare-resursleverantören](concepts-roles-permissions.md#resource-provider-registration) i Azure-prenumerationen där du skapar en datadelningsresurs och Azure-prenumerationen där dina mål Azure-datalager finns.

### <a name="receive-data-into-a-storage-account"></a>Ta emot data till ett lagringskonto: 

* Ett Azure Storage-konto: Om du inte redan har ett kan du skapa ett [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Behörighet att skriva till lagringskontot, som finns i *Microsoft.Storage/storageAccounts/write*. Den här behörigheten finns i rollen Deltagare. 
* Behörighet att lägga till rolltilldelning i lagringskontot, som finns i *Microsoft.Authorization/rolltilldelningar/skriva*. Den här behörigheten finns i rollen Ägare.  

### <a name="receive-data-into-a-sql-based-source"></a>Ta emot data till en SQL-baserad källa:

* Behörighet att skriva till databaser på SQL-servern, som finns i *Microsoft.Sql/servers/databases/write*. Den här behörigheten finns i rollen Deltagare. 
* Behörighet för datadelningsresursens hanterade identitet för att komma åt Azure SQL Database eller Azure SQL Data Warehouse. Detta kan göras genom följande steg: 
    1. Ange dig själv som Azure Active Directory Admin för SQL-servern.
    1. Anslut till Azure SQL Database/Data Warehouse med Azure Active Directory.
    1. Använd Frågeredigeraren (förhandsversionen) för att köra följande skript för att lägga till datadelningshanterad identitet som en "db_datareader, db_datawriter, db_ddladmin". Du måste ansluta med Active Directory och inte SQL Server-autentisering. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Observera att *<share_acc_name>* är namnet på din datadelningsresurs. Om du ännu inte har skapat en dataresurs kan du återkomma till den här förutsättningen senare.         

* Åtkomst till SQL Server-brandväggen för klienten. Detta kan göras genom följande steg: 
    1. I SQL-servern i Azure-portalen navigerar du till *brandväggar och virtuella nätverk*
    1. Klicka **på** växlingsknappen för att tillåta åtkomst till Azure Services.
    1. Klicka på **+Lägg till klient-IP** och klicka på **Spara**. Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du tar emot data till ett SQL-mål från Azure-portalen. Du kan också lägga till ett IP-intervall. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Ta emot data i ett Azure Data Explorer-kluster: 

* Ett Azure Data Explorer-kluster i samma Azure-datacenter som dataproviderns Data Explorer-kluster: Om du inte redan har ett kan du skapa ett [Azure Data Explorer-kluster](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal). Om du inte känner till Azure-datacentret i dataleverantörens kluster kan du skapa klustret senare i processen.
* Behörighet att skriva till Azure Data Explorer-klustret, som finns i *Microsoft.Kusto/clusters/write*. Den här behörigheten finns i rollen Deltagare. 
* Behörighet att lägga till rolltilldelning i Azure Data Explorer-klustret, som finns i *Microsoft.Authorization/role assignments/write*. Den här behörigheten finns i rollen Ägare. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="open-invitation"></a>Öppen inbjudan

1. Kontrollera inkorgen för en inbjudan från din dataleverantör. Inbjudan kommer från Microsoft Azure med namnet **Azure Data Share invitation from <yourdataprovider@domain.com> **. Notera resursnamnet för att se till att du accepterar rätt resurs om det finns flera inbjudningar. 

1. Välj på **Visa inbjudan** för att se din inbjudan i Azure. Detta tar dig till vyn Mottagna resurser.

   ![Inbjudningar](./media/invitations.png "Lista över inbjudningar") 

1. Välj den resurs som du vill visa. 

## <a name="accept-invitation"></a>Acceptera inbjudan
1. Kontrollera att alla fält granskas, inklusive **användarvillkoren**. Om du godkänner användarvillkoren måste du markera rutan för att visa att du godkänner det. 

   ![Villkor för användning](./media/terms-of-use.png "Användningsvillkor") 

1. Under *Måldatadelningskonto*väljer du den prenumerations- och resursgrupp som du ska distribuera din dataresurs till. 

   För fältet **Datadelningskonto** väljer du **Skapa nytt** om du inte har ett befintligt datadelningskonto. Annars väljer du ett befintligt datadelningskonto som du vill acceptera din dataresurs till. 

   För fältet **Mottaget aktienamn** kan du lämna standardvärdet som anges av data tillhandahållande, eller ange ett nytt namn för den mottagna resursen. 

   ![Måldatadelningskonto](./media/target-data-share.png "Måldatadelningskonto") 

1. När du har godkänt användarvillkoren och angett en plats för din resurs väljer du *på Acceptera och konfigurerar*. En aktieprenumeration skapas.

   För ögonblicksbildbaserad delning kommer nästa skärm att be dig att välja ett mållagringskonto för dina data som ska kopieras till. 

   ![Acceptera alternativ](./media/accept-options.png "Acceptera alternativ") 

   Om du föredrar att acceptera inbjudan nu men konfigurerar ditt måldatalager vid ett senare tillfälle väljer du *Acceptera och konfigurerar senare*. Information om hur du fortsätter konfigurera lagringen senare finns i konfigurera sidan [datauppsättningsmappningar](how-to-configure-mapping.md) för detaljerade steg om hur du återupptar konfigurationen av datadelning. 

   För delning på plats finns på sidan [Konfigurera datauppsättningsmappningar](how-to-configure-mapping.md) för detaljerade steg om hur du återupptar konfigurationen av datadelning. 

   Om du inte vill acceptera inbjudan väljer du *Avvisa*. 

## <a name="configure-storage"></a>Konfigurera lagring
1. Under *Inställningar för mållagring*väljer du det prenumerations-, resurs- och lagringskonto som du vill ta emot data till. 

   ![Inställningar för mållagring](./media/target-storage-settings.png "Mållagring") 

1. Om du vill ta emot regelbundna uppdateringar av dina data måste du aktivera inställningarna för ögonblicksbilder. Observera att du bara ser ett schema för en ögonblicksbildinställning om dataleverantören har inkluderat det i dataresursen. 

   ![Inställningar för ögonblicksbilder](./media/snapshot-settings.png "Inställningar för ögonblicksbilder") 

1. Välj *Spara*. 

> [!IMPORTANT]
> Om du tar emot SQL-baserade data och vill ta emot dessa data i en SQL-baserad källa, kan du gå till [konfigurera en datauppsättningsmappning](how-to-configure-mapping.md) för att lära dig hur du konfigurerar en SQL Server som mål för datauppsättningen. 

## <a name="trigger-a-snapshot"></a>Utlösa en ögonblicksbild
De här stegen gäller endast ögonblicksbildbaserad delning.

1. Du kan utlösa en ögonblicksbild på fliken Mottagna resurser -> information genom att välja **Utlösa ögonblicksbild**. Här kan du utlösa en fullständig eller inkrementell ögonblicksbild av dina data. Om det är första gången du tar emot data från din dataleverantör väljer du fullständig kopia. 

   ![Ögonblicksbild av utlösare](./media/trigger-snapshot.png "Ögonblicksbild av utlösare") 

1. När den senaste *körningsstatusen lyckades*går du till måldatalagret för att visa mottagna data. Välj **Datauppsättningar**och klicka på länken i målsökvägen. 

   ![Konsumentdatamängder](./media/consumer-datasets.png "Mappning av konsumentdatauppsättning") 

## <a name="view-history"></a>Visa historik
Om du vill visa en historik över dina ögonblicksbilder navigerar du till Mottagna resurser -> historik. Här hittar du en historik över alla ögonblicksbilder som har genererats under de senaste 60 dagarna. 

## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig att acceptera och ta emot en Azure Data Share. Om du vill veta mer om Azure Data Share-begrepp fortsätter du till [Begrepp: Azure Data Share Terminologi](terminology.md).