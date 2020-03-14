---
title: Metod tips för att använda Key Vault-Azure Key Vault | Microsoft Docs
description: I det här dokumentet beskrivs några av de rekommenderade metoderna för att använda Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cb4bec8170466f0fc667b592d44b0858c41ccd84
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270997"
---
# <a name="best-practices-to-use-key-vault"></a>Metod tips för att använda Key Vault

## <a name="control-access-to-your-vault"></a>Kontrol lera åtkomsten till ditt valv

Azure Key Vault är en moln tjänst som skyddar krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord. Eftersom dessa data är känsliga och affärs kritiska måste du skydda åtkomsten till dina nyckel valv genom att bara tillåta behöriga program och användare. Den här [artikeln](key-vault-secure-your-key-vault.md) ger en översikt över Key Vault åtkomst modell. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina nyckel valv.

Förslag när du styr åtkomsten till ditt valv är följande:
1. Lås åtkomst till din prenumeration, resurs grupp och nyckel valv (RBAC)
2. Skapa åtkomst principer för varje valv
3. Använd minsta behörighets åtkomst objekt för att bevilja åtkomst
4. Aktivera brand väggar och [VNet-tjänstens slut punkter](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Använd separata Key Vault

Vår rekommendation är att använda ett valv per program per miljö (utveckling, för produktion och produktion). Detta hjälper dig att inte dela hemligheter mellan miljöer och minskar också risken för intrång.

## <a name="backup"></a>Säkerhetskopiering

Se till att du utför regelbundna säkerhets kopieringar av ditt [valv](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) när du uppdaterar/tar bort/skapar objekt i ett valv.

## <a name="turn-on-logging"></a>Aktivera loggning

[Aktivera loggning](key-vault-logging.md) för ditt valv. Konfigurera även aviseringar.

## <a name="turn-on-recovery-options"></a>Aktivera återställnings alternativ

1. Aktivera [mjuk borttagning](key-vault-ovw-soft-delete.md).
2. Aktivera rensnings skyddet om du vill skydda mot Force borttagning av hemligheten/valvet även efter att mjuk borttagning har Aktiver ATS.
