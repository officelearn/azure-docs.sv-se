---
title: "Ställ in autentiseringsuppgifter för namngiven | Microsoft Docs"
description: "Lär dig mer om att ange autentiseringsuppgifter som Visual Studio kan använda för att autentisera förfrågningar till Azure, så att du kan publicera ett program till Azure från Visual Studio eller övervaka en befintlig molntjänst."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/22/2017
ms.author: kraigb
ms.openlocfilehash: 953b1aa459ddf5b7be00b9d32432e6dda97143e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-named-authentication-credentials"></a>Ställ in autentiseringsuppgifter för namngiven
Publicera ett program till Azure från Visual Studio eller för att övervaka en befintlig molntjänst måste du ange autentiseringsuppgifter som Visual Studio kan använda för att autentisera förfrågningar till Azure. Det finns flera platser i Visual Studio, där du kan logga in att tillhandahålla autentiseringsuppgifterna. Till exempel från Server Explorer kan du öppna snabbmenyn för den **Azure** och välj **Anslut till Microsoft Azure-prenumeration**. När du loggar in finns information om prenumerationen som är kopplad till ditt Azure-konto i Visual Studio. Det finns inget mer du behöver göra.

Ett äldre sätt att ange autentiseringsuppgifter också stöd för Azure-verktyg: med hjälp av prenumerationsfilen (.publishsettings-fil). Den här artikeln beskriver den här metoden stöds fortfarande i Azure SDK 2.2.

Följande krävs för autentisering till Azure:

* Prenumerations-ID
* Ett giltigt X.509 v3-certifikat

> [!NOTE]
> Längden för den X.509 v3-certifikat nyckeln måste vara minst 2 048 bitar. Azure avvisar alla certifikat som uppfyller inte det här kravet eller som inte är giltigt.
>
>

Visual Studio använder ditt prenumerations-ID tillsammans med certifikatdata som autentiseringsuppgifter. Prenumerationsfilen (.publishsettings-fil) som innehåller en offentlig nyckel för certifikatet som refererar till rätt autentiseringsuppgifter. Prenumerationsfilen kan innehålla referenser för mer än en prenumeration.

Du kan redigera information om prenumerationen från den **ny prenumeration** eller **redigera prenumeration** dialogrutan som beskrivs senare i den här artikeln.

Om du vill skapa ett certifikat själv kan du referera till instruktionerna i [skapa och ladda upp ett certifikat för Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) och sedan ladda upp certifikatet till manuellt i [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!NOTE]
> Dessa autentiseringsuppgifter som krävs i Visual Studio för att hantera dina molntjänster är inte samma autentiseringsuppgifter som krävs för att autentisera en begäran mot Azure storage-tjänster.
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Importera, skapa eller redigera autentiseringsuppgifter i Visual Studio

1. I Server Explorer, öppna snabbmenyn för den **Azure** och välj **hantera och Filter prenumerationer**.
2. Välj den **certifikat** fliken och Använd sedan någon av följande metoder:

    * Välj **importera** att öppna den **Importera Microsoft Azure-prenumerationer** dialogrutan. Där du kan ladda ned filen prenumerationer för den aktuella inlästa prenumerationen Bläddra fram en hämtningsplats och sedan importera den för användning vid autentisering.
    * Välj **ny** att öppna den **ny prenumeration** dialogrutan. Det, kan du ställa in en ny prenumeration för autentisering.
    * Välj **redigera** (när du har valt prenumerationen aktiv) att öppna den **redigera prenumeration** dialogrutan. Där kan redigera du en befintlig prenumeration för autentisering. 

Följande procedur förutsätter att den **ny prenumeration** dialogruta är öppen.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Att ställa in autentiseringsuppgifter i Visual Studio
1. I den **välja ett befintligt certifikat för autentisering** väljer du ett certifikat.
2. Välj den **kopiera den fullständiga sökvägen** länk. Sökväg för certifikatet (.cer-fil) kopieras till Urklipp.

   > [!IMPORTANT]
   > För att publicera ditt Azure-program från Visual Studio, måste du överföra certifikatet till den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   >
   >
3. Överför certifikatet till Azure portal:

   a. Öppna [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   
   b. Om du uppmanas logga in på portalen och bläddra sedan till **inställningar** > **Hanteringscertifikat**.
   
   c. I den **hanteringscertifikat** väljer **överför**.
   
   d. Välj din Azure-prenumeration, klistra in den fullständiga sökvägen till den .cer-fil som du precis skapade och välj **överför**.

## <a name="next-steps"></a>Nästa steg
* [Allmän översikt över Web Apps](https://docs.microsoft.com/azure/app-service/)
* [Distribuera din app till Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-local-git) 
* [Distribuera WebJobs med hjälp av Visual Studio](https://docs.microsoft.com/en-us/azure/app-service/websites-dotnet-deploy-webjobs)
* [Skapa och distribuera en tjänst i molnet](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)