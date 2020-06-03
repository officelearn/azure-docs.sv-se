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
ms.openlocfilehash: 85cf251db69d33f02e928eaea6a848f20ae7d923
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310299"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision kryptering av data i vila

Azure Custom Vision krypterar automatiskt dina data när de sparas i molnet. Custom Vision kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är bara tillgängliga resurser som skapats efter 11 maj 2020. Om du vill använda CMK med Custom Vision måste du skapa en ny Custom Vision resurs. När resursen har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

## <a name="regional-availability"></a>Regional tillgänglighet

Kundhanterade nycklar är för närvarande tillgängliga i följande regioner:

* USA, södra centrala
* USA, västra 2
* USA, östra
* US Gov, Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Nästa steg

* En fullständig lista över tjänster som stöder CMK finns i [kund hanterade nycklar för Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Formulär för Cognitive Services kundhanterad nyckel](https://aka.ms/cogsvc-cmk)
