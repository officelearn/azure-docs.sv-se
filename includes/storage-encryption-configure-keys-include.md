---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74895264"
---
Azure Storage krypterar alla data i ett lagringskonto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypteringsnycklar kan du ange kundhanterade nycklar som ska användas för kryptering av blob- och fildata.

Kundhanterade nycklar måste lagras i ett Azure Key Vault. Du kan antingen skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault API:er för att generera nycklar. Lagringskontot och nyckelvalvet måste finnas i samma region, men de kan finnas i olika prenumerationer. Mer information om Azure Storage-kryptering och nyckelhantering finns i [Azure Storage-kryptering för data i vila](../articles/storage/common/storage-service-encryption.md). Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
