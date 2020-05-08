---
title: Azure Stack Edge-säkerhet | Microsoft Docs
description: Beskriver funktioner för säkerhet och sekretess som skyddar din Azure Stack Edge-enhet, tjänst och data lokalt och i molnet.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 3d567ec4d760be24fdbb79ff85bd6db0eb4a66c8
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570075"
---
# <a name="azure-stack-edge-security-and-data-protection"></a>Azure Stack gräns säkerhet och data skydd

Säkerhet är ett viktigt problem när du använder en ny teknik, särskilt om tekniken används med konfidentiell eller patentskyddad information. Azure Stack Edge hjälper dig att se till att endast auktoriserade entiteter kan visa, ändra eller ta bort dina data.

I den här artikeln beskrivs Azure Stack Edge-säkerhetsfunktioner som skyddar varje lösnings komponent och de data som lagras i dem.

Azure Stack kanten består av fyra huvud komponenter som interagerar med varandra:

- **Azure Stack Edge-tjänsten, som finns i Azure**. Hanterings resursen som du använder för att skapa enhets ordningen, konfigurera enheten och sedan spåra beställningen.
- **Azure Stack Edge-enhet**. Den överförings enhet som skickas till dig så att du kan importera dina lokala data till Azure.
- **Klienter/värdar som är anslutna till enheten**. De klienter i infrastrukturen som ansluter till Azure Stack Edge-enheten och innehåller data som behöver skyddas.
- **Moln lagring**. Platsen i Azure Cloud Platform där data lagras. Den här platsen är vanligt vis det lagrings konto som är kopplat till Azure Stack Edge-resurs som du skapar.

## <a name="azure-stack-edge-service-protection"></a>Azure Stack Edge service Protection

Tjänsten Azure Stack Edge är en hanterings tjänst som finns i Azure. Tjänsten används för att konfigurera och hantera enheten.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-device-protection"></a>Azure Stack gräns enhets skydd

Azure Stack Edge-enheten är en lokal enhet som hjälper till att transformera data genom att bearbeta den lokalt och sedan skicka den till Azure. Din enhet:

- Behöver en aktiverings nyckel för att få åtkomst till tjänsten Azure Stack Edge.
- Skyddas hela tiden av ett enhets lösen ord.
- Är en låst enhet. Enheten BMC och BIOS är lösenordsskyddade. BIOS skyddas av begränsad användar åtkomst.
- Har säker start aktiverat.
- Kör Windows Defender Device Guard. Med Device Guard kan du bara köra betrodda program som du definierar i dina kod integritets principer.

### <a name="protect-the-device-via-activation-key"></a>Skydda enheten via aktiverings nyckeln

Endast en behörig Azure Stack gräns enhet får ansluta till den Azure Stack Edge-tjänst som du skapar i din Azure-prenumeration. Om du vill auktorisera en enhet måste du använda en aktiverings nyckel för att aktivera enheten med Azure Stack Edge-tjänsten.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Mer information finns i [Hämta en aktiverings nyckel](azure-stack-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Skydda enheten via lösen ord

Lösen ord se till att endast behöriga användare kan komma åt dina data. Azure Stack Edge-enheter startar i låst tillstånd.

Du kan:

- Anslut till det lokala webb gränssnittet på enheten via en webbläsare och ange ett lösen ord för att logga in på enheten.
- Fjärrans luta till enhetens PowerShell-gränssnitt över HTTP. Fjärrhantering är aktiverat som standard. Du kan sedan ange enhetens lösen ord för att logga in på enheten. Mer information finns i fjärrans [luta till din Azure Stack Edge-enhet](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Använd det lokala webb gränssnittet för att [ändra lösen ordet](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access). Om du ändrar lösen ordet ska du se till att meddela alla användare av fjärråtkomst så att de inte har problem med att logga in.

## <a name="protect-your-data"></a>Skydda dina data

I det här avsnittet beskrivs Azure Stack Edge-säkerhetsfunktioner som skyddar data som överförs och lagras.

### <a name="protect-data-at-rest"></a>Skydda data i vila

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker-XTS-AES 256-bit Encryption används för att skydda lokala data.


### <a name="protect-data-in-flight"></a>Skydda data i flygningen

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Skydda data via lagrings konton

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rotera och [Synkronisera dina lagrings konto nycklar](azure-stack-edge-manage-shares.md#sync-storage-keys) regelbundet för att hjälpa till att skydda ditt lagrings konto från obehöriga användare.

## <a name="manage-personal-information"></a>Hantera personlig information

Tjänsten Azure Stack Edge samlar in personlig information i följande scenarier:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Om du vill visa en lista över användare som har åtkomst till eller tar bort en resurs följer du stegen i [Hantera resurser på Azure Stack Edge](azure-stack-edge-manage-shares.md).

Mer information hittar du i sekretess policyn för Microsoft på [säkerhets Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

[Distribuera din Azure Stack Edge-enhet](azure-stack-edge-deploy-prep.md)
