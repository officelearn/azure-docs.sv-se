---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 30de9181fd23a29b28973d01899f0b23fca3ae89
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014593"
---
## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig (GA)|
|Priset|**Azure Defender för behållar register** faktureras enligt [pris sidan](../articles/security-center/security-center-pricing.md)|
|Register och avbildningar som stöds:|Linux-avbildningar i ACR-register som är tillgängliga från det offentliga Internet med shell-åtkomst|
|Register och avbildningar som inte stöds:|Windows-avbildningar<br>Privata register<br>Register med begränsad åtkomst med en brand vägg, en tjänst slut punkt eller privata slut punkter som Azure Private-länk<br>Super-minimalist bilder, till exempel [Docker Scratch](https://hub.docker.com/_/scratch/) images, eller "Distroless"-avbildningar som bara innehåller ett program och dess körnings beroenden utan paket hanteraren, Shell eller OS|
|Nödvändiga roller och behörigheter:|**Säkerhets läsare** och [Azure Container Registry roller och behörigheter](../articles/container-registry/container-registry-roles.md)|
|Moln|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Kommersiella moln<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov – det finns för närvarande endast stöd för genomsökningen av push-funktionen. Läs mer i [när skannas bilder?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)<br>:::image type="icon" source="../articles/security-center/media/icons/no-icon.png" border="false"::: Kina gov, andra gov|
|||