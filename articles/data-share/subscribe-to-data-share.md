---
title: 'Självstudie: Godkänn & ta emot data – Azure Data Share'
description: Självstudie – acceptera och ta emot data med Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 9c24f54fe846459187488b0a65b2582914e25e2a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499336"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Självstudie: Godkänn och ta emot data med Azure Data Share  

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

### <a name="receive-data-into-a-storage-account"></a>Ta emot data till ett lagrings konto: 

* Ett Azure Storage konto: om du inte redan har ett kan du skapa ett [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Behörighet att lägga till roll tilldelning till lagrings kontot, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv* behörighet. Den här behörigheten finns i ägar rollen. 
* Registrering av resurs leverantör för Microsoft. DataShare. Mer information om hur du gör detta finns i dokumentationen för [Azure Resource providers](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) . 

> [!IMPORTANT]
> Om du vill godkänna och ta emot en Azure-Dataresurs måste du först registrera resurs leverantören Microsoft. DataShare och du måste vara ägare till det lagrings konto som du accepterar data till. Följ anvisningarna som beskrivs i [Felsöka Azure-Dataresursen](data-share-troubleshoot.md) för att registrera resurs leverantören för data resursen samt lägga till dig själv som ägare av lagrings kontot. 

### <a name="receive-data-into-a-sql-based-source"></a>Ta emot data i en SQL-baserad Källa:

* Behörighet för data resursen MSI för att få åtkomst till Azure SQL Database eller Azure SQL Data Warehouse. Detta kan göras genom följande steg: 
    1. Ange själv som Azure Active Directory administratör för-servern.
    1. Anslut till Azure SQL Database/informations lagret med hjälp av Azure Active Directory.
    1. Använd Frågeredigeraren (för hands version) för att köra följande skript för att lägga till data resursen MSI som db_owner. Du måste ansluta med Active Directory och inte SQL Server autentisering. 

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Observera att *< share_acc_name >* är namnet på ditt data resurs konto. Om du inte har skapat ett data resurs konto ännu kan du komma tillbaka till det här kravet senare.         

* Klientens IP SQL Server brand Väggs åtkomst: Detta kan göras via följande steg: 1. Navigera till *brand väggar och virtuella nätverk* 1. Klicka på **Växla för** att tillåta åtkomst till Azure-tjänster. 

När dessa krav är uppfyllda är du redo att ta emot data i SQL Server.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="open-invitation"></a>Öppna inbjudan

Kontrol lera Inkorgen för en inbjudan från din data leverantör. Inbjudan är från Microsoft Azure, med titeln **Azure Data Share-inbjudan från <yourdataprovider@domain.com>** . Anteckna resurs namnet så att du vet att du accepterar rätt resurs om det finns flera inbjudningar. 

Välj på **Visa inbjudan** för att se din inbjudan i Azure. Då går du till vyn mottagna resurser.

![Inbjudningar](./media/invitations.png "Lista över inbjudningar") 

Välj den resurs som du vill visa. 

## <a name="accept-invitation"></a>Acceptera inbjudan
Se till att alla fält har granskats, inklusive **användnings villkoren**. Om du samtycker till användnings villkoren måste du markera kryss rutan för att ange att du godkänner. 

![Användningsvillkor](./media/terms-of-use.png "Användningsvillkor") 

Under *konto för mål data resurs*väljer du den prenumeration och resurs grupp som du ska distribuera data resursen till. 

För fältet **data resurs konto** väljer du **Skapa nytt** om du inte har ett befintligt data resurs konto. Annars väljer du ett befintligt data resurs konto som du vill acceptera data resursen till. 

I fältet *mottaget resurs namn* kan du lämna standardvärdet som anges av data ange eller ange ett nytt namn för den mottagna resursen. 

![Mål data resurs konto](./media/target-data-share.png "Mål data resurs konto") 

När du har samtyckt till användnings villkoren och angett en plats för din resurs väljer du *Godkänn och konfigurera*. Om du väljer det här alternativet skapas en resurs prenumeration och nästa skärm uppmanar dig att välja ett mål lagrings konto för dina data som ska kopieras till. 

![Godkänn alternativ](./media/accept-options.png "Godkänn alternativ") 

Om du vill acceptera inbjudan nu men konfigurera lagringen vid ett senare tillfälle väljer du *Godkänn och konfigurera senare*. Med det här alternativet kan du konfigurera ditt mål lagrings konto senare. Om du vill fortsätta att konfigurera lagringen senare kan du läsa mer i så här [konfigurerar du lagrings konto](how-to-configure-mapping.md) sidan för detaljerade anvisningar om hur du återupptar konfigurationen av data resurser. 

Om du inte vill acceptera inbjudan väljer du *avvisa*. 

## <a name="configure-storage"></a>Konfigurera lagring
Under *mål lagrings inställningar*väljer du den prenumeration, resurs grupp och det lagrings konto som du vill att data ska skickas till. 

![Mål lagrings inställningar](./media/target-storage-settings.png "Mål lagring") 

Se till att aktivera inställningarna för ögonblicks bilder för att få regelbundna uppdateringar av dina data. Observera att du bara ser ett schema för ögonblicks bilds inställningar om data leverantören har inkluderat den i data resursen. 

![Inställningar för ögonblicks bild](./media/snapshot-settings.png "Inställningar för ögonblicks bild") 

Välj *Spara*. 

> [!IMPORTANT]
> Om du tar emot SQL-baserade data och vill ta emot dessa data till en SQL-baserad källa kan du gå till vår [Konfigurera en uppsättning](how-to-configure-mapping.md) med instruktioner för data uppsättning för att lära dig hur du konfigurerar en SQL Server som mål för din data uppsättning. 

## <a name="trigger-a-snapshot"></a>Utlös en ögonblicks bild

Du kan utlösa en ögonblicks bild på fliken mottagna resurser – > information genom att välja **Utlös ögonblicks bild**. Här kan du utlösa en fullständig eller stegvis ögonblicks bild av dina data. Om det är första gången du tar emot data från din dataprovider väljer du fullständig kopia. 

![Utlös ögonblicks bild](./media/trigger-snapshot.png "Utlös ögonblicks bild") 

När den senaste körnings statusen har *slutförts*kan du öppna lagrings kontot för att Visa mottagna data. 

Du kan kontrol lera vilket lagrings konto du använt genom att välja på **data uppsättningar**. 

![Konsument data uppsättningar](./media/consumer-datasets.png "Mappning av konsument data uppsättning") 

## <a name="view-history"></a>Visa historik
Om du vill visa en historik över dina ögonblicks bilder navigerar du till mottagna resurser-> Historik. Här hittar du en historik över alla ögonblicks bilder som har genererats under de senaste 60 dagarna. 

## <a name="next-steps"></a>Nästa steg
I den här självstudien får du lära dig hur du godkänner och tar emot en Azure-Dataresurs. Om du vill veta mer om Azure Data Share-koncept fortsätter du till [koncept: Azure Data Share-terminologi](terminology.md).