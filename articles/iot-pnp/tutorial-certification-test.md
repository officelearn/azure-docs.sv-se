---
title: Certifiera din IoT Plug and Play Preview-enhet | Microsoft-dokument
description: I den här självstudien beskrivs hur du lägger till produktinformation i Azure Certified for IoT-enhetskatalogen, ansluter enheten till Azure IoT-certifieringstjänsten och kör sedan certifieringstesterna för IoT Plug and Play.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550170"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Självstudiekurs: Certifiera din IoT Plug and Play Preview-enhet

Om du vill publicera en IoT Plug and Play Preview-enhet i [Azure Certified for IoT-enhetskatalogen](https://aka.ms/iotdevcat)måste den klara en uppsättning certifieringstester. Använd [Azure Certified for IoT-portalen](https://aka.ms/ACFI) för att skicka in din enhet för certifiering. [Azure IoT-certifieringstjänsten](https://aka.ms/azure-iot-aics) kör certifieringstesterna.

I den här självstudien lär du dig:

> [!div class="checklist"]
> * Vilka är IoT Plug and Play-certifieringskraven.
> * Så här lägger du till ditt produktnamn och din information i portalen.
> * Så här ansluter och upptäcker du IoT Plug and Play-gränssnitt.
> * Så här granskar du IoT Plug and Play-gränssnitt och kör certifieringstester.
> * Så här publicerar du den certifierade IoT Plug and Play-enheten i katalogen.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Ett Microsoft Partner Center-konto.
* Microsoft-partnernätverks-ID: et.

Mer information finns [i Så här går du till Azure Certified for IoT-portalen.](howto-onboard-portal.md)

## <a name="certification-requirements"></a>Certifieringskrav

För att certifiera din IoT Plug and Play-enhet måste enheten uppfylla följande krav:

* IoT Plug and Play-enhetskoden måste vara installerad på enheten.
* Din IoT Plug and Play-enhetskod är byggd med Azure IoT SDK.
* Din enhetskod måste ha stöd för [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
* Enhetskoden måste implementera [enhetsinformationsgränssnittet](concepts-common-interfaces.md).
* Kapacitetsmodellen och enhetskoden fungerar med IoT Central.

Alla enheter som för närvarande finns i katalogen anses vara förcertifierade IoT Plug and Play-enheter. Det garanterar inte kvaliteten och efterlevnaden av slutprodukten av IoT Plug and Play-programvarukomponenter som SDK och digital twin definition språk.

Alla förcertifierade IoT Plug and Play-enheter måste certifiera om den allmänna tillgängligheten för IoT Plug and Play baserat på den slutliga versionen av certifieringskrav och programvarukomponenter som tillhandahålls av Microsoft.

## <a name="add-product-name"></a>Lägg till produktnamn

**Produkten** är ett eget namn för en produktmodell som en kund kan köpa. Så här lägger du till en produkt:

1. Logga in på [Azure Certified for IoT-portalen.](https://aka.ms/ACFI)
1. Gå till sidan **Produkter** i portalen från den vänstra menyn och välj **+ Nytt**.
1. Ange ditt eget produktnamn och välj **Skapa**. Namnet som anges här skiljer sig från namnet som visas i enhetskatalogen.

## <a name="add-product-information"></a>Lägg till produktinformation

När du har skapat produkten i portalen visas produkten på sidan **Produkter.** Så här lägger du till produktinformationen:

1. Välj den skapade **produktlänken** som finns på produktsidan i produktkolumnen. Staten bör vara i utkast läge.
1. Markera länken **Redigera** bredvid rubriken **Produktinformation.** Ange information om produkten på produktinformationssidan och se till att du fyller i alla obligatoriska fält.
1. Välj **Spara**. Knappen **Spara** visas bara när du har slutfört alla obligatoriska fält. Om fälten är ofullständiga står det på knappen **Spara och avsluta senare**.
1. Granska innehållet du angav. Fyll i alla obligatoriska fält för att publicera enheten i enhetskatalogen. Du kan alltid gå tillbaka för att göra ändringar i produktinformationen på produktinformationssidan genom att klicka på **redigeringslänken** bredvid Rubriken **Produktinformation.**

## <a name="connect-and-discover-interfaces"></a>Ansluta och upptäck gränssnitt

Om du vill köra certifieringstesterna ansluter du enheten till [Azure IoT-certifieringstjänsten](https://aka.ms/azure-iot-aics) (AICS) som är tillgänglig i portalen.

De här stegen är ett steg för att köra certifieringstester och det är inte nödvändigt att ändra produktenhetskoden. Börja med att följa dessa steg för att ansluta till AICS:

1. Logga in på portalen med ditt Partner Center-konto.
1. Klicka på **Anslut + test** för att starta certifieringsflödet.
1. Välj [autentiseringsmetoden](../iot-dps/concepts-security.md#attestation-mechanism) för att etablera enheten till AICS med hjälp av [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
   * Om du använder ett [X.509-certifikat](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)laddar du upp ditt genererade X.509-certifikat. Du kanske vill granska exempelkoden som visar hur du använder X.509-certifikat: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Om du använder en [symmetrisk nyckel](../iot-dps/concepts-symmetric-key-attestation.md)kopierar och klistrar du in den symmetriska nyckeln i enhetskoden.
   * TPM-autentiseringsmetod stöds inte just nu.
1. Kopiera och klistra in följande genererade ID:er i enhetskoden.
   * [Registrerings-ID](../iot-dps/use-hsm-with-sdk.md)
   * [DPS-ID](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [DPS-slutpunkt](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. När enhetskoden är byggd och distribuerad till enheten väljer du **Anslut** för att identifiera IoT Plug and Play-gränssnitten.
1. När anslutningen till AICS lyckas väljer du **Nästa** för att granska identifierade IoT Plug and Play-gränssnitten.

## <a name="run-tests-and-publish-the-device"></a>Kör tester och publicera enheten

På granskningssidan kan du granska de identifierade IoT Plug and Play-gränssnitten. Använd den här sidan för att kontrollera de primitiver som implementeras i gränssnittsvisningen korrekt. Du kan också kontrollera platsen för gränssnittet.

1. Kontrollera att nyttolastindata anges för de obligatoriska fälten. Dessa fält innehåller nyttolastinformation för kommandot primitiva för det angivna gränssnittet.
1. När du har angett all nödvändig information väljer du **Nästa**.
1. Om du vill köra testerna för de implementerade IoT Plug and Play-gränssnitten väljer du **Kör tester**.
1. Alla tester körs automatiskt. Om något test misslyckas väljer du **Visa loggar** för att visa felmeddelandena från AICS och den råa telemetrin som skickas till Azure IoT Hub.
1. Om du vill slutföra certifieringstesterna väljer du **Slutför**.
1. Publicera den certifierade IoT Plug and Play-enheten i katalogen. Om du vill lägga till den certifierade enheten i katalogen väljer du **Lägg till i katalogen** i verktygsfältet. Om **katalogen Lägg till** är nedtonad betyder det att produktinformationen antingen är ofullständig eller så har testerna misslyckats. 
1. Välj länken "CERTIFIERAD OCH I KATALOGEN" om du vill visa den publicerade enheten i enhetskatalogen.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om att certifiera IoT Plug and Play-enhet är det föreslagna nästa steget att lära dig mer om hur du hanterar kapacitetsmodeller:

> [!div class="nextstepaction"]
> [Hantera modeller](./howto-manage-models.md)
