---
title: Azure Data Box Edge security | Microsoft Docs
description: Beskriver de funktioner för säkerhet och sekretess som skyddar dina Azure Data Box Edge-enhet, tjänst och data lokalt och i molnet.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 5316ddf9d456731f2789241434926366f732993a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682108"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Säkerhet och dataskydd i Azure Data Box Edge

Säkerhet är av stor betydelse när en ny teknik, särskilt om tekniken används med konfidentiell eller upphovsrättsskyddad information. Microsoft Azure Data Box Edge lösningen säkerställer att endast auktoriserade entiteter kan visa, ändra eller ta bort dina data.

Den här artikeln beskriver Data Box Edge-säkerhetsfunktioner som skyddar alla komponenter och data som lagras i dem.

Azure Data Box Edge-lösningen består av fyra huvudsakliga komponenter som interagerar med varandra:

- **Data Box Edge-tjänsten i Azure** – resursen för hantering som används för att skapa enheten ordning, konfigurera enheten och sedan spåra ordningen för slutförande.
- **Data Box-Edge-enhet** – överföringsenhet som levereras till dig att importera dina lokala data till Azure.
- **Klienter/värdar som är anslutna till enheten** – klienter i din infrastruktur som ansluter till Data Box Edge-enhet och innehåller data som måste skyddas.
- **Molnlagring** – Platsen i Azure-molnet där data lagras. Den här platsen är vanligtvis storage-konto som är länkad till Data Box Edge-resurs som du skapade.

## <a name="data-box-edge-service-protection"></a>Data Box Edge skydd

Data Box Edge-tjänsten är en management-tjänst från Microsoft Azure. Tjänsten används för att konfigurera och hantera enheten.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Skydd för data Box-Edge-enhet

Data Box Edge-enhet är en lokal enhet som hjälper dig att transformera data med bearbetning av lokalt och sedan skicka den till Azure. Din enhet:

- Behöver en aktiveringsnyckel åtkomst till Data Box Edge-tjänsten.
- Skyddas vid alla tidpunkter av enhetens lösenord.
- Är en låst enhet. Enheten BMC och BIOS är lösenordsskyddad med begränsad användaråtkomst för BIOS.
- Har säker Start aktiverat.
- Kör Windows Defender Device Guard. Device Guard kan du köra de betrodda program som du definierar i dina kodintegritetsprinciper.

### <a name="protect-the-device-via-activation-key"></a>Skydda enheten via aktiveringsnyckeln

Endast en auktoriserad Data Box Edge-enhet får ansluta till Data Box Edge på Internet som du skapade i Azure-prenumerationen. För att auktorisera en enhet, måste du använda en aktiveringsnyckel för att aktivera enheten med Edge för Data Box-tjänsten.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Mer information går du till [få en aktiveringsnyckel](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Skydda enheten via lösenord

Lösenord kan du kontrollera att dina data är tillgängliga för auktoriserade användare. Data Box Edge-enheter starta i ett låst tillstånd.

Du kan:

- Ansluta till det lokala webbgränssnittet på enheten via en webbläsare och ange sedan ett lösenord för att logga in på enheten.
- Fjärransluta till enheten PowerShell-gränssnittet via HTTP. Fjärrhantering är aktiverat som standard. Du kan sedan ange lösenordet för enheten att logga in på enheten. Mer information går du till [Anslut via en fjärranslutning till din Data Box Edge-enhet](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Använd lokalt webbgränssnitt till [ändra lösenordet](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Om du ändrar lösenordet, måste du meddela alla fjärranslutna användare så att de inte drabbas av ett tecken i fel.

## <a name="protect-the-data"></a>Skydda data

Det här avsnittet beskriver Data Box Edge-säkerhetsfunktioner som skyddar data under överföring och lagrade data.

### <a name="protect-data-at-rest"></a>Skydda data i vila

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Skydda data som rör sig

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Skydda data via storage-konton

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rotera och sedan [synkronisera dina lagringskontonycklar](data-box-edge-manage-shares.md#sync-storage-keys) regelbundet för att säkerställa att ditt lagringskonto inte nås av obehöriga användare.

## <a name="manage-personal-information"></a>Hantera personlig information

Data Box Edge-tjänsten samlar in personlig information i följande viktiga instanser:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Om du vill visa listan över användare som kan få åtkomst till eller ta bort en resurs, följer du stegen i [hantera filresurser på Data Box Edge](data-box-edge-manage-shares.md).

Mer information finns i Microsofts sekretesspolicy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

[Distribuera din Data Box Edge-enhet](data-box-edge-deploy-prep.md).
