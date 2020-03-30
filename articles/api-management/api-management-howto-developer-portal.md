---
title: Översikt över utvecklarportalen för Azure API Management
titleSuffix: Azure API Management
description: Läs mer om utvecklarportalen i API Management.
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
ms.openlocfilehash: fefa5ff5d112b479110d484ee0ea4c358b5c88a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335898"
---
# <a name="azure-api-management-developer-portal-overview"></a>Översikt över utvecklarportalen för Azure API Management

Utvecklarportalen är en automatiskt genererad, helt anpassningsbar webbplats med dokumentation av dina API:er. Det är där API-konsumenter kan upptäcka dina API:er, lära dig hur du använder dem, begär åtkomst och provar dem.

I den här artikeln beskrivs skillnaderna mellan självvärderade och hanterade versioner av utvecklarportalen i API Management. Det förklarar också dess arkitektur och ger svar på vanliga frågor.

![Utvecklarportal för API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>Hanterade och självvärdbaserade versioner

Du kan skapa utvecklarportalen på två sätt:

- **Hanterad version** - genom att redigera och anpassa portalen, som är `<your-api-management-instance-name>.developer.azure-api.net`inbyggd i din API Management-instans och är tillgänglig via URL. I [den här dokumentationsartikeln](api-management-howto-developer-portal-customize.md) finns information om hur du kommer åt och anpassar den hanterade portalen.
- **Självvärd för version** – genom att distribuera och själv vara värd för portalen utanför en API Management-instans. Med den här metoden kan du redigera portalens kodbas och utöka den medföljande kärnfunktionerna - till exempel implementera anpassade widgets för integreringar med tredjepartssystem. I det här fallet är du portalens ansvarige och du ansvarar för att uppgradera portalen till den senaste versionen. Mer information och instruktioner finns i [GitHub-databasen med portalens källkod][1] och [självstudiekursen för att implementera en widget][3]. [Självstudien för den hanterade versionen](api-management-howto-developer-portal-customize.md) går igenom portalens administrativa panel, vilket är vanligt för hanterade och självvärdbaserade versioner.

## <a name="portal-architectural-concepts"></a>Portal arkitektoniska begrepp

Portalkomponenterna kan logiskt delas in i två kategorier: *kod* och *innehåll*.

*Koden* underhålls i [GitHub-databasen][1] och innehåller:

- Widgetar - som representerar visuella element och kombinera HTML, JavaScript, styling förmåga, inställningar och innehåll mappning. Exempel är en bild, ett textstycke, ett formulär, en lista över API:er etc.
- Styling definitioner - som anger hur widgets kan utformas
- Motor - som genererar statiska webbsidor från portalinnehåll och är skriven i JavaScript
- Visual editor - som möjliggör anpassning och redigering i webbläsare

*Innehållet* är indelat i två underkategorier: *portalinnehåll* och *API Management-innehåll*.

*Portalinnehållet* är specifikt för portalen och omfattar:

- Sidor - till exempel målsida, API-självstudier, blogginlägg
- Media - bilder, animeringar och annat filbaserat innehåll
- Layouter - mallar som matchas mot en URL och definiera hur sidor ska visas
- Stilar - värden för stylingdefinitioner, t.ex.
- Inställningar - konfiguration, t.ex.

*Portalinnehåll*, med undantag för media, uttrycks som JSON-dokument.

*API Management-innehåll* innehåller enheter som API: er, operationer, produkter, prenumerationer.

Portalen bygger på en anpassad gaffel av [Paperbits ram](https://paperbits.io/). Den ursprungliga Paperbits-funktionen har utökats för att tillhandahålla API Management-specifika widgets (till exempel en lista över API:er, en lista över produkter) och en anslutningstjänst till API Management-tjänst för att spara och hämta innehåll.

## <a name="frequently-asked-questions"></a><a name="faq"></a>Vanliga frågor och svar

I det här avsnittet svarar vi på vanliga frågor om utvecklarportalen, som är av allmän karaktär. Frågor som är specifika för den självvärdbaserade versionen finns [i wiki-avsnittet i GitHub-databasen](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/>Hur migrerar jag från förhandsgranskningsversionen av portalen?

Genom att använda förhandsversionen av utvecklarportalen har du etablerat förhandsgranskningsinnehållet i API Management-tjänsten. Standardinnehållet har ändrats avsevärt i den allmänt tillgängliga versionen för bättre användarupplevelse. Den innehåller också nya widgets.

Om du använder den hanterade versionen återställer du portalens innehåll genom att klicka på **Återställ innehåll** i avsnittet **Operations-menyn.** Om du bekräftar den här åtgärden tas allt innehåll på portalen bort och det nya standardinnehållet etableras. Portalens motor har uppdaterats automatiskt i din API Management-tjänst.

![Återställa portalinnehåll](media/api-management-howto-developer-portal/reset-content.png)

Om du använder den självvärdbaserade versionen `scripts/cleanup.bat` använder `scripts/generate.bat` du och från GitHub-databasen för att ta bort befintligt innehåll och etablera nytt innehåll. Se till att du uppgraderar portalens kod till den senaste versionen från GitHub-databasen i förväg.

Om du inte vill återställa innehållet i portalen kan du överväga att använda nytillgängade widgetar på sidorna. Befintliga widgetar har automatiskt uppdaterats till de senaste versionerna.

Om portalen har etablerats efter meddelandet om allmän tillgänglighet bör den redan innehålla det nya standardinnehållet. Ingen åtgärd krävs från din sida.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Hur migrerar jag från den gamla utvecklarportalen till utvecklarportalen?

Portaler är inkompatibla och du måste migrera innehållet manuellt.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>Har portalen alla funktioner i den gamla portalen?

Utvecklarportalen stöder inte längre *program* och *problem*.

Autentisering med OAuth i den interaktiva utvecklarkonsolen stöds ännu inte. Du kan spåra förloppet via [GitHub-problemet](https://github.com/Azure/api-management-developer-portal/issues/208).

### <a name="has-the-old-portal-been-deprecated"></a>Har den gamla portalen blivit inaktuell?

De gamla utvecklar- och utgivarportalerna är nu *äldre* funktioner – de får endast säkerhetsuppdateringar. Nya funktioner kommer endast att implementeras i den nya utvecklarportalen.

Deprecation av äldre portaler kommer att tillkännages separat. Om du har frågor, funderingar eller kommentarer tar du upp dem [i ett särskilt GitHub-problem](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Funktioner som jag behöver stöds inte i portalen

Du kan själv öppna en [funktionsbegäran](https://aka.ms/apimwish) eller [implementera den saknade funktionen.][3] Om du implementerar funktionerna själv kan du antingen själv vara värd för utvecklarportalen eller öppna en pull-begäran på GitHub för att inkludera ändringarna i den hanterade versionen.

### <a name="how-can-i-automate-portal-deployments"></a>Hur automatiserar jag portaldistributioner?

Du kan programmässigt komma åt och hantera utvecklarportalens innehåll via REST API, oavsett om du använder en hanterad eller självvärd version.

API:et dokumenteras i [GitHub-databasens wiki-avsnitt][2]. Den kan användas för att automatisera migreringar av portalinnehåll mellan miljöer - till exempel från en testmiljö till produktionsmiljön. Du kan läsa mer om den här processen [i den här dokumentationsartikeln](https://aka.ms/apimdocs/migrateportal) på GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Stöder portalen Azure Resource Manager-mallar och/eller är den kompatibel med API Management DevOps Resource Kit?

Nej.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Måste jag aktivera ytterligare VNet-anslutning för de hanterade portalberoendena?

I de flesta fall - nej.

Om din API Management-tjänst finns i ett internt virtuella nätverk är utvecklarportalen endast tillgänglig inifrån nätverket. Hanteringsslutpunktens värdnamn måste matcha till den interna VIP-tjänsten från den dator som du använder för att komma åt portalens administrativa gränssnitt. Kontrollera att hanteringsslutpunkten är registrerad i DNS.Make sure the management endpoint is registered in the DNS. Vid felkonfiguration visas ett fel: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`.

Om din API Management-tjänst finns i ett internt virtuella nätverk och du ansluter den via Application Gateway från Internet, se till att aktivera anslutning till utvecklarportalen och hanteringsslutpunkterna för API Management.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Jag har tilldelat en anpassad API Management-domän och den publicerade portalen fungerar inte

När du har uppdaterat domänen måste du [publicera om portalen](api-management-howto-developer-portal-customize.md#publish) för att ändringarna ska börja gälla.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Jag har lagt till en identitetsleverantör och jag kan inte se den i portalen

När du har konfigurerat en identitetsprovider (till exempel AAD, AAD B2C) måste du [publicera om portalen](api-management-howto-developer-portal-customize.md#publish) för att ändringarna ska börja gälla.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Jag har konfigurerat delegering och portalen använder den inte

När du har konfigurerat delegering måste du [publicera om portalen](api-management-howto-developer-portal-customize.md#publish) för att ändringarna ska börja gälla.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Mina andra KONFIGURATIONSÄNDRINGAR för API Management har inte spridits i utvecklarportalen

De flesta konfigurationsändringar (till exempel VNet, inloggning och produktvillkor) kräver [att portalen publiceras om](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>Jag får ett CORS-fel när jag använder den interaktiva konsolen

Den interaktiva konsolen gör en API-begäran på klientsidan från webbläsaren. Lös CORS-problemet genom att lägga till [en CORS-princip](api-management-cross-domain-policies.md#CORS) på dina API:er.

Du kan kontrollera status för CORS-principen i avsnittet **Portal översikt** i din API Management-tjänst i Azure-portalen. En varningsruta anger en frånvarande eller felkonfigurerad princip.

![Utvecklarportal för API Management](media/api-management-howto-developer-portal/cors-azure-portal.png)

Använd CORS-principen automatiskt genom att klicka på knappen **Aktivera CORS.**

Du kan också aktivera CORS manuellt.

1. Klicka på länken **Manuellt tillämpa den på den globala nivån** för att se den genererade principkoden.
2. Navigera till **alla API:er** i avsnittet **API:er** i din API Management-tjänst i Azure-portalen.
3. Klicka på **</>** ikonen i avsnittet **Inkommande bearbetning.**
4. Infoga principen i **<inbound>** avsnittet i XML-filen. Kontrollera att **<origin>** värdet matchar utvecklarportalens domän.

> [!NOTE]
> 
> Om du tillämpar CORS-principen i produktomfånget, i stället för API:et och ditt API använder prenumerationsnyckelautentisering via ett huvud, fungerar inte konsolen.
>
> Webbläsaren utfärdar automatiskt en HTTP-begäran om ALTERNATIV, som inte innehåller ett huvud med prenumerationsnyckeln. På grund av den saknade prenumerationsnyckeln kan API Management inte associera OPTIONS-anropet med en produkt, så det går inte att tillämpa CORS-principen.
>
> Som en lösning kan du skicka prenumerationsnyckeln i en frågeparameter.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Vilka behörigheter behöver jag för att redigera utvecklarportalen?

Om felet visas `Oops. Something went wrong. Please try again later.` när du öppnar portalen i administrativt läge kanske du saknar de behörigheter som krävs (RBAC).

De äldre portalerna `Microsoft.ApiManagement/service/getssotoken/action` krävde behörigheten`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`i tjänstomfånget ( ) för att tillåta användaradministratören åtkomst till portalerna. Den nya portalen `Microsoft.ApiManagement/service/users/token/action` kräver `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`behörighet i omfånget .

Du kan använda följande PowerShell-skript för att skapa en roll med den behörighet som krävs. Kom ihåg `<subscription-id>` att ändra parametern. 

```PowerShell
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
 
När rollen har skapats kan den beviljas alla användare från avsnittet **Åtkomstkontroll (IAM)** i Azure-portalen. Om du tilldelar den här rollen till en användare tilldelas behörigheten i tjänstomfånget. Användaren kan generera SAS-token på uppdrag av *alla* användare i tjänsten. Minst måste den här rollen tilldelas administratören för tjänsten. Följande PowerShell-kommando visar hur du tilldelar `user1` rollen till en användare med det lägsta omfånget för att undvika att bevilja onödiga behörigheter till användaren: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

När behörigheterna har beviljats en användare måste användaren logga ut och logga in igen på Azure-portalen för att de nya behörigheterna ska börja gälla.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Jag ser felet `Unable to start the portal. See if settings are specified correctly (...)`

Det här felet `GET` visas `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` när ett samtal misslyckas. Samtalet utfärdas från webbläsaren av portalens administrativa gränssnitt.

Om din API Management-tjänst finns i ett virtuella nätverk finns i VNet-anslutningsfrågan ovan.

Anropsfelet kan också orsakas av ett TLS/SSL-certifikat, som har tilldelats en anpassad domän och inte är betrodd av webbläsaren. Som en begränsning kan du ta bort den anpassade domänen för hanteringsslutpunkten - API Management kommer att återgå till standardslutpunkten med ett betrott certifikat.

### <a name="whats-the-browser-support-for-the-portal"></a>Vad har webbläsaren stöd för portalen?

| Webbläsare                     | Stöds       |
|-----------------------------|-----------------|
| Apple Safari                | Ja<sup>1</sup> |
| Google Chrome               | Ja<sup>1</sup> |
| Microsoft Edge              | Ja<sup>1</sup> |
| Microsoft Internet Explorer | Inga              |
| Mozilla Firefox             | Ja<sup>1</sup> |

 <small><sup>1</sup> Stöds i de två senaste produktionsversionerna.</small>

## <a name="next-steps"></a>Nästa steg

Läs mer om den nya utvecklarportalen:

- [Få tillgång till och anpassa den hanterade utvecklarportalen](api-management-howto-developer-portal-customize.md)
- [Konfigurera självvärdversion av portalen][2]
- [Implementera din egen widget][3]

Bläddra bland andra resurser:

- [GitHub-databas med källkoden][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend