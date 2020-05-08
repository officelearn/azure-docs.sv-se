---
title: Koncept – säkerhets kontroller för Azure våren Cloud service
description: Använd säkerhets kontroller som är inbyggda i Azure våren Cloud service.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 5b459ef57d0e8a22ce1cd53f56c44d31e53c7c93
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594992"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Säkerhets kontroller för Azure våren Cloud service
Säkerhets kontroller är inbyggda i moln tjänsten Azure våren.

En säkerhets kontroll är en kvalitet eller funktion i en Azure-tjänst som bidrar till tjänstens möjlighet att förhindra, identifiera och reagera på säkerhets risker.  För varje kontroll använder vi *Ja* eller *Nej* för att ange om den finns för närvarande för tjänsten.  Vi använder *saknas* för en kontroll som inte är tillämplig för tjänsten. 

**Säkerhets kontroller för data skydd**

| Säkerhets kontroll | Ja/nej | Obs! | Dokumentation |
|:-------------|:-------|:-------------------------------|:----------------------|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja | Användare som överfört källa och artefakter, konfigurations Server inställningar, appinställningar och data i beständig lagring lagras i Azure Storage, som automatiskt krypterar innehållet i vila.<br><br>Config server cache, binärfiler för körning som skapats utifrån överförd källa och program loggar under programmets livs längd sparas till Azure-hanterad disk, som automatiskt krypterar innehållet i vila.<br><br>Behållar avbildningar som skapats från användarens överförda källa sparas i Azure Container Registry som automatiskt krypterar avbildnings innehållet i vila. | [Azure Storage-kryptering av vilande data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Kryptering på Server sidan av Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Lagrings avbildnings lagring i Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Kryptering i tillfällig | Ja | Användar programmets offentliga slut punkter använder HTTPS för inkommande trafik som standard. |  |
| Krypterade API-anrop | Ja | Hanterings anrop för att konfigurera Azure våren Cloud service sker via Azure Resource Manager-anrop via HTTPS. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

