---
title: Konfigurera din API Management-tjänst med Git - Azure | Microsoft-dokument
description: Lär dig hur du sparar och konfigurerar api management-tjänstkonfigurationen med Git.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: f948d813ddb4d493b455a4922818e38ac3fd6eaa
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259178"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Så gör du för att spara och konfigurera din API Management-tjänstkonfiguration med Git

Varje API Management-tjänstinstans har en konfigurationsdatabas som innehåller information om konfiguration och metadata för tjänstinstansen. Ändringar kan göras i tjänstinstansen genom att ändra en inställning i Azure-portalen, använda en PowerShell-cmdlet eller göra ett REST API-anrop. Utöver dessa metoder kan du även hantera konfigurationen av tjänstinstansen med Git, vilket aktiverar scenarier för tjänsthantering, till exempel:

* Konfigurationsversionering – hämta och lagra olika versioner av tjänstkonfigurationen
* Masskonfigurationsändringar – gör ändringar i flera delar av tjänstkonfigurationen i den lokala databasen och integrera ändringarna tillbaka till servern med en enda åtgärd
* Välbekanta Git-verktygskedja och arbetsflöden – använd Git-verktyg och arbetsflöden som du redan är bekant med

Följande diagram visar en översikt över de olika sätten att konfigurera din API Management-tjänstinstans.

![Git konfigurera][api-management-git-configure]

När du gör ändringar i tjänsten med Azure-portalen, PowerShell-cmdlets eller REST API `https://{name}.management.azure-api.net` hanterar du tjänstkonfigurationsdatabasen med hjälp av slutpunkten, som visas till höger i diagrammet. Den vänstra sidan av diagrammet illustrerar hur du kan hantera tjänstkonfigurationen med Git- och Git-databasen för din tjänst som finns på `https://{name}.scm.azure-api.net`.

Följande steg innehåller en översikt över hur du hanterar din API Management-tjänstinstans med Git.

1. Få tillgång till Git-konfiguration i din tjänst
2. Spara tjänstkonfigurationsdatabasen i Git-databasen
3. Klona Git-repoen till din lokala dator
4. Dra den senaste repoen ner till din lokala maskin och begå och driva ändringar tillbaka till din repo
5. Distribuera ändringarna från din repo till din tjänstkonfigurationsdatabas

I den här artikeln beskrivs hur du aktiverar och använder Git för att hantera tjänstkonfigurationen och innehåller en referens för filerna och mapparna i Git-databasen.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Få tillgång till Git-konfiguration i din tjänst

Om du vill visa och konfigurera konfigurationsinställningarna för Git kan du klicka på **säkerhetsmenyn** och navigera till fliken **Konfigurationsdatabas.**

![Aktivera GIT][api-management-enable-git]

> [!IMPORTANT]
> Alla hemligheter som inte definieras som namngivna värden lagras i databasen och kommer att finnas kvar i dess historik tills du inaktiverar och återaktiverar Git-åtkomst. Namngivna värden är en säker plats för att hantera konstanta strängvärden, inklusive hemligheter, i alla API-konfigurationer och principer, så att du inte behöver lagra dem direkt i dina principuttalanden. Mer information finns i [Så här använder du namngivna värden i Azure API Management-principer](api-management-howto-properties.md).
>
>

