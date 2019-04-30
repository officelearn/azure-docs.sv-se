---
title: Azure Data Box Gateway-säkerhet | Microsoft Docs
description: Beskriver de funktioner för säkerhet och sekretess som skyddar dina Azure Data Box Gateway virtuella enheten och tjänsten data, både lokalt och i molnet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 230d1a28ba15a8736e46c02cb08217a28fc18599
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754358"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Säkerhet och dataskydd i Azure Data Box-Gateway

Säkerhet är av stor betydelse när du en ny teknik, särskilt om tekniken används med konfidentiell eller upphovsrättsskyddad information. Azure Data Box-Gateway kan du se till att endast behöriga entiteter kan visa, ändra eller ta bort dina data.

Den här artikeln beskriver de säkerhetsfunktionerna i Azure Data Box-Gateway som skyddar alla komponenter och data som lagras i dem.

Data Box Gateway-lösningen består av fyra huvudsakliga komponenter som interagerar med varandra:

- **Data Box-Gateway-tjänsten som finns i Azure**. Resursen för hantering som används för att skapa enhet-order konfigurera enheten och sedan spåra ordningen för slutförande.
- **Data Box-gatewayenhet**. Den virtuella enheten som du etablerar i hypervisor-programmet för systemet som du anger. Den här virtuella enheten används för att importera dina lokala data till Azure.
- **Klienter/värdar som är anslutna till enheten**. Klienter i din infrastruktur som ansluter till Data Box-Gateway-enheten och innehåller data som måste skyddas.
- **Molnlagring**. Plats i Azure-molnplattformen där data lagras. Den här platsen är vanligtvis storage-konto som är kopplat till Data Box Gateway-resursen som du skapar.


## <a name="data-box-gateway-service-protection"></a>Box-Gateway-tjänsten för dataskydd

Data Box-Gateway-tjänsten är en management-tjänst som ligger i Azure. Tjänsten används för att konfigurera och hantera enheten.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Skydd för data Box-Gateway-enhet

Data Box-Gateway-enheten är en virtuell enhet som har etablerats i ett lokalt system som du anger hypervisor-program. Enheten kan skicka data till Azure. Din enhet:

- Behöver en aktiveringsnyckel åtkomst till Data Box Edge/Data Box Gateway-tjänsten.
- Skyddas vid alla tidpunkter av enhetens lösenord.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Skydda enheten via aktiveringsnyckeln

Endast en auktoriserad Data Box-Gateway-enhet får ansluta till Data Box-Gateway-tjänsten som du skapar i Azure-prenumerationen. Du måste använda en aktivering för att aktivera enheten med Data Box-Gateway-tjänsten för att auktorisera en enhet.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Mer information finns i [få en aktiveringsnyckel](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Skydda enheten via lösenord

Lösenord Se till att endast behöriga användare kan komma åt dina data. Data Box Gateway enheter startar upp en låst.

Du kan:

- Ansluta till det lokala webbgränssnittet på enheten via en webbläsare och ange sedan ett lösenord för att logga in på enheten.
- Fjärransluta till enhetens PowerShell-gränssnittet via HTTP. Fjärrhantering är aktiverat som standard. Du kan sedan ange lösenordet för enheten att logga in på enheten. Mer information finns i [Anslut via en fjärranslutning till din Data Box-gatewayenhet](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Använd lokalt webbgränssnitt till [ändra lösenordet](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Om du ändrar lösenordet, måste du meddela alla fjärranslutna användare så att de inte har problem med att logga.


## <a name="protect-your-data"></a>Skydda dina data

Det här avsnittet beskrivs säkerhetsfunktioner för Data Box-Gateway som skyddar data under överföring och lagras.

### <a name="protect-data-at-rest"></a>Skydda data i vila

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Skydda data som rör sig

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Skydda data via storage-konton

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rotera och sedan [synkronisera dina lagringskontonycklar](data-box-gateway-manage-shares.md#sync-storage-keys) regelbundet för att skydda ditt lagringskonto från obehöriga användare.

## <a name="manage-personal-information"></a>Hantera personlig information

Data Box-Gateway-tjänsten samlar in personlig information i följande scenarier:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Om du vill visa listan över användare som kan få åtkomst till eller ta bort en resurs, följer du stegen i [hantera resurser på Data Box-Gateway](data-box-gateway-manage-shares.md).

Mer information finns i Microsofts sekretesspolicy på den [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

[Distribuera din Data Box-Gateway-enhet](data-box-gateway-deploy-prep.md)
