---
title: Konfigurera din API Management-tjänsten med Git - Azure | Microsoft Docs
description: Lär dig mer om att spara och konfigurera din API Management service configuration med Git.
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
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: 0165de82850c0c80052564c5f31a5e5cf5effb11
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938316"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Spara och konfigurera din API Management service configuration med Git

Varje instans för API Management-tjänsten har en databas som innehåller information om konfiguration och metadata för tjänstinstansen. Ändringar kan göras i tjänstinstansen genom att ändra en inställning i Azure-portalen, med hjälp av en PowerShell-cmdlet eller göra ett REST API-anrop. Du kan också hantera konfigurationen för service-instans med Git, aktivera tjänsten hanteringsscenarier som förutom dessa metoder:

* Konfigurationen versionshantering - ladda ned och lagra olika versioner av tjänstkonfigurationen av
* Massredigera konfigurationsändringar – göra ändringar i flera delar av din tjänstkonfiguration i lokala databasen och integrera ändringarna tillbaka till servern med en enda åtgärd
* Bekant Git toolchain och arbetsflöde – använda Git verktygsuppsättning och arbetsflöden som du redan är bekant med

Följande diagram visar en översikt över olika sätt att konfigurera din instans för API Management-tjänsten.

![Konfigurera Git][api-management-git-configure]

När du gör ändringar i din tjänst med hjälp av Azure portal, PowerShell-cmdlets eller REST API du hanterar din service configuration databas med hjälp av den `https://{name}.management.azure-api.net` slutpunkt, som visas till höger i diagrammet. Till vänster i diagrammet visar hur du kan hantera tjänstkonfigurationen av med Git och Git-lagringsplats för tjänsten finns på `https://{name}.scm.azure-api.net`.

Följande steg ger en översikt över hantering av din API Management service-instans med Git.

1. Konfiguration av Git i din tjänst
2. Spara dina konfigurationsdatabas Git-lagringsplatsen
3. Klona Git-lagringsplatsen till den lokala datorn
4. Hämta senaste lagringsplatsen till den lokala datorn och genomförande och push ändringarna tillbaka till din lagringsplatsen
5. Distribuera ändringarna från din lagringsplats i din konfigurationsdatabas

Den här artikeln beskriver hur du aktiverar och använder Git för att hantera tjänstkonfigurationen av och innehåller en referens för filer och mappar i Git-lagringsplats.

## <a name="access-git-configuration-in-your-service"></a>Konfiguration av Git i din tjänst

Om du vill visa och konfigurera inställningarna i Git, kan du klicka på den **säkerhet** menyn och navigera till den **Configuration databasen** fliken.

![Aktivera GIT][api-management-enable-git]

> [!IMPORTANT]
> Alla hemligheter som inte har definierats som egenskaper kommer att lagras i databasen och kommer att finnas kvar i historiken tills du inaktivera och aktivera Git-åtkomst. Egenskaper innehåller en säker plats för att hantera konstanta strängvärden, inklusive hemligheter, för alla API-konfiguration och principer, så att du inte behöver lagra dem direkt i din principrapporter. Mer information finns i [använda egenskaper i Azure API Management-principer](api-management-howto-properties.md).
> 
> 

