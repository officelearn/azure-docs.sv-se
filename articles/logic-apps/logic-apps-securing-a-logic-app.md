---
title: Säker åtkomst till Azure Logic Apps | Microsoft Docs
description: Skydda åtkomsten till utlösare, indata och utdata, åtgärdsparametrar och tjänster i arbetsflöden för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 11/22/2016
ms.openlocfilehash: 0fe35b67a424caedcea2c71885d1757943ace9d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232604"
---
# <a name="secure-access-in-azure-logic-apps"></a>Säker åtkomst i Azure Logic Apps

Här finns sätt att du kan skydda åtkomst till olika komponenter i din logikapp:

* Säker åtkomst för att utlösa ett logic app-arbetsflöde med HTTP-begäran-utlösare.
* Säker åtkomst för att hantera, redigera eller läsa en logikapp.
* Säker åtkomst till innehållet i indata och utdata för en logikappskörning.
* Skydda parametrar eller indata för åtgärder i ett logic app-arbetsflöde.
* Säker åtkomst till tjänster som tar emot begäranden från en logikappens arbetsflöde.

## <a name="secure-access-to-trigger"></a>Säker åtkomst till utlösa

När du arbetar med en logikapp som utlöses med en HTTP-begäran ([begära](../connectors/connectors-native-reqres.md) eller [Webhook](../connectors/connectors-native-webhook.md)), du kan begränsa åtkomst så att endast auktoriserade klienter kan utlösa logikappen. Alla begäranden till en logikapp krypteras och skyddas via SSL.

### <a name="shared-access-signature"></a>Signatur för delad åtkomst

