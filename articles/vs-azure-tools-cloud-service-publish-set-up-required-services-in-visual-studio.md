---
title: Förbereda för att publicera eller distribuera en molntjänst från Visual Studio | Microsoft Docs
description: Lär dig procedurer för att konfigurera moln och lagring-Kontotjänster och konfigurera ditt Azure-program.
services: visual-studio-online
author: ghogen
manager: douge
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: cbd72198dbdbe2dd49f398e98806773cbb11d0f7
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969393"
---
# <a name="prepare-to-publish-or-deploy-a-cloud-service-from-visual-studio"></a>Förbereda för att publicera eller distribuera en molntjänst från Visual Studio

Om du vill publicera ett molntjänstprojekt, måste du ställa in följande tjänster enligt beskrivningen i den här artikeln:

* En **molntjänst** att köra dina roller i Azure-miljön, och 
* En **lagringskonto** som ger åtkomst till tjänsterna Blob, Queue och Table.

## <a name="create-a-cloud-service"></a>Skapa en molntjänst

En molnbaserad tjänst körs dina roller i Azure-miljön. Du kan skapa en molntjänst som Visual Studio eller via den [Azure-portalen](https://portal.azure.com/) som beskrivs i avsnitten som följer.

### <a name="create-a-cloud-service-from-visual-studio"></a>Skapa en molnbaserad tjänst från Visual Studio

1. Med en tidigare skapad Cloud Service-projekt, högerklickar du på projektet väljer **publicera**.
1. Om det behövs, logga in med det Microsoft-konto eller organisationskonto som är associerade med din Azure-prenumeration och välj sedan **nästa** att gå vidare till den **inställningar** sidan.
1. En **skapa Molntjänsten och Lagringskontot** dialogruta (om inte, Välj **Skapa ny** från den **molntjänst** listan).
1. Ange ett skiftlägeskänsliga namn för din molntjänst som utgör en del av din URL och måste vara unikt. Också välja en Region eller Tillhörighetsgrupp och välj ett alternativ för datareplikering.

### <a name="create-a-cloud-service-through-the-azure-portal"></a>Skapa en molntjänst via Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Välj **molntjänster (klassiska)** till vänster på sidan.
1. Välj **+ Lägg till**, sedan ange nödvändig information (DNS-namn, prenumeration, resursgrupp och plats). Du behöver inte ladda upp ett paket nu eftersom du göra det senare i Visual Studio.
1. Välj **skapa** att slutföra processen.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Ett lagringskonto ger åtkomst till tjänsterna Blob, Queue och Table. Du kan skapa ett lagringskonto via Visual Studio eller [Azure-portalen](https://portal.azure.com/).

### <a name="create-a-storage-account-from-visual-studio"></a>Skapa ett lagringskonto från Visual Studio

1. I **Solution Explorer** med en tidigare skapad Cloud Service-projekt, letar du upp den **Connected Services** nod i ett rollprojekt, högerklicka och välj **Lägg till Connected Service**. (I Visual Studio 2015, högerklickar du på den **Storage** noden och väljer **skapa Lagringskonto**.)
1. I den **Connected Services** listan som visas, Välj **Molnlagring med Azure Storage**.
1. I Azure Storage-dialogrutan väljer du **+ Skapa nytt Lagringskonto**, som visar en dialogruta där du anger din prenumeration, ett namn för kontot, en prisnivå nivå, resursgrupp och plats.
1. Välj **skapa** när du är klar. Det nya lagringskontot visas i listan över tillgängliga storage-konton i din prenumeration.
1. Välj som konto och välj **Lägg till**.

### <a name="create-a-storage-account-through-the-azure-portal"></a>Skapa ett lagringskonto via Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Välj **+ ny** uppe till vänster.
1. Välj **Storage** under ”Azure Marketplace”, sedan **lagringskonto – blob, fil, tabell, kö** från höger sida.
1. Ange nödvändig information (namn, distributionsmodellen och så vidare.
1. Välj **skapa** att slutföra processen.

## <a name="configure-your-app-to-use-the-storage-account"></a>Konfigurera din app så att du använder storage-konto

När du skapar ett lagringskonto kan uppdaterar ansluta till den från Visual Studio automatiskt tjänstkonfiguration för projektet, inklusive webbadresser och åtkomstnycklar.

Om du har skapat en molnbaserad tjänst från Visual Studio med hjälp av den **Lägg till Connected Service**, du kan kontrollera anslutningarna genom att öppna `ServiceConfiguration.Cloud.cscfg` och `ServiceConfiguration.Local.cscfg`.

Om du har skapat en tjänst i molnet via Azure portal följer samma steg i [skapa ett lagringskonto från Visual Studio](#create-a-storage-account-from-visual-studio) men Välj det befintliga kontot snarare än att skapa en ny. Visual Studio uppdaterar sedan konfigurationen åt dig.

Konfigurera inställningarna manuellt, använda egenskapssidor i Visual Studio för rollen gäller i ditt molntjänstprojekt (Högerklicka på rollen och välj **egenskaper**). Mer information finns i [konfigurera en anslutningssträng för ett lagringskonto](https://docs.microsoft.com/azure/vs-azure-tools-multiple-services-project-configurations#configuring-a-connection-string-to-a-storage-account).

### <a name="about-access-keys"></a>Om åtkomstnycklar

Azure-portalen visar de URL: er som du kan använda för att komma åt resurser i Azure storage-tjänster och de primära och sekundära åtkomstnycklarna för ditt konto. Du kan använda de här nycklarna för att autentisera begäranden som görs mot storage-tjänster.

Den sekundära åtkomstnyckeln ger samma åtkomst till ditt lagringskonto som den primära åtkomstnyckeln och genereras som en säkerhetskopia bör din primära åtkomstnyckel äventyras. Dessutom rekommenderar vi att du återskapar åtkomstnycklarna regelbundet. Du kan ändra en inställning för anslutningssträngen att använda den sekundära nyckeln medan du återskapa den primära nyckeln kan du ändra det om du vill använda den återskapade primärnyckeln medan du återskapar den sekundära nyckeln.

## <a name="next-steps"></a>Nästa steg

Mer information om att publicera appar till Azure från Visual Studio, finns i [publicerar en molntjänst med Azure Tools](vs-azure-tools-publishing-a-cloud-service.md).
