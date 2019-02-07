---
title: Vanliga frågor (och svar FAQ) om FHIR tjänster i Azure - Azure-API för FHIR
description: Den här artikeln för vanliga frågor och svar får du svar på vanliga frågor och svar om Azure API för FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 7403a23e236c14d77672d5b80d953b1e11088f8a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824136"
---
# <a name="frequently-asked-questions-about-azure-api-for-fhir"></a>Vanliga frågor och svar om Azure API för FHIR

## <a name="storage-location"></a>Lagringsplats

**Är data bakom FHIR&reg; API: er lagras i Azure?** Ja, lagras data i hanterade databaser i Azure. Azure API för FHIR ger inte direkt åtkomst till det underliggande datalagringen.

## <a name="identity-providers"></a>Identitetsprovidrar

Vi stöder för närvarande Microsoft Azure Active Directory som identitetsleverantör.

## <a name="supported-fhir-version"></a>FHIR-version som stöds

Vi stöder för närvarande version 3.0.1. Se [funktioner som stöds](fhir-features-supported.md) mer information.

## <a name="oss-and-azure-api-for-fhir"></a>OS- och Azure API för FHIR

Vad är skillnaden mellan FHIR med öppen källkod-Microsoft-server för Azure och Azure API för FHIR? Azure API för FHIR är en värd och hantering version av OSS Microsoft FHIR Server för Azure. I hanterade tjänsten tillhandahåller Microsoft alla underhåll, uppdateringar osv. När du kör OSS FHIR Server för Azure, du har direkt åtkomst till de underliggande tjänsterna, men du ansvarar också för att underhålla, uppdatera servern och alla nödvändiga efterlevnad arbete om PHI datalagring.

## <a name="next-steps"></a>Nästa steg

Du har läst några vanliga frågor om Azure API för FHIR i den här artikeln. Läs mer om de API-funktionerna som stöds i Microsoft FHIR server för Azure.
 
>[!div class="nextstepaction"]
>[FHIR-funktioner som stöds](fhir-features-supported.md)