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
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 28ff7c9f6ca5fc6365b3fe1b9a91d2159c8b3f48
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247620"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Hantera API-versionsprofiler i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

API-profiler ange Azure-resursprovidern och API-version för Azure REST-slutpunkter. Du kan skapa anpassade klienter på olika språk med hjälp av API-profiler. Varje klient använder en API-profil för att kontakta rätt resursprovidern och API-version för Azure Stack.

Du kan skapa en app för att arbeta med Azure-resursprovidrar utan att behöva lösa exakt vilken version av varje resursprovider API är kompatibel med Azure Stack. Justera bara ditt program till en profil; SDK: N återställs till rätt API-version.

Det här avsnittet hjälper dig att:

 - Förstå profiler för API för Azure Stack.
 - Lär dig hur du kan använda API-profiler för att utveckla dina lösningar.
 - Se var du hittar koden tjänstspecifik vägledning.

## <a name="summary-of-api-profiles"></a>Sammanfattning av API-profiler

- API-profiler används för att representera en uppsättning Azure resursprovidrar och deras API-versioner.
- API-profiler har skapats för dig att skapa mallar över flera Azure-moln. Profiler är en kompatibel och stabil gränssnitt.
- Profiler släpps fyra gånger per år.
- Tre profil namnkonventioner används:
    - **latest**  
        Innehåller de senaste API-versioner som introducerades i globala Azure.
    - **yyyy-mm-dd-hybrid**  
    Är två gånger per år, fokuserar den här versionen på konsekvent och stabil i flera moln. Den här profilen riktar sig mot optimala Azure Stack-kompatibilitet.
    - **åååå-mm-dd-profil** balanserar optimala stabilitet och de senaste funktionerna.

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure API-profiler och Azure Stack-kompatibilitet

De senaste Azure API-profilerna är inte kompatibla med Azure Stack. Du kan använda följande namngivningsregler för att identifiera vilka profiler som ska användas för Azure Stack-lösningar.

**senaste**  
Den här profilen har de senaste API-versioner finns i globala Azure, som inte fungerar i Azure Stack. **Senaste** har det största antalet icke-bakåtkompatibla ändringar. Profilen har tagits ur bruk stabilitet och kompatibilitet med andra moln. Om du vill använda de senaste API-versionerna **senaste** är profilen som du ska använda.

**Yyyy-mm-dd-hybrid**  
Den här profilen släpps i mars och September varje år. Den har optimala stabilitet och kompatibilitet med olika moln. **Åååå-mm-dd-hybrid** har utformats för att ange globala Azure och Azure Stack som mål. Azure API-versioner som anges i den här profilen ska vara samma som de som visas på Azure Stack. Du kan använda den här profilen för att utveckla kod för hybridmolnlösningar.

**yyyy-mm-dd-profile**  
Den här profilen släpps för global Azure i juni och December. Det fungerar inte med Azure Stack; normalt kan finnas det många ändringar. Även om det balanserar optimala stabilitet och de senaste funktionerna, skillnaden mellan **senaste** och den här profilen är att **senaste** alltid består av de senaste API-versionerna, oavsett när API: T var publicerat. Till exempel om en ny API-version har skapats för beräknings-API i morgon, den API-versionen visas i den **senaste**, men inte i den **åååå-mm-dd-profil** profilen eftersom det redan finns i den här profilen. **åååå-mm-dd-profil** omfattar de senaste versionerna som släppts före juni eller innan December.

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager API-profiler

Azure Stack använder inte den senaste versionen av API-versioner som finns i globala Azure. När du skapar en lösning måste du hitta API-versionen för varje Azure-resursprovidern som är kompatibel med Azure Stack.

Du kan i stället använda en API-profil än undersöka varje resursprovider och den specifika versionen som stöds av Azure Stack. Profilen anger en uppsättning resursprovidrar och API-versioner. SDK: N eller ett verktyg som skapats med SDK: N, återgår till målet `api-version` anges i profilen. Du kan ange en profil-version som gäller för en hel mall och vid körning med API-profiler, Azure Resource Manager väljer rätt version av resursen.

API-profiler fungerar med verktyg som använder Azure Resource Manager, som PowerShell, Azure CLI, koden i SDK och Microsoft Visual Studio. Verktyg och SDK: er kan använda profiler för att läsa vilken version av moduler och bibliotek att inkludera när du skapar ett program.

Exempel: Om du använder PowerShell för att skapa ett storage-konto med den **Microsoft.Storage** resursprovidern som stöder **api-versionen** 2016-03-30 och en virtuell dator med den  **Microsoft.Compute** -resursprovidern med **api-versionen** 2015-12-01, du måste leta upp vilken PowerShell-modulen stöder 2016-03-30 för lagring och vilka modulen stöder 2015-02-01 för beräkning, och sedan installera dem. Du kan i stället använda en profil. Använd cmdleten `Install-Profile <profilename>`, och PowerShell läser in rätt version av moduler.

När du använder Python SDK för att skapa en Python-baserade program, på samma sätt kan du ange profilen. SDK: N läser in rätt moduler för de resursprovidrar som du har angett i skriptet.

Som utvecklare kan fokusera du på att din lösning. I stället för att undersöka vilka API-versioner, resursprovidern och molnet fungerar tillsammans, kan du använda en profil och vet att din kod fungerar i alla moln som har stöd för profilen.

## <a name="api-profile-code-samples"></a>Kodexempel för API-profil

Du kan hitta kodexempel för att integrera din lösning med det språk du föredrar med Azure Stack med hjälp av profiler. För närvarande kan hittar du anvisningar och exempel för följande språk:

- **.NET** du kan använda .NET API-profil för att få den senaste och mest stabila versionen av varje resurstyp i ett paket för resource provider. Mer information finns i [Använd API-versionsprofiler med .NET i Azure Stack](azure-stack-version-profiles-net.md).
- **PowerShell**  
Du kan använda den **AzureRM.Bootstrapper** modulen i PowerShell-galleriet för att hämta PowerShell-cmdlets som krävs för att arbeta med API-versionsprofiler. Mer information finns i [Använd API-versionsprofiler för PowerShell](azure-stack-version-profiles-powershell.md).
- **Azure CLI**  
Du kan uppdatera din miljökonfiguration om du vill använda Azure Stack-profil för versionen av specifika API: et. Mer information finns i [Använd API-versionsprofiler för Azure CLI](azure-stack-version-profiles-azurecli2.md).
- **Go**  
En profil är en kombination av olika resurstyper med olika versioner från olika tjänster i Go-SDK. Profiler är tillgängliga under profiler/sökväg, med versionerna i den **åååå-MM-DD** format. Mer information finns i [Använd API-versionsprofiler för Go](azure-stack-version-profiles-go.md).
- **Ruby**  
Ruby SDK för Azure Stack Resource Manager innehåller verktyg som hjälper dig att skapa och hantera infrastrukturen. Resursprovidrar i SDK innehåller beräkning, virtuella nätverk och lagring med Ruby-språket. Mer information finns i [Använd API-versionsprofiler med Ruby](azure-stack-version-profiles-ruby.md)
- **Python**  
Python SDK har stöd för API-versionsprofiler för att rikta olika plattformar, till exempel Azure Stack och globala Azure. Du kan använda API-profiler för att skapa lösningar för ett hybridmoln. Mer information finns i [Använd API-versionsprofiler med Python](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>Nästa steg

* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
* [Konfigurera PowerShell-miljö för Azure Stack-användare](azure-stack-powershell-configure-user.md)
* [Läs mer om resource provider API-versioner som stöds av profilerna](azure-stack-profiles-azure-resource-manager-versions.md).
