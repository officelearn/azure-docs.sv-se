---
title: Formulär igenkännings tjänst kryptering av vilande data
titleSuffix: Azure Cognitive Services
description: Formulär tolkens kryptering av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 782e03f572f007b8523c3ae797dd626c383a01f4
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310724"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Formulär tolkens kryptering av data i vila

Azure formulär tolken krypterar dina data automatiskt när de sparas i molnet. Formulär igenkännings kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är bara tillgängliga resurser som skapats efter 11 maj 2020. Om du vill använda CMK med formulär tolken måste du skapa en ny formulär igenkännings resurs. När resursen har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Nästa steg

* [Formulär tolkens formulär för hanterad nyckel för formulär](https://aka.ms/cogsvc-cmk)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


