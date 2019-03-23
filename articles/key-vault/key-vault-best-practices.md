---
title: Bästa praxis att använda Key Vault - Azure Key Vault | Microsoft Docs
description: Det här dokumentet beskriver några av metodtipsen för att använda Key Vault
services: key-vault
documentationcenter: ''
author: yvprashanth
manager: barbkess
tags: azure-key-vault
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: prashanthyv
ms.openlocfilehash: ee4418700cec5de1dc404c3669dd5de315aab983
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368618"
---
# <a name="best-practices-to-use-key-vault"></a>Bästa praxis att använda Key Vault

## <a name="control-access-to-your-vault"></a>Kontrollera åtkomsten till ditt valv

Azure Key Vault är en molnbaserad tjänst som skyddar krypteringsnycklar och hemligheter som certifikat, anslutningssträngar och lösenord. Eftersom dessa data är känsliga och verksamhetskritiska, som du behöver för att skydda åtkomst till dina nyckelvalv genom att tillåta att endast auktoriserade program och användare. Detta [artikeln](key-vault-secure-your-key-vault.md) innehåller en översikt över åtkomstmodellen för Nyckelvalvet. Det förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina nyckelvalv.

Förslag när du styr åtkomst till ditt valv är följande:
1. Låsa åtkomsten till din prenumeration, resursgrupp och valv nyckel (RBAC)
2. Skapa principer för åtkomst för varje valv
3. Använda minsta privilegium åtkomst huvudnamn för att bevilja åtkomst
4. Aktivera brandväggen och [tjänstslutpunkter för virtuellt nätverk](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Använda separata Key Vault

Vår rekommendation är att använda ett valv per program per miljö (utveckling, Förproduktion och produktion). Det ger dig inte dela hemligheter mellan miljöer och minskar också hotet om ett intrång.

## <a name="backup"></a>Backup

Kontrollera att du regelbundet ta tillbaka in av din [vault](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) update/delete/skapar för objekt inom ett valv.

## <a name="turn-on-logging"></a>Aktivera loggning

[Aktivera loggning](key-vault-logging.md) för ditt valv. Också ställa in aviseringar.

## <a name="turn-on-recovery-options"></a>Aktivera återställningsalternativ

1. Aktivera [mjuk borttagning](key-vault-ovw-soft-delete.md).
2. Aktivera Rensa skydd om du vill skydda mot Framtvinga borttagning av hemligheten / valv även när mjuk borttagning har aktiverats.
