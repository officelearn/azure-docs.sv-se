---
title: Säkerhetsöversikt över Azure Data Share
description: Säkerhetsöversikt över Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 1fdf026e9271ef6eb30c2b4ca96a04880b65be75
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578101"
---
# <a name="security-overview-for-azure-data-share"></a>Säkerhetsöversikt över Azure Data Share

Den här artikeln innehåller en säkerhets översikt över Azure Data Share-tjänsten.

## <a name="security-overview"></a>Säkerhetsöversikt

Azure Data Share utnyttjar den underliggande säkerhet som Azure erbjuder för att skydda data i vila och under överföring. Data krypteras i vila, där de stöds av det underliggande data lagret. Data krypteras också i överföring med TLS 1,2. Metadata om en data resurs krypteras också i vila och under överföring. Azure Data Share lagrar inte innehållet i de kund uppgifter som delas.

Azure Data Share utnyttjar hanterad identitet (tidigare känt som MSI) för att komma åt data lager som används för data delning. Det finns inget utbyte av autentiseringsuppgifter mellan en data leverantör och en data konsument. Mer information om hanterad identitet finns i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Mer information om roller och behörigheter som krävs för att dela data finns i [roller och krav](concepts-roles-permissions.md).

Åtkomst kontroller till Azure Data Resource kan ställas in på resurs nivån data resurs för att se till att den används av de som är auktoriserade. 

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Dela data från eller till data lager med brand vägg aktive rad
Om du vill dela data från eller till lagrings konton med brand vägg aktive rad måste du aktivera **Tillåt betrodda Microsoft-tjänster** i ditt lagrings konto. Mer information finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) .


## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .
