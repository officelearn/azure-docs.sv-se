---
title: Hantera profiler för API-version i Azure-stacken | Microsoft Docs
description: Mer information om API-version-profiler i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 1ea65c9c1f69c8eec77eb498a5963b0d77ce57f1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Hantera profiler för API-version i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

API-profiler ange Azure-resursprovider och API-version för Azure REST-slutpunkter. Du kan skapa anpassade klienter på olika språk med hjälp av API-profiler. Varje klient använder en API-profil för att kontakta rätt resursprovidern och API-version för Azure-stacken. 

Du kan skapa en app att fungera med Azure-resursprovidrar utan att behöva lösa exakt vilken version av varje resursprovider API är kompatibel med Azure-stacken. Justera just ditt program till en profil; SDK återgår till rätt API-versioner.


Det här avsnittet hjälper dig att:
 - Förstå API-profiler för Azure-stacken.
 - Hur du kan använda API-profiler för att utveckla dina lösningar.
 - Var kod-specifik information.

## <a name="summary-of-api-profiles"></a>Översikt över API-profiler

- API profiler används för att representera en uppsättning Azure resursproviders och deras API-versioner.
- API-profiler har skapats för utvecklare att skapa mallar över flera Azure-moln. De har utformats för att uppfylla behovet av ett kompatibelt och stabil gränssnitt.
- Profiler släpps fyra gånger per år.
- Namnkonventioner för tre profil är:
    - **senaste**  
        Den senaste API-versioner släpps i Azure.
    - **åååå-mm-dd-hybrid**  
    Publicerat på vartannat år takt den här versionen fokuserar på konsekvent och stabil mellan flera moln. Den här profilen mål optimala Azure Stack-kompatibilitet. 
    - **åååå-mm-dd-profil**  
    Placeras mellan optimala stabilitet och de senaste funktionerna.

### <a name="api-profiles-and-azure-stack-compatibility"></a>API-profiler och Azure Stack-kompatibilitet

De senaste API-profilerna är inte kompatibla med Azure-stacken. Namnkonventionerna hjälper dig att identifiera profilerna som ska användas i Azure Stack-lösningar.

**senaste**  
Den här profilen är de senaste API-versioner som finns i globala Azure, som inte fungerar i Azure-stacken. Den här profilen har det största antalet viktiga förändringar. Profilen placerar reserverats stabilitet och kompatibilitet med andra moln. Om du försöker använda den senaste API-versioner är den profil som du ska använda.

**Åååå-mm-dd-hybrid**  
Den här profilen ges ut mars och September varje år. Den här profilen har optimala stabilitet och kompatibilitet med olika moln. Den här profilen är utformat för att rikta både globala Azure och Azure-stacken. Azure API-versioner som anges i den här profilen ska vara samma som de som visas på Azure-stacken. Du kan använda den här profilen för att utveckla kod för hybrid molnlösningar.

**åååå-mm-dd-profil**  
Den här profilen släpps för globala Azure i juni och December. Den här profilen fungerar inte mot Azure Stack; Det blir många viktiga förändringar. När den befinner sig bakom optimala stabilitet och senaste funktionerna är skillnaden mellan senaste och den här profilen att senaste alltid består av de senaste API-versionerna oavsett när API: et släpptes. Om en ny API-version har skapats för Compute-API i framtiden, visas i den senaste profilen att API-version, men inte i profilen åååå-mm-dd-profil som den här profilen har upprättats i förväg. Det täcker de senaste versionerna som getts ut före juni eller December.

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager API-profiler

Azure-stacken använder inte den senaste versionen av API-versioner som finns i globala Azure. Skapa din egen lösning som du behöver hitta API-versionen för varje resursprovider i Azure som är kompatibel med Azure-stacken.

Snarare än forskning varje resursprovider och den version som stöds av Azure-stacken, kan du använda en API-profil. Profilen anger en uppsättning resursproviders och API-versioner. SDK eller ett verktyg som byggts med SDK: N, återgår till mål-api-versionen som angavs i profilen. Med API-profiler, kan du ange en profil-version som är kopplad till en mall för hela och vid körning, Azure Resource Manager väljer rätt version av resursen.

API-profiler fungerar med verktyg som använder Azure Resource Manager, till exempel PowerShell, Azure CLI, koden i SDK och Microsoft Visual Studio. Verktyg och SDK: er kan använda profiler för att läsa vilken version av moduler och bibliotek med när du skapar ett program.

Om exempelvis Använd PowerShell för att skapa en storage-konto med hjälp av den **Microsoft.Storage** resursprovidern som har stöd för api-versionen 2016-03-30 och en virtuell dator med hjälp av Microsoft.Compute-resursprovidern med api-version 2015-12-01 , måste du leta upp som har stöd för PowerShell-modulen 2016-03-30 för lagring och vilken modul som stöder 2015-02-01 för beräkning och installera dem. Du kan i stället använda en profil. Använd cmdlet ** Installera profil * profilnamn *** och PowerShell läser du in rätt version av moduler.

På samma sätt kan du ange vilken profil när du använder Python SDK för att skapa ett program med Python. SDK läser in rätt moduler för resursleverantörer som du har angett i skriptet.

Som utvecklare kan fokusera du på att skriva din lösning. I stället för att undersöka vilka api-versioner, resursleverantör, och vilka molnappar som arbetar tillsammans, att du använder en profil och vet att din kod ska fungera över alla moln som har stöd för den här profilen.

## <a name="api-profile-code-samples"></a>Kodexempel för API-profil

Du kan hitta kodexempel som hjälper dig att integrera din lösning med ditt språk med Azure Stack med hjälp av profiler. För närvarande kan hittar du anvisningar och exempel för följande språk:

- **PowerShell**  
Du kan använda den **AzureRM.Bootstrapper** modul som är tillgängliga via PowerShell-galleriet för att hämta PowerShell-cmdlets som krävs för att arbeta med profiler för API-version. Mer information finns i [Använd API-version profiler för PowerShell](azure-stack-version-profiles-powershell.md).
- **Azure CLI 2.0**  
Du kan uppdatera din miljö konfiguration om du vill använda Azure Stack API-version profilen. Mer information finns i [Använd API-version profiler för Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GÅ**  
I gå-SDK är en profil en kombination av olika resurstyper med olika versioner från olika tjänster. profiler är tillgängliga under profilerna / sökväg med deras version i den **åååå-MM-DD** format. Mer information finns i [Använd API-version profiler för Gå](azure-stack-version-profiles-go.md).
- **Ruby**  
Ruby-SDK för Azure Stack Resource Manager innehåller verktyg som hjälper dig att skapa och hantera infrastrukturen. Resursproviders i SDK innehåller beräkning, virtuella nätverk och lagring med Ruby språk. Mer information finns i [Använd API-version profiler med Ruby](azure-stack-version-profiles-ruby.md)

## <a name="next-steps"></a>Nästa steg
* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
* [Konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md)
* [Granska informationen om resurs tjänstproviderns API-versioner som stöds av profilerna](azure-stack-profiles-azure-resource-manager-versions.md).
