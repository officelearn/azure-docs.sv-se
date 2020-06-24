---
title: Översikt över Azure API Management Developer-portalen
titleSuffix: Azure API Management
description: Lär dig mer om Developer-portalen i API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: ecbd4d97bb092ccbdb286e4865bf04e770ca9473
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85207897"
---
# <a name="azure-api-management-developer-portal-overview"></a>Översikt över Azure API Management Developer-portalen

Developer Portal är en automatiskt genererad, helt anpassningsbar webbplats med dokumentationen för dina API: er. Det är där API-konsumenter kan identifiera dina API: er, lära sig hur de används, begära åtkomst och testa dem.

I den här artikeln beskrivs skillnaderna mellan egna värdbaserade och hanterade versioner av Developer-portalen i API Management. Den förklarar också dess arkitektur och ger svar på vanliga frågor.

![API Management Developer-portalen](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>Hanterade och egen värdbaserade versioner

Du kan bygga din utvecklings Portal på två sätt:

- **Hanterad version** – genom att redigera och anpassa portalen, som är inbyggd i API Management-instansen och är tillgänglig via URL: en `<your-api-management-instance-name>.developer.azure-api.net` . Läs [den här dokumentations artikeln](api-management-howto-developer-portal-customize.md) för att lära dig hur du får åtkomst till och anpassar den hanterade portalen.
- **Egen värd version** – genom att distribuera och självbetjäning av portalen utanför en API Management-instans. Med den här metoden kan du redigera portalens kodbas och utöka de tillhandahållna kärn funktionerna, till exempel implementera anpassade widgetar för integreringar med system från tredje part. I det här scenariot är du portalens underhållare och du ansvarar för att uppgradera portalen till den senaste versionen. Mer information och anvisningar finns i GitHub- [lagringsplatsen med käll koden för portalen][1] och [självstudien om hur du implementerar en widget][3]. [Självstudien för den hanterade versionen](api-management-howto-developer-portal-customize.md) går igenom portalens administrations panel, som är gemensam för de hanterade och de egna värd versionerna.

## <a name="portal-architectural-concepts"></a>Arkitektur koncept för portalen

Portal komponenterna kan delas upp logiskt i två kategorier: *kod* och *innehåll*.

*Koden* underhålls i [GitHub-lagringsplatsen][1] och inkluderar:

- Widgetar – som representerar visuella element och kombinerar HTML, Java Script, format funktioner, inställningar och innehålls mappning. Exempel är en bild, ett text stycke, ett formulär, en lista över API: er osv.
- Format definitioner – som anger hur widgetar kan formateras
- Motor – som genererar statiska webb sidor från Portal innehåll och som är skrivet i Java Script
- Visuell redigerare – som möjliggör anpassning och redigering i webbläsare

*Innehållet* är uppdelat i två under Kategorier: *portal innehåll* och *API Management innehåll*.

*Portal innehållet* är särskilt för portalen och innehåller:

- Sidor – till exempel landnings sida, API-självstudier, blogg inlägg
- Media – bilder, animationer och annat filbaserat innehåll
- Layout – mallar som matchas mot en URL och definierar hur sidor visas
- Stilar – värden för format definitioner, t. ex. teckensnitt, färger, kant linjer
- Inställningar – konfiguration, t. ex. favicon, webbplatsens metadata

*Portal innehåll*, förutom media, uttrycks som JSON-dokument.

*API Management innehåll* innehåller entiteter som API: er, åtgärder, produkter, prenumerationer.

Portalen baseras på en anpassad förgrening av [Paperbits-ramverket](https://paperbits.io/). De ursprungliga Paperbits-funktionerna har utökats för att tillhandahålla API Management-/regionsspecifika widgetar (till exempel en lista över API: er, en lista över produkter) och en koppling till API Management tjänsten för att spara och hämta innehåll.

## <a name="frequently-asked-questions"></a><a name="faq"></a>Vanliga frågor och svar

I det här avsnittet svarar vi på vanliga frågor om Developer-portalen, som är av allmän natur. För frågor som är speciella för den självbetjänings versionen, se [wiki-avsnittet i GitHub-lagringsplatsen](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a>Hur kan jag migrera från för hands versionen av portalen?

Med hjälp av för hands versionen av Developer-portalen etablerade du att förhands gransknings innehållet i API Managements tjänsten. Standard innehållet har ändrats avsevärt i den allmänt tillgängliga versionen för bättre användar upplevelse. Den innehåller också nya widgetar.

Om du använder den hanterade versionen återställer du portalens innehåll genom att klicka på **Återställ innehåll** i menyn **åtgärder** . Om du bekräftar åtgärden tas allt innehåll i portalen bort och det nya standard innehållet etableras. Portalens motor har uppdaterats automatiskt i din API Management-tjänst.

![Återställ Portal innehåll](media/api-management-howto-developer-portal/reset-content.png)

Om du använder den själv värdbaserade versionen använder du `scripts/cleanup.bat` och `scripts/generate.bat` från GitHub-lagringsplatsen för att ta bort befintligt innehåll och etablera nytt innehåll. Se till att du uppgraderar din portal kod till den senaste versionen från GitHub-databasen i förväg.

Om du inte vill återställa portalens innehåll kan du överväga att använda nyligen tillgängliga widgetar på alla sidor. Befintliga widgetar har uppdaterats automatiskt till de senaste versionerna.

Om portalen etablerades efter det allmänna tillgänglighets meddelandet bör den redan ha det nya standard innehållet. Ingen åtgärd krävs från din sida.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Hur kan jag migrera från den gamla Developer-portalen till Developer-portalen?

Portalerna är inkompatibla och du måste migrera innehållet manuellt.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>Har portalen alla funktioner från den gamla portalen?

Developer-portalen stöder inte längre *program* och *problem*.

Autentisering med OAuth i den interaktiva Developer-konsolen stöds inte ännu. Du kan följa förloppet genom [GitHub-problemet](https://github.com/Azure/api-management-developer-portal/issues/208).

### <a name="has-the-old-portal-been-deprecated"></a>Har den gamla portalen föråldrats?

De gamla portarna för utvecklare och utgivare är nu *äldre* funktioner – de kommer endast att ta emot säkerhets uppdateringar. Nya funktioner kommer endast att implementeras i den nya Developer-portalen.

Utfasningen av äldre portaler kommer att meddelas separat. Om du har frågor, rör eller kommentarer kan du höja dem [i ett dedikerat GitHub-problem](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Funktioner jag behöver stöds inte i portalen

Du kan öppna en [funktions förfrågan](https://aka.ms/apimwish) eller [implementera de funktioner som saknas själv][3]. Om du implementerar funktionerna själv, kan du antingen själv vara värd för Developer-portalen eller öppna en pull-begäran på GitHub för att inkludera ändringarna i den hanterade versionen.

### <a name="how-can-i-automate-portal-deployments"></a>Hur kan jag automatisera Portal distributioner?

Du kan program mässigt komma åt och hantera Developer portalens innehåll via REST API, oavsett om du använder en hanterad eller en egen version.

API: et finns dokumenterat i [GitHub-lagringsplatsens wiki-avsnitt][2]. Den kan användas för att automatisera migrering av Portal innehåll mellan miljöer, till exempel från en test miljö till produktions miljön. Du kan lära dig mer om den här processen [i den här dokumentations artikeln](https://aka.ms/apimdocs/migrateportal) på GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Stöder portalen Azure Resource Manager mallar och/eller är den kompatibel med API Management DevOps Resource Kit?

Nej.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Måste jag aktivera ytterligare VNet-anslutning för de hanterade portalernas beroenden?

I de flesta fall – nej.

Om din API Management-tjänst finns i ett internt virtuellt nätverk är din utvecklings Portal bara tillgänglig i nätverket. Värd namnet för hanterings slut punkten måste matcha den interna VIP för tjänsten från den dator som du använder för att få åtkomst till portalens administrations gränssnitt. Kontrol lera att hanterings slut punkten är registrerad i DNS. I händelse av fel konfiguration visas ett fel meddelande: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Om din API Management-tjänst finns i ett internt virtuellt nätverk och du har åtkomst till den via Application Gateway från Internet, så se till att aktivera anslutningen till Developer-portalen och hanterings slut punkterna för API Management.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Jag har tilldelat en anpassad API Management domän och den publicerade portalen fungerar inte

När du har uppdaterat domänen måste du [publicera portalen](api-management-howto-developer-portal-customize.md#publish) igen för att ändringarna ska börja gälla.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Jag har lagt till en identitets leverantör och jag kan inte se den i portalen

När du har konfigurerat en identitets leverantör (till exempel AAD, AAD B2C) måste du [publicera portalen](api-management-howto-developer-portal-customize.md#publish) igen för att ändringarna ska börja gälla.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Jag har konfigurerat delegering och portalen använder den inte

När du har konfigurerat delegering måste du [publicera portalen](api-management-howto-developer-portal-customize.md#publish) igen för att ändringarna ska börja gälla.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Mina andra API Management konfigurations ändringar har inte spridits i Developer-portalen

De flesta konfigurations ändringar (till exempel VNet, inloggning och produkt villkor) kräver [att portalen publiceras om](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>Jag får ett CORS-fel när jag använder den interaktiva konsolen

Den interaktiva konsolen gör en API-begäran på klient sidan från webbläsaren. Lös CORS-problemet genom att lägga till [en CORS-princip](api-management-cross-domain-policies.md#CORS) på dina API: er.

Du kan kontrol lera status för CORS-principen i avsnittet **Portal översikt** i API Management tjänsten i Azure Portal. En varnings ruta anger att en princip saknas eller är felkonfigurerad.

![API Management Developer-portalen](media/api-management-howto-developer-portal/cors-azure-portal.png)

Tillämpa CORS-principen automatiskt genom att klicka på knappen **Aktivera CORS** .

Du kan också aktivera CORS manuellt.

1. Klicka på den **manuellt tillämpa den på länken global nivå** för att se den genererade princip koden.
2. Gå till **alla API:** er i avsnittet **API: er** i din API Management-tjänst i Azure Portal.
3. Klicka på **</>** ikonen i avsnittet **inkommande bearbetning** .
4. Infoga principen i **<inbound>** avsnittet i XML-filen. Se till att **<origin>** värdet matchar din utvecklares Portal domän.

> [!NOTE]
> 
> Om du använder CORS-principen i produkt omfånget, i stället för API: erna och ditt API använder prenumerations nyckel autentisering via en rubrik, fungerar inte konsolen.
>
> Webbläsaren utfärdar automatiskt ett alternativ HTTP-begäran, som inte innehåller något sidhuvud med prenumerations nyckeln. På grund av den saknade prenumerations nyckeln kan API Management inte associera alternativ anropet med en produkt, så det går inte att använda CORS-principen.
>
> Som en lösning kan du skicka prenumerations nyckeln i en frågeparameter.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Vilka behörigheter behöver jag för att redigera Developer-portalen?

Om du ser `Oops. Something went wrong. Please try again later.` felet när du öppnar portalen i administrations läge, kan det hända att du saknar de nödvändiga behörigheterna (RBAC).

De äldre portalerna krävde behörigheten för `Microsoft.ApiManagement/service/getssotoken/action` tjänst omfånget ( `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` ) för att ge användar administratörs åtkomst till portalerna. Den nya portalen kräver behörighet `Microsoft.ApiManagement/service/users/token/action` i omfånget `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

Du kan använda följande PowerShell-skript för att skapa en roll med den behörighet som krävs. Kom ihåg att ändra `<subscription-id>` parametern. 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
När rollen har skapats kan den beviljas till alla användare från avsnittet **Access Control (IAM)** i Azure Portal. Om du tilldelar den här rollen till en användare tilldelas behörigheten på tjänstens omfång. Användaren kommer att kunna generera SAS-token för *en* användares räkning i tjänsten. Rollen måste ha tilldelats rollen som administratör för tjänsten. Följande PowerShell-kommando visar hur du tilldelar rollen till en användare `user1` vid det lägsta omfånget för att undvika att ge användaren onödiga behörigheter: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

När behörigheterna har beviljats till en användare måste användaren logga ut och logga in igen till Azure Portal för att de nya behörigheterna ska börja gälla.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Jag ser `Unable to start the portal. See if settings are specified correctly (...)` felet

Det här felet visas när ett `GET` anrop `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` Miss lyckas. Anropet utfärdas från webbläsaren av portalens administrations gränssnitt.

Om din API Management-tjänst finns i ett VNet – hänvisa till den VNet-anslutningens fråga ovan.

Anrops felen kan också orsakas av ett TLS/SSL-certifikat, som är tilldelat en anpassad domän och som inte är betrodd av webbläsaren. Som en minskning kan du ta bort den anpassade domänen för hanterings slut punkten – API Management kommer att återgå till standard slut punkten med ett betrott certifikat.

### <a name="whats-the-browser-support-for-the-portal"></a>Vad är webb läsar stöd för portalen?

| Webbläsare                     | Stöds       |
|-----------------------------|-----------------|
| Apple Safari                | Ja<sup>1</sup> |
| Google Chrome               | Ja<sup>1</sup> |
| Microsoft Edge              | Ja<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Ja<sup>1</sup> |

 <small><sup>1</sup> stöds i de två senaste produktions versionerna.</small>

## <a name="next-steps"></a>Nästa steg

Läs mer om den nya Developer-portalen:

- [Komma åt och anpassa Managed Developer-portalen](api-management-howto-developer-portal-customize.md)
- [Konfigurera en egen värd version av portalen][2]
- [Implementera din egen widget][3]

Bläddra bland andra resurser:

- [GitHub-lagringsplats med käll koden][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
