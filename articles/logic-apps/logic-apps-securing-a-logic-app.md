---
title: Säker åtkomst och data Azure Logic Apps
description: Lägg till säkerhet för att skydda indata, utdata, begär ande-baserade utlösare, körnings historik, hanterings uppgifter och åtkomst till andra resurser i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: b219eb50254ecd347f731d332c3236be028834ba
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73045038"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Säker åtkomst och data i Azure Logic Apps

Om du vill kontrol lera åtkomsten och skydda data i Azure Logic Apps kan du konfigurera säkerheten i följande områden:

* [Åtkomst till begär ande-baserade utlösare](#secure-triggers)
* [Åtkomst till Logic app-åtgärder](#secure-operations)
* [Åtkomst för att köra tidigare indata och utdata](#secure-run-history)
* [Åtkomst till parameter indata](#secure-action-parameters)
* [Åtkomst till tjänster och system som anropas från Logic Apps](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Åtkomst till begär ande-baserade utlösare

Om din Logic app använder en begärd utlösare, som tar emot inkommande samtal eller begär Anden, till exempel [begäran](../connectors/connectors-native-reqres.md) eller [webhook](../connectors/connectors-native-webhook.md) -utlösare, kan du begränsa åtkomsten så att endast auktoriserade klienter kan anropa din Logic app. Alla begär Anden som tas emot av en Logic app krypteras och skyddas med Secure Sockets Layer SSL-protokoll.

Följande är de sätt som du kan använda för att skydda åtkomsten till den här utlösaren:

* [Generera signaturer för delad åtkomst](#sas)
* [Begränsa inkommande IP-adresser](#restrict-inbound-ip-addresses)
* [Lägg till Azure Active Directory, OAuth eller annan säkerhet](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generera signaturer för delad åtkomst (SAS)

Varje begär ande slut punkt i en Logic app har en [signatur för delad åtkomst (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) i slut punktens URL, som följer detta format:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Varje URL innehåller Frågeparametern `sp`, `sv`och `sig` enligt beskrivningen i den här tabellen:

| Frågeparameter | Beskrivning |
|-----------------|-------------|
| `sp` | Anger behörigheter för de tillåtna HTTP-metoder som ska användas. |
| `sv` | Anger den SAS-version som ska användas för att skapa signaturen. |
| `sig` | Anger signaturen som ska användas för att autentisera åtkomsten till utlösaren. Den här signaturen genereras med hjälp av SHA256-algoritmen med en hemlig åtkomst nyckel på alla URL-sökvägar och egenskaper. Aldrig exponeras eller publiceras den här nyckeln krypteras och lagras med Logic app. Din Logic app godkänner bara de utlösare som innehåller en giltig signatur som skapats med den hemliga nyckeln. |
|||

Mer information om hur du skyddar åtkomst med SAS finns i följande avsnitt i det här avsnittet:

* [Återskapa åtkomst nycklar](#access-keys)
* [Skapa återanrops-URL: er som upphör](#expiring-urls)
* [Skapa URL: er med primär eller sekundär nyckel](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Återskapa åtkomstnycklar

Använd Azure REST API eller Azure Portal för att skapa en ny säker åtkomst nyckel när som helst. Alla tidigare genererade URL: er som använder den gamla nyckeln är ogiltiga och har inte längre behörighet att utlösa Logic-appen. URL: erna som du hämtar efter återskapandet är signerad med den nya åtkomst nyckeln.

1. I [Azure Portal](https://portal.azure.com)öppnar du den Logic-app som har den nyckel som du vill återskapa.

1. Gå till menyn för Logic-appen, Välj **åtkomst nycklar**under **Inställningar**.

1. Välj den nyckel som du vill återskapa och slutföra processen.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Skapa återanrops-URL: er som upphör

Om du delar slut punkts-URL: en för en begärd utlösare med andra parter kan du generera återanrops-URL: er som använder vissa nycklar och som har förfallo datum. På så sätt kan du sömlöst återställa nycklar eller begränsa åtkomsten till att utlösa din Logi Kap par baserat på ett angivet tidsintervall. Om du vill ange ett förfallo datum för en URL använder du [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), till exempel:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

I bröd texten inkluderar du `NotAfter`property med hjälp av en JSON-datum sträng. Den här egenskapen returnerar en återanrops-URL som bara är giltig förrän `NotAfter`-datum och-tid.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Skapa URL: er med primär eller sekundär hemlig nyckel

När du skapar eller listar återanrops-URL: er för en begärd utlösare kan du ange den nyckel som ska användas för att signera URL: en. Om du vill generera en URL som är signerad av en viss nyckel använder du [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), till exempel:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

I bröd texten inkluderar du egenskapen `KeyType` som antingen `Primary` eller `Secondary`. Den här egenskapen returnerar en URL som är signerad med den angivna säkra nyckeln.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Begränsa inkommande IP-adresser

Tillsammans med signaturen för delad åtkomst (SAS) kanske du vill begränsa vilka klienter som kan anropa din Logic app. Om du till exempel hanterar din begärans slut punkt med hjälp av Azure API Management, kan du begränsa din Logic-app så att den endast accepterar begär Anden från IP-adressen för API Management-instansen.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Begränsa inkommande IP-intervall i Azure Portal

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. På din Logic Apps-meny, under **Inställningar**, väljer du **arbets flödes inställningar**.

1. Under **konfiguration av åtkomst kontroll** > **tillåtna inkommande IP-adresser**väljer du **vissa IP-intervall**.

1. Under **IP-intervall för utlösare**anger du de IP-adressintervall som utlösaren accepterar.

   Ett giltigt IP-intervall använder följande format: *x. x. x. x/x* eller *x. x. x* . x-x. x

Om du vill att din Logic app endast ska utlösa som en kapslad Logic-app väljer du **endast andra Logic Apps**i listan **tillåtna inkommande IP-adresser** . Med det här alternativet skrivs en tom matris till din Logic app-resurs. På så sätt kan endast anrop från Logic Appss tjänsten (överordnade Logic Apps) utlösa den kapslade Logic-appen.

> [!NOTE]
> Oavsett IP-adress kan du fortfarande köra en Logic-app som har en begäran-baserad utlösare genom att använda `/triggers/<trigger-name>/run` via Azure-REST API eller via API Management. Det här scenariot kräver dock fortfarande autentisering mot Azure-REST API. Alla händelser visas i gransknings loggen i Azure. Se till att du anger principer för åtkomst kontroll i enlighet med detta.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Begränsa inkommande IP-intervall i Azure Resource Manager mall

Om du [automatiserar distributionen för logi Kap par med hjälp av Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du ange IP-intervallen med hjälp av avsnittet `accessControl` med avsnittet `triggers` i din Logic Apps resurs definition, till exempel:

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

Överväg att använda [Azure API Management](../api-management/api-management-key-concepts.md) -tjänsten om du vill lägga till fler Authorization-protokoll i din Logic app. Den här tjänsten hjälper dig att exponera din Logic app som ett API och innehåller omfattande övervakning, säkerhet, principer och dokumentation för alla slut punkter. API Management kan exponera en offentlig eller privat slut punkt för din Logic app. Du kan sedan använda Azure Active Directory, OAuth, certifikat eller andra säkerhets standarder för att auktorisera åtkomst till den slut punkten. När API Management tar emot en begäran skickar tjänsten begäran till din Logic app, vilket även gör nödvändiga transformeringar eller begränsningar på vägen. Om du bara vill låta API Management utlösa din Logic-app kan du använda din Logic Apps inställningar för inkommande IP-intervall.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Åtkomst till Logic app-åtgärder

Du kan endast tillåta vissa användare eller grupper att köra vissa uppgifter, till exempel hantera, redigera och Visa Logic Apps. Om du vill kontrol lera deras behörigheter använder du [Azure rollbaserad Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) så att du kan tilldela anpassade eller inbyggda roller till medlemmarna i din Azure-prenumeration:

* [Logic app-deltagare](../role-based-access-control/built-in-roles.md#logic-app-contributor): låter dig hantera Logi Kap par, men du kan inte ändra åtkomsten till dem.

* [Logic app-operator](../role-based-access-control/built-in-roles.md#logic-app-operator): låter dig läsa, aktivera och inaktivera Logic Apps, men du kan inte redigera eller uppdatera dem.

Om du vill hindra andra från att ändra eller ta bort din Logic app kan du använda [Azure Resource lock](../azure-resource-manager/resource-group-lock-resources.md). Den här funktionen förhindrar andra från att ändra eller ta bort produktions resurser.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Åtkomst till körning av historik data

När en Logic App körs [krypteras alla data under överföringen](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) med hjälp av Transport Layer Security (TLS) och [rest](../security/fundamentals/encryption-atrest.md). När din Logic-app är klar kan du Visa historiken för den körningen, inklusive de steg som kördes tillsammans med status, varaktighet, indata och utdata för varje åtgärd. Den här utförliga informationen ger inblick i hur din Logi Kap par kördes och var du kan börja felsöka eventuella problem som uppstår.

När du visar den logiska appens körnings historik autentiserar Logic Apps åtkomsten och tillhandahåller länkar till indata och utdata för begär Anden och svar för varje körning. För åtgärder som hanterar lösen ord, hemligheter, nycklar eller annan känslig information vill du dock hindra andra från att visa och komma åt dessa data. Om din Logic app till exempel får en hemlighet från [Azure Key Vault](../key-vault/key-vault-overview.md) att använda vid autentisering av en HTTP-åtgärd, vill du dölja hemligheten från vyn.

Om du vill kontrol lera åtkomsten till indata och utdata i din Logic Apps körnings historik har du följande alternativ:

* [Begränsa åtkomst efter IP-adressintervall](#restrict-ip).

  Med det här alternativet kan du skydda åtkomsten till körnings historik baserat på begär Anden från ett speciellt IP-adressintervall.

* [Dölj data från körnings historiken med hjälp av döljande](#obfuscate).

  I många utlösare och åtgärder kan du dölja indata, utdata eller båda från en Logic Apps körnings historik.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Begränsa åtkomst efter IP-adressintervall

Du kan begränsa åtkomsten till indata och utdata i din Logic Apps körnings historik så att endast förfrågningar från vissa IP-adressintervall kan visa dessa data. Om du till exempel vill blockera alla från att komma åt indata och utdata anger du ett IP-adressintervall som `0.0.0.0-0.0.0.0`. Endast en person med administratörs behörighet kan ta bort den här begränsningen, vilket ger möjlighet till just-in-Time-åtkomst till din Logic Apps-data. Du kan ange vilka IP-intervall som ska begränsas genom att använda Azure Portal eller i en Azure Resource Manager mall som du använder för Logic app-distribution.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Begränsa IP-intervall i Azure Portal

1. I Azure Portal öppnar du din Logic app i Logic Apps designer.

1. På din Logic Apps-meny, under **Inställningar**, väljer du **arbets flödes inställningar**.

1. Under **konfiguration av åtkomst kontroll** > **tillåtna inkommande IP-adresser**väljer du **vissa IP-intervall**.

1. Under **IP-intervall för innehåll**anger du de IP-adressintervall som har åtkomst till innehåll från indata och utdata. 

   Ett giltigt IP-intervall använder följande format: *x. x. x. x/x* eller *x. x. x* . x-x. x

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Begränsa IP-intervall i Azure Resource Manager mall

Om du [automatiserar distributionen för logi Kap par med hjälp av Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du ange IP-intervallen med hjälp av avsnittet `accessControl` med avsnittet `contents` i din Logic Apps resurs definition, till exempel:

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

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

   ![Öppna Logic-appen i Logic App Designer](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. På utlösaren eller åtgärden där du vill skydda data väljer du knappen med ellipser ( **...** ) och väljer sedan **Inställningar**.

   ![Öppna utlösare eller åtgärds inställningar](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Aktivera antingen **säkra indata**, **säkra utdata**eller både och. När du är klar väljer du **Klar**.

   ![Aktivera säkra indata eller utdata](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Åtgärden eller utlösaren visar nu en Lås ikon i namn listen.

   ![Åtgärds-eller Utlösarens namn List visar lås ikonen](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokens som representerar skyddade utdata från tidigare åtgärder visar också lås ikoner. Om du till exempel väljer utdata från listan med dynamiskt innehåll som ska användas i en åtgärd, visar denna token en Lås ikon.

   ![Välj token för skyddade utdata](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. När Logic-appen har körts kan du se historiken för den här körningen.

   1. I fönstret **Översikt** för Logic Apps väljer du den körning som du vill visa.

   1. I fönstret **körning av Logic-app** expanderar du de åtgärder som du vill granska.

      Om du väljer att skydda både indata och utdata visas dessa värden nu som dolda.

      ![Dolda indata och utdata i körnings historik](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Säkra indata och utdata i kodvyn

I den underliggande utlösaren eller åtgärds definitionen lägger du till eller uppdaterar matrisen `runtimeConfiguration.secureData.properties` med något av eller båda dessa värden:

* `"inputs"`: skyddar indata i körnings historik.
* `"outputs"`: skyddar utdata i körnings historiken.

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

* Om du vill skydda utdata från en åtgärd som skyddar indata eller om du uttryckligen använder säkra utdata, aktiverar du **säkert utdata** manuellt i den åtgärden.

* Kontrol lera att du aktiverar **säkra indata** eller **säkra utdata** i efterföljande åtgärder där du förväntar dig körnings historiken för att skydda dessa data.

  **Inställning av säkra utdata**

  När du aktiverar **säkra utdata** manuellt i en utlösare eller åtgärd, Logic Apps säkrar dessa utdata i körnings historiken. Om en underordnad åtgärd uttryckligen använder dessa skyddade utdata som indata, Logic Apps döljer den här åtgärdens indata i körnings historiken, men *aktiverar inte* åtgärdens inställning för **säker indata** .

  ![Skyddade utdata som indata och effekt påverkan på de flesta åtgärder](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Åtgärderna Skriv, parsa JSON och Response har bara inställningen **säkra indata** . När inställningen är aktive rad döljs även dessa åtgärders utdata. Om dessa åtgärder uttryckligen använder överordnade säkra utdata som indata, Logic Apps döljer dessa åtgärders indata och utdata, men *aktiverar inte* dessa åtgärders inställning för **säkra indata** . Om en underordnad åtgärd uttryckligen använder dolda utdata från Skriv-, parsa-JSON-eller Response-åtgärder som indata, kan Logic Apps *inte dölja den här underordnade åtgärdens indata eller utdata*.

  ![Skyddade utdata som indata med effekt på vissa åtgärder](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Inställningar för säker indata**

  När du aktiverar **säkra indata** manuellt i en utlösare eller åtgärd, Logic Apps säkrar dessa indata i körnings historiken. Om en underordnad åtgärd uttryckligen använder synliga utdata från den utlösaren eller åtgärden som indata, kan Logic Apps dölja denna underordnade åtgärds indata i körnings historiken, men *aktiverar inte* **säkra inmatningar** i den här åtgärden och döljer inte den här åtgärden utdata.

  ![Skyddade indata och effekt påverkan på de flesta åtgärder](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Om Skriv, parsa JSON och svars åtgärder uttryckligen använder synliga utdata från utlösaren eller åtgärden som har skyddade indata, Logic Apps döljer dessa åtgärders indata och utdata, men *aktiverar inte* den här åtgärdens **säkra indata** inställningen. Om en underordnad åtgärd uttryckligen använder dolda utdata från Skriv-, parsa-JSON-eller Response-åtgärder som indata, kan Logic Apps *inte dölja den här underordnade åtgärdens indata eller utdata*.

  ![Skyddade indata och effekter på vissa åtgärder](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Åtkomst till parameter indata

Om du distribuerar över olika miljöer bör du överväga att parametriserade värdena i arbets flödes definitionen som varierar beroende på dessa miljöer. På så sätt kan du undvika hårdkodade data med hjälp av en [Azure Resource Manager mall](../azure-resource-manager/template-deployment-overview.md) för att distribuera din Logic app, skydda känsliga data genom att definiera säkra parametrar och skicka dessa data som separata indata via [mallens parametrar](../azure-resource-manager/template-parameters.md) med en [parameter fil](../azure-resource-manager/resource-manager-parameter-files.md).

Om du till exempel autentiserar HTTP-åtgärder med [Azure Active Directory](#azure-active-directory-oauth-authentication)kan du definiera och skydda parametrarna som godkänner klient-ID och klient hemlighet som används för autentisering. Om du vill definiera dessa parametrar i din Logic app använder du avsnittet `parameters` i din Logic Apps arbets flödes definition och Resource Manager-mall för distribution. Om du vill dölja parameter värden som du inte vill ska visas när du redigerar din Logic app eller visar körnings historik definierar du parametrarna med hjälp av `securestring` eller `secureobject` typ och använder kodning vid behov. Parametrar som har den här typen returneras inte med resurs definitionen och är inte tillgängliga när du visar resursen efter distributionen. Om du vill komma åt dessa parameter värden under körningen använder du uttrycket `@parameters('<parameter-name>')` i arbets flödes definitionen. Det här uttrycket utvärderas bara vid körning och beskrivs av språket för [arbets flödes definitionen](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Om du använder en parameter i ett rubrik eller brödtext för begäran kan den parametern visas när du visar din Logic Apps körnings historik och utgående HTTP-begäran. Se till att du även anger dina principer för innehålls åtkomst. Du kan också använda [döljande](#obfuscate) för att dölja indata och utdata i körnings historiken. Authorization-rubriker visas aldrig i indata eller utdata. Så om en hemlighet används där, så kan det inte går att hämta hemligheten.

Mer information finns i följande avsnitt i det här avsnittet:

* [Säkra parametrar i arbets flödes definitioner](#secure-parameters-workflow)
* [Dölj data från körnings historiken med hjälp av döljande](#obfuscate)

Om du [automatiserar distributionen för logi Kap par med hjälp av Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du definiera säkra [mallparametrar](../azure-resource-manager/template-parameters.md), som utvärderas vid distribution, med hjälp av `securestring` och `secureobject` typer. Om du vill definiera mallparametrar använder du mallens översta nivå `parameters` avsnitt, som skiljer sig från arbets flödes definitionens `parameters` avsnitt. Använd en separat [parameter fil](../azure-resource-manager/resource-manager-parameter-files.md)för att ange värden för mallparametrar.

Om du till exempel använder hemligheter kan du definiera och använda säkra mallparametrar som hämtar dessa hemligheter från [Azure Key Vault](../key-vault/key-vault-overview.md) vid distributionen. Sedan kan du referera till nyckel valvet och hemligheten i parameter filen. Mer information finns i följande avsnitt:

* [Skicka känsliga värden vid distribution med hjälp av Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* [Säkra parametrar i Azure Resource Manager mallar](#secure-parameters-deployment-template) senare i det här avsnittet

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Säkra parametrar i arbets flödes definitioner

Om du vill skydda känslig information i din Logic Apps arbets flödes definition använder du skyddade parametrar så att informationen inte visas när du har sparat din Logic app. Anta till exempel att du har en HTTP-åtgärd som kräver grundläggande autentisering, som använder ett användar namn och lösen ord. I arbets flödes definitionen definierar avsnittet `parameters` parametrarna `basicAuthPasswordParam` och `basicAuthUsernameParam` med hjälp av typen `securestring`. Åtgärds definitionen refererar sedan till dessa parametrar i avsnittet `authentication`.

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

En [Resource Manager-mall](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) för en Logic app har flera `parameters` avsnitt. För att skydda lösen ord, nycklar, hemligheter och annan känslig information, definiera säkra parametrar på mallnivå-och arbets flödes definitions nivå genom att använda typen `securestring` eller `secureobject`. Du kan sedan lagra dessa värden i [Azure Key Vault](../key-vault/key-vault-overview.md) och använda [parameter filen](../azure-resource-manager/resource-manager-parameter-files.md) för att referera till nyckel valvet och hemligheten. Din mall hämtar sedan den informationen vid distributionen. Mer information finns i [Skicka känsliga värden vid distribution med hjälp av Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

Här är mer information om dessa `parameters`-avsnitt:

* På mallens översta nivå definierar ett `parameters`-avsnittet parametrarna för de värden som mallen använder vid *distributionen*. Dessa värden kan till exempel innehålla anslutnings strängar för en enskild distributions miljö. Du kan sedan lagra dessa värden i en separat [parameter fil](../azure-resource-manager/resource-manager-parameter-files.md), vilket gör det enklare att ändra dessa värden.

* I din Logic Apps resurs definition, men utanför arbets flödes definitionen, anger ett `parameters`-avsnitt värdena för arbets flödets definitions parametrar. I det här avsnittet kan du tilldela dessa värden med hjälp av mall-uttryck som refererar till mallens parametrar. Dessa uttryck utvärderas vid distribution.

* I arbets flödes definitionen definierar ett `parameters`-avsnittet de parametrar som din Logic app använder vid körning. Du kan sedan referera till dessa parametrar i din Logic Apps-arbetsflöde genom att använda definitions uttryck för arbets flöde, som utvärderas vid körning.

Den här exempel mal len har flera skyddade parameter definitioner som använder `securestring`-typen:

| Parameternamn | Beskrivning |
|----------------|-------------|
| `TemplatePasswordParam` | En mallparameter som accepterar ett lösen ord som sedan skickas till arbets flödes definitionens `basicAuthPasswordParam`-parameter |
| `TemplateUsernameParam` | En mallparameter som accepterar ett användar namn som sedan skickas till arbets flödes definitionens `basicAuthUserNameParam`-parameter |
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

Här följer några exempel på hur du kan skydda slut punkter som tar emot samtal eller förfrågningar från din Logic app:

* Lägg till autentisering i utgående begär Anden.

  När du arbetar med en HTTP-baserad utlösare eller åtgärd som gör utgående samtal, till exempel HTTP, HTTP + Swagger eller webhook, kan du lägga till autentisering till den begäran som skickas av din Logic app. Du kan till exempel använda grundläggande autentisering, autentisering av klient certifikat, [Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) -autentisering eller en hanterad identitet. Mer information finns i [lägga till autentisering till utgående samtal](#add-authentication-outbound) senare i det här avsnittet.

* Begränsa åtkomsten från Logic app-IP-adresser.

  Alla anrop till slut punkter från Logic Apps härstammar från angivna IP-adresser som baseras på dina Logic Apps-regioner. Du kan lägga till filtrering som bara accepterar begär Anden från dessa IP-adresser. För att hämta de här IP-adresserna, se [gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Säkra anslutningar till lokala system.

  Azure Logic Apps tillhandahåller integrering med dessa tjänster för säker och tillförlitlig kommunikation på plats.

  * Lokal datagateway

    Många hanterade anslutningar i Azure Logic Apps ger säkra anslutningar till lokala system, till exempel fil system, SQL, SharePoint och DB2. Gatewayen skickar data från lokala källor på krypterade kanaler via Azure Service Bus. All trafik kommer som säker utgående trafik från Gateway-agenten. Lär dig [hur den lokala datagatewayen fungerar](logic-apps-gateway-install.md#gateway-cloud-service).

  * Anslut via Azure API Management

    [Azure API Management](../api-management/api-management-key-concepts.md) tillhandahåller lokala anslutnings alternativ, till exempel plats-till-plats-virtuellt privat nätverk och ExpressRoute-integrering för skyddad proxy och kommunikation till lokala system. Från din Logic Apps arbets flöde i Logic Apps Designer kan du välja ett API som exponeras av API Management, vilket ger snabb åtkomst till lokala system.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Lägg till autentisering i utgående samtal

HTTP-och HTTPS-slutpunkter stöder olika typer av autentisering. Baserat på utlösaren eller åtgärden som du använder för att göra utgående anrop eller begär Anden som har åtkomst till dessa slut punkter, kan du välja mellan olika typer av autentiseringstyper. För att säkerställa att du skyddar känslig information som din Logic app hanterar, Använd skyddade parametrar och koda data vid behov. Mer information om hur du använder och skyddar parametrar finns i [åtkomst till parameter indata](#secure-action-parameters).

| Autentiseringstyp | Stöds av |
|---------------------|--------------|
| [Basic](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP webhook |
| [Klient certifikat](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP-webhook |
| [Outspädd](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP-webhook |
| [Hanterad identitet](#managed-identity-authentication) (endast system-Assigned) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP-webhook |
|||

> [!NOTE]
> I Logic App Designer kan egenskapen **Authentication** vara dold på vissa utlösare och åtgärder där du kan ange autentiseringstyp. Om du vill att egenskapen ska visas i dessa fall går du till utlösaren eller åtgärden och öppnar listan **Lägg till ny parameter** och väljer **autentisering**. Mer information finns i [autentisera åtkomst med hanterad identitet](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Grundläggande autentisering

Om alternativet [grundläggande](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) är tillgängligt anger du följande egenskaps värden:

| Egenskap (designer) | Egenskap (JSON) | Krävs | Värde | Beskrivning |
|---------------------|-----------------|----------|-------|-------------|
| **Autentisering** | `type` | Ja | Basic | Autentiseringstypen som ska användas |
| **Användarnamn** | `username` | Ja | <*användar namn*>| Användar namnet för att autentisera åtkomsten till mål tjänstens slut punkt |
| **Lösenord** | `password` | Ja | <*lösen ord*> | Lösen ordet för att autentisera åtkomsten till mål tjänstens slut punkt |
||||||

När du använder [skyddade parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en [Azure Resource Manager mall för automatisk distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kan du använda uttryck för att få åtkomst till dessa parameter värden vid körning. Detta exempel på en HTTP-åtgärds definition anger autentiseringen `type` som `Basic` och använder [funktionen parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parameter värden:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Autentisering av klient certifikat

Om alternativet [klient certifikat](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) är tillgängligt anger du följande egenskaps värden:

| Egenskap (designer) | Egenskap (JSON) | Krävs | Värde | Beskrivning |
|---------------------|-----------------|----------|-------|-------------|
| **Autentisering** | `type` | Ja | **Klient certifikat** <br>eller <br>`ClientCertificate` | Autentiseringstypen som ska användas för Secure Sockets Layer (SSL)-klient certifikat. Även om självsignerade certifikat stöds, stöds inte självsignerade certifikat för SSL. |
| **-** | `pfx` | Ja | <- *kodad-PFX-File-content*> | Det Base64-kodade innehållet från en PFX-fil (personal information Exchange) |
| **Lösenord** | `password`| Ja | <*password-for-PFX-file*> | Lösen ordet för att komma åt PFX-filen |
|||||

När du använder [skyddade parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en [Azure Resource Manager mall för automatisk distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kan du använda uttryck för att få åtkomst till dessa parameter värden vid körning. Detta exempel på en HTTP-åtgärds definition anger autentiseringen `type` som `ClientCertificate` och använder [funktionen parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parameter värden:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Mer information om hur du skyddar tjänster med hjälp av autentisering av klient certifikat finns i följande avsnitt:

* [Säkra API: er med autentisering av klient certifikat i Azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Säkra backend-tjänster med autentisering av klient certifikat i Azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Skydda din RESTfuL-tjänst genom att använda klient certifikat](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
* [Autentiseringsuppgifter för program-autentisering](../active-directory/develop/active-directory-certificate-credentials.md)
* [Använd ett SSL-certifikat i program koden i Azure App Service](../app-service/app-service-web-ssl-cert-load.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure Active Directory OAuth-autentisering

Om alternativet [Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) är tillgängligt anger du följande egenskaps värden:

| Egenskap (designer) | Egenskap (JSON) | Krävs | Värde | Beskrivning |
|---------------------|-----------------|----------|-------|-------------|
| **Autentisering** | `type` | Ja | **Active Directory OAuth** <br>eller <br>`ActiveDirectoryOAuth` | Autentiseringstypen som ska användas. Logic Apps följer för närvarande [OAuth 2,0-protokollet](../active-directory/develop/v2-overview.md). |
| **Innehav** | `tenant` | Ja | <*klient organisations-ID* > | Klient-ID för Azure AD-klienten |
| **Filmen** | `audience` | Ja | <*resurs-till-auktorisera* > | Den resurs som du vill använda för auktorisering, till exempel `https://management.core.windows.net/` |
| **Klient-ID** | `clientId` | Ja | <*klient-ID* > | Klient-ID för appen som begär auktorisering |
| **Autentiseringstyp** | `credentialType` | Ja | Certifikat <br>eller <br>Hemlighet | Autentiseringstypen som klienten använder för att begära auktorisering. Den här egenskapen och värdet visas inte i din Logic Apps underliggande definition, men avgör vilka egenskaper som visas för den valda autentiseringstypen. |
| **Hemlighet** | `secret` | Ja, men endast för autentiseringstypen "hemlig" | <*klient hemlighet*> | Klient hemligheten för att begära auktorisering |
| **-** | `pfx` | Ja, men endast för Credential-typen "certifikat" | <- *kodad-PFX-File-content*> | Det Base64-kodade innehållet från en PFX-fil (personal information Exchange) |
| **Lösenord** | `password` | Ja, men endast för Credential-typen "certifikat" | <*password-for-PFX-file*> | Lösen ordet för att komma åt PFX-filen |
| **Tullmyndighet** | `authority` | Nej | <*URL-för-Authority-token-utfärdare* > | URL för den myndighet som tillhandahåller autentiseringstoken. Som standard är det här värdet `https://login.windows.net`. <p>**Obs!** om du vill att den här egenskapen ska vara synlig i designern går du till utlösaren eller åtgärden och öppnar listan **Lägg till ny parameter** och väljer **auktoritet**. |
|||||

När du använder [skyddade parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en [Azure Resource Manager mall för automatisk distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kan du använda uttryck för att få åtkomst till dessa parameter värden vid körning. Det här exemplet på en HTTP-åtgärd anger autentiseringen `type` som `ActiveDirectoryOAuth`, autentiseringstypen typ `Secret`och använder [funktionen parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parameter värden:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>RAW-autentisering

Om alternativet **RAW** är tillgängligt kan du använda den här autentiseringstypen när du måste använda [autentiseringsscheman](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) som inte följer [OAuth 2,0-protokollet](https://oauth.net/2/). Med den här typen skapar du ett Authorization-huvud-värde som du skickar med den utgående begäran, och anger detta huvud värde i utlösaren eller åtgärden.

Här är till exempel ett exempel på en rubrik för en HTTPS-begäran som följer [OAuth 1,0-protokollet](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

I utlösaren eller åtgärden som stöder RAW-autentisering anger du följande egenskaps värden:

| Egenskap (designer) | Egenskap (JSON) | Krävs | Värde | Beskrivning |
|---------------------|-----------------|----------|-------|-------------|
| **Autentisering** | `type` | Ja | outspädd | Autentiseringstypen som ska användas |
| **Värde** | `value` | Ja | <*Authorization-Head-value*> | Det Authorization-huvud värde som ska användas för autentisering |
||||||

När du använder [skyddade parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en [Azure Resource Manager mall för automatisk distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kan du använda uttryck för att få åtkomst till dessa parameter värden vid körning. Detta exempel på en HTTP-åtgärds definition anger autentiseringen `type` som `Raw`, och använder [funktionen parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parameter värden:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>Autentisering av hanterad identitet

Om alternativet för [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) är tillgängligt kan din Logic app använda den systemtilldelade identiteten för att autentisera åtkomsten till resurser i andra Azure Active Directory (Azure AD)-klienter utan att logga in. Azure hanterar den här identiteten för dig och skyddar dina autentiseringsuppgifter eftersom du inte behöver ange eller rotera hemligheter. Läs mer om [Azure-tjänster som har stöd för hanterade identiteter för Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Innan din Logic-app kan använda den systemtilldelade identiteten följer du stegen i [autentisera åtkomst till Azure-resurser med hjälp av hanterade identiteter i Azure Logic Apps](../logic-apps/create-managed-service-identity.md). De här stegen aktiverar den hanterade identiteten på din Logic app och konfigurerar identitetens åtkomst till Azure-resursen.

1. Innan en Azure-funktion kan använda den systemtilldelade identiteten [aktiverar du först autentisering för Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. I utlösaren eller åtgärden där du vill använda den hanterade identiteten anger du följande egenskaps värden:

   | Egenskap (designer) | Egenskap (JSON) | Krävs | Värde | Beskrivning |
   |---------------------|-----------------|----------|-------|-------------|
   | **Autentisering** | `type` | Ja | **Hanterad identitet** <br>eller <br>`ManagedServiceIdentity` | Autentiseringstypen som ska användas |
   | **Filmen** | `audience` | Ja | <*target-resurs-ID*> | Resurs-ID för den mål resurs som du vill komma åt. <p>`https://storage.azure.com/` till exempel blir åtkomsttoken för autentisering giltig för alla lagrings konton. Du kan dock också ange en rot tjänst-URL, till exempel `https://fabrikamstorageaccount.blob.core.windows.net` för ett angivet lagrings konto. <p>**Obs**: den här egenskapen kan vara dold i vissa utlösare eller åtgärder. Om du vill att den här egenskapen ska vara synlig i utlösaren eller åtgärden öppnar du listan **Lägg till ny parameter** och väljer **mål grupp**. <p><p>**Viktigt**: se till att det här mål resurs-ID: t exakt matchar det värde som Azure AD förväntar sig, inklusive eventuella avslutande snedstreck. Det betyder att `https://storage.azure.com/` resurs-ID för alla Azure Blob Storage-konton kräver ett avslutande snedstreck. Resurs-ID för ett angivet lagrings konto kräver dock inte något avslutande snedstreck. Du hittar dessa resurs-ID: n i [Azure-tjänster som stöder Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   När du använder [skyddade parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en [Azure Resource Manager mall för automatisk distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kan du använda uttryck för att få åtkomst till dessa parameter värden vid körning. Detta exempel på en HTTP-åtgärds definition anger autentiseringen `type` som `ManagedServiceIdentity` och använder [funktionen parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parameter värden:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>Nästa steg

* [Automatisera distribution för Azure Logic Apps](logic-apps-azure-resource-manager-templates-overview.md)  
* [Övervaka dina logikappar](logic-apps-monitor-your-logic-apps.md)  
* [Diagnostisera fel och problem med Logic app](logic-apps-diagnosing-failures.md)  
* [Automatisera Logic app-distribution](logic-apps-azure-resource-manager-templates-overview.md)
