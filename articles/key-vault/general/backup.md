---
title: Azure Key Vault säkerhets kopiering | Microsoft Docs
description: Använd det här dokumentet för att säkerhetskopiera en hemlighet, nyckel eller ett certifikat som lagras i Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147832"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault säkerhets kopiering

I det här dokumentet visas hur du säkerhetskopierar enskilda hemligheter, nycklar och certifikat som lagras i ditt nyckel valv. Den här säkerhets kopian är avsedd att ge dig en offlinekopia av alla dina hemligheter i förmodad händelse att du förlorar åtkomst till ditt nyckel valv.

## <a name="overview"></a>Översikt

Key Vault tillhandahåller automatiskt flera funktioner för att upprätthålla tillgänglighet och förhindra data förlust. Den här säkerhets kopian bör endast göras om det finns en viktig affärs motivering för att upprätthålla en säkerhets kopia av dina hemligheter. Säkerhets kopiering av hemligheter i nyckel valvet kan medföra ytterligare drifts svårigheter, till exempel att underhålla flera uppsättningar av loggar, behörigheter och säkerhets kopior när hemligheterna upphör att gälla eller roteras.

Key Vault behåller tillgänglighet i katastrof scenarier och växlar automatiskt över begär anden till en kopplad region utan att någon åtgärd krävs från en användare. Mer information finns i följande länk. [Azure Key Vault haveri beredskap](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

Key Vault skyddar mot oavsiktlig och skadlig borttagning av dina hemligheter genom mjuk borttagning och rensning av skydd. Om du vill skydda mot oavsiktlig eller skadlig borttagning av dina hemligheter konfigurerar du funktionerna för mjuk borttagning och rensning av skydd på nyckel valvet. Mer information finns i följande dokument. [Azure Key Vault återställning](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>Begränsningar

Azure Key Vault stöder för närvarande inte ett sätt att säkerhetskopiera hela nyckel valvet i en enda åtgärd. Alla försök att använda de kommandon som anges i det här dokumentet för att utföra en automatisk säkerhets kopiering av ett nyckel valv stöds inte av Microsoft eller Azure Key Vault-teamet.

Försök att använda de kommandon som visas i dokumentet nedan för att skapa en anpassad Automation kan resultera i fel.

* Att säkerhetskopiera hemligheter med flera versioner kan orsaka timeout-fel.
* Säkerhets kopiering skapar en tidpunkts ögonblicks bild. Hemligheter kan förnyas under en säkerhets kopiering som orsakar en matchning av krypterings nycklar.
* Om du överskrider begränsningarna för Key Vault-tjänsten för begär Anden per sekund så kommer ditt nyckel valv att begränsas och leda till att säkerhets kopieringen Miss kopie ras.

## <a name="design-considerations"></a>Designöverväganden

När du säkerhetskopierar ett objekt som lagras i Key Vault (hemlighet, nyckel eller certifikat) hämtar säkerhets kopierings åtgärden objektet som en krypterad blob. Denna BLOB kan inte dekrypteras utanför Azure. Om du vill hämta användbara data från denna BLOB måste du återställa blobben till ett nyckel valv i samma Azure-prenumeration och Azure geografi.
[Azure-geografiska områden](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Krav

* Deltagar nivå eller högre behörigheter för en Azure-prenumeration
* Ett primärt nyckel valv som innehåller hemligheter som du vill säkerhetskopiera
* Ett sekundärt nyckel valv där hemligheter kommer att återställas.

## <a name="back-up-and-restore-with-azure-portal"></a>Säkerhetskopiera och återställa med Azure Portal

### <a name="back-up"></a>Säkerhetskopiera

1. Gå till Azure-portalen.
2. Välj ditt nyckel valv.
3. Navigera till objektet (hemligt, nyckel eller certifikat) som du vill säkerhetskopiera.

    ![Bild](../media/backup-1.png)

4. Markera objektet.
5. Välj Hämta säkerhets kopia

    ![Bild](../media/backup-2.png)
    
6. Klicka på knappen Ladda ned.

    ![Bild](../media/backup-3.png)
    
7. Lagra den krypterade blobben på en säker plats.

### <a name="restore"></a>Återställ

1. Gå till Azure-portalen.
2. Välj ditt nyckel valv.
3. Navigera till den typ av objekt (hemlighet, nyckel eller certifikat) som du vill återställa.
4. Välj Återställ säkerhets kopia

    ![Bild](../media/backup-4.png)
    
5. Bläddra till den plats där du sparade den krypterade blobben.
6. Välj OK.

## <a name="back-up-and-restore-with-the-azure-cli"></a>Säkerhetskopiera och återställa med Azure CLI

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Nästa steg

Aktivera loggning och övervakning för din Azure Key Vault. [Azure Key Vault loggning](https://docs.microsoft.com/azure/key-vault/general/logging)
