---
title: Konfigurera API Management-tjänsten med hjälp av Git - Azure | Microsoft Docs
description: Lär dig mer om att spara och konfigurera din API Management-tjänstkonfigurationen med Git.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: adf4d8d5cfcef2dde8193ce1b7f2805a44e2d93d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657950"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Spara och konfigurera din API Management-tjänstkonfigurationen med Git

Varje API Management-tjänstinstans upprätthåller en databas som innehåller information om konfiguration och metadata för tjänstinstansen. Ändringar kan göras i tjänstinstansen genom att ändra en inställning i Azure portal, med hjälp av en PowerShell-cmdlet eller gör ett REST API-anrop. Utöver dessa metoder, kan du också hantera din instans tjänstkonfiguration med Git, aktivera scenarier för hantering av tjänsten som:

* Konfigurationen versionshantering – hämta och lagra olika versioner av dina tjänstkonfiguration
* Massregistrera konfigurationsändringar – göra ändringar i flera delar av din tjänstkonfiguration i din lokala lagringsplats och integrera ändringarna tillbaka till servern med en enda åtgärd
* Välbekanta Git-verktygskedja och arbetsflöde – använda Git-verktyg och arbetsflöden som du redan är bekant med

Följande diagram visar en översikt över olika sätt att konfigurera din API Management-tjänstinstans.

![Konfigurera Git][api-management-git-configure]

När du gör ändringar i dina tjänster med Azure portal, PowerShell-cmdletar eller REST API du hanterar din service configuration database med hjälp av den `https://{name}.management.azure-api.net` slutpunkt, som visas på höger sida av diagrammet. Till vänster i diagrammet visar hur du kan hantera din tjänstkonfiguration med hjälp av Git och Git-lagringsplats för din tjänst som finns på `https://{name}.scm.azure-api.net`.

I följande steg finns en översikt över hantering av din API Management-tjänstinstans med Git.

1. Konfiguration av Git i din tjänst
2. Spara din konfigurationsdatabas till Git-lagringsplatsen
3. Klona Git-lagringsplatsen till din lokala dator
4. Hämta den senaste lagringsplatsen till din lokala dator och commit och push-ändringarna tillbaka till din lagringsplats
5. Distribuera ändringarna från din lagringsplats till din konfigurationsdatabas

Den här artikeln beskriver hur du aktiverar och använder Git för att hantera din tjänstkonfiguration och innehåller en referens för filer och mappar i Git-lagringsplats.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Konfiguration av Git i din tjänst

Om du vill visa och konfigurera dina inställningar för Git, kan du klicka på den **Security** menyn och navigera till den **konfigurationsdatabasen** fliken.

![Aktivera GIT][api-management-enable-git]

> [!IMPORTANT]
> Hemligheter som inte har definierats som heter värden kommer att lagras i databasen och finns kvar i dess historik tills du inaktiverar och återaktiverar Git-åtkomst. Namngivna värden är en säker plats för att hantera konstant strängvärden, inklusive hemligheter, för alla API-konfiguration och principer, så att du inte behöver lagra dem direkt i din principrapporter. Mer information finns i [hur du använder med namnet värden i Azure API Management-principer](api-management-howto-properties.md).
>
>

