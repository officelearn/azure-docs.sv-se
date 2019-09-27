---
title: 'Självstudier: Acceptera & ta emot data – för hands version av Azure Data-resurs'
description: Självstudie – acceptera och ta emot data med Azure Data repreview
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 235ef25b2d655c4388dee5bdcf88d179f3373697
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327401"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>Självstudier: Godkänn och ta emot data med hjälp av Azure Data Share Preview

I den här självstudien får du lära dig hur du accepterar en data delnings-inbjudan med Azure Data repreview. Du får lära dig hur du tar emot data som delas med dig, samt hur du aktiverar ett vanligt uppdaterings intervall för att säkerställa att du alltid har den senaste ögonblicks bilden av de data som delas med dig. 

> [!div class="checklist"]
> * Så här godkänner du en Azure Data repreview-inbjudan
> * Skapa ett konto för för hands version av Azure Data-resurs
> * Ange ett mål för dina data
> * Skapa en prenumeration på din data resurs för schemalagd uppdatering

## <a name="prerequisites"></a>Förutsättningar
Innan du kan acceptera en inbjudan till en data resurs måste du etablera ett antal Azure-resurser som visas nedan. 

Se till att alla krav är uppfyllda innan du accepterar en inbjudan om data delning. 

* Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Ett Azure Storage konto: Om du inte redan har en, kan du skapa ett [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* En inbjudan till data resursen: En inbjudan från Microsoft Azure med ett ämne med rubriken "Azure Data Share-inbjudan från **<yourdataprovider@domain.com>** ".
* Behörighet att lägga till roll tilldelning till lagrings kontot, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv* behörighet. Den här behörigheten finns i ägar rollen. 
* Registrering av resurs leverantör för Microsoft. DataShare. Mer information om hur du gör detta finns i dokumentationen för [Azure Resource providers](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) . 

> [!IMPORTANT]
> Om du vill godkänna och ta emot en Azure-Dataresurs måste du först registrera resurs leverantören Microsoft. DataShare och du måste vara ägare till det lagrings konto som du accepterar data till. Följ anvisningarna som beskrivs i [Felsöka för hands versionen av Azure Data](data-share-troubleshoot.md) Resource för att registrera resurs leverantören för data resursen samt lägga till dig själv som ägare av lagrings kontot. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Öppna inbjudan

Kontrol lera Inkorgen för en inbjudan från din data leverantör. Inbjudan är från Microsoft Azure, med titeln **Azure Data Share-inbjudan från <yourdataprovider@domain.com>** . Anteckna resurs namnet så att du vet att du accepterar rätt resurs om det finns flera inbjudningar. 

Välj på **Visa inbjudan** för att se din inbjudan i Azure. Då går du till vyn mottagna resurser.

![Inbjudnings](./media/invitations.png "lista över inbjudningar") 

Välj den resurs som du vill visa. 

## <a name="accept-invitation"></a>Acceptera inbjudan
Se till att alla fält har granskats, inklusive **användnings villkoren**. Om du samtycker till användnings villkoren måste du markera kryss rutan för att ange att du godkänner. 

![Användningsvillkor](./media/terms-of-use.png "användningsvillkor") 

Under *konto för mål data resurs*väljer du den prenumeration och resurs grupp som du ska distribuera data resursen till. 

För fältet **data resurs konto** väljer du **Skapa nytt** om du inte har ett befintligt data resurs konto. Annars väljer du ett befintligt data resurs konto som du vill acceptera data resursen till. 

I fältet *mottaget resurs namn* kan du lämna standardvärdet som anges av data ange eller ange ett nytt namn för den mottagna resursen. 

Mål ![data]resurs konto(./media/target-data-share.png "mål data resurs konto") 

När du har samtyckt till användnings villkoren och angett en plats för din resurs väljer du *Godkänn och konfigurera*. Om du väljer det här alternativet skapas en resurs prenumeration och nästa skärm uppmanar dig att välja ett mål lagrings konto för dina data som ska kopieras till. 

Godkänn alternativ för(./media/accept-options.png "godkännande") av ![alternativ] 

Om du vill acceptera inbjudan nu men konfigurera lagringen vid ett senare tillfälle väljer du *Godkänn och konfigurera senare*. Med det här alternativet kan du konfigurera ditt mål lagrings konto senare. Om du vill fortsätta att konfigurera lagringen senare kan du läsa mer i så här [konfigurerar du lagrings konto](how-to-configure-mapping.md) sidan för detaljerade anvisningar om hur du återupptar konfigurationen av data resurser. 

Om du inte vill acceptera inbjudan väljer du *avvisa*. 

## <a name="configure-storage"></a>Konfigurera lagring
Under *mål lagrings inställningar*väljer du den prenumeration, resurs grupp och det lagrings konto som du vill att data ska skickas till. 

Mål(./media/target-storage-settings.png "lagring") för ![mål lagrings inställningar] 

Se till att aktivera inställningarna för ögonblicks bilder för att få regelbundna uppdateringar av dina data. Observera att du bara ser ett schema för ögonblicks bilds inställningar om data leverantören har inkluderat den i data resursen. 

![](./media/snapshot-settings.png "Inställningar") för ögonblicks bild inställningar 

Välj *Spara*. 

## <a name="trigger-a-snapshot"></a>Utlös en ögonblicks bild

Du kan utlösa en ögonblicks bild på fliken mottagna resurser – > information genom att välja **Utlös ögonblicks bild**. Här kan du utlösa en fullständig eller stegvis ögonblicks bild av dina data. Om det är första gången du tar emot data från din dataprovider väljer du fullständig kopia. 

Utlös(./media/trigger-snapshot.png "ögonblicks bild") av ![ögonblicks bild] 

När den senaste körnings statusen har *slutförts*kan du öppna lagrings kontot för att Visa mottagna data. 

Du kan kontrol lera vilket lagrings konto du använt genom att välja på **data uppsättningar**. 

![](./media/consumer-datasets.png "Mappning av konsument") data uppsättningar för konsument data uppsättningar 

## <a name="view-history"></a>Visa historik
Om du vill visa en historik över dina ögonblicks bilder navigerar du till mottagna resurser-> Historik. Här hittar du en historik över alla ögonblicks bilder som har genererats under de senaste 60 dagarna. 

## <a name="next-steps"></a>Nästa steg
I den här självstudien får du lära dig hur du godkänner och tar emot en Azure-Dataresurs. Om du vill veta mer om Azure Data Share-koncept fortsätter du till [Concepts: Azure Data Share-terminologi @ no__t-0.