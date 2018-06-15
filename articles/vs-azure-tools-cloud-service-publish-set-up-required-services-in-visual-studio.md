---
title: Förbereda för att publicera eller distribuera en molnbaserad tjänst från Visual Studio | Microsoft Docs
description: Läs om procedurerna för att konfigurera moln och storage-Kontotjänster och konfigurera Azure-program.
services: visual-studio-online
author: ghogen
manager: douge
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: 7656f0ea7ddc283d430eacdccb60a1a2df6e3b70
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798989"
---
# <a name="prepare-to-publish-or-deploy-a-cloud-service-from-visual-studio"></a>Förbereda för att publicera eller distribuera en molnbaserad tjänst från Visual Studio

Om du vill publicera ett molntjänstprojekt, måste du ställa in följande tjänster som beskrivs i den här artikeln:

* En **Molntjänsten** att köra dina roller i Azure-miljön, och 
* En **lagringskonto** som ger åtkomst till tjänsterna Blob, köer och tabellen.

## <a name="create-a-cloud-service"></a>Skapa en molntjänst

En molnbaserad tjänst körs dina roller i Azure-miljön. Du kan skapa en tjänst i molnet Visual Studio eller via den [Azure-portalen](https://portal.azure.com/) som beskrivs i avsnitten som följer.

### <a name="create-a-cloud-service-from-visual-studio"></a>Skapa en molnbaserad tjänst från Visual Studio

1. Högerklicka på projektet väljer med en tidigare skapad Cloud Service-projekt **publicera**.
1. Om det behövs, logga in med Microsoft eller organisationskonto som är associerade med din Azure-prenumeration och välj sedan **nästa** att gå vidare till den **inställningar** sidan.
1. En **skapa Molntjänsten och Lagringskontot** visas (om inte, Välj **Skapa nytt** från den **Molntjänsten** listan).
1. Ange ett icke-skiftlägeskänsliga namn för din molntjänst som utgör en del av URL: en och måste vara unika. Välj en Region eller Affinitetsgrupp också och väljer ett replikeringsalternativ för.

### <a name="create-a-cloud-service-through-the-azure-portal"></a>Skapa en molntjänst via Azure portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **molntjänster (klassisk)** till vänster på sidan.
1. Välj **+ Lägg till**, ange informationen som krävs (DNS-namn, prenumeration, resursgrupp och plats). Det är inte nödvändigt att överföra ett paket nu eftersom du göra det senare i Visual Studio.
1. Välj **skapa** att slutföra processen.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Ett lagringskonto ger åtkomst till tjänsterna Blob, köer och tabellen. Du kan skapa ett lagringskonto via Visual Studio eller [Azure-portalen](https://portal.azure.com/).

### <a name="create-a-storage-account-from-visual-studio"></a>Skapa ett lagringskonto från Visual Studio

1. I **Solution Explorer** med en tidigare skapad Cloud Service-projekt, leta upp den **anslutna tjänster** nod inom en rollprojekt, högerklicka och välj **Lägg till ansluten tjänst**. (I Visual Studio 2015, högerklickar du på den **lagring** och välj **skapa Lagringskonto**.)
1. I den **anslutna tjänster** listan som visas, väljer **lagringsutrymmet i molnet med Azure Storage**.
1. Välj i dialogrutan Azure Storage **+ Skapa nytt Lagringskonto**, som visar en dialogruta där du anger din prenumeration ett namn för kontot, en prisnivå nivån, resursgrupp och plats.
1. Välj **skapa** när du är klar. Det nya lagringskontot visas i listan över tillgängliga storage-konton i din prenumeration.
1. Välj som kontot och markera **Lägg till**.

### <a name="create-a-storage-account-through-the-azure-portal"></a>Skapa ett lagringskonto via Azure portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **+ ny** på upp till vänster.
1. Välj **lagring** under ”Azure Marketplace”, sedan **lagringskonto - blob, fil, tabell, kö** från höger sida.
1. Ange informationen som krävs (namn, distributionsmodellen och så vidare.
1. Välj **skapa** att slutföra processen.

## <a name="configure-your-app-to-use-the-storage-account"></a>Konfigurera appen för att använda storage-konto

När du har skapat ett lagringskonto, uppdaterar ansluta till den från Visual Studio automatiskt tjänstkonfiguration för projektet, inklusive URL: er och åtkomstnycklar.

Om du har skapat en molnbaserad tjänst från Visual Studio med hjälp av den **Lägg till ansluten tjänst**, du kan kontrollera anslutningarna genom att öppna `ServiceConfiguration.Cloud.cscfg` och `ServiceConfiguration.Local.cscfg`.

Om du har skapat en tjänst i molnet via Azure portal, följer du samma steg i [skapa ett lagringskonto från Visual Studio](#create-a-storage-account-from-visual-studio) men markera det befintliga kontot i stället för att skapa en ny. Visual Studio uppdaterar konfigurationen åt dig.

Konfigurera inställningarna manuellt, använder egenskapssidor i Visual Studio för rollen tillämpliga i ditt molntjänstprojekt (Högerklicka på rollen och välj **egenskaper**). Mer information finns i [konfigurera en anslutningssträng för ett lagringskonto](https://docs.microsoft.com/azure/vs-azure-tools-multiple-services-project-configurations#configuring-a-connection-string-to-a-storage-account).

### <a name="about-access-keys"></a>Om snabbtangenter

Azure-portalen visar de URL: er som du kan använda för att komma åt resurser i varje Azure-lagringstjänster och primära och sekundära åtkomstnycklarna för ditt konto. Du kan använda dessa nycklar för att autentisera förfrågningar som görs mot storage-tjänster.

Den sekundära åtkomstnyckeln ger samma åtkomst till ditt lagringskonto som den primära åtkomstnyckeln och genereras som en säkerhetskopia bör din primärnyckeln äventyras. Vi rekommenderar dessutom att du återskapar dina åtkomstnycklar regelbundet. Du kan ändra inställningen för en anslutningssträngen använda den sekundära nyckeln medan du återskapa den primära nyckeln kan du ändra det om du vill använda den återskapade primära nyckeln medan du återskapa den sekundära nyckeln.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du publicerar appar till Azure från Visual Studio i [publicering av en tjänst i molnet med hjälp av Azure-verktyg](vs-azure-tools-publishing-a-cloud-service.md).