Varje begäran-slutpunkten för en logic app innehåller en [signatur för delad åtkomst (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) som en del av URL: en. Varje URL: en innehåller en `sp`, `sv`, och `sig` frågeparameter. Behörigheter som anges av `sp`, och motsvarar HTTP-metoder som tillåts, `sv` är den version som används för att generera, och `sig` används för att autentisera åtkomsten till utlösa. Signaturen genereras med SHA256-algoritmen med en hemlig nyckel på alla URL-sökvägar och egenskaper. Den hemliga nyckeln är aldrig exponeras och publicerats och förblir krypterade och lagras som en del av logikappen. Logikappen auktoriserar endast utlösare som innehåller en giltig signatur som skapats med den hemliga nyckeln.

#### <a name="regenerate-access-keys"></a>Återskapa åtkomstnycklar

Du kan återskapa en ny säker nyckel vid när som helst via REST API eller Azure-portalen. Alla aktuella URL: er som har skapats tidigare med den gamla nyckeln ogiltig och inte längre behörighet att utlösa logikappen.

1. Öppna logikappen som du vill återskapa en nyckel i Azure-portalen
1. Klicka på den **åtkomstnycklar** menyalternativet under **inställningar**
1. Välj på för att återskapa och slutföra processen

URL: er som du hämtar efteråt signeras med den nya åtkomstnyckeln.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Skapa Motringnings-URL: er med ett förfallodatum

Om du delar URL: en med andra parter, kan du generera URL: er med specifika nycklar och förfallodatum efter behov. Du kan sedan sömlöst återställa nycklar eller se till att åtkomsten till utlöses en app är begränsad till ett visst tidsintervall. Du kan ange ett sista giltighetsdatum för en URL via den [logikappar REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers):

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

I själva brödtexten, ta med egenskapen `NotAfter` som en JSON-datumsträng som returnerar en Motringnings-URL som endast är giltig tills den `NotAfter` datum och tid.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Skapa URL: er med primär eller sekundär hemlig nyckel

Du kan även ange vilken nyckel som ska använda för att logga URL: en när du genererar eller lista Motringnings-URL: er för begäran-baserade utlösare.  Du kan generera en URL som signerats av en viss nyckel via den [logikappar REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) på följande sätt:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

I själva brödtexten, ta med egenskapen `KeyType` som antingen `Primary` eller `Secondary`.  Detta returnerar en URL som signerats av säker nyckeln som har angetts.

### <a name="restrict-incoming-ip-addresses"></a>Begränsa inkommande IP-adresser

Förutom Shared Access Signature, kanske du vill begränsa anropa en logikapp endast från specifika klienter.  Om du hanterar din slutpunkt via Azure API Management kan begränsa du till exempel logikappen så att endast acceptera begäran när begäran kommer från IP-adressen för API Management-instans.

Den här inställningen kan konfigureras i inställningarna för logic app:

1. Öppna logikappen som du vill lägga till IP-adressbegränsningar i Azure-portalen
1. Klicka på den **arbetsflödesinställningarna** menyalternativet under **inställningar**
1. Ange listan med IP-adresser som ska godkännas av utlösaren

En giltig IP-adressintervall har formatet `192.168.1.1/32`. Om du vill logikappen som ska bara aktiveras som en kapslad logikapp, Välj den **enbart andra logic apps** alternativet. Det här alternativet skriver en tom matris till resursen, betydelse endast anropas från själva (överordnade logikappar) tjänsten utlöses har.

> [!NOTE]
> Du kan fortfarande köra en logikapp med en begäransutlösare via REST API / Management `/triggers/{triggerName}/run` oavsett IP. Det här scenariot kräver autentisering mot Azure REST-API och alla händelser som visas i granskningsloggen för Azure. Ställ in åtkomst till principer för åtkomstkontroll i enlighet med detta.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Ange IP-intervall i resursdefinitionen

Om du använder en [Distributionsmall](logic-apps-create-deploy-template.md) för att automatisera dina distributioner, inställningar för IP-adressintervall kan konfigureras på resource-mallen.  

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

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Lägga till Azure Active Directory, OAuth eller andra

Att lägga till fler auktoriseringsprotokoll ovanpå en logikapp [Azure API Management](https://azure.microsoft.com/services/api-management/) erbjuder omfattande övervakning, säkerhet, principer och dokumentation för valfri slutpunkt med möjlighet att exponera en logikapp som ett API. Azure API Management kan exponera en offentlig eller privat slutpunkt för logikappen, som kan använda Azure Active Directory, certifikat, OAuth eller andra säkerhetskrav. När en begäran tas emot, vidarebefordrar begäran till logikappen (och alla nödvändiga omvandlingar eller begränsningar för relä) i Azure API Management. Du kan använda inställningar för inkommande IP-intervall på logikappen att endast tillåta logikappen utlöses från API Management.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>Säker åtkomst för att hantera eller redigera logikappar

Du kan begränsa åtkomsten till hanteringsåtgärder i en logikapp så att endast specifika användare eller grupper kan utföra åtgärder på resursen. Logikappar Använd Azure [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md) funktion och kan anpassas med samma verktyg.  Det finns några inbyggda roller som du kan även tilldela medlemmar i din prenumeration för att:

* **Logic App-deltagare** -ger möjlighet att visa, redigera och uppdatera en logikapp.  Det går inte att ta bort resursen eller utföra administrativa åtgärder.
* **Logic App-operatör** – kan visa logikappen och körningshistorik och aktivera/inaktivera.  Det går inte att redigera eller uppdatera definitionen.

Du kan också använda [Azure-resurslås](../azure-resource-manager/resource-group-lock-resources.md) att förhindra att ändra eller ta bort logikappar. Den här funktionen är användbar för att förhindra produktionsresurser från ändringar eller raderingar.

## <a name="secure-access-to-contents-of-the-run-history"></a>Säker åtkomst till innehållet i körningshistoriken

Du kan begränsa åtkomsten till innehållet i indata eller utdata från tidigare körningar till specifika IP-adressintervall.  

Alla data i ett arbetsflöde kör krypteras vid överföring och i vila. När ett anrop till körningshistorik görs, autentiserar begäran tjänsten och länkar till begäranden och svar indata och utdata. Den här länken kan skyddas så att endast begäranden om att visa innehåll från en IP-adressintervall returnera innehållet. Du kan använda den här funktionen för ytterligare åtkomstkontroll. Du kan även ange en IP-adress som `0.0.0.0` så ingen kan komma åt indata/utdata. Endast användare med administratörsbehörighet kan ta bort den här begränsningen att tillhandahålla möjligheten för ”just-in-time-åtkomst till innehåll för arbetsflödet.

Den här inställningen kan konfigureras i resursinställningarna för i Azure Portal:

1. Öppna logikappen som du vill lägga till IP-adressbegränsningar i Azure-portalen
2. Klicka på den **åtkomstkontrollskonfiguration** menyalternativet under **inställningar**
3. Ange listan med IP-adressintervall för åtkomst till innehåll

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Ange IP-intervall i resursdefinitionen

Om du använder en [Distributionsmall](logic-apps-create-deploy-template.md) för att automatisera dina distributioner, inställningar för IP-adressintervall kan konfigureras på resource-mallen.  

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

Du kanske vill Parameterisera vissa aspekter av en arbetsflödesdefinition för distribution i alla miljöer. Vissa parametrar kan också vara säkra parametrar som du inte vill ska visas när du redigerar ett arbetsflöde, till exempel ett klient-ID och klienthemligheten för [Azure Active Directory-autentisering](../connectors/connectors-native-http.md#authentication) av en HTTP-åtgärd.

### <a name="using-parameters-and-secure-parameters"></a>Med hjälp av parametrarna och säker

Åtkomst till värdet för en resursparametern vid körning, den [definitionsspråk för arbetsflödet](https://aka.ms/logicappsdocs) tillhandahåller en `@parameters()` igen. Du kan också [ange parametrar i mallen för distribution av resursen](../azure-resource-manager/resource-group-authoring-templates.md#parameters). Men om du anger parametertyp som `securestring`, parametern inte returneras med resten av resursdefinitionen och kan inte nås genom att visa resursen efter distributionen.

> [!NOTE]
> Om din parameter används i sidhuvuden eller brödtexten i en begäran, vara parametern synliga genom att gå till körningshistorik och utgående HTTP-begäran. Se till att ange principer för åtkomst till innehåll i enlighet med detta.
> Auktorisering rubriker är aldrig synliga via indata eller utdata. Så om hemligheten används det, hemligheten inte kan hämtas.

#### <a name="resource-deployment-template-with-secrets"></a>Resurs-Distributionsmall med hemligheter

I följande exempel visas en distribution som refererar till en säker parameter för `secret` vid körning. I en separat parameterfilen, kan du ange miljö-värde för den `secret`, eller Använd [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) för att hämta hemligheter på distribuera tid.

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
   "resources": [ {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
         "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
         "definition": {
            "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
            "actions": {
               "Call_External_API": {
                  "type": "Http",
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
   } ],
   "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Säker åtkomst till tjänster som tar emot begäranden från ett arbetsflöde

Det finns många sätt för att skydda valfri slutpunkt logikappen behöver åtkomst till.

### <a name="using-authentication-on-outbound-requests"></a>Med autentisering på utgående begäranden

När du arbetar med en HTTP-, HTTP + Swagger (Open API) eller Webhook-åtgärd, kan du lägga till autentisering på begäran som skickas. Du kan inkludera grundläggande autentisering eller autentisering med datorcertifikat Azure Active Directory-autentisering. Information om hur du konfigurerar den här autentiseringen hittar [i den här artikeln](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Begränsa åtkomsten till IP-adresser för logic app

Alla anrop från logikappar kommer från en specifik uppsättning IP-adresser per region. Du kan lägga till ytterligare filtrera för att endast acceptera begäranden från de angivna IP-adresserna. En lista över de IP-adresserna finns i [logic app-gränser och konfiguration](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Lokala anslutningsmöjligheter

Logic apps är integrering med flera tjänster att tillhandahålla säker och tillförlitlig lokala kommunikation.

#### <a name="on-premises-data-gateway"></a>Lokal datagateway

Många hanterade anslutningsappar för logic apps ger säkra anslutningar till lokala system, inklusive filsystem, SQL, SharePoint, DB2 och mycket mer. Gatewayen vidarebefordrar data från lokala källor på krypterade kanaler via Azure Service Bus. All trafik som samlas in som säker utgående trafik från gateway-agenten. Läs mer om [hur datagateway fungerar](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Azure API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) har lokala anslutningsmöjligheter, inklusive plats-till-plats VPN och ExpressRoute-integrering för säker proxy och kommunikation till lokala system. I Logic App Designer, kan du snabbt välja ett API som visas från Azure API Management i ett arbetsflöde, vilket ger snabb åtkomst till lokala system.

## <a name="next-steps"></a>Nästa steg
[Skapa en Distributionsmall](logic-apps-create-deploy-template.md)  
[Undantagshantering](logic-apps-exception-handling.md)  
[Övervaka dina logikappar](logic-apps-monitor-your-logic-apps.md)  
[Diagnosticera logikappfel och problem](logic-apps-diagnosing-failures.md)  
