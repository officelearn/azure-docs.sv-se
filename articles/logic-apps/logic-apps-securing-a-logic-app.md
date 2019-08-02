---
title: Säker åtkomst och data Azure Logic Apps
description: Skydda parameter indata, HTTP-begäranden för utlösare, körnings historik, Logic app-åtgärder och anslutningar till andra tjänster i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: f27dfd1f907d106ddb3b1b9dd7534d56380149c2
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385492"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Säker åtkomst och data i Azure Logic Apps

Om du vill kontrol lera åtkomsten och skydda data i Azure Logic Apps kan du konfigurera säkerheten i följande områden:

* [Åtkomst till utlösare för HTTP-begäranden](#secure-triggers)
* [Åtkomst till Logic app-åtgärder](#secure-operations)
* [Åtkomst för att köra tidigare indata och utdata](#secure-run-history)
* [Åtkomst till parameter indata](#secure-action-parameters)
* [Åtkomst till tjänster och system som anropas från Logic Apps](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-http-request-triggers"></a>Åtkomst till utlösare för HTTP-begäranden

När din Logic app använder en HTTP-begäran-baserad utlösare, till exempel [begäran](../connectors/connectors-native-reqres.md) eller webhook-utlösaren, kan du begränsa åtkomsten så att endast auktoriserade klienter kan starta din Logic app. [](../connectors/connectors-native-webhook.md) Alla begär Anden som tas emot av en Logic app krypteras och skyddas med Secure Sockets Layer SSL-protokoll. 

Följande är de sätt som du kan använda för att skydda åtkomsten till den här utlösaren:

* [Generera signaturer för delad åtkomst](#sas)
* [Begränsa inkommande IP-adresser](#restrict-incoming-ip-addresses)
* [Lägg till Azure Active Directory, OAuth eller annan säkerhet](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generera signaturer för delad åtkomst (SAS)

Varje begär ande slut punkt i en Logic app har en [signatur för delad åtkomst (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) i slut punktens URL, som följer detta format:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Varje URL innehåller en `sp`- `sv`,- `sig` och-frågeparameter enligt beskrivningen i den här tabellen:

| Frågeparameter | Beskrivning |
|-----------------|-------------|
| `sp` | Anger behörigheter för de tillåtna HTTP-metoder som ska användas. |
| `sv` | Anger den SAS-version som ska användas för att skapa signaturen. |
| `sig` | Anger signaturen som ska användas för att autentisera åtkomsten till utlösaren. Den här signaturen genereras med hjälp av SHA256-algoritmen med en hemlig åtkomst nyckel på alla URL-sökvägar och egenskaper. Aldrig exponeras eller publiceras den här nyckeln krypteras och lagras med Logic app. Din Logic app godkänner bara de utlösare som innehåller en giltig signatur som skapats med den hemliga nyckeln. |
|||

Mer information om hur du skyddar åtkomst med signaturen för delad åtkomst finns i följande avsnitt i det här avsnittet:

* [Återskapa åtkomst nycklar](#access-keys)
* [Skapa återanrops-URL: er som upphör](#expiring-urls)
* [Skapa URL: er med primär eller sekundär nyckel](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Återskapa åtkomstnycklar

Använd Azure REST API eller Azure Portal för att skapa en ny säker åtkomst nyckel när som helst. Alla tidigare genererade URL: er som använder den gamla nyckeln är ogiltiga och har inte längre behörighet att utlösa Logic-appen. URL: erna som du hämtar efter återskapandet är signerad med den nya åtkomst nyckeln.

1. I Azure Portal öppnar du den Logic-app som har den nyckel som du vill återskapa.

1. Gå till menyn för Logic-appen, Välj **åtkomst nycklar**under **Inställningar**.

1. Välj den nyckel som du vill återskapa och slutföra processen.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Skapa återanrops-URL: er som upphör

Om du delar slut punkts-URL: en för en HTTP-förfrågan-baserad utlösare med andra parter kan du generera återanrops-URL: er som använder vissa nycklar och som har förfallo datum På så sätt kan du sömlöst återställa nycklar eller begränsa åtkomsten till att utlösa din Logi Kap par baserat på ett angivet tidsintervall. Om du vill ange ett förfallo datum för en URL använder du [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), till exempel:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

I bröd texten inkluderar du `NotAfter`egenskapen med hjälp av en JSON-datum sträng. Den här egenskapen returnerar en callback-URL som endast är giltig `NotAfter` fram till datum och tid.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Skapa URL: er med primär eller sekundär hemlig nyckel

När du skapar eller listar återanrops-URL: er för HTTP-begäran-baserade utlösare kan du ange den nyckel som ska användas för att signera URL: en. Om du vill generera en URL som är signerad av en viss nyckel använder du [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), till exempel:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

I bröd texten inkluderar du `KeyType` egenskapen som antingen `Primary` eller `Secondary`. Den här egenskapen returnerar en URL som är signerad med den angivna säkra nyckeln.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Begränsa inkommande IP-adresser

Tillsammans med signaturen för delad åtkomst kanske du vill begränsa vilka klienter som kan anropa din Logic app. Om du till exempel hanterar din begärans slut punkt med hjälp av Azure API Management, kan du begränsa din Logic-app så att den endast accepterar begär Anden från IP-adressen för API Management-instansen.

#### <a name="restrict-incoming-ip-ranges-in-azure-portal"></a>Begränsa inkommande IP-intervall i Azure Portal

1. I Azure Portal öppnar du din Logic app i Logic Apps designer.

1. På din Logic Apps-meny, under **Inställningar**, väljer du **arbets flödes inställningar**.

1. Under **åtkomst kontroll konfiguration** > **tillåtna inkommande IP-adresser**väljer du **vissa IP-intervall**.

1. Under **IP-intervall för**utlösare anger du de IP-adressintervall som utlösaren accepterar.

   Ett giltigt IP-intervall använder följande format: *x. x. x. x/x* eller *x. x. x* . x-x. x

Om du vill att din Logic app endast ska utlösa som en kapslad Logic-app väljer du **endast andra Logic Apps**i listan **tillåtna inkommande IP-adresser** . Med det här alternativet skrivs en tom matris till din Logic app-resurs. På så sätt kan endast anrop från Logic Appss tjänsten (överordnade Logic Apps) utlösa den kapslade Logic-appen.

> [!NOTE]
> Oavsett IP-adress kan du fortfarande köra en Logic-app som har en http-begäran-baserad utlösare `/triggers/<trigger-name>/run` genom att använda via Azure-REST API eller via API Management. Det här scenariot kräver dock fortfarande autentisering mot Azure-REST API. Alla händelser visas i gransknings loggen i Azure. Se till att du anger principer för åtkomst kontroll i enlighet med detta.

#### <a name="restrict-incoming-ip-ranges-in-azure-resource-manager-template"></a>Begränsa inkommande IP-intervall i Azure Resource Manager mall

Om du automatiserar Logic app-distributioner med hjälp av en [Azure Resource Manager-mall](../logic-apps/logic-apps-create-deploy-template.md)kan du ange IP-intervallen med hjälp `triggers` `accessControl` av avsnittet med avsnittet i din Logic Apps resurs definition, till exempel:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
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
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Lägg till Azure Active Directory, OAuth eller annan säkerhet

Överväg att använda [Azure API Management](../api-management/api-management-key-concepts.md)om du vill lägga till fler Authorization-protokoll i din Logic app. Den här tjänsten ger dig möjlighet att exponera din Logic app som ett API och innehåller omfattande övervakning, säkerhet, principer och dokumentation för alla slut punkter. API Management kan exponera en offentlig eller privat slut punkt för din Logic app, som sedan kan använda Azure Active Directory, OAuth, certifikat eller andra säkerhets standarder. När API Management tar emot en begäran skickar tjänsten begäran till din Logic app, vilket även gör nödvändiga transformeringar eller begränsningar på vägen. Om du bara vill låta API Management utlösa din Logic-app kan du använda din Logic Apps inställningar för inkommande IP-intervall.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Åtkomst till Logic app-åtgärder

Du kan endast tillåta vissa användare eller grupper att köra vissa åtgärder, till exempel hantera, redigera och Visa Logic Apps. Om du vill kontrol lera deras behörigheter använder du [Azure Role-baserade Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) för att tilldela anpassade eller inbyggda roller till medlemmar i din Azure-prenumeration:

* [Logic app-deltagare](../role-based-access-control/built-in-roles.md#logic-app-contributor): Låter dig hantera Logi Kap par, men du kan inte ändra åtkomsten till dem.

* [Logic app-operator](../role-based-access-control/built-in-roles.md#logic-app-operator): Låter dig läsa, aktivera och inaktivera Logi Kap par, men du kan inte redigera eller uppdatera dem.

Om du vill hindra andra från att ändra eller ta bort din Logi Kap par kan du använda [Azure Resource lock](../azure-resource-manager/resource-group-lock-resources.md), vilket förhindrar andra från att ändra eller ta bort produktions resurser.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Åtkomst till körning av historik data

När en Logic App körs krypteras alla data under överföring och i vila. När din Logic-app är klar kan du Visa historiken för den körningen, inklusive de steg som kördes tillsammans med status, varaktighet, indata och utdata för varje åtgärd. Den här utförliga informationen ger inblick i hur din Logi Kap par kördes och var du kan börja felsöka eventuella problem som uppstår.

När du kommer åt din Logic Apps körnings historik autentiserar Logic Apps åtkomsten och tillhandahåller länkar till indata och utdata från förfrågningarna och svaren i din Logic app-körning. För åtgärder som hanterar lösen ord, hemligheter, nycklar eller annan känslig information vill du dock hindra andra från att visa och komma åt dessa data. Om din Logic app till exempel får en hemlighet från [Azure Key Vault](../key-vault/key-vault-whatis.md) att använda vid autentisering av en HTTP-åtgärd, vill du dölja hemligheten från vyn.

Om du vill kontrol lera åtkomsten till indata och utdata i din Logic Apps körnings historik har du följande alternativ:

* [Begränsa åtkomst efter IP-adressintervall](#restrict-ip).

  Med det här alternativet kan du skydda åtkomsten till körnings historik baserat på begär Anden från ett speciellt IP-adressintervall.

* [Dölj data från körnings historiken med hjälp av döljande](#obfuscate).

  I många utlösare och åtgärder kan du dölja indata, utdata eller båda från en Logic Apps körnings historik.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Begränsa åtkomst efter IP-adressintervall

Du kan begränsa åtkomsten till indata och utdata i din Logic Apps körnings historik så att endast förfrågningar från vissa IP-adressintervall kan visa dessa data. Om du till exempel vill blockera alla från att komma åt indata och utdata anger du ett IP-adressintervall `0.0.0.0-0.0.0.0`som. Endast en person med administratörs behörighet kan ta bort den här begränsningen, vilket ger möjlighet till just-in-Time-åtkomst till din Logic Apps-data. Du kan ange vilka IP-intervall som ska begränsas genom att använda Azure Portal eller i en Azure Resource Manager mall som du använder för Logic app-distribution.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Begränsa IP-intervall i Azure Portal

1. I Azure Portal öppnar du din Logic app i Logic Apps designer.

1. På din Logic Apps-meny, under **Inställningar**, väljer du **arbets flödes inställningar**.

1. Under **åtkomst kontroll konfiguration** > **tillåtna inkommande IP-adresser**väljer du **vissa IP-intervall**.

1. Under **IP-intervall för innehåll**anger du de IP-adressintervall som har åtkomst till innehåll från indata och utdata. 

   Ett giltigt IP-intervall använder följande format: *x. x. x. x/x* eller *x. x. x* . x-x. x

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Begränsa IP-intervall i Azure Resource Manager mall

Om du automatiserar Logic app-distributioner med hjälp av en [Azure Resource Manager-mall](../logic-apps/logic-apps-create-deploy-template.md)kan du ange IP-intervallen med hjälp `contents` `accessControl` av avsnittet med avsnittet i din Logic Apps resurs definition, till exempel:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
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
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Dölj data från körnings historiken med hjälp av döljande

Många utlösare och åtgärder har inställningar för att dölja indata, utdata eller både och från en Logic Apps körnings historik. Här följer några [saker att tänka på](#obfuscation-considerations) när du använder dessa inställningar för att skydda dessa data.

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Säkra indata och utdata i designern

1. Om din Logic app inte redan är öppen i [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

   ![Öppna exempel på Logic-appen](media/logic-apps-securing-a-logic-app/sample-logic-app.png)

1. På utlösaren eller åtgärden där du vill skydda data väljer du knappen med ellipser ( **...** ) och väljer sedan **Inställningar**.

   ![Öppna inställningar](media/logic-apps-securing-a-logic-app/open-settings.png)

1. Aktivera antingen **säkra indata**, **säkra utdata**eller både och. När du är klar väljer du **Klar**.

   ![Aktivera säkra indata eller utdata](media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Åtgärden eller utlösaren visar nu en Lås ikon i namn listen.

   ![Lås ikon i namn listen](media/logic-apps-securing-a-logic-app/title-bar-lock-icon.png)

   Tokens som representerar skyddade utdata från tidigare åtgärder visar också lås ikoner. Om du till exempel väljer utdata från listan med dynamiskt innehåll som ska användas i en åtgärd, visar denna token en Lås ikon.

   ![Välj utdata](media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. När Logic-appen har körts kan du se historiken för den här körningen.

   1. I fönstret **Översikt** för Logic Apps väljer du den körning som du vill visa.

   1. I fönstret **körning av Logic-app** expanderar du de åtgärder som du vill granska.

      Om du väljer att skydda både indata och utdata visas dessa värden nu som dolda.

      ![Dolda data i körnings historik](media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Säkra indata och utdata i kodvyn

I den underliggande utlösaren eller åtgärds definitionen lägger du `runtimeConfiguration.secureData.properties` till eller uppdaterar matrisen med något av eller båda dessa värden:

* `"inputs"`: Skyddar indata i körnings historik.
* `"outputs"`: Skyddar utdata i körnings historiken.

Här följer några [saker att tänka på](#obfuscation-considerations) när du använder dessa inställningar för att skydda dessa data.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Att tänka på när du döljer indata och utdata

* När du skyddar indata eller utdata på en utlösare eller åtgärd skickar Logic Apps inte skyddade data till Azure Log Analytics. Du kan inte heller lägga till [spårade egenskaper](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details) till den utlösaren eller åtgärden för övervakning.

* [Logic Apps-API för hantering av arbets flödes historik](https://docs.microsoft.com/rest/api/logic/) returnerar inte säkra utdata.

* I en åtgärd där du skyddar indata eller som uttryckligen använder säkra utdata, och den här åtgärden returnerar ett svar med utdata som innehåller dessa skyddade data, måste du manuellt aktivera **säkra utdata** i den här åtgärden för att skydda dessa utdata.

* Kontrol lera att du aktiverar **säkra indata** eller **säkra utdata** i efterföljande åtgärder där du förväntar dig körnings historiken för att skydda dessa data.

  **Inställning av säkra utdata**

  När du aktiverar **säkra utdata** manuellt i en utlösare eller åtgärd, Logic Apps säkrar dessa utdata i körnings historiken. Om en underordnad åtgärd uttryckligen använder dessa skyddade utdata som indata, Logic Apps döljer den här åtgärdens indata i körnings historiken, men *aktiverar inte* åtgärdens inställning för **säker indata** .

  ![Skyddade utdata som indata och effekt påverkan på de flesta åtgärder](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Åtgärderna Skriv, parsa JSON och Response har bara inställningen **säkra indata** . När inställningen är aktive rad döljs även dessa åtgärders utdata. Om dessa åtgärder uttryckligen använder överordnade säkra utdata som indata, Logic Apps döljer dessa åtgärders indata och utdata, men *aktiverar inte* dessa åtgärders inställning för **säkra indata** . Om en underordnad åtgärd uttryckligen använder dolda utdata från Skriv-, parsa-JSON-eller Response-åtgärder som indata, kan Logic Apps *inte dölja den här underordnade åtgärdens indata eller utdata*.

  ![Skyddade utdata som indata med effekt på vissa åtgärder](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Inställningar för säker indata**

  När du aktiverar **säkra indata** manuellt i en utlösare eller åtgärd, Logic Apps säkrar dessa indata i körnings historiken. Om en underordnad åtgärd uttryckligen använder synliga utdata från den utlösaren eller åtgärden som indata, kan Logic Apps dölja denna underordnade åtgärds indata i körnings historiken, men *aktiverar inte* **säkra** inmatningar i den här åtgärden och döljer inte den här åtgärden utdata.

  ![Skyddade indata och effekt påverkan på de flesta åtgärder](media/logic-apps-securing-a-logic-app/secure-inputs-flow.png)

  Om Skriv, parsa JSON och svars åtgärder uttryckligen använder synliga utdata från utlösaren eller åtgärden som har skyddade indata, Logic Apps döljer dessa åtgärders indata och utdata, men *aktiverar inte* den här åtgärdens **säkra indata** inställningen. Om en underordnad åtgärd uttryckligen använder dolda utdata från Skriv-, parsa-JSON-eller Response-åtgärder som indata, kan Logic Apps *inte dölja den här underordnade åtgärdens indata eller utdata*.

  ![Skyddade indata och effekter på vissa åtgärder](media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Åtkomst till parameter indata

Om du distribuerar över olika miljöer bör du överväga att parametriserade värdena i arbets flödes definitionen som varierar beroende på dessa miljöer. På så sätt kan du använda en [Azure Resource Manager mall](../azure-resource-manager/resource-group-authoring-templates.md#parameters) för att distribuera din Logi Kap par, skydda känslig information genom att definiera säkra parametrar och tillhandahålla dessa parametrar separat genom att använda mallens parametrar genom att använda en [parameter filen](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Om du till exempel autentiserar HTTP-åtgärder med [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)kan du definiera och skydda de parametrar som godkänner det klient-ID och den klient hemlighet som används för autentisering. Du definierar dessa parametrar för din Logi Kap par genom att `parameters` använda avsnittet i din Logic Apps arbets flödes definition. Om du vill skydda parameter värden som du inte vill ska visas när du redigerar din Logic app eller visar körnings historik definierar du `securestring` parametrarna `secureobject` med hjälp av typen eller och använder kodning vid behov. Parametrar som har den här typen returneras inte med resurs definitionen och är inte tillgängliga när du visar resursen efter distributionen. Om du vill komma åt dessa parameter värden under körningen använder du `@parameters('<parameter-name>')` uttrycket i arbets flödes definitionen. Det här uttrycket utvärderas bara vid körning och beskrivs av språket för [arbets flödes definitionen](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Om du använder en parameter i en HTTP-begärans rubriker eller brödtext kan den parametern visas när du visar din Logic Apps körnings historik och utgående HTTP-begäran. Se till att du även anger dina principer för innehålls åtkomst. Authorization-rubriker visas aldrig i indata eller utdata. Så om en hemlighet används där, så kan det inte går att hämta hemligheten.

Mer information finns i [säkra parametrar i arbets flödes definitioner](#secure-parameters-workflow) senare i det här avsnittet.

När du automatiserar distributioner med [Azure Resource Manager mallar](../azure-resource-manager/resource-group-authoring-templates.md#parameters)kan du definiera säkra mallparametrar, som utvärderas vid distribution, med hjälp `securestring` av-och `secureobject` -typerna. Om du vill definiera mallparametrar använder du mallens översta nivå `parameters` -avsnitt, som är åtskilda och skiljer sig från arbets `parameters` flödets definitions avsnitt. Använd en separat [parameter fil](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)för att ange värden för mallparametrar.

Om du till exempel använder hemligheter kan du definiera och använda säkra mallparametrar som hämtar dessa hemligheter från [Azure Key Vault](../key-vault/key-vault-whatis.md) vid distributionen. Sedan kan du referera till nyckel valvet och hemligheten i parameter filen. Mer information finns i följande avsnitt:

* [Använd Azure Key Vault för att skicka säkra parameter värden vid distribution](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* [Säkra parametrar i Azure Resource Manager mallar](#secure-parameters-deployment-template) senare i det här avsnittet

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Säkra parametrar i arbets flödes definitioner

Om du vill skydda känslig information i din Logic Apps arbets flödes definition använder du skyddade parametrar så att informationen inte visas när du har sparat din Logic app. Anta till exempel att du har en HTTP-åtgärd som kräver grundläggande autentisering, som använder ett användar namn och lösen ord. I arbets flödes definitionen `parameters` `basicAuthPasswordParam` definierar avsnittet parametrarna och `basicAuthUsernameParam` genom att `securestring` använda typen. Åtgärds definitionen refererar sedan till dessa parametrar i `authentication` avsnittet.

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
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
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

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Säkra parametrar i Azure Resource Manager mallar

En Resource Manager-mall för en Logic app har `parameters` flera avsnitt. Om du vill skydda lösen ord, nycklar, hemligheter och annan känslig information definierar du skyddade parametrar på mallnivå-och arbets flödes definitions `secureobject` nivå med hjälp `securestring` av-eller-typen. Du kan sedan lagra dessa värden i [Azure Key Vault](../key-vault/key-vault-whatis.md) och använda [parameter filen](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values) för att referera till nyckel valvet och hemligheten. Din mall hämtar sedan den informationen vid distributionen. Mer information finns i [använda Azure Key Vault för att skicka säkra parameter värden vid distribution](../azure-resource-manager/resource-manager-keyvault-parameter.md).

Här är mer information om de `parameters` här avsnitten:

* På mallens översta nivå definierar ett `parameters` avsnitt parametrarna för de värden som mallen använder vid *distributionen*. Dessa värden kan till exempel innehålla anslutnings strängar för en enskild distributions miljö. Du kan sedan lagra dessa värden i en separat [parameter fil](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values), vilket gör det enklare att ändra dessa värden.

* I din Logic Apps resurs definition, men utanför arbets flödes definitionen, anger `parameters` ett avsnitt värdena för arbets flödets definitions parametrar. I det här avsnittet kan du tilldela dessa värden med hjälp av mall-uttryck som refererar till mallens parametrar. Dessa uttryck utvärderas vid distribution.

* I arbets flödes definitionen definierar `parameters` ett avsnitt de parametrar som din Logic app använder vid körning. Du kan sedan referera till dessa parametrar i din Logic Apps-arbetsflöde genom att använda definitions uttryck för arbets flöde, som utvärderas vid körning.

Den här exempel mal len har flera skyddade parameter definitioner som använder `securestring` typen:

| Parameternamn | Beskrivning |
|----------------|-------------|
| `TemplatePasswordParam` | En mallparameter som accepterar ett lösen ord som sedan skickas till arbets flödets definitions `basicAuthPasswordParam` parameter |
| `TemplatePasswordParam` | En mallparameter som accepterar ett användar namn som sedan skickas till arbets flödets definitions `basicAuthUserNameParam` parameter |
| `basicAuthPasswordParam` | En parameter för arbets flödes definition som godkänner lösen ordet för grundläggande autentisering i en HTTP-åtgärd |
| `basicAuthUserNameParam` | En parameter för arbets flödes definition som godkänner användar namnet för grundläggande autentisering i en HTTP-åtgärd |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
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
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
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
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
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
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Åtkomst till tjänster och system som anropas från Logic Apps

Här följer några exempel på hur du kan skydda slut punkter där din Logi Kap par behöver åtkomst till sändnings begär Anden:

* Lägg till autentisering för utgående begär Anden.

  När du arbetar med en HTTP-, HTTP + Swagger-eller webhook-åtgärd kan du lägga till autentisering till begäran som skickas av din Logic app. Du kan till exempel använda grundläggande autentisering, certifikatautentisering eller Azure Active Directory autentisering. Mer information finns i [autentisera utlösare eller åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

* Begränsa åtkomsten från Logic app-IP-adresser.

  Alla anrop till slut punkter från Logic Apps härstammar från angivna IP-adresser som baseras på dina Logic Apps-regioner. Du kan lägga till filtrering som bara accepterar begär Anden från dessa IP-adresser. För att hämta de här IP-adresserna, se [gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Autentisera och få åtkomst till resurser som hanterade identiteter.

  För att få åtkomst till resurser i andra Azure Active Directory (Azure AD)-klienter kan din Logi Kap par autentisera din identitet utan att logga in med hjälp av en hanterad identitet (tidigare Hanterad tjänstidentitet eller MSI) i stället för autentiseringsuppgifter eller hemligheter. Azure hanterar den här identiteten för dig och skyddar dina autentiseringsuppgifter eftersom du inte behöver ange eller rotera hemligheter. Mer information om hur du konfigurerar och använder en systemtilldelad hanterad identitet för din Logic app finns i [autentisera och få åtkomst till resurser med hanterade identiteter i Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

* Säkra anslutningar till lokala system.

  Azure Logic Apps tillhandahåller integrering med dessa tjänster för säker och tillförlitlig kommunikation på plats.

  * Lokal dataanslutningsgateway

    Många hanterade anslutningar i Azure Logic Apps ger säkra anslutningar till lokala system, till exempel fil system, SQL, SharePoint och DB2. Gatewayen skickar data från lokala källor på krypterade kanaler via Azure Service Bus. All trafik kommer som säker utgående trafik från Gateway-agenten. Lär dig [hur den lokala datagatewayen fungerar](logic-apps-gateway-install.md#gateway-cloud-service).

  * Anslut via Azure API Management

    [Azure API Management](../api-management/api-management-key-concepts.md) tillhandahåller lokala anslutnings alternativ, till exempel plats-till-plats-virtuellt privat nätverk och ExpressRoute-integrering för skyddad proxy och kommunikation till lokala system. Från din Logic Apps arbets flöde i Logic Apps Designer kan du välja ett API som exponeras av API Management, vilket ger snabb åtkomst till lokala system.

## <a name="next-steps"></a>Nästa steg

* [Skapa mallar för distribution](logic-apps-create-deploy-template.md)  
* [Övervaka dina logikappar](logic-apps-monitor-your-logic-apps.md)  
* [Diagnostisera fel och problem med Logic app](logic-apps-diagnosing-failures.md)  
* [Automatisera Logic app-distribution](logic-apps-azure-resource-manager-templates-overview.md)
