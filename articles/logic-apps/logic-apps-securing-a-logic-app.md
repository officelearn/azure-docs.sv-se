---
title: Säker åtkomst till Azure Logic Apps | Microsoft Docs
description: Lägger till säkerhet för att skydda åtkomsten till utlösare, indata och utdata, åtgärdsparametrar och tjänster som används med arbetsflöden i Azure Logic Apps.
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: ''
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 2042fdaa037fe1928fdb81727968a532ddfae0a6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="secure-access-to-your-logic-apps"></a>Säker åtkomst till dina logic apps

Det finns många verktyg som är tillgängliga för att skydda din logikapp.

* Skydda åtkomsten till att utlösa en logikapp (http-begäran utlösare)
* Skydda åtkomsten till hantera, redigera eller läsa en logikapp
* Skydda åtkomsten till innehållet i indata och utdata för en körning
* Att säkra parametrar eller indata i åtgärder i ett arbetsflöde
* Skydda åtkomsten till tjänster som tar emot förfrågningar från ett arbetsflöde

## <a name="secure-access-to-trigger"></a>Säker åtkomst till utlösa

När du arbetar med en logikapp som utlöses på en HTTP-begäran ([begära](../connectors/connectors-native-reqres.md) eller [Webhook](../connectors/connectors-native-webhook.md)), kan du begränsa åtkomsten så att endast auktoriserade klienter kan utlösa logikappen. Alla förfrågningar till en logikapp krypteras och skyddas via SSL.

### <a name="shared-access-signature"></a>Signatur för delad åtkomst

Varje begäran slutpunkt för en logikapp innehåller en [delade signatur åtkomst (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) som en del av URL: en. Varje URL innehåller en `sp`, `sv`, och `sig` Frågeparametern. Behörigheter som anges av `sp`, och motsvarar HTTP-metoder tillåts, `sv` är den version som används för att generera, och `sig` används för att autentisera åtkomsten till utlösa. Signaturen skapas med en hemlig nyckel på den URL-sökvägar och egenskaperna SHA256-algoritmen. Den hemliga nyckeln är aldrig exponeras och publiceras, och hålls krypterade och lagras som en del av logikappen. Din logikapp tillåter endast utlösare som innehåller en giltig signatur som skapats med den hemliga nyckeln.

#### <a name="regenerate-access-keys"></a>Återskapa åtkomstnycklar

Du kan återskapa en ny säker nyckel vid när som helst via REST API- eller Azure-portalen. Alla aktuella URL: er som har skapats tidigare med den gamla nyckeln är ogiltig och inte längre behörighet eller logikappen.

1. Öppna logikappen som du vill återskapa en nyckel i Azure-portalen
1. Klicka på den **åtkomstnycklar** menyalternativet **inställningar**
1. Välj för att återskapa och slutföra processen

URL: er som du hämtar efter återskapandet signeras med den nya åtkomstnyckeln.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Skapa återanrop URL: er med ett förfallodatum

Om du delar URL: en med andra parter kan generera du URL: er med specifika nycklar och förfallodatum efter behov. Du kan sedan sömlöst Återställ nycklar eller kontrollera åtkomsten eller en app är begränsad till en viss timespan. Du kan ange ett sista giltighetsdatum för en URL till den [logikappar REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers):

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

I själva brödtexten, innehåller egenskapen `NotAfter` som en JSON-datumsträng som returnerar en motringning-URL som är endast giltig förrän den `NotAfter` datum och tid.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Skapa URL: er med primär eller sekundär hemlig nyckel

När du generera eller listan återanrop URL: er för frågebaserad utlösare kan ange du också vilka nyckel ska användas för att logga in URL: en.  Du kan generera en URL som signerats av en viss nyckel via den [logikappar REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) på följande sätt:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

I själva brödtexten, innehåller egenskapen `KeyType` som antingen `Primary` eller `Secondary`.  Detta returnerar en URL som signerats av säker nyckeln som anges.

### <a name="restrict-incoming-ip-addresses"></a>Begränsa inkommande IP-adresser

Förutom den signatur för delad åtkomst, kan du begränsa anropar en logikapp endast från specifika klienter.  Du kan till exempel begränsa logikappen så att endast acceptera begäran när begäran kommer från IP-adress för API Management-instans om du hanterar din slutpunkt via Azure API Management.

Den här inställningen kan konfigureras i logik app-inställningar:

1. Öppna logikappen som du vill lägga till IP-adressbegränsningar i Azure-portalen
1. Klicka på den **inställningar för arbetsflöde** menyalternativet **inställningar**
1. Ange en lista över IP-adressintervall som accepteras av utlösaren

En giltig IP-adressintervall har formatet `192.168.1.1/255`. Om du vill logikappen eller endast som en kapslad logikapp, Välj den **andra logikappar** alternativet. Det här alternativet skrivs en tom matris till resursen, betydelse endast anropas från tjänsten sig själv (överordnade logikappar) eller har.

> [!NOTE]
> Du kan fortfarande köra en logikapp med en utlösare för begäran via REST API / Management `/triggers/{triggerName}/run` oavsett IP. Det här scenariot kräver autentisering mot Azure REST-API och alla händelser som visas i Azure granskningsloggen. Ange åtkomstkontroll principer därefter.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Ange IP-intervall i resursdefinitionen

Om du använder en [Distributionsmall](logic-apps-create-deploy-template.md) för att automatisera dina distributioner, IP-adressintervall inställningar kan konfigureras på resource-mallen.  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "triggers": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Lägga till Azure Active Directory, OAuth eller annan säkerhet

Att lägga till flera auktorisering protokoll ovanpå en logikapp [Azure API Management](https://azure.microsoft.com/services/api-management/) erbjuder omfattande övervakning, säkerhet, principer och för valfri slutpunkt med möjlighet att exponera en logikapp som en API-dokumentationen. Azure API Management kan exponera en offentlig eller privat slutpunkt för logikappen som kan använda Azure Active Directory, certifikat, OAuth eller andra säkerhetskrav. När en begäran tas emot vidarebefordrar Azure API Management begäran till logikappen (utför alla nödvändiga transformationer eller begränsningar relä). Du kan använda inställningar för inkommande IP-intervall på logikappen att endast tillåta logikappen ska utlösas från API-hantering.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>Säker åtkomst för att hantera eller redigera logikappar

Du kan begränsa åtkomsten till hanteringsåtgärder på en logikapp så att bara vissa användare eller grupper kan utföra åtgärder på resursen. Logikappar använder Azure [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md) funktion och kan anpassas med samma verktyg.  Det finns några inbyggda roller som du kan tilldela medlemmar i din prenumeration samt:

* **Logik App deltagare** -ger möjlighet att visa, redigera och uppdatera en logikapp.  Det går inte att ta bort resursen eller utföra administrativa åtgärder.
* **Logik App operatorn** – kan visa logikappen och kör historik och aktivera/inaktivera.  Det går inte att redigera eller uppdatera definitionen.

Du kan också använda [Azure Resource Lås](../azure-resource-manager/resource-group-lock-resources.md) att förhindra att ändra eller ta bort logikappar. Den här funktionen är användbar för att förhindra produktionsresurser från ändringar eller borttagningar.

## <a name="secure-access-to-contents-of-the-run-history"></a>Säker åtkomst till innehållet i körningshistoriken

Du kan begränsa åtkomsten till innehållet i in- eller utdata från tidigare körs till specifika IP-adressintervall.  

Alla data i ett arbetsflöde kör krypteras under överföring och i vila. När ett anrop till kör historik görs tjänsten autentiserar en begäran och innehåller länkar till förfrågan och svar indata och utdata. Den här länken kan skyddas så att endast begäranden om att visa innehåll från ett IP-adressintervall returnera innehållet. Du kan använda den här funktionen för ytterligare behörighet. Du kan även ange en IP-adress som `0.0.0.0` så ingen kan komma åt indata/utdata. Endast användare med administratörsbehörigheter kan ta bort den här begränsningen att tillhandahålla möjligheten för 'just-in-time-åtkomst till innehåll för arbetsflödet.

Den här inställningen kan konfigureras i resursinställningar på Azure-portalen:

1. Öppna logikappen som du vill lägga till IP-adressbegränsningar i Azure-portalen
1. Klicka på den **konfiguration för behörighetskontroll** menyalternativet **inställningar**
1. Ange en lista över IP-adressintervall för åtkomst till innehåll

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Ange IP-intervall i resursdefinitionen

Om du använder en [Distributionsmall](logic-apps-create-deploy-template.md) för att automatisera dina distributioner, IP-adressintervall inställningar kan konfigureras på resource-mallen.  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "contents": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Säker parametrar och indata i ett arbetsflöde

Du kanske vill parameterstyra vissa aspekter av en arbetsflödesdefinition för distribution i miljöer. Vissa parametrar kan också vara säkra parametrar som du inte vill ska visas när du redigerar ett arbetsflöde, till exempel en klient-ID och klienthemlighet för [Azure Active Directory-autentisering](../connectors/connectors-native-http.md#authentication) av en HTTP-åtgärd.

### <a name="using-parameters-and-secure-parameters"></a>Med hjälp av parametrarna och säker

Åtkomst till värdet för en resursparametern vid körning, den [språk i arbetsflödesdefinitionen](http://aka.ms/logicappsdocs) ger en `@parameters()` igen. Du kan också [ange parametrar i mallen för distribution av resursen](../azure-resource-manager/resource-group-authoring-templates.md#parameters). Men om du anger parametertyp som `securestring`, parametern inte returneras med resten av resursdefinitionen och inte är tillgängliga genom att visa resursen efter distributionen.

> [!NOTE]
> Om parametern används i sidhuvuden eller en begäran om kanske parametern visas genom att öppna körningshistorik och utgående HTTP-begäran. Se till att ange principer för åtkomst till innehåll i enlighet med detta.
> Auktorisering huvuden visas aldrig via in- eller utdata. Så om hemligheten som används det hemligheten inte strängfält.

#### <a name="resource-deployment-template-with-secrets"></a>Resurs-Distributionsmall med hemligheter

I följande exempel visas en distribution som refererar till en säker parameter av `secret` vid körning. I en separat parameterfilen kan du ange miljö-värde för den `secret`, eller Använd [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) för att hämta distribuera hemligheter i tid.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secretDeploymentParam": {
      "type": "securestring"
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
        "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "actions": {
            "Call_External_API": {
              "type": "http",
              "inputs": {
                "headers": {
                  "Authorization": "@parameters('secret')"
                },
                "body": "This is the request"
              },
              "runAfter": {}
            }
          },
          "parameters": {
            "secret": {
              "type": "SecureString"
            }
          },
          "triggers": {
            "manual": {
              "type": "Request",
              "kind": "Http",
              "inputs": {
                "schema": {}
              }
            }
          },
          "contentVersion": "1.0.0.0",
          "outputs": {}
        },
        "parameters": {
          "secret": {
            "value": "[parameters('secretDeploymentParam')]"
          }
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Säker åtkomst till tjänster som tar emot förfrågningar från ett arbetsflöde

Det finns många sätt för att skydda valfri slutpunkt logikappen behöver åtkomst till.

### <a name="using-authentication-on-outbound-requests"></a>Med autentisering för utgående förfrågningar

När du arbetar med en HTTP-, http- + Swagger (Öppna API) eller Webhook-åtgärden kan du lägga till autentisering på begäran som skickas. Du kan inkludera grundläggande autentisering eller autentisering med datorcertifikat Azure Active Directory-autentisering. Information om hur du konfigurerar den här autentiseringen kan hittas [i den här artikeln](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Begränsa åtkomsten till logik app IP-adresser

Alla anrop från logikappar komma från en specifik uppsättning IP-adresser per region. Du kan lägga till ytterligare filtrering så att den endast accepterar begäranden från de angivna IP-adresserna. En lista över de IP-adresserna, se [logik app gränser och konfiguration](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Lokala anslutningsmöjligheter

Logikappar ange integrering med flera tjänster att ge säkert och tillförlitligt lokalt kommunikation.

#### <a name="on-premises-data-gateway"></a>Lokal datagateway

Många hanterade anslutningar för logic apps ge säker anslutning till lokalt system, inklusive filsystem, SQL, SharePoint, DB2 och mycket mer. Gatewayen som vidarebefordrar data från lokala datakällor på krypterade kanaler via Azure Service Bus. Det kommer all trafik som säkra utgående trafik från gateway-agenten. Lär dig mer om [hur datagateway fungerar](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Azure API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) har lokala anslutningsmöjligheter, inklusive integrering med plats-till-plats VPN- och ExpressRoute för säker proxy och kommunikation till lokala system. Du kan snabbt markera en API exponeras från Azure API Management i ett arbetsflöde, vilket ger snabb åtkomst till lokala system i logik App Designer.

## <a name="next-steps"></a>Nästa steg
[Skapa en Distributionsmall för](logic-apps-create-deploy-template.md)  
[Undantagshantering](logic-apps-exception-handling.md)  
[Övervaka dina logikappar](logic-apps-monitor-your-logic-apps.md)  
[Diagnostisera logik app fel och problem](logic-apps-diagnosing-failures.md)  
