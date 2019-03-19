---
title: Säker åtkomst till Azure Logic Apps | Microsoft Docs
description: Lägga till säkerhet för Azure Logic Apps, inklusive utlösare, indata och utdata, parametrar och andra tjänster
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: 34076b790a91b1c0e9d8bee224423aab0db7c8f3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57891815"
---
# <a name="secure-access-in-azure-logic-apps"></a>Säker åtkomst i Azure Logic Apps

Här följer element i din logikapp där du kan skydda åtkomst:

* [Begäran eller Webhook-utlösare](#secure-triggers)
* [Åtgärder som att hantera, redigera eller visa](#secure-operations) logikappen
* [Indata och utdata](#secure-run-history) från din logikapp körningshistorik
* [Åtgärdsparametrar och indata](#secure-action-parameters)
* [Tjänster som get-begäranden](#secure-requests) från din logikapp

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>Säker åtkomst till utlösare för begäran

När logikappen använder en HTTP-begäran-baserade utlösare, till exempel den [begäran](../connectors/connectors-native-reqres.md) eller [Webhook](../connectors/connectors-native-webhook.md) utlösare, du kan begränsa åtkomst så att endast auktoriserade klienter kan börja din logikapp. Alla begäranden som tas emot av en logikapp krypteras och skyddas med Secure Sockets Layer (SSL)-protokollet. Här finns olika sätt som du kan skydda åtkomst till den här utlösartypen:

* [Skapa signaturer för delad åtkomst](#sas)
* [Begränsa inkommande IP-adresser](#restrict-incoming-IP)
* [Lägg till Azure Active Directory, OAuth eller andra](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>Skapa signaturer för delad åtkomst

Varje begäran-slutpunkten på en logic app innehåller en [signatur för delad åtkomst (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) i URL: en för slutpunkten. Varje URL: en innehåller en `sp`, `sv`, och `sig` frågeparameter:

* `sp` Anger behörigheter, som mappar till http-metoder som är tillåtet för användning.
* `sv` Anger den version som används för att skapa signaturen.
* `sig` används för att autentisera åtkomst till utlösaren.

Signaturen genereras med SHA256-algoritmen med en hemliga åtkomstnyckel för alla URL-sökvägar och egenskaper. Den hemliga nyckeln är aldrig exponeras eller publicerats och förblir krypterade och lagrade med logikappen. Logikappen auktoriserar dessa utlösare som innehåller en giltig signatur som skapats med den hemliga nyckeln. 

Här finns mer information om hur du skyddar åtkomst med signatur för delad åtkomst:

* [Återskapa åtkomstnycklar](#access-keys)
* [Skapa upphör Motringnings-URL: er](#expiring-URLs)
* [Skapa URL: er med primär eller sekundär nyckel](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Återskapa åtkomstnycklar

Använd Azure REST API eller Azure-portalen för att återskapa en ny nyckel för säker åtkomst när som helst. Alla tidigare genererade URL: er som används som den gamla nyckeln är ogiltig och inte längre behörighet för att utlösa logikappen. De webbadresser som du hämtar när återskapas signeras med den nya åtkomstnyckeln.

1. Öppna logikappen som den nyckel som du vill återskapa i Azure-portalen.

1. Menyn för logic app under **inställningar**väljer **åtkomstnycklar**.

1. Markera den nyckel som du vill återskapa och slutföra processen.

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>Skapa Motringnings-URL: er med förfallodatum

Om du delar en frågebaserad utlösaren slutpunkts-URL med andra parter kan du generera Motringnings-URL: er med specifika nycklar och förfallodatum efter behov. Du kan sedan smidigt distribuera nycklar eller begränsa åtkomsten för att utlösa logikappen till ett specifikt tidsintervall. Du kan ange ett sista giltighetsdatum för en URL med hjälp av den [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), till exempel:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

I själva brödtexten, inkluderar den `NotAfter`egenskap med en JSON datum sträng. Den här egenskapen returnerar en Motringnings-URL som är giltig tills den `NotAfter` datum och tid.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Skapa URL: er med primär eller sekundär hemlig nyckel

När du genererar eller lista Motringnings-URL: er för begäran-baserade utlösare, du kan också ange nyckeln som ska användas för signering av URL: en. Du kan generera en URL som är signerat av en viss nyckel med hjälp av den [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), till exempel:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

I själva brödtexten, inkluderar den `KeyType` egenskapen som antingen `Primary` eller `Secondary`. Den här egenskapen returnerar en URL som har signerats av den angivna säkra nyckeln.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Begränsa inkommande IP-adresser

Du kanske vill begränsa vissa klienter som kan anropa logikappen tillsammans med signatur för delad åtkomst.  
Om du hanterar din begäran slutpunkt med Azure API Management kan begränsa du till exempel din logikapp för att godkänna begäranden endast från IP-adressen för API Management-instans. 

#### <a name="set-ip-ranges---azure-portal"></a>Ange IP-intervall - Azure-portalen

Om du vill konfigurera den här begränsningen i Azure-portalen går du till din logikapp inställningar: 

1. Öppna logikappen i Logic App Designer i Azure-portalen. 

1. På din logikapp-menyn, under **inställningar**väljer **arbetsflödesinställningarna**.

1. Under **åtkomstkontrollskonfiguration** > 
**tillåtna inkommande IP-adresser**väljer **specifika IP-intervall**.

1. Under **IP-intervall för utlösare**, ange IP-adressintervall som utlösaren accepterar. En giltig IP-intervall använder dessa format: *x.x.x.x/x* eller *x.x.x.x-x.x.x.x* 

Om du vill att logikappen utlöses endast som en kapslad logikapp från den **tillåtna inkommande IP-adresser** väljer **enbart andra Logic Apps**. Det här alternativet skriver en tom matris till din logikappresursen så att endast anrop från Logic Apps-tjänsten (överordnade logikappar) kan utlösa kapslad logikapp.

> [!NOTE]
> Oavsett IP-adress, kan du fortfarande köra en logikapp som har en begäran-baserade utlösare med hjälp av `/triggers/{triggerName}/run` via Azure REST-API eller via API Management. Men det här scenariot kräver fortfarande autentisering mot Azure REST-API och alla händelser som visas i granskningsloggen för Azure. Se till att ange principer för åtkomstkontroll på lämpligt sätt.

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>Ange IP-intervall - Distributionsmall för logic app

Om du automatiserar logic app-distributionerna med hjälp av en [mall för distribution av Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), du kan ange IP-adressintervall i mallen, till exempel:

``` json
{
   "properties": {
      "definition": {},
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
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Lägg till Azure Active Directory, OAuth eller andra

Överväg att använda för att lägga till fler auktoriseringsprotokoll i logikappen [Azure API Management](https://azure.microsoft.com/services/api-management/). Tjänsten erbjuder omfattande övervakning, säkerhet, principer och dokumentation för valfri slutpunkt och ger dig möjlighet att exponera din logikapp som ett API. API Management kan exponera en slutpunkt för offentliga eller privata för logikappen, som sedan kan använda Azure Active Directory, OAuth, certifikat eller andra säkerhetskrav. När API Management tar emot en begäran, skickar en begäran till din logikapp gör även alla nödvändiga omvandlingar eller begränsningar på vägen i tjänsten. Du kan använda inställningar för din logikapp inkommande IP-adressintervall om du vill att endast API Management utlösa logikappen. 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>Säker åtkomst till logic app-funktioner

Du kan begränsa åtkomsten på uppgifter som att hantera, redigera och visa så att endast specifika användare eller grupper som köra åtgärder på din logikapp. Logic Apps stöder [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md), som du kan anpassa eller tilldela inbyggda roller till medlemmar i din prenumeration, till exempel:

* **Logic App-deltagare**: Användare kan visa, redigera och uppdatera din logikapp. Den här rollen kan inte ta bort logikappen eller köra åtgärder för administratören.
* **Logic App-operatör**: Användare kan visa din logikapp och körningshistorik, och aktivera eller inaktivera logikappen. Den här rollen kan inte redigera eller uppdatera din logikapp.

Du kan använda för att förhindra andra från att ändra eller ta bort din logikapp [Azure-resurslås](../azure-resource-manager/resource-group-lock-resources.md). Den här funktionen kan du hindra andra från att ändra eller ta bort produktionsresurser.

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>Säker åtkomst till logikappskörningen historik

För att skydda innehåll som skickas som indata eller utdata från föregående logikapp-körningar, kan du begränsa åtkomsten till specifika IP-adressintervall. Den här funktionen ger dig större åtkomstkontroll. Alla data i en logikapp-körningen är krypterad under överföringen och i vila. När du begär en logikapp körningshistoriken, autentiserar det begäran Logic Apps och innehåller länkar till indata och utdata från begäranden och svar i logikappens arbetsflöde. Du kan skydda dessa länkar så att endast begäranden från en specifik IP-adress returnera innehållet. Exempel: du kan även ange en IP-adress som `0.0.0.0-0.0.0.0` så ingen kan komma åt indata och utdata. Endast en användare med administratörsbehörighet kan ta bort den här begränsningen att tillhandahålla möjligheten för ”just-in-time” åtkomst till innehåll som din logikapp.

### <a name="set-ip-ranges---azure-portal"></a>Ange IP-intervall - Azure-portalen

Om du vill konfigurera den här begränsningen i Azure-portalen går du till din logikapp inställningar:

1. Öppna logikappen i Logic App Designer i Azure-portalen. 

1. På din logikapp-menyn, under **inställningar**väljer **arbetsflödesinställningarna**.

1. Under **åtkomstkontrollskonfiguration** > 
    **tillåtna inkommande IP-adresser**väljer **specifika IP-intervall**.

1. Under **IP-intervall för innehåll**, ange IP-adressintervall som kan komma åt innehåll från indata och utdata. 
   En giltig IP-intervall använder dessa format: *x.x.x.x/x* eller *x.x.x.x-x.x.x.x* 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>Ange IP-intervall - Distributionsmall för logic app

Om du automatiserar logic app-distributionerna med hjälp av en [mall för distribution av Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), du kan ange IP-adressintervall i mallen, till exempel:

``` json
{
   "properties": {
      "definition": {},
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
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>Säkra åtgärdsparametrar och indata

När du distribuerar mellan olika miljöer, kanske du vill Parameterisera specifika element i din logikapp arbetsflödesdefinitionen. På så sätt kan du ange indata som baseras på de miljöer som du använder och skyddar känslig information. Exempel: Om du autentisera HTTP-åtgärder med [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), definiera och skyddar de parametrar som accepterar klient-ID och klienthemlighet som används för autentisering. För dessa parametrar sina logikapp-definitioner har sin egen `parameters` avsnittet.
Om du vill öppna parametervärden vid körning, du kan använda den `@parameters('parameterName')` uttryck, som tillhandahålls av den [Definitionsspråk för arbetsflödet](https://aka.ms/logicappsdocs). 

Om du vill skydda parametrar och värden som du inte vill ska visas när du redigerar logikappen eller visa kör historik, kan du definiera parametrar med den `securestring` Skriv och Använd kodning kan vid behov. Parametrar som har den här typen returneras inte med resursdefinitionen och är inte tillgänglig när du visar resursen efter distributionen.

> [!NOTE]
> Om du använder en parameter i sidhuvuden eller brödtext för en begäran måste vara parametern synliga vid åtkomst till din logikapp körningshistorik och utgående HTTP-begäran. Kontrollera att du även ange principer för åtkomst till innehåll i enlighet med detta.
> Auktorisering rubriker är aldrig synliga via indata eller utdata. Om en hemlighet används det inte så det hemlighet hämtningsbara.

Läs mer om hur du skyddar parametrar i logic app-definitioner [skydda parametrar i logic app-definitioner](#secure-parameters-workflow) senare på den här sidan.

Om du automatisera distributioner med [Azure Resource Manager-distributionsmallar](../azure-resource-manager/resource-group-authoring-templates.md#parameters), du kan också använda säker parametrar i dessa mallar. Du kan till exempel använda parametrar för att hämta KeyVault-hemligheter när du skapar din logikapp. Malldefinitionen för din distribution har sin egen `parameters` avsnittet separat från din logikapp `parameters` avsnittet. Mer information om hur du skyddar parametrar i mallar för distribution finns i [skydda parametrar i distributionsmallar](#secure-parameters-deployment-template) senare på den här sidan.

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>Skydda parametrar i logic app-definitioner

Använd säkra parametrar för att skydda känslig information i ditt arbetsflöde för logikappsdefinitionen, så att den här informationen inte visas när du har sparat din logikapp. Anta exempelvis att du använder `Basic` autentisering i en definition för HTTP-åtgärd. Det här exemplet innehåller en `parameters` avsnitt som definierar parametrar för åtgärdsdefinition plus en `authentication` avsnittet som accepterar `username` och `password` parametervärden. Om du vill ange värden för dessa parametrar du vill kan du använda en separat parameterfilen, till exempel:

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
         "type": "securestring"
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
}
```

Om du använder hemligheter kan du hämta dessa hemligheter vid tidpunkten för distribution med [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>Säker parametrar i Azure Resource Manager-distributionsmallar

Det här exemplet visar en Resource Manager-Distributionsmall som använder mer än en runtime-parameter med den `securestring` typ:

* `armTemplatePasswordParam`, som är indata för logikappsdefinitionen `logicAppWfParam` parameter

* `logicAppWfParam`, som är indata för HTTP-åtgärd som använder grundläggande autentisering

Det här exemplet innehåller en inre `parameters` som hör till din logikapp arbetsflödesdefinitionen och en yttre `parameters` som hör till mallen för distribution. Du kan använda en separat parameterfilen om du vill ange miljö-värden för parametrar. 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
                     "type": "securestring"
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
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

Om du använder hemligheter kan du hämta dessa hemligheter vid tidpunkten för distribution med [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>Säker åtkomst till tjänster som tar emot begäranden

Här är några sätt som du kan skydda valfri slutpunkt där logikappen behöver åtkomst och skickar begäranden.

### <a name="add-authentication-on-outbound-requests"></a>Lägg till autentisering för utgående förfrågningar

När du arbetar med en HTTP-, HTTP + Swagger (Open API) eller Webhook-åtgärd, kan du lägga till autentisering på begäran som skickas av din logikapp. Du kan till exempel använda grundläggande autentisering eller autentisering med datorcertifikat Azure Active Directory-autentisering. Mer information finns i [autentisera utlösare eller åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

### <a name="restrict-access-to-logic-app-ip-addresses"></a>Begränsa åtkomsten till IP-adresser för logic app

Alla anrop från logikappar kommer från specifika avsedda IP-adresser baserat på region. Du kan lägga till filtrering som accepterar begäranden endast från de IP-adresserna. Dessa IP-adresser, se [gränser och konfigurering för Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

### <a name="secure-on-premises-connectivity"></a>Skydda lokal anslutning

Azure Logic Apps ger integrering med dessa tjänster för säker och tillförlitlig lokala kommunikation.

#### <a name="on-premises-data-gateway"></a>Lokal datagateway

Många hanterade anslutningsappar för Azure Logic Apps ger säkra anslutningar till lokala system, till exempel filsystem, SQL, SharePoint, DB2 och andra. Gatewayen skickar data från lokala källor på krypterade kanaler via Azure Service Bus. All trafik som samlas in som säker utgående trafik från gateway-agenten. Lär dig [hur den lokala datagatewayen fungerar](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Azure API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) ger den lokala anslutningsalternativ som plats-till-plats VPN och ExpressRoute-integrering för säker proxy och kommunikation till lokala system. I Logic App Designer, kan du välja ett API som exponeras av API Management från logikappens arbetsflöde att ge snabb åtkomst till lokala system.

## <a name="next-steps"></a>Nästa steg

* [Skapa en Distributionsmall](logic-apps-create-deploy-template.md)  
* [Undantagshantering](logic-apps-exception-handling.md)  
* [Övervaka dina logikappar](logic-apps-monitor-your-logic-apps.md)  
* [Diagnosticera logikappfel och problem](logic-apps-diagnosing-failures.md)  
