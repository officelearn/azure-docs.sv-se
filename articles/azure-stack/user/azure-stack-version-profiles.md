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
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: adbe88a44ac38868a68a6845c328ef4cf7fba60c
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604445"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Hantera profiler för API-version i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

API-profiler ange Azure-resursprovider och API-version för Azure REST-slutpunkter. Du kan skapa anpassade klienter på olika språk med hjälp av API-profiler. Varje klient använder en API-profil för att kontakta rätt resurs-providern och API-versionen för Azure-stacken.

Du kan skapa en app att fungera med Azure-resursprovidrar utan att behöva lösa exakt vilken version av varje resursprovider API är kompatibel med Azure-stacken. Justera just ditt program till en profil; SDK återgår till rätt API-version.

Det här avsnittet hjälper dig att:

 - Förstå API-profiler för Azure-stacken.
 - Lär dig hur du kan använda API-profiler för att utveckla dina lösningar.
 - Se var du hittar koden-specifik information.

## <a name="summary-of-api-profiles"></a>Översikt över API-profiler

- API profiler används för att representera en uppsättning Azure resursproviders och deras API-versioner.
- API-profiler har skapats att skapa mallar över flera Azure-moln. Profiler är utformade för att uppfylla behovet av ett kompatibelt och stabil gränssnitt.
- Profiler släpps fyra gånger per år.
- Tre profil konventioner används:
    - **senaste**  
        Innehåller de senaste API-versioner som getts ut som global Azure.
    - **Åååå-mm-dd-hybrid**  
    Publicerat på vartannat år takt fokuserar den här versionen på konsekvent och stabil mellan flera moln. Den här profilen mål optimala Azure Stack-kompatibilitet.
    - **åååå-mm-dd-profil** placeras mellan optimala stabilitet och de senaste funktionerna.

### <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure-stacken kompatibilitet och Azure API-profiler

De senaste Azure API-profilerna är inte kompatibla med Azure-stacken. Du kan använda följande namngivningsregler för att identifiera vilka profiler för dina Azure Stack-lösningar.

**senaste**  
Den här profilen har de senaste API-versioner som finns i globala Azure, vilket inte fungerar i Azure-stacken. **Senaste** har det största antalet viktiga förändringar. Profilen placerar reserverats stabilitet och kompatibilitet med andra moln. Om du försöker använda den senaste API-versioner **senaste** är profilen du ska använda.

**Åååå-mm-dd-hybrid**  
Den här profilen släpps i mars och September varje år. Den här profilen har optimala stabilitet och kompatibilitet med olika moln. **Åååå-mm-dd-hybrid** är utformat för att rikta globala Azure och Azure-stacken. Azure API-versioner som anges i den här profilen ska vara samma som de som visas på Azure-stacken. Du kan använda den här profilen för att utveckla kod för hybrid molnlösningar.

**åååå-mm-dd-profil**  
Den här profilen släpps för globala Azure i juni och December. Den här profilen fungerar inte mot Azure Stack; vanligtvis finns många viktiga förändringar. Även om den befinner sig mellan optimala stabilitet och senaste funktioner, skillnaden mellan **senaste** och den här profilen är att **senaste** alltid består av de senaste API-versionerna, oavsett när API: et släpptes. Till exempel om en ny API-version skapas för Compute-API i framtiden kan den API-versionen visas i den **senaste**, men inte i den **åååå-mm-dd-profil** eftersom det redan finns i den här profilen.  **åååå-mm-dd-profil** omfattar de senaste versionerna som publicerades före juni eller före December.

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager API-profiler

Azure-stacken använder inte den senaste versionen av API-versioner som finns i globala Azure. När du skapar en lösning kan behöva du hitta API-versionen för varje leverantör i Azure-resurs som är kompatibel med Azure-stacken.

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
- **Python**  
- Python SDK stöder profiler för API-version att målplattformar annat moln, till exempel Azure-stacken och global Azure. Du kan använda API-profiler för att skapa lösningar för ett hybridmoln. Mer information finns i [Använd API-version profiler med Python](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>Nästa steg

* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
* [Konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md)
* [Granska informationen om resurs tjänstproviderns API-versioner som stöds av profilerna](azure-stack-profiles-azure-resource-manager-versions.md).