Mer information om att aktivera eller inaktivera Git-åtkomst med hjälp av REST-API finns [aktivera eller inaktivera Git-åtkomst med hjälp av REST API](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Spara konfigurationen för tjänsten i Git-lagringsplats

Det första steget innan kloning databasen är att spara det aktuella tillståndet för tjänstens konfiguration i databasen. Klicka på **spara databasen**.

Gör eventuella ändringar på bekräftelsesidan och klicka på **Ok** att spara.

Efter en liten stund konfigurationen sparas och visas status för konfiguration av databasen, inklusive datum och tid för senaste konfigurationsändringen och den senaste synkroniseringen mellan konfigurationen för tjänsten och -databasen.

När konfigurationen har sparats i databasen, kan den klonas.

Information om hur du utför den här åtgärden med hjälp av REST-API finns [Commit configuration ögonblicksbild med hjälp av REST-API](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Att klona databasen på den lokala datorn

Om du vill klona en databas måste URL: en till databasen, ett användarnamn och ett lösenord. För att få användarnamn och andra autentiseringsuppgifter, klickar du på **åt autentiseringsuppgifterna** överst på sidan.  
 
För att generera ett lösenord måste du först kontrollera att den **upphör att gälla** är inställd på önskad utgångsdatum och utgångstid och klicka sedan på **generera**.

> [!IMPORTANT]
> Anteckna det här lösenordet. När du lämnar den här sidan visas inte lösenordet igen.
> 

I följande exempel används verktyget Git Bash från [Git för Windows](http://www.git-scm.com/downloads) men du kan använda alla Git-verktyg som du är bekant med.

Öppna Git-verktyget i mappen önskade och kör följande kommando för att klona git-lagringsplatsen till den lokala datorn med hjälp av kommandot som tillhandahålls av Azure-portalen.

```
git clone https://bugbashdev4.scm.azure-api.net/
```

Ange användarnamn och lösenord när du uppmanas.

Om du får inga fel, försök att ändra din `git clone` kommando för att inkludera användarnamn och lösenord, som visas i följande exempel.

```
git clone https://username:password@bugbashdev4.scm.azure-api.net/
```

Om detta ger ett fel kan du försöka URL kodning lösenord delen av kommandot. Ett snabbt sätt att göra detta är att öppna Visual Studio och kör följande kommando i den **kommandofönstret**. Öppna den **kommandofönstret**, öppna någon lösning eller ett projekt i Visual Studio (eller skapa en ny tom konsolapp), och välj **Windows**, **Immediate** från den **Felsöka** menyn.

```
?System.NetWebUtility.UrlEncode("password from the Azure portal")
```

Använda kodade lösenordet tillsammans med din Användarplats för namn och databasen för att skapa git-kommandot.

```
git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/
```

När databasen är klonad kan du visa och arbeta med det i det lokala filsystemet. Mer information finns i [fil- och struktur referens för lokal Git-lagringsplats](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Uppdatera din lokal databas med aktuell instans konfigurationen för tjänsten

Om du gör ändringar i API Management service-instans i Azure-portalen eller med hjälp av REST-API måste du spara dessa ändringar till databasen innan du kan uppdatera din lokala databasen med de senaste ändringarna. Gör detta genom att klicka på **spara konfigurationen till databasen** på den **Configuration databasen** i Azure-portalen och sedan kör du följande kommando i en lokal databas.

```
git pull
```

Innan du kör `git pull` så att du är i mappen för din lokala databas. Om du precis har slutfört den `git clone` kommando, och sedan måste du ändra katalogen till din lagringsplats genom att köra ett kommando som följande.

```
cd bugbashdev4.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Att skicka ändringar från din lokala lagringsplatsen till lagringsplatsen för server
För att skicka ändringar från din lokala databasen till server-databasen, måste du genomför ändringarna och skicka dem till server-databasen. Öppna kommandoradsverktyg för Git för att genomföra ändringarna, växla till katalogen för lokala databasen och utfärda följande kommandon.

```
git add --all
git commit -m "Description of your changes"
```

För att vidarebefordra alla incheckningar till servern, kör du följande kommando.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Att distribuera konfigurationsändringar service till instansen för API Management-tjänsten

När din lokala ändringar allokerat och pushas till server-databasen, kan du distribuera dem till din API Management service-instans.

Information om hur du utför den här åtgärden med hjälp av REST-API finns [distribuera Git ändras till konfigurationsdatabasen med hjälp av REST API](https://docs.microsoft.com/rest/api/apimanagement/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Fil- och structure-referens för lokal Git-lagringsplats

Filer och mappar i den lokala git-lagringsplatsen innehåller konfigurationsinformation om tjänstinstansen.

| Objekt | Beskrivning |
| --- | --- |
| rotmapp för api management |Innehåller översta konfiguration för tjänstinstansen |
| API: er för mappen |Innehåller konfiguration för API: er i instansen för tjänsten |
| mappen grupper |Innehåller konfiguration för grupper i tjänstinstansen |
| för principmappen |Innehåller principer i tjänstinstansen |
| portalStyles mapp |Innehåller konfiguration för developer portal anpassningar i tjänstinstansen |
| produkter mapp |Innehåller konfiguration för produkter i tjänstinstansen |
| mallmappen |Innehåller konfiguration för e-mallarna i tjänstinstansen |

Varje mapp kan innehålla en eller flera filer och i vissa fall en eller flera mappar, till exempel en mapp för varje API, produkt eller grupp. Filerna i mappen är specifika för entitetstypen beskrivs av mappnamnet.

| Filtyp | Syfte |
| --- | --- |
| JSON |Konfigurationsinformation om respektive enhet |
| HTML |Beskrivningar av entiteten ofta visas i developer-portalen |
| xml |Principrapporter |
| CSS |Formatmallar för developer portal anpassning |

Dessa filer kan skapas, tas bort, redigera och hanteras inte i det lokala filsystemet och ändringarna distribueras tillbaka till den din API Management service-instans.

> [!NOTE]
> Följande enheter finns inte i Git-lagringsplats och kan inte konfigureras med Git.
> 
> * Användare
> * Prenumerationer
> * Egenskaper
> * Developer portal enheter än format
> 

### <a name="root-api-management-folder"></a>Rotmapp för api management
Roten `api-management` mappen innehåller en `configuration.json` -fil som innehåller översta information om instansen för tjänsten i följande format.

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
    "DelegationValidationKey": ""
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

De fyra första inställningarna (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, och `UserRegistrationTermsConsentRequired`) mappas till följande inställningar på den **identiteter** fliken i den **säkerhet** avsnitt.

| Identity-inställning | Mappas till |
| --- | --- |
| RegistrationEnabled |**Omdirigera anonyma användare till inloggningssidan** kryssruta |
| UserRegistrationTerms |**Villkor för användning på användaren signup** textruta |
| UserRegistrationTermsEnabled |**Visa användningsvillkoren på inloggningssidan** kryssruta |
| UserRegistrationTermsConsentRequired |**Kräv godkännande** kryssruta |

Inställningarna för följande fyra (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, och `DelegationValidationKey`) mappas till följande inställningar på den **delegering** fliken i den **säkerhet** avsnitt.

| Delegeringsinställningen | Mappas till |
| --- | --- |
| DelegationEnabled |**Delegera inloggning & anmälan** kryssruta |
| DelegationUrl |**Delegering slutpunkts-URL** textruta |
| DelegatedSubscriptionEnabled |**Delegera produkten prenumeration** kryssruta |
| DelegationValidationKey |**Delegera valideringsnyckel** textruta |

Den sista inställningen `$ref-policy`, mappar till den globala principfilen instruktioner för tjänstinstansen.

### <a name="apis-folder"></a>API: er för mappen
Den `apis` mappen innehåller en mapp för varje API i tjänstinstansen som innehåller följande objekt.

* `apis\<api name>\configuration.json` -Detta är konfigurationen för API: et och innehåller information om URL: en för backend-tjänsten och åtgärder. Det här är samma information som returneras om du anropa [få en specifika API: et](https://docs.microsoft.com/en-us/rest/api/apimanagement/api/get) med `export=true` i `application/json` format.
* `apis\<api name>\api.description.html` -Detta är en beskrivning av API: et och motsvarar den `description` -egenskapen för den [API entiteten](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.table._entity_property).
* `apis\<api name>\operations\` -den här mappen innehåller `<operation name>.description.html` filer som mappar till åtgärder i API: et. Varje fil som innehåller en beskrivning av en enda åtgärd i API som mappar till den `description` -egenskapen för den [åtgärden entiteten](https://docs.microsoft.com/en-us/rest/api/visualstudio/operations/list#operationproperties) i REST-API.

### <a name="groups-folder"></a>mappen grupper
Den `groups` mappen innehåller en mapp för varje grupp som definierats i tjänstinstansen.

* `groups\<group name>\configuration.json` -Detta är konfigurationen för gruppen. Det här är samma information som returneras om du anropar den [få en specifik grupp](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) igen.
* `groups\<group name>\description.html` -Detta är en beskrivning av gruppen som motsvarar den `description` -egenskapen för den [gruppen entiteten](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>för principmappen
Den `policies` mappen innehåller hanteringsprinciper för service-instans.

* `policies\global.xml` -innehåller principer som definierats vid globalt scope för service-instans.
* `policies\apis\<api name>\` -Om du har några definierade principer på API-scope, de ingår i den här mappen.
* `policies\apis\<api name>\<operation name>\` mapp - om du har några definierade principer på åtgärdsomfånget de ingår i den här mappen i `<operation name>.xml` filer som mappas till principrapporter för varje åtgärd.
* `policies\products\` -Om du har några principer som definierats i produktomfattningen de ingår i den här mappen som innehåller `<product name>.xml` filer som mappas till principrapporter för varje produkt.

### <a name="portalstyles-folder"></a>portalStyles mapp
Den `portalStyles` mappen innehåller konfiguration och style sheets developer portal anpassningar för tjänstinstansen.

* `portalStyles\configuration.json` -innehåller namnen på de formatmallar som används av developer-portalen
* `portalStyles\<style name>.css` -varje `<style name>.css` filen innehåller format för developer-portalen (`Preview.css` och `Production.css` som standard).

### <a name="products-folder"></a>produkter mapp
Den `products` mappen innehåller en mapp för varje produkt som definierats i tjänstinstansen.

* `products\<product name>\configuration.json` -Detta är konfigurationen för produkten. Det här är samma information som returneras om du anropar den [få en specifik produkt](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) igen.
* `products\<product name>\product.description.html` -Detta är en beskrivning av produkten som motsvarar den `description` -egenskapen för den [entiteten för produkt](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) i REST-API.

### <a name="templates"></a>mallar
Den `templates` mappen innehåller konfigurationen för den [e-mallar](api-management-howto-configure-notifications.md) i tjänstinstansen.

* `<template name>\configuration.json` -Detta är konfigurationen för e-postmallen.
* `<template name>\body.html` -Detta är innehållet i e-postmallen.

## <a name="next-steps"></a>Nästa steg
Information om andra sätt att hantera din tjänstinstansen finns:

* Hantera din service-instans med hjälp av följande PowerShell-cmdlets
  * [Tjänstdistributionen PowerShell cmdlet-referens](https://msdn.microsoft.com/library/azure/mt619282.aspx)
  * [Service management PowerShell-cmdlet-referens](https://msdn.microsoft.com/library/azure/mt613507.aspx)
* Hantera din service-instans med hjälp av REST-API
  * [API Management REST API-referens](https://msdn.microsoft.com/library/azure/dn776326.aspx)


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




