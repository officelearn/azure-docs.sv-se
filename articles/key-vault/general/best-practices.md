---
title: Metodtips för att använda Key Vault - Azure Key Vault | Microsoft-dokument
description: I det här dokumentet beskrivs några av de bästa metoderna för att använda Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 57bb897978f47a66adebac069d8892d596ba78f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430869"
---
# <a name="best-practices-to-use-key-vault"></a>Metodtips för att använda Key Vault

## <a name="control-access-to-your-vault"></a>Kontrollera åtkomsten till ditt valv

Azure Key Vault är en molntjänst som skyddar krypteringsnycklar och hemligheter som certifikat, anslutningssträngar och lösenord. Eftersom dessa data är känsliga och affärskritiska måste du skydda åtkomsten till dina nyckelvalv genom att endast tillåta auktoriserade program och användare. Den här [artikeln](secure-your-key-vault.md)) innehåller en översikt över åtkomstmodellen för Key Vault. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina nyckelvalv.

Förslag när du kontrollerar åtkomsten till valvet är följande:
1. Lås åtkomsten till din prenumeration, resursgrupp och nyckelvalv (RBAC)
2. Skapa åtkomstprinciper för varje valv
3. Använd huvudobjekt för minsta behörighetsåtkomst för att bevilja åtkomst
4. Aktivera slutpunkter för brandvägg och [VNET-tjänst](overview-vnet-service-endpoints.md))

## <a name="use-separate-key-vault"></a>Använda separat Nyckelvalv

Vår rekommendation är att använda ett valv per applikation per miljö (utveckling, förproduktion och produktion). Detta hjälper dig att inte dela hemligheter mellan miljöer och även minskar hotet i händelse av ett brott.

## <a name="backup"></a>Backup

Se till att du tar regelbundna säkerhetskopierare av [ditt valv](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) på uppdatering / ta bort / skapa objekt i ett valv.

## <a name="turn-on-logging"></a>Aktivera loggning

[Aktivera loggning](logging.md)) för ditt valv. Ställ också in aviseringar.

## <a name="turn-on-recovery-options"></a>Aktivera återställningsalternativ

1. Aktivera [Mjuk borttagning](overview-soft-delete.md)).
2. Aktivera rensningsskydd om du vill skydda dig mot borttagning av hemliga/valv även efter att mjuk borttagning har aktiverats.
