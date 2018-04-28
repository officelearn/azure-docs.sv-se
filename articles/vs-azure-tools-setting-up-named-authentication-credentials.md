---
title: Ställ in autentiseringsuppgifter för namngiven | Microsoft Docs
description: Lär dig mer om att ange autentiseringsuppgifter som Visual Studio kan använda för att autentisera förfrågningar till Azure, så att du kan publicera ett program till Azure från Visual Studio eller övervaka en befintlig molntjänst.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 61570907-42a1-40e8-bcd6-952b21a55786
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 3dce80f5c6611cb2a22293d1a574980db5ea8378
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="set-up-named-authentication-credentials"></a>Ställ in autentiseringsuppgifter för namngiven

Publicera ett program till Azure eller för att övervaka en befintlig molntjänst kräver Visual Studio autentiseringsuppgifter för att autentisera förfrågningar till Azure, nämligen Azure prenumerations-ID och ett giltigt X.509 v3-certifikat med en nyckel på minst 2 048 bitar. Du anger dessa autentiseringsuppgifter genom någon av följande metoder:

- I Visual Studio väljer **Visa > Server Explorer**, högerklicka på den **Azure** väljer **Anslut till Microsoft Azure-prenumeration**, och logga in.
- Skapa en prenumeration-fil (`.publishsettings`), som innehåller en offentlig nyckel för certifikatet. Prenumerationsfilen kan innehålla referenser för mer än en prenumeration som beskrivs i den här artikeln.

Obs: de här autentiseringsuppgifterna skiljer sig från autentiseringsuppgifter som används för att autentisera förfrågningar till Azure storage-tjänster.

## <a name="create-a-subscription-file"></a>Skapa en prenumeration-fil

I Server Explorer högerklickar du på den **Azure** och välj **hantera och Filter prenumerationer**. Välj sedan den **certifikat** fliken och gör sedan något av följande åtgärder:

- Välj **importera** att öppna den **Importera Microsoft Azure-prenumerationer** dialogrutan. Välj den **prenumerationsfilen** länka och spara den hämta filen till en tillfällig plats i webbläsaren. Bläddra till nedladdningsplatsen tillbaka i dialogrutan och sedan importera den för användning vid autentisering.
- Välj en aktiv prenumeration och välj **redigera**, som öppnar en dialogruta där du kan redigera en befintlig prenumeration för autentisering.
- Välj **ny** att öppna den **ny prenumeration** dialogrutan rutan och ange nödvändig information. För att överföra certifikatet till ditt moln service anges i dialogrutan Logga in på Azure portal, navigerar till Molntjänsten, Välj **Inställningar > Hanteringscertifikat**väljer **överför**, sedan Ange sökvägen till den `.cer` filen.

Om du vill skapa ett certifikat själv kan du referera till instruktionerna i [skapa och ladda upp ett certifikat för Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) och sedan ladda upp certifikatet till manuellt i [Azure-portalen](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

- [Allmän översikt över Web Apps](https://docs.microsoft.com/azure/app-service/)
- [Distribuera din app till Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-deploy-local-git) 
- [Distribuera WebJobs med hjälp av Visual Studio](https://docs.microsoft.com/azure/app-service/websites-dotnet-deploy-webjobs)
- [Skapa och distribuera en tjänst i molnet](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)