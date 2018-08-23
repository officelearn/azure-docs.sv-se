---
title: Hantera API-versionsprofiler i Azure Stack | Microsoft Docs
description: Läs mer om API-versionsprofiler i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: ba8778bad9ed1b8ec095da084ffcec79658bdea8
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055802"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Hantera API-versionsprofiler i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

API-profiler ange Azure-resursprovidern och API-version för Azure REST-slutpunkter. Du kan skapa anpassade klienter på olika språk med hjälp av API-profiler. Varje klient använder en API-profil för att kontakta rätt resursprovidern och API-version för Azure Stack.

Du kan skapa en app för att arbeta med Azure-resursprovidrar utan att behöva lösa exakt vilken version av varje resursprovider API är kompatibel med Azure Stack. Justera bara ditt program till en profil; SDK: N återställs till rätt API-version.

Det här avsnittet hjälper dig att:

 - Förstå profiler för API för Azure Stack.
 - Lär dig hur du kan använda API-profiler för att utveckla dina lösningar.
 - Se var du hittar koden tjänstspecifik vägledning.

## <a name="summary-of-api-profiles"></a>Sammanfattning av API-profiler

- API-profiler används för att representera en uppsättning Azure resursprovidrar och deras API-versioner.
- API-profiler har skapats för dig att skapa mallar över flera Azure-moln. Profiler är utformade för att uppfylla dina behov av ett kompatibelt och stabil gränssnitt.
- Profiler släpps fyra gånger per år.
- Tre profil namnkonventioner används:
    - **senaste**  
        Innehåller de senaste API-versioner som introducerades i globala Azure.
    - **åååå-mm-dd-hybrid**  
    Släppts på en vartannat år takt, fokuserar den här versionen på konsekvent och stabil i flera moln. Den här profilen riktar sig mot optimala Azure Stack-kompatibilitet.
    - **åååå-mm-dd-profil** varandra optimala stabilitet och de senaste funktionerna.

### <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure API-profiler och Azure Stack-kompatibilitet

De senaste Azure API-profilerna är inte kompatibla med Azure Stack. Du kan använda följande namngivningsregler för att identifiera vilka profiler som ska användas för Azure Stack-lösningar.

**senaste**  
Den här profilen har de senaste API-versioner som finns i globala Azure, vilket inte fungerar i Azure Stack. **Senaste** har det största antalet icke-bakåtkompatibla ändringar. Profilen har tagits ur bruk stabilitet och kompatibilitet med andra moln. Om du försöker att använda de senaste API-versionerna **senaste** är profilen som du ska använda.

**Åååå-mm-dd-hybrid**  
Den här profilen släpps i mars och September varje år. Den här profilen har optimala stabilitet och kompatibilitet med de olika moln. **Åååå-mm-dd-hybrid** har utformats för att ange globala Azure och Azure Stack som mål. Azure API-versioner som anges i den här profilen ska vara samma som de som visas på Azure Stack. Du kan använda den här profilen för att utveckla kod för hybridmolnlösningar.

**åååå-mm-dd-profil**  
Den här profilen släpps för global Azure i juni och December. Den här profilen fungerar inte mot Azure Stack; normalt kan finnas det många ändringar. Även om den sitter mellan optimala stabilitet och senaste funktionerna, skillnaden mellan **senaste** och den här profilen är att **senaste** alltid består av de senaste API-versionerna, oavsett när API: et släpptes. Till exempel om en ny API-version har skapats för beräknings-API i morgon, den API-versionen visas i den **senaste**, men inte i den **åååå-mm-dd-profil** eftersom det redan finns i den här profilen.  **åååå-mm-dd-profil** omfattar de senaste versionerna som släppts före juni eller innan December.

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager API-profiler

Azure Stack använder inte den senaste versionen av API-versioner som finns i globala Azure. När du skapar en lösning kan behöva du hitta API-versionen för varje Azure-resursprovidern som är kompatibel med Azure Stack.

Du kan i stället använda en API-profil än undersöka varje resursprovider och den specifika versionen som stöds av Azure Stack. Profilen anger en uppsättning resursprovidrar och API-versioner. SDK: N eller ett verktyg som skapats med SDK: N, återgår till mål-api-versionen anges i profilen. Med API-profiler, kan du ange en profil-version som gäller för en hel mall och vid körning, Azure Resource Manager väljer rätt version av resursen.

API-profiler fungerar med verktyg som använder Azure Resource Manager, som PowerShell, Azure CLI, koden i SDK och Microsoft Visual Studio. Verktyg och SDK: er kan använda profiler för att läsa vilken version av moduler och bibliotek att inkludera när du skapar ett program.

Exempel: om Använd PowerShell för att skapa ett storage-konto med hjälp av den **Microsoft.Storage** resursprovidern och som har stöd för api-versionen 2016-03-30 och en virtuell dator med Microsoft.Compute-resursprovidern med api-version 2015-12-01 , skulle du behöva leta upp vilken PowerShell-modulen stöder 2016-03-30 för lagring och vilken modul stöder 2015-02-01 för databearbetning och installera den. Du kan i stället använda en profil. Använd cmdleten ** Installera profil * profilename *** och PowerShell läser du in rätt version av moduler.

När du använder Python SDK för att skapa en Python-baserade program, på samma sätt kan du ange profilen. SDK: N läser in rätt moduler för de resursprovidrar som du har angett i skriptet.

Som utvecklare kan fokusera du på att din lösning. I stället för att undersöka vilka api-versioner, resursprovidern, och vilket moln fungerar tillsammans kan du använda en profil och vet att din kod fungerar i alla moln som har stöd för profilen.

## <a name="api-profile-code-samples"></a>Kodexempel för API-profil

Du kan hitta kodexempel för att integrera din lösning med det språk du föredrar med Azure Stack med hjälp av profiler. För närvarande kan hittar du anvisningar och exempel för följande språk:

- **PowerShell**  
Du kan använda den **AzureRM.Bootstrapper** modulen i PowerShell-galleriet för att hämta PowerShell-cmdlets som krävs för att arbeta med API-versionsprofiler. Mer information finns i [Använd API-versionsprofiler för PowerShell](azure-stack-version-profiles-powershell.md).
- **Azure CLI 2.0**  
Du kan uppdatera din miljökonfiguration om du vill använda Azure Stack-profil för versionen av specifika API: et. Mer information finns i [Använd API-versionsprofiler för Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
En profil är en kombination av olika resurstyper med olika versioner från olika tjänster i GO-SDK. profiler är tillgängliga under profilerna / sökväg med versionerna i den **åååå-MM-DD** format. Mer information finns i [Använd API-versionsprofiler för GO](azure-stack-version-profiles-go.md).
- **Ruby**  
Ruby SDK för Azure Stack Resource Manager innehåller verktyg som hjälper dig att skapa och hantera infrastrukturen. Resursprovidrar i SDK innehåller beräkning, virtuella nätverk och lagring med Ruby-språket. Mer information finns i [Använd API-versionsprofiler med Ruby](azure-stack-version-profiles-ruby.md)
- **Python**  
- Python SDK har stöd för API-versionsprofiler för att rikta olika plattformar, till exempel Azure Stack och globala Azure. Du kan använda API-profiler i att skapa lösningar för ett hybridmoln. Mer information finns i [Använd API-versionsprofiler med Python](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>Nästa steg

* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
* [Konfigurera PowerShell-miljö för Azure Stack-användare](azure-stack-powershell-configure-user.md)
* [Läs mer om resource provider API-versioner som stöds av profilerna](azure-stack-profiles-azure-resource-manager-versions.md).