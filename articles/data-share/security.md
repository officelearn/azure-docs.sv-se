---
title: Säkerhets översikt för Azure Data-resurs
description: Säkerhets översikt för Azure Data-resurs
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: e299f63777847c557fc13afb4d36d01c12b3b52a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637125"
---
# <a name="security-overview-for-azure-data-share"></a>Säkerhets översikt för Azure Data-resurs

Den här artikeln innehåller en säkerhets översikt över Azure Data Share-tjänsten.

## <a name="security-overview"></a>Säkerhetsöversikt

Azure Data Share utnyttjar den underliggande säkerhet som Azure erbjuder för att skydda data i vila och under överföring. Data krypteras i vila, där de stöds av det underliggande data lagret. Data krypteras också vid överföring. Metadata om en data resurs krypteras också i vila och under överföring. 

Åtkomst kontroller kan ställas in på resurs nivån på Azure-dataresursen för att säkerställa att de kan nås av de som är auktoriserade. 

Azure Data Share utnyttjar hanterad identitet (tidigare känt som MSI) för att komma åt data lager som används för data delning. Det finns inget utbyte av autentiseringsuppgifter mellan en data leverantör och en data konsument. Mer information om hanterad identitet finns i [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). Mer information om roller och behörigheter som krävs för att dela data finns i [roller och krav](concepts-roles-permissions.md).

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .




