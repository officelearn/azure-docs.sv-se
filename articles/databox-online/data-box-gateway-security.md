---
title: Azure Data Box Gateway-säkerhet | Microsoft Docs
description: Beskriver de funktioner för säkerhet och sekretess som skyddar dina Azure Data Box Gateway virtuella enheten och tjänsten data lokalt och i molnet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: b27b712128ddfbb07a7a7f68f616c20ec3fb53d3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685982"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Säkerhet och dataskydd i Azure Data Box-Gateway

Säkerhet är av stor betydelse när en ny teknik, särskilt om tekniken används med konfidentiell eller upphovsrättsskyddad information. Microsoft Azure Data Box Gateway-lösning hjälper till att säkerställa att endast auktoriserade entiteter kan visa, ändra eller ta bort dina data.

Den här artikeln beskriver Azure Data Box-Gateway-säkerhetsfunktioner som skyddar alla komponenter och data som lagras i dem.

Data Box Gateway-lösningen består av fyra huvudsakliga komponenter som interagerar med varandra:

- **Data Box-Gateway-tjänsten i Azure** – resursen för hantering som används för att skapa enheten ordning, konfigurera enheten och sedan spåra ordningen för slutförande.
- **Data Box-gatewayenhet** – den virtuella enheten som du etablerade i hypervisor-program i systemet som du angav. Den här virtuella enheten används för att importera dina lokala data till Azure.
- **Klienter/värdar som är anslutna till enheten** – klienter i din infrastruktur som ansluter till Data Box-Gateway-enheten och innehåller data som måste skyddas.
- **Molnlagring** – Platsen i Azure-molnet där data lagras. Den här platsen är vanligtvis storage-konto som är kopplat till Data Box Gateway-resursen som du skapade.


## <a name="data-box-gateway-service-protection"></a>Box-Gateway-tjänsten för dataskydd

Data Box-Gateway-tjänsten är en management-tjänst från Microsoft Azure. Tjänsten används för att konfigurera och hantera enheten.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Skydd för data Box-Gateway-enhet

Data Box-Gateway-enheten är en virtuell enhet som har etablerats i ett lokalt system som du anger hypervisor-programmet. Enheten kan skicka data till Azure. Din enhet:

- Behöver en aktiveringsnyckel åtkomst till Data Box Edge/Data Box Gateway-tjänsten.
- Skyddas vid alla tidpunkter av enhetens lösenord.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Skydda enheten via aktiveringsnyckeln

Endast en auktoriserad Data Box-Gateway-enhet får ansluta till Data Box-Gateway-tjänsten som du skapade i Azure-prenumerationen. För att auktorisera en enhet, måste du använda en aktiveringsnyckel för att aktivera enheten med Data Box-Gateway-tjänsten.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Mer information går du till [få en aktiveringsnyckel](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Skydda enheten via lösenord

Lösenord kan du kontrollera att dina data är tillgängliga för auktoriserade användare. Data Box Gateway enheter startar upp en låst.

Du kan:

- Ansluta till det lokala webbgränssnittet på enheten via en webbläsare och ange sedan ett lösenord för att logga in på enheten.
- Fjärransluta till enheten PowerShell-gränssnittet via HTTP. Fjärrhantering är aktiverat som standard. Du kan sedan ange lösenordet för enheten att logga in på enheten. Mer information går du till [Anslut via en fjärranslutning till din Data Box-gatewayenhet](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Använd lokalt webbgränssnitt till [ändra lösenordet](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Om du ändrar lösenordet, måste du meddela alla fjärranslutna användare så att de inte drabbas av ett tecken i fel.


## <a name="protect-the-data"></a>Skydda data

Det här avsnittet beskrivs säkerhetsfunktioner för Data Box-Gateway som skyddar data under överföring och lagrade data.

### <a name="protect-data-at-rest"></a>Skydda data i vila

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Skydda data som rör sig

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Skydda data via storage-konton

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rotera och sedan [synkronisera dina lagringskontonycklar](data-box-gateway-manage-shares.md#sync-storage-keys) regelbundet för att säkerställa att ditt lagringskonto inte nås av obehöriga användare.

## <a name="manage-personal-information"></a>Hantera personlig information

Data Box-Gateway-tjänsten samlar in personlig information i följande viktiga instanser:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Om du vill visa listan över användare som kan få åtkomst till eller ta bort en resurs, följer du stegen i [hantera resurser på Data Box-Gateway](data-box-gateway-manage-shares.md).

Mer information finns i Microsofts sekretesspolicy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

[Distribuera din Data Box-gatewayenhet](data-box-gateway-deploy-prep.md).