Information om hur du aktiverar eller inaktiverar Git-åtkomst med REST API finns i [Aktivera eller inaktivera Git-åtkomst med REST API](/rest/api/apimanagement/2019-12-01/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Så här sparar du tjänstkonfigurationen i Git-databasen

Det första steget innan du klonar databasen är att spara det aktuella tillståndet för tjänstkonfigurationen i databasen. Klicka på **Spara i databasen**.

Gör önskade ändringar på bekräftelseskärmen och klicka på **Ok** för att spara.

Efter en stund sparas konfigurationen och konfigurationsstatusen för databasen visas, inklusive datum och tid för den senaste konfigurationsändringen och den senaste synkroniseringen mellan tjänstkonfigurationen och databasen.

När konfigurationen har sparats i databasen kan den klonas.

Information om hur du utför den här åtgärden med REST API finns i [Commit-konfigurationsögonblicksbild med REST API](/rest/api/apimanagement/2019-12-01/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Så här klonar du databasen till den lokala datorn

Om du vill klona en databas behöver du URL:en till databasen, ett användarnamn och ett lösenord. Om du vill hämta användarnamn och andra autentiseringsuppgifter klickar du på **Åtkomstuppgifter** högst upp på sidan.

Om du vill generera ett lösenord kontrollerar du först att **förfallodatumet** är inställt på önskat utgångsdatum och tid och klickar sedan på **Generera**.

> [!IMPORTANT]
> Anteckna det här lösenordet. När du lämnar den här sidan visas inte lösenordet igen.
>

I följande exempel används verktyget Git Bash från [Git för Windows,](https://www.git-scm.com/downloads) men du kan använda vilket Git-verktyg som du är bekant med.

Öppna Git-verktyget i önskad mapp och kör följande kommando för att klona git-databasen till den lokala datorn med hjälp av kommandot som tillhandahålls av Azure-portalen.

```
git clone https://{name}.scm.azure-api.net/
```

Ange användarnamn och lösenord när du uppmanas att göra det.

Om du får några fel kan `git clone` du försöka ändra kommandot så att det innehåller användarnamnet och lösenordet, vilket visas i följande exempel.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Om detta ger ett fel kan du prova URL-kodning av lösenordsdelen av kommandot. Ett snabbt sätt att göra detta är att öppna Visual Studio och utfärda följande kommando i **det omedelbara fönstret**. Om du vill öppna **det omedelbara fönstret**öppnar du en lösning eller ett projekt i Visual Studio (eller skapar ett nytt tomt konsolprogram) och väljer **Windows**, **Omedelbar** på **Felsökningsmenyn.**

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

Använd det kodade lösenordet tillsammans med ditt användarnamn och din databasplats för att skapa kommandot git.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

När databasen har klonats kan du visa och arbeta med den i det lokala filsystemet. Mer information finns i [Referens för fil- och mappstruktur i den lokala Git-databasen](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Så här uppdaterar du den lokala databasen med den senaste tjänstinstanskonfigurationen

Om du gör ändringar i din API Management-tjänstinstans i Azure-portalen eller använder REST API måste du spara dessa ändringar i databasen innan du kan uppdatera den lokala databasen med de senaste ändringarna. Det gör du genom att klicka på **Spara konfiguration till databas** på fliken **Konfigurationsdatabas** i Azure-portalen och sedan utfärda följande kommando i din lokala databas.

```
git pull
```

Innan `git pull` du kör se till att du är i mappen för din lokala databas. Om du just `git clone` har slutfört kommandot måste du ändra katalogen till din repo genom att köra ett kommando som följande.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Om du vill driva ändringar från din lokala repo till servern repo
Om du vill skicka ändringar från den lokala databasen till serverdatabasen måste du genomföra ändringarna och sedan skicka dem till serverdatabasen. Om du vill genomföra ändringarna öppnar du kommandoverktyget Git, växlar till katalogen för den lokala databasen och utfärdar följande kommandon.

```
git add --all
git commit -m "Description of your changes"
```

Om du vill skicka alla åtaganden till servern kör du följande kommando.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Så här distribuerar du ändringar av tjänstkonfigurationen till API Management-tjänstinstansen

När dina lokala ändringar har genomförts och vidaresänts till serverdatabasen kan du distribuera dem till tjänstinstansen för API Management.

Information om hur du utför den här åtgärden med REST API finns i [Distribuera Git-ändringar i konfigurationsdatabasen med REST API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Fil- och mappstrukturreferens för lokal Git-databas

Filerna och mapparna i den lokala git-databasen innehåller konfigurationsinformation om tjänstinstansen.

| Objekt | Beskrivning |
| --- | --- |
| rot api-hanteringsmapp |Innehåller konfiguration på den högsta nivån för tjänstinstansen |
| apis-mappen |Innehåller konfigurationen för api:erna i tjänstinstansen |
| mappen grupper |Innehåller konfigurationen för grupperna i tjänstinstansen |
| mappen principer |Innehåller principerna i tjänstinstansen |
| portalStyles-mappen |Innehåller konfigurationen för utvecklarportalens anpassningar i tjänstinstansen |
| produktmapp |Innehåller konfigurationen för produkterna i tjänstinstansen |
| mallar mapp |Innehåller konfigurationen för e-postmallarna i tjänstinstansen |

Varje mapp kan innehålla en eller flera filer och i vissa fall en eller flera mappar, till exempel en mapp för varje API, produkt eller grupp. Filerna i varje mapp är specifika för den entitetstyp som beskrivs av mappnamnet.

| Filtyp | Syfte |
| --- | --- |
| json |Konfigurationsinformation om respektive entitet |
| html |Beskrivningar om entiteten, som ofta visas i utvecklarportalen |
| xml |Principrapporter |
| css |Formatmallar för anpassning av utvecklarportalen |

Dessa filer kan skapas, tas bort, redigeras och hanteras i det lokala filsystemet och ändringarna distribueras tillbaka till tjänstinstansen för API Management.

> [!NOTE]
> Följande entiteter finns inte i Git-databasen och kan inte konfigureras med Git.
>
> * [Användare](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/user)
> * [Prenumerationer](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription)
> * [Namngivna värden](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/property)
> * Andra utvecklarportalenheter än formatmallar
>

### <a name="root-api-management-folder"></a>Rot api-hanteringsmapp
Rotmappen `api-management` innehåller `configuration.json` en fil som innehåller information på den högsta nivån om tjänstinstansen i följande format.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

De första fyra`RegistrationEnabled` `UserRegistrationTerms`inställningarna ( , , `UserRegistrationTermsEnabled`och `UserRegistrationTermsConsentRequired`) mappas till följande inställningar på fliken **Identiteter** i avsnittet **Säkerhet.**

| Identitetsinställning | Kartor till |
| --- | --- |
| RegistrationEnabled |Närvaro av användarnamn och lösenordsidentitetsleverantör **Username and password** |
| AnvändareRegistrationTerms |**Användningsvillkor för** textruta för användaranmälning |
| UserRegistrationTermsEnabled |**Visa användningsvillkor på registreringssidan** |
| UserRegistrationTermsConsentRequired |**Kräv kryssrutan Begär medgivande** |
| KrävAnvändareSigninEnabled |**Omdirigera anonyma användare till** kryssrutan Logga in sida |

De följande fyra`DelegationEnabled` `DelegationUrl`inställningarna ( `DelegatedSubscriptionEnabled`, , och `DelegationValidationKey`) mappas till följande inställningar på fliken **Delegering** i avsnittet **Säkerhet.**

| Inställning för delegering | Kartor till |
| --- | --- |
| DelegationEnabled |**Kryssrutan Delegera inloggning & registreringsruta** |
| DelegationUrl (på ett år) |**Textruta för url-textruta för delegering av slutpunkt** |
| Delegerad PrenumerationEn kan nås |Kryssrutan **Delegera produktprenumeration** |
| DelegationValidationKey |**Textruta för delegera valideringsnyckel** |

Den slutliga `$ref-policy`inställningen mappas till den globala principutdragsfilen för tjänstinstansen.

### <a name="apis-folder"></a>apis-mappen
Mappen `apis` innehåller en mapp för varje API i tjänstinstansen, som innehåller följande objekt.

* `apis\<api name>\configuration.json`- Detta är konfigurationen för API och innehåller information om serverda tjänsten URL och åtgärder. Detta är samma information som skulle returneras om du skulle `export=true` `application/json` ringa Hämta ett [specifikt API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/apis/get) med i format.
* `apis\<api name>\api.description.html`- Detta är beskrivningen av API och `description` motsvarar egenskapen för [API-entiteten](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.entityproperty).
* `apis\<api name>\operations\`- Den här `<operation name>.description.html` mappen innehåller filer som mappas till åtgärderna i API:et. Varje fil innehåller en beskrivning av en enskild åtgärd `description` i API:et, som mappar till egenskapen för [operationsentiteten](https://docs.microsoft.com/rest/api/visualstudio/operations/list#operationproperties) i REST API.Each file contains the description of a single operation in the API, which maps to the property of the operation entity in the REST API.

### <a name="groups-folder"></a>mappen grupper
Mappen `groups` innehåller en mapp för varje grupp som definierats i tjänstinstansen.

* `groups\<group name>\configuration.json`- Det här är gruppens konfiguration. Det här är samma information som skulle returneras om du skulle anropa [åtgärden Hämta en viss grupp.](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/group/get)
* `groups\<group name>\description.html`- Detta är beskrivningen av gruppen och `description` motsvarar [egenskapen](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity)för koncernenheten .

### <a name="policies-folder"></a>mappen principer
Mappen `policies` innehåller principutdrag för tjänstinstansen.

* `policies\global.xml`- innehåller principer som definierats globalt för din tjänstinstans.
* `policies\apis\<api name>\`- Om du har några principer definierade på API-scope, finns de i den här mappen.
* `policies\apis\<api name>\<operation name>\`mapp - om du har några principer definierade vid åtgärdens `<operation name>.xml` omfattning, finns de i den här mappen i filer som mappas till principsatserna för varje åtgärd.
* `policies\products\`- Om du har några principer definierade i produktomfattning finns `<product name>.xml` de i den här mappen, som innehåller filer som mappas till principuttalanden för varje produkt.

### <a name="portalstyles-folder"></a>portalStyles-mappen
Mappen `portalStyles` innehåller konfigurations- och formatmallar för anpassningar av utvecklarportalen för tjänstinstansen.

* `portalStyles\configuration.json`- innehåller namnen på de formatmallar som används av utvecklarportalen
* `portalStyles\<style name>.css`- `<style name>.css` varje fil innehåller formatmallar`Preview.css` `Production.css` för utvecklarportalen (och som standard).

### <a name="products-folder"></a>produktmapp
Mappen `products` innehåller en mapp för varje produkt som definierats i tjänstinstansen.

* `products\<product name>\configuration.json`- Detta är konfigurationen för produkten. Detta är samma information som skulle returneras om du skulle ringa [hämta en viss produktåtgärd.](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/product/get)
* `products\<product name>\product.description.html`- detta är beskrivningen av produkten och `description` motsvarar [egenskapen](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) hos produktenheten i REST API.

### <a name="templates"></a>mallar
Mappen `templates` innehåller konfiguration för [tjänstinstansens e-postmallar.](api-management-howto-configure-notifications.md)

* `<template name>\configuration.json`- Detta är konfigurationen för e-postmallen.
* `<template name>\body.html`- Det här är e-postmallens brödtext.

## <a name="next-steps"></a>Nästa steg
Information om andra sätt att hantera din tjänstinstans finns i:

* Hantera din tjänstinstans med följande PowerShell-cmdlets
  * [Referens för PowerShell-cmdleten för tjänstdistribution](https://docs.microsoft.com/powershell/module/wds)
  * [Referens för PowerShell-cmdleten för tjänsthantering](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)
* Hantera din tjänstinstans med REST API
  * [API-referens för API-hantering för API](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




