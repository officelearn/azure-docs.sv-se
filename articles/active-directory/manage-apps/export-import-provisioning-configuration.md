---
title: Exportera eller importera etablerings konfigurationen med Graph API | Microsoft Docs
description: Lär dig hur du exporterar och importerar etablerings konfiguration med Graph API.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 609031bfad23a14a954a09a447e363e89a9d29d5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711740"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Exportera eller importera etablerings konfigurationen med Graph API

Du kan använda Microsoft Graph API och Graph Explorer för att exportera mappningar och schemat för användar etablerings attribut till en JSON-fil och importera tillbaka dem till Azure AD. Du kan också använda stegen som beskrivs här för att skapa en säkerhets kopia av etablerings konfigurationen. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Steg 1: Hämta etablerings App Serviceens huvud-ID (objekt-ID)

1. Starta [Azure Portal](https://portal.azure.com)och navigera till avsnittet Egenskaper i ditt etablerings program. T. ex. om du vill exportera en *arbets dag till AD User Provisioning Application* -mappning navigerar du till avsnittet Egenskaper i appen. 
1. I avsnittet Egenskaper i din etablerings app kopierar du det GUID-värde som är associerat med fältet *objekt-ID* . Det här värdet kallas även **ServicePrincipalId** för din app och används i Graph-åtgärder.

   ![Workday App Service huvud-ID](media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Steg 2: Logga in på Microsoft Graph Explorer

1. Starta [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Klicka på knappen "logga in med Microsoft" och logga in med Azure AD global admin-eller app admin-autentiseringsuppgifter.

    ![Graph-inloggning](media/export-import-provisioning-mappings/wd_export_02.png)

1. Vid lyckad inloggning visas användar konto informationen i den vänstra rutan.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Steg 3: Hämta etablerings jobb-ID: t för etablerings appen

Kör följande GET-fråga i Microsoft Graph Explorer och Ersätt [servicePrincipalId] med **servicePrincipalId** som extraherats från [steg 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Du får ett svar på det sätt som visas nedan. Kopiera "ID-attributet" som finns i svaret. Det här värdet är **ProvisioningJobId** och kommer att användas för att hämta underliggande metadata för schemat.

   [![etablerings jobb-ID](media/export-import-provisioning-mappings/wd_export_03.png)](media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>Steg 4: Ladda ned etablerings schema

Kör följande GET-fråga i Microsoft Graph Explorer och Ersätt [servicePrincipalId] och [ProvisioningJobId] med ServicePrincipalId och ProvisioningJobId som hämtades i föregående steg.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopiera JSON-objektet från svaret och spara det i en fil för att skapa en säkerhets kopia av schemat.

## <a name="step-5-import-the-provisioning-schema"></a>Steg 5: importera etablerings schema

> [!CAUTION]
> Utför bara det här steget om du behöver ändra schemat för konfigurationen som inte kan ändras med hjälp av Azure Portal eller om du behöver återställa konfigurationen från en tidigare säkerhetskopierad fil med ett giltigt och fungerande schema.

I Microsoft Graph Explorer konfigurerar du följande skicka fråga, ersätter [servicePrincipalId] och [ProvisioningJobId] med ServicePrincipalId och ProvisioningJobId som hämtades i föregående steg.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

På fliken "begär ande text" kopierar du innehållet i JSON-schemafilen.

   [![begär ande text](media/export-import-provisioning-mappings/wd_export_04.png)](media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

På fliken "begär ande rubriker" lägger du till attributet Content-Type Head med värdet "Application/JSON"

   [![begärandehuvuden](media/export-import-provisioning-mappings/wd_export_05.png)](media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

Klicka på knappen Kör fråga för att importera det nya schemat.
