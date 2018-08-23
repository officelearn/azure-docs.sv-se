---
title: Konfigurera namngivna autentiseringsuppgifter | Microsoft Docs
description: Lär dig mer om att ange autentiseringsuppgifter som Visual Studio kan använda för att autentisera förfrågningar till Azure, så att du kan publicera ett program till Azure från Visual Studio eller övervaka en befintlig molntjänst.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 61570907-42a1-40e8-bcd6-952b21a55786
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 48c410df1768c2cae7807aa1538d76867a581c71
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055888"
---
# <a name="set-up-named-authentication-credentials"></a>Konfigurera namngivna autentiseringsuppgifter

Publicera ett program till Azure eller för att övervaka en befintlig molntjänst, kräver Visual Studio autentiseringsuppgifter för att autentisera förfrågningar till Azure, nämligen Azure prenumerations-ID och ett giltigt X.509 v3-certifikat med en nyckel på minst 2 048 bitar. Du kan ange dessa autentiseringsuppgifter genom någon av följande metoder:

- I Visual Studio väljer **Visa > Server Explorer**, högerklicka på den **Azure** noden **Anslut till Microsoft Azure-prenumeration**, och logga in.
- Skapa en prenumeration-fil (`.publishsettings`), som innehåller en offentlig nyckel för certifikatet. Prenumerationsfilen kan innehålla autentiseringsuppgifter för mer än en prenumeration enligt beskrivningen i den här artikeln.

Obs: de här autentiseringsuppgifterna skiljer sig från autentiseringsuppgifterna som används för att autentisera förfrågningar till Azure storage-tjänster.

## <a name="create-a-subscription-file"></a>Skapa en prenumeration-fil

I Server Explorer högerklickar du på den **Azure** noden och väljer **hantera och filtrera prenumerationer**. Välj sedan den **certifikat** fliken och gör sedan något av följande åtgärder:

- Välj **Import** att öppna den **Importera Microsoft Azure-prenumerationer** dialogrutan. Välj den **Download prenumerationsfilen** länka och spara den nedladdade filen till en tillfällig plats i webbläsaren. Bläddra till nedladdningsplatsen tillbaka i dialogrutan, och sedan importera den för användning i autentisering.
- Välj en aktiv prenumeration och välj **redigera**, som du öppnar en dialogruta där du kan redigera en befintlig prenumeration för användning i autentisering.
- Välj **New** att öppna den **ny prenumeration** dialogrutan rutan och ange informationen som krävs. För att överföra certifikatet till en molnbaserad tjänst anges i dialogrutan Logga in på Azure-portalen, navigera till din molntjänst, Välj **Inställningar > Hanteringscertifikat**väljer **överför**, sedan Ange sökvägen till den `.cer` filen.

Om du vill skapa ett certifikat själv kan du referera till anvisningarna i [skapa och ladda upp ett certifikat för Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) och överför sedan certifikatet till manuellt i [Azure-portalen](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

- [Allmän översikt över Web Apps](https://docs.microsoft.com/azure/app-service/)
- [Distribuera din app till Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-deploy-local-git) 
- [Distribuera WebJobs med hjälp av Visual Studio](https://docs.microsoft.com/azure/app-service/websites-dotnet-deploy-webjobs)
- [Skapa och distribuera en molntjänst](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)