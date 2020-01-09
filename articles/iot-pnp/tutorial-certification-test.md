---
title: Certifiera din IoT Plug and Play förhands gransknings enhet | Microsoft Docs
description: I den här självstudien beskrivs hur du lägger till din produkt information till Azure-certifierad för IoT-katalogen, ansluter enheten till Azure IoT-certifierings tjänsten och sedan kör IoT-Plug and Play certifierings test.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550170"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Självstudie: certifiera din IoT Plug and Play förhands gransknings enhet

Om du vill publicera en IoT-Plug and Play för hands version i [Azure Certified för IoT-katalogen](https://aka.ms/iotdevcat)måste den klara en uppsättning certifierings test. Använd [Azure-certifierad för IoT](https://aka.ms/ACFI) -portalen för att skicka in din enhet för certifiering. [Azure IoT-certifierings tjänsten](https://aka.ms/azure-iot-aics) kör certifierings testen.

I den här självstudien får du lära dig:

> [!div class="checklist"]
> * Vilka är IoT-Plug and Playens certifierings krav.
> * Så här lägger du till produkt namn och information i portalen.
> * Så här ansluter och identifierar du IoT Plug and Play-gränssnitt.
> * Så här granskar du IoT Plug and Play-gränssnitt och kör certifierings test.
> * Publicera den certifierade IoT Plug and Play-enheten i katalogen.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Ett Microsoft Partner Center-konto.
* Microsoft Partner Network-ID.

Mer information finns i [Publicera till Azure-certifierad för IoT](howto-onboard-portal.md) -portalen.

## <a name="certification-requirements"></a>Certifieringskrav

För att certifiera din IoT Plug and Play-enhet måste enheten uppfylla följande krav:

* Din IoT Plug and Play enhets kod måste vara installerad på enheten.
* Din IoT Plug and Play enhets kod har skapats med Azure IoT SDK.
* Enhets koden måste ha stöd för [Azure-IoT Hub Device Provisioning service](../iot-dps/about-iot-dps.md).
* Enhets koden måste implementera [enhets informations gränssnittet](concepts-common-interfaces.md).
* Kapacitets modellen och enhets koden fungerar med IoT Central.

Alla enheter som för närvarande finns i katalogen anses vara förcertifierade IoT Plug and Play-enheter. Den garanterar inte kvaliteten och efterlevnaden av den slutliga produkten av IoT Plug and Play program varu komponenter som SDK och det digitala, dubbla definitions språket.

Alla förauktoriserade IoT Plug and Play-enheter måste certifieras på grund av att IoT-Plug and Play är allmänt tillgängliga, baserat på den slutgiltiga versionen av certifierings krav och program varu komponenter från Microsoft.

## <a name="add-product-name"></a>Lägg till produkt namn

**Produkten** är ett eget namn för en produkt modell som en kund kan köpa. Lägga till en produkt:

1. Logga in på [Azure-certifierad för IoT](https://aka.ms/ACFI) -portalen.
1. Gå till sidan **produkter** i portalen från den vänstra menyn och välj **+ ny**.
1. Ange ditt egna produkt namn och välj **skapa**. Namnet som anges här skiljer sig från namnet som visas i enhets katalogen.

## <a name="add-product-information"></a>Lägg till produkt information

När du har skapat produkten i portalen visas produkten på sidan **produkter** . Så här lägger du till produkt informationen:

1. Välj den skapade produkt länken som finns på **produkt** sidan i kolumnen produkt. Statusen ska vara i utkast läge.
1. Välj **Redigera** -länken bredvid **produkt informations** rubriken. Ange information om produkten på sidan produkt information och se till att du fyller i alla obligatoriska fält.
1. Välj **Spara**. Knappen **Spara** visas bara när du har slutfört alla obligatoriska fält. Om fälten är ofullständiga, säger knappen **Spara och slutför senare**.
1. Granska det innehåll som du har angett. Fyll i alla obligatoriska fält för att publicera enheten i enhets katalogen. Du kan alltid gå tillbaka för att göra ändringar i produkt informationen på produkt informations sidan genom att klicka på länken **redigera** bredvid **produkt informations** rubrik.

## <a name="connect-and-discover-interfaces"></a>Ansluta och identifiera gränssnitt

Om du vill köra certifierings testerna ansluter du enheten till Azure IoT-AICS ( [Azure IoT Certificate service](https://aka.ms/azure-iot-aics) ) som är tillgänglig i portalen.

De här stegen är ett gångs steg för att köra certifierings test och det är inte nödvändigt att ändra koden för produkt enheten. Starta genom att följa de här stegen för att ansluta till AICS:

1. Logga in på portalen med ditt partner Center-konto.
1. Klicka på **Anslut + test** för att starta certifierings flödet.
1. Välj [autentiseringsmetoden](../iot-dps/concepts-security.md#attestation-mechanism) för att etablera enheten till AICS med hjälp av [Azure-IoT Hub Device Provisioning service](../iot-dps/about-iot-dps.md).
   * Om du använder ett [X. 509-certifikat](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)laddar du upp det genererade x. 509-certifikatet. Du kanske vill granska exempel koden som visar hur du använder X. 509-certifikat: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Om du använder en [symmetrisk nyckel](../iot-dps/concepts-symmetric-key-attestation.md)kopierar du och klistrar in den symmetriska nyckeln i enhets koden.
   * Autentiseringsmetoden för TPM stöds inte för tillfället.
1. Kopiera och klistra in följande genererade ID: n i enhets koden.
   * [Registrerings-ID](../iot-dps/use-hsm-with-sdk.md)
   * [DPS-ID](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [DPS-slutpunkt](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. När din enhets kod har skapats och distribuerats till enheten väljer du **Anslut** för att identifiera IoT plug and Play-gränssnitt.
1. När anslutningen till AICS har slutförts väljer du **Nästa** för att granska identifierade IoT plug and Play-gränssnitt.

## <a name="run-tests-and-publish-the-device"></a>Köra tester och publicera enheten

På sidan Granska kan du granska identifierade IoT Plug and Play-gränssnitt. Använd den här sidan om du vill kontrol lera de primitiver som implementerats i gränssnittets visas korrekt. Du kan också kontrol lera gränssnittets plats.

1. Se till att nyttolast-indata anges för de obligatoriska fälten. Dessa fält innehåller nytto Last information för kommando primitiven för det angivna gränssnittet.
1. När du har angett all information som krävs väljer du **Nästa**.
1. Om du vill köra testerna för de implementerade IoT Plug and Play-gränssnitten väljer du **Kör tester**.
1. Alla tester körs automatiskt. Om ett test Miss lyckas väljer du **Visa loggar** för att visa fel meddelandena från AICS och den obehandlade telemetri som skickas till Azure IoT Hub.
1. Slutför certifierings testen genom att välja **Slutför**.
1. Publicera den certifierade IoT Plug and Play-enheten i katalogen. Om du vill lägga till den certifierade enheten i katalogen väljer du **Lägg till i katalog** i verktygsfältet. Om **katalogen Lägg till i** är nedtonad, betyder det antingen att produkt informationen är ofullständig eller att testerna har misslyckats. 
1. Välj länken "certifierad och i katalogen" för att visa din publicerade enhet i enhets katalogen.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om att certifiera IoT Plug and Play-enheten är det föreslagna nästa steg att lära dig mer om att hantera kapacitets modeller:

> [!div class="nextstepaction"]
> [Hantera modeller](./howto-manage-models.md)
