---
title: Skapa ett erbjudande för IoT Edge-modulen | Microsoft Docs
description: Hur du publicerar en ny IoT Edge-modul för Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: d3cc1a09963c5f7fee613af24c63fd15b1cfffee
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811297"
---
# <a name="how-to-publish-a-new-iot-edge-module-in-the-cloud-partner-portal"></a>Hur du publicerar en ny IoT Edge-modul i partnerportalen i molnet

Den här artikeln beskriver steg för att publicera ett nytt erbjudande för IoT Edge-modulen.

## <a name="prerequisites"></a>Förutsättningar

Följande krav gäller för att publicera en IoT Edge-modul för Azure Marketplace.

-   Åtkomst till den [Cloud Partner Portal (CPP)](https://cloudpartner.azure.com/#alloffers). Mer information finns i den [publiceringsguide](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   Har din IoT Edge-modul finns i ett Azure Container Registry.

-   Har din IoT Edge-modulen metadata redo (inklusive icke-uttömmande förteckning):

    -   Titel

    -   Beskrivning (grundläggande HTML-format)

    -   En logotyp i png-avbildningsformatet och följande storlekar: 40 bildpunkter x 40 bildpunkter, 90 bildpunkter x 90 bildpunkter, 115 bildpunkter x 115 bildpunkter, 255 bildpunkter x 115 bildpunkter.

    -   Användningsvillkoren och sekretesspolicyn princip

<a name="prepare-your-iot-edge-module-listing-in-cpp"></a>Förbereda din IoT Edge-modul-registrering i CPP
-------------------------------------------

### <a name="create-a-new-offer-of-the-type-iot-edge-module"></a>Skapa ett nytt erbjudande av typen IoT Edge-modul 

Följ dessa steg för att förbereda din IoT Edge-modul-registrering:

-   Logga in till din [CPP konto](https://cloudpartner.azure.com/).

>[!Note]
>Allmän information om Cloud Partner Portal kan du kontrollera den [Läs dokumentationen](https://cloudpartner.azure.com/#learn)

-   Välj **nytt erbjudande**, och välj sedan **IoT Edge-modul**.

>[!NOTE]
>En IoT Edge-modul är en behållare som har utformats för att köra på IoT Edge. Scenarier som bemöts av en IoT Edge-modul måste vara klokt i en IoT Edge-kontext. Den innehåller också standardkonfigurationsinställningar för att göra en IoT Edge-enhet schemabaserad distributionen. Behållaren också innefatta IoT Edge-modulen SDK för att aktivera kommunikation med edgeHub och IoT Hub.

### <a name="define-your-offer-settings"></a>Definiera inställningar för erbjudandet

Ange information för ditt erbjudande i erbjuder inställningar-fliken.

![Erbjud identitet](./media/cloud-partner-portal-create-iot-edge-module-offer/offer-identity.png)


-   Den **erbjudande-ID** identifierar ditt erbjudande i CPP och kan användas i kundinriktad URL: er.

-   Den **namn** visas endast av dig för refererar till det här erbjudandet i CPP.

### <a name="create-one-or-more-skus"></a>Skapa en eller flera SKU: er

Varje SKU motsvarar en behållaravbildning. Du måste ha minst en SKU och kan lägga till fler än en. Det finns två delar till en SKU:

-   SKU-metadata

-   Behållare för metadata

**Skapa en SKU:**

Välj den **SKU: er** fliken och välj sedan **nya SKU: N**.

![Nya SKU: N](./media/cloud-partner-portal-create-iot-edge-module-offer/SKUs.png)

SKU-metadata innehåller följande fält som krävs:
- SKU-ID – en unik identifierare.
- Rubrik - SKU-rubriken, högst 50 tecken.
- Sammanfattning – en kort beskrivning, upp till 100 tecken.
- Beskrivning – en lång beskrivning.
- Dölj den här SKU – standardvärdet är **nr**.
   
![SKU-information](./media/cloud-partner-portal-create-iot-edge-module-offer/SKU-settings.png)

#### <a name="iot-edge-module-metadata-and-the-container-registry"></a>IoT Edge-modulen metadata och container registry

IoT Edge-modul metadata innehåller referensinformation för avbildning som lagras i Azure container registry (ACR). Azure Marketplace kopierar avbildningen till registret offentliga marknaden och det är tillgängligt för kunder efter certifiering. Alla användarbegäran om att använda en IoT Edge-modulen bild hanteras från Marketplace container registry.

![Containeravbildningar](./media/cloud-partner-portal-create-iot-edge-module-offer/container-images.png)

**Behållaravbildningar**

Avbildningsdetaljerna som databasen har följande obligatoriska fält:

-   **Prenumerations-ID** Azure prenumerations-id där ACR-registret finns.

-   **Resursgruppens namn** – resursgruppens namn för ACR-registret.

-   **Registernamn** – The ACR registernamn.

-   **Namnet på lagringsplatsen** – namnet på lagringsplatsen. När kan det här värdet inte ändras senare. Namnet bör vara unikt så att inga andra erbjudanden under ditt konto har samma namn.

-   **Användarnamn** – användarnamnet som är associerade med ACR (administratörsanvändarnamn).

-   **Lösenord** – lösenordet som associeras med ACR.

    >[!Note]
    >Det användarnamn och lösenord krävs för att se till att partner har åtkomst till ACR som beskrivs i publiceringsprocessen.

När du publicerar en IoT Edge-modul-avbildning, kan du ange en eller flera taggar. Glöm inte att lägga till taggen ”senaste” (standard) i din modul så att du lätt kan identifiera modulen under testningen.

Du kan också ange följande IoT Edge-modulen ärendets:

-   **createOptions** -standard createOptions att skicka så att den här IoT Edge-modulen kan startas direkt ur lådan.

-   **Twin:** -standard-twin att skicka så att den här IoT Edge-modulen kan startas direkt när du använder SDK för IoT-modulen.

-   **vägar:** -standardvägar att skicka så att den här IoT Edge-modulen kan startas direkt om du använder SDK för IoT-modulen.

### <a name="describe-your-iot-edge-module-for-your-customers"></a>Beskriv IoT Edge-modul för dina kunder

Lägg till innehåll för din marknadsföring på fliken Marketplace. Den här informationen är vad blir offentligt synlig och visas på Azure Marketplace.

![Översikt över IoT Edge-modul](./media/cloud-partner-portal-create-iot-edge-module-offer/overview.png)

-   **Rubrik** -rubriken för din IoT Edge-modul som offentlig.

-   **Sammanfattning av** -sammanfattning av din IoT Edge-modul som offentlig i de flesta sidor, till exempel den surfning sidor.

-   **Lång sammanfattning** -sammanfattning av din IoT Edge-modul som offentlig när modulen är aktuell. 

-   **Beskrivning av** -beskrivning av din IoT Edge-modul som offentlig på sidan med produkten. (Du kan använda grundläggande HTML-taggar för att formatera en beskrivning.)

-   **Marknadsföring identifierare** – en unik identifierare som används för att skapa din produkt-URL. Den här URL: en är i följande format: *azuremarketplace.microsoft.com/enus/marketplace/apps/yourpublisherid.youriotedgemodulemarketingidentifier*.

-   **Förhandsgranska prenumerations-ID** – användare som har åtkomst till dessa prenumerationer kommer att kunna se IoT Edge-modul efter certifieringssteget och innan det lanseras.

-   **Användbara länkar** – du kan lägga till upp till 10 länkar som visas på din produktsidan för information.

-   **Föreslås kategorier** -Välj upp till fem kategorier. De visas på din produktsidan för information. För närvarande alla IoT Edge-moduler visas den *Internet of Things \> IoT Edge-modul* kategorier på Bläddra-sidor.

-   **Logotyper** – ladda upp din IoT Edge-modulen logotypbilder i PNG-format. Använd följande storlekar: och exakt i följande storlekar: 40 bildpunkter x 40 bildpunkter, 90 bildpunkter x 90 bildpunkter, 115 bildpunkter x 115 bildpunkter, 255 bildpunkter x 115 bildpunkter.

-   **Skärmbilder** -skärmbilder visas på din produktsidan för information. Det är ett bra sätt att förmedla visuellt vad din IoT Edge-modul gör och hur det fungerar. Du kan visa Arkitekturdiagram eller använda fallet illustrationer exempelvis.

-   **Videor** -videor visas på din produktsidan för information. Det är ett bra sätt att förmedla visuellt vad din IoT Edge-modul gör och hur det fungerar.

-   **Lead-hantering** – du kan välja ett system att samla in alla leads som visar hur du din produkt.

-   **Sekretess** -du måste ha en URL som pekar till din sekretesspolicy.

-   **Användningsvillkor** -du måste ha användningsvillkor. Du kan använda HTML-taggar för att formatera den här sidan eller pekar mot en av dina andra sidor.

### <a name="enter-your-support-contact-information"></a>Ange din kontaktinformation för support

På fliken Support ger **Engineering Contact** och **kundsupport** information.

![Supportkontakter](./media/cloud-partner-portal-create-iot-edge-module-offer/support-contact-info.png)


## <a name="certify-your-iot-edge-module"></a>Certifiera din IoT Edge-modul

När du har angett all information som krävs, Välj **publicera** att skicka din IoT Edge-modul för certifiering. Du ser en tidslinje som visar är stegen i certifieringsprocessen.

**Verifiering av modul**

Din modul har verifierats av våra certifiering. När modulen är certifierat får du en privat länk för att testa din modul. Om du behöver göra ändringar efter testning kan redigera ditt erbjudande metadata och skicka nytt modulen till teamet för certifiering. 

## <a name="publish-your-iot-edge-module"></a>Publicera din IoT Edge-modul

När du har slutfört testningen och är redo att publicera, Välj **Go Live** att publicera din IoT Edge-modul.

>[!Important]
>Om du vill ha din IoT Edge-modul som presenterades vid Ignite-evenemanget måste din modul vara offentliga 09/23/2018.
