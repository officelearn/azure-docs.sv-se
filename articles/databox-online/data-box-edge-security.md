---
title: Azure Data Box Edge-säkerhet | Microsoft-dokument
description: Beskriver de säkerhets- och sekretessfunktioner som skyddar din Azure Data Box Edge-enhet, tjänst och data lokalt och i molnet.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69970885"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Säkerhet och dataskydd i Azure Data Box Edge

Säkerhet är ett stort problem när du använder en ny teknik, särskilt om tekniken används med konfidentiella eller proprietära data. Azure Data Box Edge hjälper dig att se till att endast auktoriserade entiteter kan visa, ändra eller ta bort dina data.

I den här artikeln beskrivs säkerhetsfunktionerna i Data Box Edge som skyddar var och en av lösningskomponenterna och de data som lagras i dem.

Azure Data Box Edge består av fyra huvudkomponenter som interagerar med varandra:

- **Data Box Edge-tjänst, som finns i Azure**. Hanteringsresursen som du använder för att skapa enhetsordningen, konfigurera enheten och sedan spåra ordern till slutförande.
- **Data Box Edge-enhet**. Överföringsenheten som levereras till dig så att du kan importera lokala data till Azure.
- **Klienter/värdar som är anslutna till enheten**. Klienterna i infrastrukturen som ansluter till Data Box Edge-enheten och innehåller data som måste skyddas.
- **Molnlagring**. Platsen i Azure-molnplattformen där data lagras. Den här platsen är vanligtvis det lagringskonto som är kopplat till den Data Box Edge-resurs som du skapar.

## <a name="data-box-edge-service-protection"></a>Skydd av databoxens kanttjänst

Data Box Edge-tjänsten är en hanteringstjänst som finns i Azure. Tjänsten används för att konfigurera och hantera enheten.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Skydd mot Data Box Edge-enhet

Data Box Edge-enheten är en lokal enhet som hjälper till att omvandla dina data genom att bearbeta dem lokalt och sedan skicka dem till Azure. Enheten:

- Behöver en aktiveringsnyckel för att komma åt databoxens kanttjänst.
- Skyddas hela tiden av ett enhetslösenord.
- Är en låst enhet. Enheten BMC och BIOS är lösenordsskyddade. BIOS skyddas av begränsad användaråtkomst.
- Har säker uppstart aktiverad.
- Kör Windows Defender Device Guard. Med Device Guard kan du bara köra betrodda program som du definierar i dina principer för kodintegritet.

### <a name="protect-the-device-via-activation-key"></a>Skydda enheten via aktiveringsnyckel

Endast en auktoriserad Data Box Edge-enhet tillåts ansluta till Data Box Edge-tjänsten som du skapar i din Azure-prenumeration. Om du vill auktorisera en enhet måste du använda en aktiveringsnyckel för att aktivera enheten med tjänsten Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Mer information finns i [Hämta en aktiveringsnyckel](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Skydda enheten via lösenord

Lösenord säkerställer att endast behöriga användare kan komma åt dina data. Data Box Edge-enheter startas i låst tillstånd.

Du kan:

- Anslut till enhetens lokala webbgränssnitt via en webbläsare och ange sedan ett lösenord för att logga in på enheten.
- Fjärranslut till enhetens PowerShell-gränssnitt via HTTP. Fjärrhantering är aktiverat som standard. Du kan sedan ange enhetslösenordet för att logga in på enheten. Mer information finns i [Ansluta på distans till databoxens edge-enhet](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Använd det lokala webbgränssnittet för att [ändra lösenordet](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Om du ändrar lösenordet måste du meddela alla fjärråtkomstanvändare så att de inte har problem med att logga in.

## <a name="protect-your-data"></a>Skydda dina data

I det här avsnittet beskrivs säkerhetsfunktionerna För databoxkanten som skyddar under överföring och lagrade data.

### <a name="protect-data-at-rest"></a>Skydda data i vila

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256-bitars kryptering används för att skydda lokala data.


### <a name="protect-data-in-flight"></a>Skydda data under flygning

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Skydda data via lagringskonton

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rotera och synkronisera sedan [dina lagringskontonycklar](data-box-edge-manage-shares.md#sync-storage-keys) regelbundet för att skydda ditt lagringskonto från obehöriga användare.

## <a name="manage-personal-information"></a>Hantera personlig information

Data Box Edge-tjänsten samlar in personlig information i följande scenarier:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Om du vill visa listan över användare som kan komma åt eller ta bort en resurs följer du stegen i [Hantera resurser i datarutekanten](data-box-edge-manage-shares.md).

Mer information finns i Microsofts sekretesspolicy i [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

[Distribuera din Data Box Edge-enhet](data-box-edge-deploy-prep.md)
