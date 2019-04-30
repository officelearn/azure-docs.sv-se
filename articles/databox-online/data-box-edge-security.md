---
title: Azure Data Box Edge security | Microsoft Docs
description: Beskriver de funktioner för säkerhet och sekretess som skyddar dina Azure Data Box Edge-enhet, service och data lagras lokalt och i molnet.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 8823aebe17a5446b3c507878833c2525c338dde1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756246"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Säkerhet och dataskydd i Azure Data Box Edge

Säkerhet är av stor betydelse när du en ny teknik, särskilt om tekniken används med konfidentiell eller upphovsrättsskyddad information. Azure Data Box Edge kan du se till att endast behöriga entiteter kan visa, ändra eller ta bort dina data.

Den här artikeln beskriver Data Box Edge-säkerhetsfunktioner som skyddar alla komponenter och data som lagras i dem.

Azure Data Box Edge består av fyra huvudsakliga komponenter som interagerar med varandra:

- **Data Box Edge-tjänsten som finns i Azure**. Resursen för hantering som används för att skapa enhet-order konfigurera enheten och sedan spåra ordningen för slutförande.
- **Data Box-Edge-enhet**. Överföringsenhet som levereras till dig så att du kan importera dina lokala data till Azure.
- **Klienter/värdar som är anslutna till enheten**. Klienter i din infrastruktur som ansluter till Data Box Edge-enhet och innehåller data som måste skyddas.
- **Molnlagring**. Plats i Azure-molnplattformen där data lagras. Den här platsen är vanligtvis storage-konto som är kopplat till Data Box Edge-resurs som du skapar.

## <a name="data-box-edge-service-protection"></a>Data Box Edge skydd

Data Box Edge-tjänsten är en management-tjänst som ligger i Azure. Tjänsten används för att konfigurera och hantera enheten.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Skydd för data Box-Edge-enhet

Data Box Edge-enhet är en lokal enhet som hjälper dig att omvandla dina data genom att behandla lokalt och skicka den till Azure. Din enhet:

- Behöver en aktiveringsnyckel åtkomst till Data Box Edge-tjänsten.
- Skyddas vid alla tidpunkter av enhetens lösenord.
- Är en låst enhet. Enheten BMC och BIOS är lösenordsskyddad. BIOS skyddas av begränsad användaråtkomst.
- Har säker Start aktiverat.
- Kör Windows Defender Device Guard. Device Guard kan du köra de betrodda program som du definierar i dina kodintegritetsprinciper.

### <a name="protect-the-device-via-activation-key"></a>Skydda enheten via aktiveringsnyckeln

Endast en auktoriserad Data Box Edge-enhet får ansluta till Data Box Edge på Internet som du skapar i Azure-prenumerationen. Du måste använda en aktivering för att aktivera enheten med Edge för Data Box-tjänsten för att auktorisera en enhet.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Mer information finns i [få en aktiveringsnyckel](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Skydda enheten via lösenord

Lösenord Se till att endast behöriga användare kan komma åt dina data. Data Box Edge-enheter starta i ett låst tillstånd.

Du kan:

- Ansluta till det lokala webbgränssnittet på enheten via en webbläsare och ange sedan ett lösenord för att logga in på enheten.
- Fjärransluta till enheten PowerShell-gränssnittet via HTTP. Fjärrhantering är aktiverat som standard. Du kan sedan ange lösenordet för enheten att logga in på enheten. Mer information finns i [Anslut via en fjärranslutning till din Data Box Edge-enhet](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Använd lokalt webbgränssnitt till [ändra lösenordet](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Om du ändrar lösenordet, måste du meddela alla fjärranslutna användare så att de inte har problem med att logga.

## <a name="protect-your-data"></a>Skydda dina data

Det här avsnittet beskriver Data Box Edge-säkerhetsfunktioner som skyddar data under överföring och lagras.

### <a name="protect-data-at-rest"></a>Skydda data i vila

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Skydda data som rör sig

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Skydda data via storage-konton

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rotera och sedan [synkronisera dina lagringskontonycklar](data-box-edge-manage-shares.md#sync-storage-keys) regelbundet för att skydda ditt lagringskonto från obehöriga användare.

## <a name="manage-personal-information"></a>Hantera personlig information

Data Box Edge-tjänsten samlar in personlig information i följande scenarier:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Om du vill visa listan över användare som kan få åtkomst till eller ta bort en resurs, följer du stegen i [hantera filresurser på Data Box Edge](data-box-edge-manage-shares.md).

Mer information finns i Microsofts sekretesspolicy på den [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

[Distribuera din Data Box Edge-enhet](data-box-edge-deploy-prep.md)
