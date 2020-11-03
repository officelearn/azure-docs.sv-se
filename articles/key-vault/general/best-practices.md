---
title: Metod tips för att använda Key Vault-Azure Key Vault | Microsoft Docs
description: Lär dig mer om metod tips för Azure Key Vault, inklusive kontroll av åtkomst, när du ska använda separata nyckel valv, säkerhetskopiera, logga och återställnings alternativ.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4a522e58677e2c71cb52ad7e04b288622ad62d91
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287339"
---
# <a name="best-practices-to-use-key-vault"></a>Metod tips för att använda Key Vault

## <a name="control-access-to-your-vault"></a>Kontrol lera åtkomsten till ditt valv

Azure Key Vault är en moln tjänst som skyddar krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord. Eftersom dessa data är känsliga och affärs kritiska måste du skydda åtkomsten till dina nyckel valv genom att bara tillåta behöriga program och användare. Den här [artikeln](secure-your-key-vault.md) ger en översikt över Key Vault åtkomst modell. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina nyckel valv.

Förslag när du styr åtkomsten till ditt valv är följande:
1. Lås åtkomst till din prenumeration, resurs grupp och nyckel valv (RBAC)
2. Skapa åtkomst principer för varje valv
3. Använd minsta behörighets åtkomst objekt för att bevilja åtkomst
4. Aktivera brand väggar och [VNet-tjänstens slut punkter](overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Använd separata Key Vault

Vår rekommendation är att använda ett valv per program per miljö (utveckling, för produktion och produktion). Detta hjälper dig att inte dela hemligheter mellan miljöer och minskar också risken för intrång.

## <a name="backup"></a>Backup

Se till att du utför regelbundna säkerhets kopieringar av ditt valv när du uppdaterar/tar bort/skapar objekt i ett valv.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell säkerhets kopierings kommandon

* [Säkerhetskopiera certifikat](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate?view=azurermps-6.13.0)
* [Säkerhets kopierings nyckel](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey?view=azurermps-6.13.0)
* [Säkerhets kopierings hemlighet](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret?view=azurermps-6.13.0)

### <a name="azure-cli-backup-commands"></a>Kommandon för Azure CLI-säkerhetskopiering

* [Säkerhetskopiera certifikat](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-backup)
* [Säkerhets kopierings nyckel](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-backup)
* [Säkerhets kopierings hemlighet](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Aktivera loggning

[Aktivera loggning](logging.md) för ditt valv. Konfigurera även aviseringar.

## <a name="turn-on-recovery-options"></a>Aktivera återställnings alternativ

1. Aktivera [mjuk borttagning](soft-delete-overview.md).
2. Aktivera rensnings skyddet om du vill skydda mot Force borttagning av hemligheten/valvet även efter att mjuk borttagning har Aktiver ATS.