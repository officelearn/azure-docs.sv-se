---
title: Custom Vision kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Custom Vision kryptering av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 287344b325237843a549973ae61f569eae6dac93
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202275"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision kryptering av data i vila

Azure Custom Vision krypterar automatiskt dina data när de sparas i molnet. Custom Vision kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är bara tillgängliga resurser som skapats efter 11 maj 2020. Om du vill använda CMK med Custom Vision måste du skapa en ny Custom Vision resurs. När resursen har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

### <a name="regional-availability"></a>Regional tillgänglighet

Kundhanterade nycklar är för närvarande tillgängliga i följande regioner:

* USA, södra centrala
* USA, västra 2
* USA, östra
* US Gov, Virginia

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Nästa steg

* [Formulär för Custom Vision kundhanterad nyckel](https://aka.ms/cogsvc-cmk)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


