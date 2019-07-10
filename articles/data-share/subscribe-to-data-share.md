---
title: Självstudie – acceptera och ta emot data med hjälp av Azure Data resurs Preview
description: Självstudie – acceptera och ta emot data med hjälp av Azure Data resurs Preview
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 2dc4994d88fc03c23a6d5722d6018c926e7d6b8c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788166"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>Självstudier: Acceptera och ta emot data med hjälp av Azure Data resurs Preview

I den här självstudien får du lära dig hur du accepterar en inbjudan för resursen av data med hjälp av Azure Data dela Preview. Du kommer lära dig hur du tar emot data som delas med dig, samt hur du aktiverar en vanlig uppdateringsintervall så att du alltid har den senaste ögonblicksbilden av data som delas med dig. 

> [!div class="checklist"]
> * Hur du accepterar en förhandsversion av Azure Data resurs inbjudan
> * Skapa ett konto för förhandsversionen av Azure Data resurs
> * Ange ett mål för dina data
> * Skapa en prenumeration till din data filresurs för schemalagd uppdatering

## <a name="prerequisites"></a>Förutsättningar
Innan du kan godkänna en inbjudan till dela data, måste du etablera ett antal Azure-resurser, som visas nedan. 

Se till att alla förutsättningar är uppfyllda innan du godkänner en inbjudan till dela data. 

* Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Ett Azure Storage-konto: Om du inte redan har ett kan du skapa en [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* En inbjudan Dataresurs: En inbjudan från Microsoft Azure med ett ämne som heter ”Azure Dataresurs inbjudan från **<yourdataprovider@domain.com>** ”.

> [!IMPORTANT]
> För att acceptera och ta emot en Azure-filresurs för Data, måste du först registrera resursprovidern Microsoft.DataShare och du måste vara ägare till det lagringskonto som du godkänner data till. Följ anvisningarna som beskrivs i [felsöka Azure dela Dataförhandsgranskning](data-share-troubleshoot.md) att registrera resursprovidern för resursen data samt lägga till dig själv som ägare av storage-konto. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Öppna inbjudan

Kontrollera Inkorgen för en inbjudan från din dataprovidern. Inbjudan är från Microsoft Azure, med rubriken **Azure Dataresurs inbjudan från <yourdataprovider@domain.com>** . Ta del av resursnamnet att se till att du tar emot rätt resurs om det finns flera inbjudningar. 

Välj på **visa inbjudan** att se din inbjudan i Azure. Då kommer du till vyn emot resurser.

![Inbjudningar](./media/invitations.png "lista över inbjudningar") 

Markera resursen som du vill visa. 

## <a name="accept-invitation"></a>Acceptera inbjudan
Kontrollera att alla fält granskas, inklusive den **användningsvillkor**. Om du samtycker till villkoren kommer du att behöva markera kryssrutan för att ange godkänner. 

![Användningsvillkor](./media/terms-of-use.png "användningsvillkor") 

Under *målkonto Data resursen*, Välj prenumeration och resursgrupp som du kommer att distribuera din Data filresurs i. 

För den **Data resurs konto** väljer **Skapa nytt** om du inte har ett befintligt Dataresurs-konto. Annars väljer du ett befintligt Data dela konto som du vill godkänna din data filresurs i. 

För den *emot resursnamn* fältet, du kan lämna standardnamnet som anges av Data ge eller ange ett nytt namn för mottagna resursen. 

![Peka på data resurs konto](./media/target-data-share.png "måldata dela konto") 

När du har accepterat användningsvillkoren och anges en plats för din resurs, Välj på *acceptera och konfigurera*. Om du väljer det här alternativet skapas en prenumeration för resursen och nästa skärm blir du ombedd att välja ett mållagringskonto där data ska kopieras till. 

![Acceptera alternativ](./media/accept-options.png "acceptera alternativ") 

Om du föredrar att acceptera inbjudan nu men konfigurera din lagring vid ett senare tillfälle, Välj *accepterar och konfigurera senare*. Det här alternativet kan du konfigurera din mål-lagringskontot senare. Om du vill konfigurera lagringen senare [så här konfigurerar du ditt lagringskonto](how-to-configure-mapping.md) sidan för detaljerade anvisningar om hur du återupptar dina data delar konfiguration. 

Om du inte vill att tacka ja till inbjudan, Välj *avvisa*. 

## <a name="configure-storage"></a>Konfigurera lagring
Under *Storage Målinställningar*, Välj prenumerationen, resursgrupp och storage-konto som du vill få dina data till. 

![Rikta in Lagringsinställningar](./media/target-storage-settings.png "Mållagringskontot") 

Kontrollera att du aktiverar snapshot-inställningarna om du vill få regelbundna uppdateringar av dina data. Observera att du bara ser ett schema för inställning av ögonblicksbilder om DataProvider har inkluderat i dataresursen. 

![Ta en ögonblicksbild inställningar](./media/snapshot-settings.png "ögonblicksbild inställningar") 

Välj *Spara*. 

## <a name="trigger-a-snapshot"></a>Utlösa en ögonblicksbild

Du kan utlösa en ögonblicksbild i resurserna tas emot -> fliken information genom att välja **utlösaren ögonblicksbild**. Här kan du utlösa en fullständig eller inkrementell ögonblicksbild av dina data. Om det är första gången du tar emot data från din DataProvider väljer du fullständig kopia. 

![Utlösaren ögonblicksbild](./media/trigger-snapshot.png "utlösaren ögonblicksbild") 

När den senaste körningsstatusen är *lyckade*, öppna storage-konto om du vill visa den mottagna data. 

Om du vill kontrollera vilket lagringskonto som du använde, väljer du på **datauppsättningar**. 

![Konsumenten datauppsättningar](./media/consumer-datasets.png "konsument datauppsättning mappning") 

## <a name="view-history"></a>Visa historik
Du visar en historik över din ögonblicksbilder, gå till emot resurser -> Historik. Här hittar du en historik över alla ögonblicksbilder som har genererats under de senaste 60 dagarna. 

## <a name="next-steps"></a>Nästa steg
I de här självstudierna erfarenheter så här godkänner och mottagning av en Azure-filresurs för Data. Om du vill veta mer om Azure Data filresurs begrepp, även i fortsättningen [begrepp: Azure Data dela terminologi](terminology.md).