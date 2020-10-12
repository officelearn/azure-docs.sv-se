---
title: Custom Vision kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Microsoft erbjuder Microsoft-hanterade krypterings nycklar och du kan också hantera dina Cognitive Services prenumerationer med dina egna nycklar, som kallas Kundhanterade nycklar (CMK). Den här artikeln beskriver data kryptering i vila för Custom Vision och hur du aktiverar och hanterar CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 39257419f179bdce8c94f2ddb3a7cd8f5ac2d34f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89077763"
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
* East US
* US Gov, Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Nästa steg

* En fullständig lista över tjänster som stöder CMK finns i [kund hanterade nycklar för Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Formulär för begäran om Cognitive Services Customer-Managed nyckel](https://aka.ms/cogsvc-cmk)
