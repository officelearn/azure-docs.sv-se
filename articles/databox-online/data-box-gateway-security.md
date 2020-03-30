---
title: Azure Data Box Gateway-säkerhet | Microsoft-dokument
description: Beskriver de säkerhets- och sekretessfunktioner som skyddar din virtuella Azure Data Box Gateway-enhet, tjänst och data, lokalt och i molnet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900610"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box Gateway säkerhet och dataskydd

Säkerhet är ett stort problem när du använder en ny teknik, särskilt om tekniken används med konfidentiella eller proprietära data. Azure Data Box Gateway hjälper dig att se till att endast auktoriserade entiteter kan visa, ändra eller ta bort dina data.

I den här artikeln beskrivs säkerhetsfunktionerna för Azure Data Box Gateway som skyddar var och en av lösningskomponenterna och de data som lagras i dem.

Data Box Gateway-lösningen består av fyra huvudkomponenter som interagerar med varandra:

- **Data Box Gateway-tjänst, som finns i Azure**. Hanteringsresursen som du använder för att skapa enhetsordningen, konfigurera enheten och sedan spåra ordern till slutförande.
- **Data Box Gateway-enhet**. Den virtuella enhet som du etablerar i hypervisorn för det system som du tillhandahåller. Den här virtuella enheten används för att importera lokala data till Azure.
- **Klienter/värdar som är anslutna till enheten**. Klienterna i infrastrukturen som ansluter till Data Box Gateway-enheten och innehåller data som måste skyddas.
- **Molnlagring**. Platsen i Azure-molnplattformen där data lagras. Den här platsen är vanligtvis det lagringskonto som är kopplat till databoxgatewayresursen som du skapar.


## <a name="data-box-gateway-service-protection"></a>Skydd av tjänsten Data Box Gateway

Data Box Gateway-tjänsten är en hanteringstjänst som finns i Azure. Tjänsten används för att konfigurera och hantera enheten.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Skydd av Data Box Gateway-enhet

Data Box Gateway-enheten är en virtuell enhet som är etablerad i hypervisorn för ett lokalt system som du tillhandahåller. Enheten hjälper till att skicka data till Azure. Enheten:

- Behöver en aktiveringsnyckel för att komma åt tjänsten Data Box Edge/Data Box Gateway.
- Skyddas hela tiden av ett enhetslösenord.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Skydda enheten via aktiveringsnyckel

Endast en auktoriserad Data Box Gateway-enhet tillåts ansluta till Tjänsten Data Box Gateway som du skapar i din Azure-prenumeration. Om du vill auktorisera en enhet måste du använda en aktiveringsnyckel för att aktivera enheten med tjänsten Data Box Gateway.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

Mer information finns i [Hämta en aktiveringsnyckel](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Skydda enheten via lösenord

Lösenord säkerställer att endast behöriga användare kan komma åt dina data. Data Box Gateway-enheter startas i låst tillstånd.

Du kan:

- Anslut till enhetens lokala webbgränssnitt via en webbläsare och ange sedan ett lösenord för att logga in på enheten.
- Fjärranslut till enhetens PowerShell-gränssnitt via HTTP. Fjärrhantering är aktiverat som standard. Du kan sedan ange enhetslösenordet för att logga in på enheten. Mer information finns i [Ansluta på distans till databoxgatewayenheten](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Använd det lokala webbgränssnittet för att [ändra lösenordet](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Om du ändrar lösenordet måste du meddela alla fjärråtkomstanvändare så att de inte har problem med att logga in.


## <a name="protect-your-data"></a>Skydda dina data

I det här avsnittet beskrivs säkerhetsfunktionerna för Data Box Gateway som skyddar under överföring och lagrade data.

### <a name="protect-data-at-rest"></a>Skydda data i vila

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Skydda data under flygning

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Skydda data via lagringskonton

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rotera och synkronisera sedan [dina lagringskontonycklar](data-box-gateway-manage-shares.md#sync-storage-keys) regelbundet för att skydda ditt lagringskonto från obehöriga användare.

## <a name="manage-personal-information"></a>Hantera personlig information

Tjänsten Data Box Gateway samlar in personlig information i följande scenarier:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Om du vill visa listan över användare som kan komma åt eller ta bort en resurs följer du stegen i [Hantera resurser i Data Box Gateway](data-box-gateway-manage-shares.md).

Mer information finns i Microsofts sekretesspolicy i [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

[Distribuera enheten för databoxgateway](data-box-gateway-deploy-prep.md)