Information om att aktivera eller inaktivera Git-åtkomst med hjälp av REST-API finns i [aktivera eller inaktivera Git-åtkomst med hjälp av REST-API](/rest/api/apimanagement/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Spara konfigurationen av Git-lagringsplatsen

Det första steget innan kloning databasen är att spara det aktuella tillståndet för konfigurationen av i databasen. Klicka på **spara databasen**.

Gör eventuella ändringar på bekräftelseskärmen och klicka på **Ok** att spara.

Efter en liten stund konfigurationen sparas och visas status för konfiguration av databasen, inklusive datum och tid för senaste konfigurationsändring och den senaste synkroniseringen mellan tjänstkonfigurationen och databasen.

När konfigurationen har sparats i databasen, kan det klonas.

Information om hur du utför den här åtgärden med hjälp av REST-API finns i [Commit configuration ögonblicksbild med hjälp av REST API](/rest/api/apimanagement/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Klona lagringsplatsen till din lokala dator

Om du vill klona en lagringsplats, måste URL: en till din lagringsplats, ett användarnamn och ett lösenord. För att få användarnamn och andra autentiseringsuppgifter, klickar du på **autentiseringsuppgifter för åtkomst** överst på sidan.

Om du vill skapa ett lösenord du först kontrollera att den **Förfallohändelser** är inställd på önskad utgångsdatum och förfallotid och klicka sedan på **generera**.

> [!IMPORTANT]
> Anteckna det här lösenordet. När du lämnar den här sidan visas inte lösenordet igen.
>

I följande exempel används Git Bash-verktyget från [Git för Windows](https://www.git-scm.com/downloads) men du kan använda valfri Git-verktyg som du är bekant med.

Öppna Git-verktyget i mappen önskad och kör följande kommando för att klona git-lagringsplatsen till den lokala datorn med hjälp av kommandot som tillhandahålls av Azure-portalen.

```
git clone https://{name}.scm.azure-api.net/
```

Ange användarnamn och lösenord när du uppmanas till detta.

Om du får några fel, försök att ändra din `git clone` kommando för att inkludera användarnamn och lösenord, som visas i följande exempel.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Om detta ger ett fel, försök URL-kodning delar av lösenord för kommandot. Ett snabbt sätt att göra detta är att öppna Visual Studio och kör du följande kommando i den **kommandofönstret**. Öppna den **kommandofönstret**, öppna någon lösning eller projekt i Visual Studio (eller skapa ett nytt tomt konsolprogram), och välj **Windows**, **Immediate** från den **Felsöka** menyn.

```
?System.NetWebUtility.UrlEncode("password from the Azure portal")
```

Använda det kodade lösenordet tillsammans med din Användarplats för namn och databasen för att konstruera git-kommando.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

När databasen är klonad, kan du visa och arbeta med dem i ditt lokala filsystem. Mer information finns i [fil- och strukturera referens för lokal Git-lagringsplats](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Uppdatera din lokala lagringsplats med den senaste instansen tjänstkonfigurationen

Om du gör ändringar i din API Management-tjänstinstans i Azure portal eller med hjälp av REST-API, måste du spara ändringarna i databasen innan du kan uppdatera din lokala lagringsplats med de senaste ändringarna. Gör detta genom att klicka på **spara konfigurationen till lagringsplatsen** på den **konfigurationsdatabasen** i Azure portal och sedan kör du följande kommando i din lokala lagringsplats.

```
git pull
```

Innan du kör `git pull` se till att du är i mappen för din lokala lagringsplats. Om du är klar med den `git clone` kommandot, och sedan måste du ändra katalogen till din lagringsplats genom att köra ett kommando som liknar följande.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Att skicka ändringar från ditt lokala lager till lagringsplatsen för server
Om du vill skicka ändringar från din lokala lagringsplats till server-databasen, måste du spara ändringarna och sedan skicka dem till server-databasen. Öppna ditt verktyg för Git-kommando för att genomföra ändringarna, växla till katalogen för din lokala lagringsplats och utfärda följande kommandon.

```
git add --all
git commit -m "Description of your changes"
```

För att skicka alla allokeringarna till servern, kör du följande kommando.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Att distribuera konfigurationsändringar service till API Management-tjänstinstans

När din lokala ändringar är allokerade och skickas till server-databasen, kan du distribuera dem till din API Management-tjänstinstans.

Information om hur du utför den här åtgärden med hjälp av REST-API finns i [distribuera Git ändras till konfigurationsdatabasen med hjälp av REST API](https://docs.microsoft.com/rest/api/apimanagement/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Referens för fil- och strukturen för lokal Git-lagringsplats

Filer och mappar i den lokala git-lagringsplatsen innehåller konfigurationsinformation om tjänstinstansen.

| Objekt | Beskrivning |
| --- | --- |
| rotmapp för api-hantering |Innehåller översta konfigurationen för tjänstinstansen |
| API: er för mappen |Innehåller konfigurationen för API: er i tjänstinstansen |
| mappen grupper |Innehåller konfiguration för grupper i tjänstinstansen |
| för principmappen |Innehåller principerna i tjänstinstansen |
| portalStyles mapp |Innehåller konfiguration för utvecklare portalanpassningar i tjänstinstansen |
| produkter mapp |Innehåller konfiguration för produkter i tjänstinstansen |
| mallmappen |Innehåller konfigurationen för de e-postmallar i tjänstinstansen |

Varje mapp kan innehålla en eller flera filer och i vissa fall en eller flera mappar, till exempel en mapp för varje API, produkt eller grupp. Filer i varje mapp är specifika för enhetstypen som beskrivs av mappnamnet.

| Filtyp | Syfte |
| --- | --- |
| JSON |Konfigurationsinformation om respektive entiteten |
| html |Beskrivningar av entiteten, som ofta visas i developer-portalen |
| xml |Principrapporter |
| css |Formatmallar för anpassning av utvecklare |

Dessa filer kan skapas, tas bort, redigera och hanteras på det lokala filsystemet och ändringarna distribueras tillbaka till din API Management-tjänstinstans.

> [!NOTE]
> Följande entiteter finns inte i Git-lagringsplats och kan inte konfigureras med Git.
>
> * [Användare](https://docs.microsoft.com/en-us/rest/api/apimanagement/user)
> * [Prenumerationer](https://docs.microsoft.com/en-us/rest/api/apimanagement/subscription)
> * [Namngivna värden](https://docs.microsoft.com/en-us/rest/api/apimanagement/property)
> * Developer portal enheter än format
>

### <a name="root-api-management-folder"></a>rotmapp för api-hantering
Roten `api-management` mappen innehåller en `configuration.json` -fil som innehåller översta information om tjänstinstansen i följande format.

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

De första fyra inställningarna (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, och `UserRegistrationTermsConsentRequired`) mappas till följande inställningar på den **identiteter** fliken i den **Security** avsnittet.

| Identitet har värdet | Mappar till |
| --- | --- |
| RegistrationEnabled |Förekomst av **användarnamn och lösenord** identitetsprovider |
| UserRegistrationTerms |**Användningsvillkor vid användarregistrering** textrutan |
| UserRegistrationTermsEnabled |**Visa användningsvillkor på registreringssidan** kryssrutan |
| UserRegistrationTermsConsentRequired |**Kräver godkännande** kryssrutan |
| RequireUserSigninEnabled |**Omdirigera anonyma användare till inloggningssidan** kryssrutan |

Följande fyra inställningar (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, och `DelegationValidationKey`) mappas till följande inställningar på den **delegering** fliken i den **Security** avsnittet.

| Delegeringsinställningen | Mappar till |
| --- | --- |
| DelegationEnabled |**Delegera inloggning och registrering** kryssrutan |
| DelegationUrl |**URL för delegeringsslutpunkt** textrutan |
| DelegatedSubscriptionEnabled |**Delegera produktprenumeration** kryssrutan |
| DelegationValidationKey |**Delegera valideringsnyckel** textrutan |

Den slutliga inställningen `$ref-policy`, mappar till den globala principfilen instruktioner för tjänstinstansen.

### <a name="apis-folder"></a>API: er för mappen
Den `apis` mappen innehåller en mapp för varje API i tjänstinstansen, som innehåller följande objekt.

* `apis\<api name>\configuration.json` -Detta är konfigurationen för API: et och innehåller information om URL: en för backend-tjänsten och åtgärderna. Det här är samma information som skulle returneras om du anropar [hämta ett visst API](https://docs.microsoft.com/rest/api/apimanagement/apis/get) med `export=true` i `application/json` format.
* `apis\<api name>\api.description.html` -Detta är beskrivningen av API: et och motsvarar den `description` egenskapen för den [API-entiteten](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._entity_property).
* `apis\<api name>\operations\` – den här mappen innehåller `<operation name>.description.html` filer som mappar till åtgärder i API: et. Varje fil innehåller en beskrivning av en enda åtgärd i API: T, som mappar till den `description` egenskapen för den [åtgärden entitet](https://docs.microsoft.com/rest/api/visualstudio/operations/list#operationproperties) i REST-API.

### <a name="groups-folder"></a>mappen grupper
Den `groups` mappen innehåller en mapp för varje grupp som definierats i tjänstinstansen.

* `groups\<group name>\configuration.json` -Detta är konfigurationen för gruppen. Det här är samma information som skulle returneras om du anropar den [hämta en specifik grupp](https://docs.microsoft.com/rest/api/apimanagement/group/get) igen.
* `groups\<group name>\description.html` -Detta är beskrivningen av gruppen och motsvarar den `description` egenskapen för den [gruppen entitet](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity).

### <a name="policies-folder"></a>för principmappen
Den `policies` mappen innehåller principrapporter för din tjänstinstans.

* `policies\global.xml` -innehåller principer som definierats för det globala området för din tjänstinstans.
* `policies\apis\<api name>\` -Om du har några principer som definierats i omfattningen för API kan de ingår i den här mappen.
* `policies\apis\<api name>\<operation name>\` mapp - om du har några definierade principer på åtgärdsomfång kan de ingår i den här mappen i `<operation name>.xml` filer som mappar till principrapporter för varje åtgärd.
* `policies\products\` -Om du har några definierade principer på produktomfattningsnivå kan de ingår i den här mappen som innehåller `<product name>.xml` filer som mappar till principrapporter för varje produkt.

### <a name="portalstyles-folder"></a>portalStyles mapp
Den `portalStyles` mappen innehåller konfigurations- och style sheets developer portal anpassningar för tjänstinstansen.

* `portalStyles\configuration.json` -innehåller namnen på de formatmallar som används på utvecklarportalen
* `portalStyles\<style name>.css` -varje `<style name>.css` filen innehåller format för utvecklarportalen (`Preview.css` och `Production.css` som standard).

### <a name="products-folder"></a>produkter mapp
Den `products` mappen innehåller en mapp för varje produkt som definierats i tjänstinstansen.

* `products\<product name>\configuration.json` -Detta är konfigurationen för produkten. Det här är samma information som skulle returneras om du anropar den [hämta en specifik produkt](https://docs.microsoft.com/rest/api/apimanagement/product/get) igen.
* `products\<product name>\product.description.html` -Detta är beskrivningen av produkten och motsvarar den `description` egenskapen för den [produktentiteten](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) i REST-API.

### <a name="templates"></a>mallar
Den `templates` mappen innehåller konfigurationen för den [e-postmallar](api-management-howto-configure-notifications.md) för tjänstinstansen.

* `<template name>\configuration.json` -Detta är konfigurationen för e-postmallen.
* `<template name>\body.html` – Det här är brödtexten i e-postmallen.

## <a name="next-steps"></a>Nästa steg
Information om andra sätt att hantera din tjänstinstans finns i:

* Hantera din tjänstinstans med följande PowerShell-cmdletar
  * [Tjänstdistributionen PowerShell cmdlet-referens](https://docs.microsoft.com/powershell/module/wds)
  * [Service management PowerShell cmdlet-referens](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)
* Hantera din tjänstinstans med hjälp av REST-API
  * [API Management REST API-referens](/rest/api/apimanagement/)


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




