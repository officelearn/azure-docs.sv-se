---
title: Exportera etableringskonfigurationen och återställ till ett fungerande tillstånd för haveriberedskap.| Microsoft-dokument
description: Lär dig hur du exporterar etableringskonfigurationen och återställer till ett fungerande tillstånd för haveriberedskap.
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
ms.date: 03/19/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92a40a5fe3067cf96d3c742102c9ca66078cd5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051317"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Exportera etableringskonfigurationen och återställ till ett känt bra tillstånd

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Exportera och importera etableringskonfigurationen från Azure-portalen

### <a name="how-can-i-export-my-provisioning-configuration"></a>Hur exporterar jag min etableringskonfiguration?
Så här exporterar du konfigurationen:
1. Välj Azure Active Directory på den vänstra navigeringspanelen i [Azure-portalen](https://portal.azure.com/). **Azure Active Directory**
2. I fönstret **Azure Active Directory** väljer du **Enterprise-program** och väljer ditt program.
3. Välj **etablering**i det vänstra navigeringsfönstret . Från etableringskonfigurationssidan klickar du på **attributmappningar**och visar sedan **avancerade alternativ**och granskar slutligen **schemat**. Detta tar dig till schemaredigeraren. 
5. Klicka på hämta i kommandofältet högst upp på sidan för att ladda ner ditt schema.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Katastrofåterställning - rulla tillbaka till ett känt bra tillstånd
När du exporterar och sparar konfigurationen kan du återställa till en tidigare version av konfigurationen. Vi rekommenderar att du exporterar etableringskonfigurationen och sparar den för senare användning när du gör en ändring av attributmappningar eller omfångsfilter. Allt du behöver göra är att öppna upp JSON-filen som du hämtade i stegen ovan, kopiera hela innehållet i JSON-filen, ersätta hela innehållet i JSON-nyttolasten i schemaredigeraren och spara sedan. Om det finns en aktiv etableringscykel slutförs den och nästa cykel kommer att använda det uppdaterade schemat. Nästa cykel kommer också att vara en inledande cykel, som omvärderar varje användare och grupp baserat på den nya konfigurationen. Tänk på följande när du går tillbaka till en tidigare konfiguration:
* Användare utvärderas igen för att avgöra om de ska vara i omfattning. Om omfångsfiltren har ändrats är en användare inte längre i omfattning. Även om detta är det önskade beteendet i de flesta fall finns det tillfällen där du kanske vill förhindra detta och kan använda [funktionen hoppa utanför scopeborttagningarna.](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions) 
* Om du ändrar etableringskonfigurationen startas tjänsten om och en [inledande cykel utlöses](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental).


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Exportera och importera etableringskonfigurationen med hjälp av Microsoft Graph API
Du kan använda Microsoft Graph API och Microsoft Graph Explorer för att exportera attributmappningar och schema för användare till en JSON-fil och importera tillbaka den till Azure AD. Du kan också använda stegen som fångas här för att skapa en säkerhetskopia av etableringskonfigurationen. 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Steg 1: Hämta ditt etableringsapptjänsthuvudhuvuden-ID (object ID)

1. Starta [Azure-portalen](https://portal.azure.com)och navigera till avsnittet Egenskaper i etableringsprogrammet. Om du till exempel vill exportera din *arbetsdag till AD-användare etablering programmappning* navigera till egenskapen i den appen. 
1. Kopiera GUID-värdet som är associerat med fältet *Objekt-ID* i avsnittet Egenskaper i etableringsappen. Det här värdet kallas också **ServicePrincipalId** för din app och används i Microsoft Graph Explorer-åtgärder.

   ![Huvud-ID för tjänsten Workday-tjänsten](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Steg 2: Logga in på Microsoft Graph Explorer

1. Starta [Utforskaren i Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Klicka på knappen "Logga in med Microsoft" och logga in med Azure AD Global Admin- eller App Admin-autentiseringsuppgifter.

    ![Inloggning i Microsoft Graph](./media/export-import-provisioning-configuration/wd_export_02.png)

1. När du har loggat in igen visas information om användarkontot i den vänstra rutan.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Steg 3: Hämta etableringsjobb-ID:t för etableringsappen

I Utforskaren i Microsoft Graph kör du följande GET-fråga som ersätter [servicePrincipalId] med **ServicePrincipalId** som extraherats från [steg 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Du kommer att få ett svar som visas nedan. Kopiera det "id-attribut" som finns i svaret. Det här värdet är **ProvisioningJobId** och används för att hämta de underliggande schemametadata.

   [![Etablering av jobb-ID](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Steg 4: Hämta etableringsschemat

I Utforskaren i Microsoft Graph kör du följande GET-fråga och ersätter [servicePrincipalId] och [ProvisioningJobId] med ServicePrincipalId och ProvisioningJobId som hämtats i föregående steg.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopiera JSON-objektet från svaret och spara det i en fil för att skapa en säkerhetskopia av schemat.

### <a name="step-5-import-the-provisioning-schema"></a>Steg 5: Importera etableringsschemat

> [!CAUTION]
> Utför bara det här steget om du behöver ändra schemat för konfiguration som inte kan ändras med Azure-portalen eller om du behöver återställa konfigurationen från en tidigare säkerhetskopierad fil med giltigt och fungerande schema.

I Utforskaren i Microsoft Graph konfigurerar du följande PUT-fråga och ersätter [servicePrincipalId] och [ProvisioningJobId] med ServicePrincipalId och ProvisioningJobId som hämtats i föregående steg.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopiera innehållet i JSON-schemafilen på fliken "Begärandeförkropp".

   [![Begärandetext](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Lägg till rubrikattributet innehållstyp på fliken "Begär sidhuvud" med värdet "application/json"

   [![Begär rubriker](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Klicka på knappen "Kör fråga" för att importera det nya schemat.
