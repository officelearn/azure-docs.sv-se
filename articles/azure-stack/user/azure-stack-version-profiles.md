---
title: Hantera profiler för API-version i Azure-stacken | Microsoft Docs
description: Mer information om API-version-profiler i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 8A336052-8520-41D2-AF6F-0CCE23F727B4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 452ed1de0588b380747edaa44dd0cc3805c51392
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
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
- API-profiler har skapats för utvecklare att skapa mallar över flera Azure-moln. De har utformats för att uppfylla behovet av en kompatibel och stabil gränssnitt.
- Profiler släpps fyra gånger per år.
- Namnkonventioner för tre profil är:
    - **latest**  
        Den senaste API-versioner släpps i Azure.
    - **yyyy-mm-dd-hybrid**  
    Publicerat på vartannat år takt den här versionen fokuserar på konsekvent och stabil mellan flera moln.
    - **yyyy-mm-dd-profile**  
    Placeras mellan optimala stabilitet och de senaste funktionerna.

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
Du kan använda den **AzureRM.Bootstrapper** modul som är tillgängliga via PowerShell-galleriet för att hämta PowerShell-cmdlets som krävs för att arbeta med profiler för API-version.  
Mer information finns i [Använd API-version profiler för PowerShell](azure-stack-version-profiles-powershell.md).
- **Azure CLI 2.0**  
Du kan uppdatera din miljö konfiguration om du vill använda Azure Stack API-version profilen.  
Mer information finns i [Använd API-version profiler för Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
I gå-SDK är en profil en kombination av olika resurstyper med olika versioner från olika tjänster. profiler är tillgängliga under profilerna / sökväg med deras version i den **åååå-MM-DD** format.  
Mer information finns i [Använd API-version profiler för Gå](azure-stack-version-profiles-go.md).

## <a name="next-steps"></a>Nästa steg
* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
* [Konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md)
* [Granska informationen om resurs tjänstproviderns API-versioner som stöds av profilerna](azure-stack-profiles-azure-resource-manager-versions.md).
