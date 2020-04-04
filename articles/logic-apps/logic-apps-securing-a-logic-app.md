---
title: Skydda åtkomst och data
description: Säker åtkomst till indata, utdata, begärandebaserade utlösare, körningshistorik, hanteringsuppgifter och åtkomst till andra resurser i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 4fc4960eb3af8a3d3c9902c9b24505bb5610b709
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657164"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Säker åtkomst och data i Azure Logic Apps

Om du vill styra åtkomst och skydda data i Azure Logic Apps kan du ställa in säkerhet i följande områden:

* [Tillgång till begärandebaserade utlösare](#secure-triggers)
* [Tillgång till logikappåtgärder](#secure-operations)
* [Åtkomst till körningshistorikindata och utdata](#secure-run-history)
* [Tillgång till parameterindata](#secure-action-parameters)
* [Tillgång till tjänster och system som anropas från logikappar](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Tillgång till begärandebaserade utlösare

Om logikappen använder en begäran-baserad utlösare, som tar emot inkommande samtal eller begäranden, till exempel [begäran eller](../connectors/connectors-native-reqres.md) [Webhook-utlösaren,](../connectors/connectors-native-webhook.md) kan du begränsa åtkomsten så att endast behöriga klienter kan anropa logikappen. Alla begäranden som tas emot av en logikapp krypteras och skyddas med TLS (Transport Layer Security), som tidigare kallades SSL (Secure Sockets Layer), som tidigare kallades SSL (Secure Sockets Layer).

Här är några alternativ som kan hjälpa dig att skydda åtkomsten till den här utlösartypen:

* [Generera signaturer för delad åtkomst](#sas)
* [Begränsa inkommande IP-adresser](#restrict-inbound-ip-addresses)
* [Lägga till OAuth för Azure Active Directory eller annan säkerhet](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generera signaturer för delad åtkomst (SAS)

Varje slutpunkt för begäran i en logikapp har en [SAS -signatur (Shared Access Signature)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) i slutpunktens URL, som följer det här formatet:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Varje URL innehåller `sp` `sv`parametern `sig` , och frågeparameter enligt beskrivningen i den här tabellen:

| Parameter för fråga | Beskrivning |
|-----------------|-------------|
| `sp` | Anger behörigheter för de tillåtna HTTP-metoder som ska användas. |
| `sv` | Anger den SAS-version som ska användas för att generera signaturen. |
| `sig` | Anger den signatur som ska användas för att autentisera åtkomsten till utlösaren. Den här signaturen genereras med hjälp av SHA256-algoritmen med en hemlig åtkomstnyckel på alla URL-sökvägar och egenskaper. Aldrig exponeras eller publiceras, den här nyckeln hålls krypterad och lagras med logikappen. Logikappen auktoriserar endast de utlösare som innehåller en giltig signatur som skapats med den hemliga nyckeln. |
|||

Mer information om hur du skyddar åtkomsten med SAS finns i följande avsnitt i det här avsnittet:

* [Återskapa åtkomstnycklar](#access-keys)
* [Skapa utgående url:er för motringning](#expiring-urls)
* [Skapa webbadresser med primär eller sekundär nyckel](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Återskapa åtkomstnycklar

Om du vill generera en ny säkerhetsåtkomstnyckel när som helst använder du Azure REST API eller Azure-portalen. Alla tidigare genererade webbadresser som använder den gamla nyckeln är ogiltiga och har inte längre behörighet att utlösa logikappen. Webbadresserna som du hämtar efter förnyelse är signerade med den nya åtkomstnyckeln.

1. Öppna logikappen som har nyckeln som du vill återskapa i [Azure-portalen.](https://portal.azure.com)

1. Välj **Åtkomstnycklar**under **Inställningar**på logikappens meny .

1. Välj den nyckel som du vill återskapa och slutföra processen.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Skapa utgående url:er för motringning

Om du delar slutpunkts-URL:en för en begäran-baserad utlösare med andra parter kan du generera motringningsadresser som använder specifika nycklar och har förfallodatum. På så sätt kan du sömlöst rulla nycklar eller begränsa åtkomsten till att utlösa logikappen baserat på en viss tidsrymd. Om du vill ange ett utgångsdatum för en URL använder du [REST-API:et](https://docs.microsoft.com/rest/api/logic/workflowtriggers)för Logic Apps, till exempel:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Inkludera egenskapen i `NotAfter`brödtexten med hjälp av en JSON-datumsträng. Den här egenskapen `NotAfter` returnerar en motringnings-URL som bara är giltig fram till datum och tid.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Skapa webbadresser med primär eller sekundär hemlig nyckel

När du skapar eller listar motringningsadresser för en begäran-baserad utlösare kan du ange vilken nyckel som ska användas för att signera WEBBADRESSEN. Om du vill generera en URL som signeras av en viss nyckel använder du [REST-API:et](https://docs.microsoft.com/rest/api/logic/workflowtriggers)för Logic Apps, till exempel:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

I kroppen, inkludera `KeyType` egenskapen `Primary` `Secondary`som antingen eller . Den här egenskapen returnerar en URL som signeras av den angivna säkerhetsnyckeln.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Begränsa inkommande IP-adresser

Tillsammans med SAS (Shared Access Signature) kanske du specifikt vill begränsa de klienter som kan anropa logikappen. Om du till exempel hanterar slutpunkten för begäran med hjälp av Azure API Management kan du begränsa logikappen så att den bara accepterar begäranden från IP-adressen för API Management-instansen.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Begränsa inkommande IP-intervall i Azure-portalen

1. Öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Välj **Arbetsflödesinställningar**under **Inställningar**på logikappens meny .

1. Under **Åtkomstkontrollkonfiguration** > **Tillåten inkommande IP-adresser**väljer du Specifika **IP-intervall**.

1. Under **IP-intervall för utlösare**anger du de IP-adressintervall som utlösaren accepterar.

   Ett giltigt IP-intervall använder dessa format: *x.x.x.x/x* eller *x.x.x.x-x.x.x.x.x.x*

Om du vill att logikappen bara ska utlösas som en kapslad logikapp väljer du **Endast andra logicappar**i listan **Tillåtna inkommande IP-adresser** . Det här alternativet skriver en tom matris till logikappresursen. På så sätt kan endast anrop från Logic Apps-tjänsten (överordnade logikappar) utlösa den kapslade logikappen.

> [!NOTE]
> Oavsett IP-adress kan du fortfarande köra en logikapp som `/triggers/<trigger-name>/run` har en begäran-baserad utlösare med hjälp av Azure REST API eller via API Management. Det här scenariot kräver dock fortfarande autentisering mot Azure REST API. Alla händelser visas i Azure Audit Log. Se till att du anger principer för åtkomstkontroll därefter.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Begränsa inkommande IP-intervall i Azure Resource Manager-mall

Om du [automatiserar distributionen för logikappar med hjälp av Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du ange IP-intervallen `accessControl` genom att använda avsnittet med avsnittet i logikappens `triggers` resursdefinition, till exempel:

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Lägga till OAuth för Azure Active Directory eller annan säkerhet

Om du vill lägga till fler auktoriseringsprotokoll i logikappen bör du överväga att använda [Azure API Management-tjänsten.](../api-management/api-management-key-concepts.md) Den här tjänsten hjälper dig att exponera din logikapp som ett API och erbjuder omfattande övervakning, säkerhet, princip och dokumentation för alla slutpunkter. API Management kan exponera en offentlig eller privat slutpunkt för logikappen. Om du vill auktorisera åtkomst till den här slutpunkten kan du använda [Azure Active Directory OAuth,](#azure-active-directory-oauth-authentication) [klientcertifikat](#client-certificate-authentication)eller andra säkerhetsstandarder för att auktorisera åtkomst till den slutpunkten. När API Management tar emot en begäran skickar tjänsten begäran till din logikapp, vilket också gör nödvändiga omvandlingar eller begränsningar längs vägen. Om du bara vill att API Management endast ska utlösa logikappen kan du använda logikappens inkommande IP-intervallinställningar.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Tillgång till logikappåtgärder

Du kan bara tillåta att specifika användare eller grupper kör specifika uppgifter, till exempel hantera, redigera och visa logikappar. Om du vill styra deras behörigheter använder du [RBAC (Azure Role-Based Access Control)](../role-based-access-control/role-assignments-portal.md) så att du kan tilldela anpassade eller inbyggda roller till medlemmarna i din Azure-prenumeration:

* [Logic App Contributor](../role-based-access-control/built-in-roles.md#logic-app-contributor): Låter dig hantera logikappar, men du kan inte ändra åtkomst till dem.

* [Logic App Operator](../role-based-access-control/built-in-roles.md#logic-app-operator): Låter dig läsa, aktivera och inaktivera logikappar, men du kan inte redigera eller uppdatera dem.

Om du vill förhindra att andra ändrar eller tar bort logikappen kan du använda [Azure Resource Lock](../azure-resource-manager/management/lock-resources.md). Den här funktionen hindrar andra från att ändra eller ta bort produktionsresurser.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Åtkomst till körningshistorikdata

Under en logikappkörning [krypteras](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) alla data under överföringen med hjälp av TLS (Transport Layer Security) och [i vila](../security/fundamentals/encryption-atrest.md). När logikappen körs kan du visa historiken för den körningen, inklusive stegen som kördes tillsammans med status, varaktighet, indata och utdata för varje åtgärd. Den här omfattande detaljen ger insikt i hur logikappen kördes och var du kan börja felsöka eventuella problem som uppstår.

När du visar logikappens körhistorik autentiserar Logic Apps din åtkomst och tillhandahåller sedan länkar till indata och utdata för begäranden och svar för varje körning. Men för åtgärder som hanterar lösenord, hemligheter, nycklar eller annan känslig information vill du hindra andra från att visa och komma åt dessa data. Om logikappen till exempel får en hemlighet från [Azure Key Vault](../key-vault/key-vault-overview.md) som ska användas när du autentiserar en HTTP-åtgärd, vill du dölja hemligheten från vyn.

Om du vill styra åtkomsten till in- och utgångarna i logikappens körningshistorik har du följande alternativ:

* [Begränsa åtkomsten per IP-adressintervall](#restrict-ip).

  Det här alternativet hjälper dig att skydda åtkomsten till körningshistorik baserat på begäranden från ett visst IP-adressintervall.

* [Dölj data från körningshistorik med hjälp av förvirring](#obfuscate).

  I många utlösare och åtgärder kan du dölja deras indata, utdata eller båda från en logikapps körningshistorik.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Begränsa åtkomsten per IP-adressintervall

Du kan begränsa åtkomsten till in- och utdata i logikappens körningshistorik så att endast begäranden från specifika IP-adressintervall kan visa dessa data. Om du till exempel vill blockera alla från att komma åt `0.0.0.0-0.0.0.0`indata och utdata anger du ett IP-adressintervall som . Endast en person med administratörsbehörighet kan ta bort den här begränsningen, vilket ger möjlighet till "just-in-time"-åtkomst till logikappens data. Du kan ange IP-intervallen för att begränsa antingen med hjälp av Azure-portalen eller i en Azure Resource Manager-mall som du använder för logikappdistribution.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Begränsa IP-intervall i Azure-portalen

1. Öppna logikappen i Logic App Designer i Azure-portalen.

1. Välj **Arbetsflödesinställningar**under **Inställningar**på logikappens meny .

1. Under **Åtkomstkontrollkonfiguration** > **Tillåten inkommande IP-adresser**väljer du Specifika **IP-intervall**.

1. Under **IP-intervall för innehåll**anger du de IP-adressintervall som kan komma åt innehåll från indata och utdata. 

   Ett giltigt IP-intervall använder dessa format: *x.x.x.x/x* eller *x.x.x.x-x.x.x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Begränsa IP-intervall i Azure Resource Manager-mall

Om du [automatiserar distributionen för logikappar med hjälp av Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du ange IP-intervallen `accessControl` genom att använda avsnittet med avsnittet i logikappens `contents` resursdefinition, till exempel:

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

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Dölja data från körningshistorik med hjälp av förvirring

Många utlösare och åtgärder har inställningar för att dölja indata, utdata eller båda från en logikapps körningshistorik. Här är några [funderingar att granska](#obfuscation-considerations) när du använder de här inställningarna för att skydda dessa data.

#### <a name="hide-inputs-and-outputs-in-the-designer"></a>Dölja in- och utdata i designern

1. Öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

   ![Öppna logikapp i Logic App Designer](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. På utlösaren eller åtgärden där du vill dölja känsliga data markerar du knappen ellipser (**...**) och väljer sedan **Inställningar**.

   ![Öppna utlösar- eller åtgärdsinställningar](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Aktivera antingen **säkra ingångar,** **säkra utdata**eller båda. När du är klar väljer du **Klar**.

   ![Aktivera "Säkra ingångar" eller "Säkra utgångar"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Åtgärden eller utlösaren visar nu en låsikon i namnlisten.

   ![Åtgärd eller utlösa namnlist visar låsikon](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokens som representerar säkra utdata från tidigare åtgärder visar också låsikoner. När du till exempel väljer en sådan utdata från listan med dynamiskt innehåll som ska användas i en åtgärd, visar den token en låsikon.

   ![Välj token för säker utdata](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. När logikappen har körts kan du visa historiken för den körningen.

   1. I fönstret Översikt **i** logikappen väljer du den körning som du vill visa.

   1. Expandera de åtgärder som du vill granska i fönstret **Logikappkörning.**

      Om du väljer att dölja både indata och utdata visas dessa värden nu dolda.

      ![Dolda in- och utdata i körningshistorik](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="hide-inputs-and-outputs-in-code-view"></a>Dölja indata och utdata i kodvyn

I den underliggande utlösaren eller åtgärdsdefinitionen lägger du till eller uppdaterar matrisen `runtimeConfiguration.secureData.properties` med något av eller båda av dessa värden:

* `"inputs"`: Säkrar ingångar i körningshistoriken.
* `"outputs"`: Säkrar utdata i körningshistoriken.

Här är några [funderingar att granska](#obfuscation-considerations) när du använder de här inställningarna för att skydda dessa data.

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

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Överväganden när du döljer indata och utdata

* När du skymmer indata eller utdata på en utlösare eller åtgärd skickar Logic Apps inte skyddade data till Azure Log Analytics. Du kan inte heller lägga till [spårade egenskaper](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) till den utlösaren eller åtgärden för övervakning.

* [Api:et för logikappar för hantering av arbetsflödeshistorik](https://docs.microsoft.com/rest/api/logic/) returnerar inte skyddade utdata.

* Om du vill dölja utdata från en åtgärd som skymmer indata eller uttryckligen skymmer utdata aktiverar du manuellt **säkra utdata** i den åtgärden.

* Se till att du aktiverar **säkra indata** eller säkra utdata i nedströmsåtgärder där du förväntar dig att körningshistoriken ska dölja dessa data. **Secure Outputs**

  **Inställning för säkra utdata**

  När du aktiverar **säkra utdata** manuellt i en utlösare eller åtgärd säkrar Logic Apps dessa utdata i körningshistoriken. Om en nedströmsåtgärd uttryckligen använder dessa skyddade utdata som indata döljer Logic Apps den här åtgärdens indata i körningshistoriken, men *aktiverar inte* åtgärdens inställning för **säkra indata.**

  ![Säkrade utdata som ingångar och nedströms påverkan på de flesta åtgärder](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Åtgärderna Komponera, Parse JSON och Svar har bara inställningen **Säkra ingångar.** När den är aktiverad döljer inställningen även dessa åtgärders utdata. Om dessa åtgärder uttryckligen använder de säkrade utdata uppströms som indata döljer Logic Apps dessa åtgärders indata och utdata, men *aktiverar inte* inställningen för säkra **indata** för dessa åtgärder. Om en nedströmsåtgärd uttryckligen använder de dolda utgångarna från åtgärderna Komponera, Parse JSON eller Svar som indata döljer Logic Apps *inte den underordnade åtgärdens indata eller utdata.*

  ![Säkrade utdata som ingångar med nedströmspåverkan på specifika åtgärder](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Inställning för säkra ingångar**

  När du aktiverar **säkra indata** manuellt i en utlösare eller åtgärd skyddar Logic Apps dessa indata i körningshistoriken. Om en nedströmsåtgärd uttryckligen använder de synliga utdata från utlösaren eller åtgärden som indata döljer Logic Apps den nedströmsåtgärdens indata i körningshistoriken, men *aktiverar inte* **säkra indata** i den här åtgärden och döljer inte den här åtgärdens utdata.

  ![Säkrade ingångar och nedströms påverkan på de flesta åtgärder](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Om åtgärderna Komponera, Parse JSON och Svar uttryckligen använder de synliga utdata från utlösaren eller åtgärden som har de säkrade ingångarna, döljer Logic Apps dessa åtgärders indata och utdata, men *aktiverar inte* inställningen För **säker indata** för åtgärden. Om en nedströmsåtgärd uttryckligen använder de dolda utgångarna från åtgärderna Komponera, Parse JSON eller Svar som indata döljer Logic Apps *inte den underordnade åtgärdens indata eller utdata.*

  ![Säkrade insatsvaror och nedströms påverkan på specifika åtgärder](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Tillgång till parameterindata

Om du distribuerar över olika miljöer bör du överväga att parameterisera värdena i arbetsflödesdefinitionen som varierar beroende på dessa miljöer. På så sätt kan du undvika hårdkodade data genom att använda en [Azure Resource Manager-mall](../azure-resource-manager/templates/overview.md) för att distribuera logikappen, skydda känsliga data genom att definiera säkra parametrar och skicka dessa data som separata indata via [mallens parametrar](../azure-resource-manager/templates/template-parameters.md) med hjälp av en [parameterfil](../azure-resource-manager/templates/parameter-files.md).

Om du till exempel autentiserar HTTP-åtgärder med [Azure Active Directory OAuth](#azure-active-directory-oauth-authentication)kan du definiera och dölja de parametrar som accepterar klient-ID och klienthemlighet som används för autentisering. Om du vill definiera dessa parametrar `parameters` i logikappen använder du avsnittet i logikappens arbetsflödesdefinition och Resource Manager-mall för distribution. Om du vill dölja parametervärden som du inte vill ska visas när du `securestring` redigerar logikappen eller visningskörningshistoriken definierar du parametrarna med hjälp av typen eller `secureobject` använder kodningen efter behov. Parametrar som har den här typen returneras inte med resursdefinitionen och är inte tillgängliga när resursen visas efter distributionen. Om du vill komma åt dessa `@parameters('<parameter-name>')` parametervärden under körning använder du uttrycket i arbetsflödesdefinitionen. Det här uttrycket utvärderas endast vid körning och beskrivs av [arbetsflödesdefinitionsspråket](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Om du använder en parameter i ett begärandehuvud eller en brödtext kan den parametern vara synlig när du visar logikappens körningshistorik och utgående HTTP-begäran. Se till att du också anger dina principer för innehållsåtkomst i enlighet med detta. Du kan också använda [förvirring](#obfuscate) för att dölja indata och utdata i din körningshistorik. Auktoriseringshuvuden visas aldrig via indata eller utdata. Så om en hemlighet används där, är den hemligheten inte hämtningsbar.

Mer information finns i följande avsnitt i följande avsnitt:

* [Säkra parametrar i arbetsflödesdefinitioner](#secure-parameters-workflow)
* [Dölja data från körningshistorik med hjälp av förvirring](#obfuscate)

Om du [automatiserar distributionen för logikappar med hjälp av Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du `securestring` `secureobject` definiera skyddade [mallparametrar](../azure-resource-manager/templates/template-parameters.md), som utvärderas vid distributionen, med hjälp av och typerna. Om du vill definiera mallparametrar `parameters` använder du mallens översta nivåavsnitt, som är separat och skiljer sig från avsnittet arbetsflödesdefinition. `parameters` Om du vill ange värden för mallparametrar använder du en separat [parameterfil](../azure-resource-manager/templates/parameter-files.md).

Om du till exempel använder hemligheter kan du definiera och använda skyddade mallparametrar som hämtar hemligheterna från [Azure Key Vault](../key-vault/key-vault-overview.md) vid distributionen. Du kan sedan referera till nyckelvalvet och hemligheten i parameterfilen. Mer information finns i de här ämnena:

* [Skicka känsliga värden vid distributionen med hjälp av Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Säkra parametrar i Azure Resource Manager-mallar](#secure-parameters-deployment-template) senare i det här avsnittet

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Säkra parametrar i arbetsflödesdefinitioner

Om du vill skydda känslig information i logikappens arbetsflödesdefinition använder du säkra parametrar så att den här informationen inte visas när du har sparat logikappen. Anta till exempel att du har en HTTP-åtgärd som kräver grundläggande autentisering, som använder ett användarnamn och lösenord. I arbetsflödesdefinitionen `parameters` definierar `basicAuthPasswordParam` avsnittet `basicAuthUsernameParam` parametrarna `securestring` och med hjälp av typen. Åtgärdsdefinitionen refererar sedan `authentication` till dessa parametrar i avsnittet .

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Säkra parametrar i Azure Resource Manager-mallar

En [Resource Manager-mall](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) för `parameters` en logikapp har flera avsnitt. Om du vill skydda lösenord, nycklar, hemligheter och annan känslig information definierar du `securestring` `secureobject` skyddade parametrar på mallnivå och arbetsflödesdefinitionsnivå med hjälp av typen eller. Du kan sedan lagra dessa värden i [Azure Key Vault](../key-vault/key-vault-overview.md) och använda [parameterfilen](../azure-resource-manager/templates/parameter-files.md) för att referera till nyckelvalvet och hemligheten. Mallen hämtar sedan informationen vid distributionen. Mer information finns i [Skicka känsliga värden vid distributionen med hjälp av Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Här är mer `parameters` information om dessa avsnitt:

* På mallens översta nivå `parameters` definierar ett avsnitt parametrarna för de värden som mallen använder vid *distributionen*. Dessa värden kan till exempel innehålla anslutningssträngar för en viss distributionsmiljö. Du kan sedan lagra dessa värden i en separat [parameterfil,](../azure-resource-manager/templates/parameter-files.md)vilket gör det enklare att ändra dessa värden.

* I logikappens resursdefinition, men utanför arbetsflödesdefinitionen anger ett `parameters` avsnitt värdena för parametrarna för arbetsflödesdefinitionen. I det här avsnittet kan du tilldela dessa värden med hjälp av malluttryck som refererar till mallens parametrar. Dessa uttryck utvärderas vid distributionen.

* I arbetsflödesdefinitionen `parameters` definierar ett avsnitt de parametrar som logikappen använder under körning. Du kan sedan referera till dessa parametrar i logikappens arbetsflöde med hjälp av arbetsflödesdefinitionsuttryck, som utvärderas vid körning.

Den här exempelmallen som har flera `securestring` skyddade parameterdefinitioner som använder typen:

| Parameternamn | Beskrivning |
|----------------|-------------|
| `TemplatePasswordParam` | En mallparameter som accepterar ett lösenord som sedan `basicAuthPasswordParam` skickas till parametern för arbetsflödesdefinitionen |
| `TemplateUsernameParam` | En mallparameter som accepterar ett användarnamn som sedan `basicAuthUserNameParam` skickas till parametern för arbetsflödesdefinitionen |
| `basicAuthPasswordParam` | En arbetsflödesdefinitionsparameter som accepterar lösenordet för grundläggande autentisering i en HTTP-åtgärd |
| `basicAuthUserNameParam` | En arbetsflödesdefinitionsparameter som accepterar användarnamnet för grundläggande autentisering i en HTTP-åtgärd |
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

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Tillgång till tjänster och system som anropas från logikappar

Här är några sätt som du kan hjälpa till att skydda slutpunkter som tar emot samtal eller begäranden från logikappen:

* Lägg till autentisering i utgående begäranden.

  När du arbetar med en HTTP-baserad utlösare eller åtgärd som utför utgående samtal, till exempel HTTP, HTTP + Swagger eller Webhook, kan du lägga till autentisering i begäran som skickas av logikappen. Du kan till exempel använda följande autentiseringstyper:

  * [Grundläggande autentisering](#basic-authentication)

  * [Autentisering av klientcertifikat](#client-certificate-authentication)

  * [OAuth-autentisering i Active Directory](#azure-active-directory-oauth-authentication)

  * [Autentisering av hanterad identitet](#managed-identity-authentication)
  
  Mer information finns i [Lägga till autentisering i utgående samtal](#add-authentication-outbound) senare i det här avsnittet.

* Begränsa åtkomsten från IP-adresser för logikappar.

  Alla anrop till slutpunkter från logikappar kommer från specifika angivna IP-adresser som baseras på logikapparnas regioner. Du kan lägga till filtrering som endast accepterar begäranden från dessa IP-adresser. Information om hur du hämtar dessa IP-adresser finns i [Gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Förbättra säkerheten för anslutningar till lokala system.

  Azure Logic Apps tillhandahåller integrering med dessa tjänster för att ge säkrare och mer tillförlitlig lokal kommunikation.

  * Lokal datagateway

    Många hanterade anslutningsappar i Azure Logic Apps underlättar säkra anslutningar till lokala system, till exempel Filsystem, SQL, SharePoint och DB2. Gatewayen skickar data från lokala källor på krypterade kanaler via Azure Service Bus. All trafik kommer som säker utgående trafik från gateway-agenten. Lär dig [hur den lokala datagatewayen fungerar](logic-apps-gateway-install.md#gateway-cloud-service).

  * Ansluta via Azure API Management

    [Azure API Management](../api-management/api-management-key-concepts.md) innehåller lokala anslutningsalternativ, till exempel virtuellt privat nätverk för plats till plats och ExpressRoute-integrering för säker proxy och kommunikation till lokala system. Från logikappens arbetsflöde i Logic App Designer kan du välja ett API som exponeras av API Management, som ger snabb åtkomst till lokala system.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Lägga till autentisering i utgående samtal

HTTP- och HTTPS-slutpunkter stöder olika typer av autentisering. Baserat på den utlösare eller åtgärd som du använder för att ringa utgående samtal eller begäranden som kommer åt dessa slutpunkter kan du välja mellan olika autentiseringstyper. Om du vill vara säker på att du skyddar känslig information som logikappen hanterar använder du säkra parametrar och kodar data efter behov. Mer information om hur du använder och skyddar parametrar finns i [Åtkomst till parameterindata](#secure-action-parameters).

> [!NOTE]
> I Logic App Designer kan egenskapen **Authentication** vara dold på vissa utlösare och åtgärder där du kan ange autentiseringstypen. Om du vill att egenskapen ska visas i dessa fall öppnar du listan **Lägg till ny parameter** i utlösaren eller åtgärden och väljer **Autentisering**. Mer information finns i [Autentisera åtkomst med hanterad identitet](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

| Autentiseringstyp | Stöds av |
|---------------------|--------------|
| [Basic](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Klientcertifikat](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [Rådata](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [Hanterad identitet](#managed-identity-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Grundläggande autentisering

Om alternativet [Grundläggande](../active-directory-b2c/secure-rest-api.md) är tillgängligt anger du dessa egenskapsvärden:

| Egenskap (designer) | Egenskap (JSON) | Krävs | Värde | Beskrivning |
|---------------------|-----------------|----------|-------|-------------|
| **Autentisering** | `type` | Ja | Basic | Den autentiseringstyp som ska användas |
| **Användarnamn** | `username` | Ja | <*användarnamn*>| Användarnamnet för att autentisera åtkomst till slutpunkten för måltjänsten |
| **Lösenord** | `password` | Ja | <*Lösenord*> | Lösenordet för att autentisera åtkomst till slutpunkten för måltjänsten |
||||||

När du använder [säkra parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en Azure [Resource Manager-mall för automatisering av distribution,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du använda uttryck för att komma åt dessa parametervärden vid körning. I det här exemplet anger `type` `Basic` HTTP-åtgärdsdefinitionen autentiseringen som och använder [funktionen parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parametervärdena:

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

### <a name="client-certificate-authentication"></a>Autentisering av klientcertifikat

Om alternativet [Klientcertifikat](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) är tillgängligt anger du dessa egenskapsvärden:

| Egenskap (designer) | Egenskap (JSON) | Krävs | Värde | Beskrivning |
|---------------------|-----------------|----------|-------|-------------|
| **Autentisering** | `type` | Ja | **Klientcertifikat** <br>eller <br>`ClientCertificate` | Autentiseringstypen som ska användas för TLS/SSL-klientcertifikat. Även om självsignerade certifikat stöds stöds inte självsignerade certifikat för TLS/SSL. |
| **Pfx** | `pfx` | Ja | <*kodad-pfx-fil-innehåll*> | Det base64-kodade innehållet från en PFX-fil (Personal Information Exchange) <p><p>Om du vill konvertera PFX-filen till base64-kodat format kan du använda PowerShell genom att följa följande steg: <p>1. Spara certifikatinnehållet i en variabel: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Konvertera certifikatinnehållet `ToBase64String()` med hjälp av funktionen och spara innehållet i en textfil: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Lösenord** | `password`| Inga | <*lösenord-för-pfx-fil*> | Lösenordet för åtkomst till PFX-filen |
|||||

När du använder [säkra parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en Azure [Resource Manager-mall för automatisering av distribution,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du använda uttryck för att komma åt dessa parametervärden vid körning. I det här exemplet anger `type` `ClientCertificate` HTTP-åtgärdsdefinitionen autentiseringen som och använder [funktionen parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parametervärdena:

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

Mer information om hur du skyddar tjänster med hjälp av autentisering av klientcertifikat finns i följande avsnitt:

* [Förbättra säkerheten för API:er med hjälp av klientcertifikatautentisering i Azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Förbättra säkerheten för backend-tjänster med hjälp av klientcertifikatautentisering i Azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Förbättra säkerheten för din RESTfuL-tjänst med hjälp av klientcertifikat](../active-directory-b2c/secure-rest-api.md)
* [Certifikatautentiseringsuppgifter för programautentisering](../active-directory/develop/active-directory-certificate-credentials.md)
* [Använda ett TLS/SSL-certifikat i koden i Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Oauth-autentisering i Azure Active Directory

Om alternativet [Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) är tillgängligt anger du följande egenskapsvärden:

| Egenskap (designer) | Egenskap (JSON) | Krävs | Värde | Beskrivning |
|---------------------|-----------------|----------|-------|-------------|
| **Autentisering** | `type` | Ja | **Active Directory OAuth** <br>eller <br>`ActiveDirectoryOAuth` | Den autentiseringstyp som ska användas. Logic Apps följer för närvarande [OAuth 2.0-protokollet](../active-directory/develop/v2-overview.md). |
| **Myndighet** | `authority` | Inga | <*URL-för-myndighet-token-utfärdare*> | URL:en för den myndighet som tillhandahåller autentiseringstoken. Som standard är `https://login.windows.net`det här värdet . |
| **Klientorganisation** | `tenant` | Ja | <*klient-ID*> | Klient-ID för Azure AD-klienten |
| **Målgrupp** | `audience` | Ja | <*resurs-att-auktorisera*> | Den resurs som du vill använda för auktorisering, till exempel`https://management.core.windows.net/` |
| **Klient-ID** | `clientId` | Ja | <*klient-ID*> | Klient-ID för appen som begär auktorisering |
| **Typ av autentiseringsuppgifter** | `credentialType` | Ja | Certifikat <br>eller <br>Hemlighet | Den autentiseringsuppgifterstyp som klienten använder för att begära auktorisering. Den här egenskapen och värdet visas inte i logikappens underliggande definition, men bestämmer vilka egenskaper som visas för den valda autentiseringsuppgifterna. |
| **Hemlighet** | `secret` | Ja, men bara för autentiseringsuppgifternas "hemliga" autentiseringsuppgifter | <*klienthemlighet*> | Klienthemligheten för begäran om auktorisering |
| **Pfx** | `pfx` | Ja, men bara för autentiseringsuppgifterna för "Certifikat" | <*kodad-pfx-fil-innehåll*> | Det base64-kodade innehållet från en PFX-fil (Personal Information Exchange) |
| **Lösenord** | `password` | Ja, men bara för autentiseringsuppgifterna för "Certifikat" | <*lösenord-för-pfx-fil*> | Lösenordet för åtkomst till PFX-filen |
|||||

När du använder [säkra parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en Azure [Resource Manager-mall för automatisering av distribution,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du använda uttryck för att komma åt dessa parametervärden vid körning. I det här exemplet anges `type` `ActiveDirectoryOAuth`autentiseringen som , `Secret`autentiseringsuppgifterna som , och [funktionen parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) används för att hämta parametervärdena:

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

### <a name="raw-authentication"></a>Rå autentisering

Om alternativet **Rå** är tillgängligt kan du använda den här autentiseringstypen när du måste använda [autentiseringsscheman](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) som inte följer [OAuth 2.0-protokollet](https://oauth.net/2/). Med den här typen skapar du manuellt värdet för auktoriseringshuvudet som du skickar med den utgående begäran och anger det huvudvärdet i utlösaren eller åtgärden.

Här är till exempel ett exempel på ett exempel på en HTTPS-begäran som följer [OAuth 1.0-protokollet:](https://tools.ietf.org/html/rfc5849)

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

Ange dessa egenskapsvärden i utlösaren eller åtgärden som stöder råautentisering:

| Egenskap (designer) | Egenskap (JSON) | Krävs | Värde | Beskrivning |
|---------------------|-----------------|----------|-------|-------------|
| **Autentisering** | `type` | Ja | Rådata | Den autentiseringstyp som ska användas |
| **Värde** | `value` | Ja | <*auktoriseringshuvud-värde*> | Värdet för auktoriseringshuvudet som ska användas för autentisering |
||||||

När du använder [säkra parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en Azure [Resource Manager-mall för automatisering av distribution,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du använda uttryck för att komma åt dessa parametervärden vid körning. I det här exemplet anger `type` `Raw`HTTP-åtgärdsdefinitionen autentiseringen som och [använder funktionen parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parametervärdena:

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

Om alternativet [Hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) är tillgängligt kan logikappen använda den systemtilldelade identiteten eller en *enda* manuellt skapad användartilldelade identitet för att autentisera åtkomst till resurser i andra Azure Active Directory-klienter (Azure AD) utan att logga in. Azure hanterar den här identiteten åt dig och hjälper till att skydda dina autentiseringsuppgifter eftersom du inte behöver ange eller rotera hemligheter. Läs mer om [Azure-tjänster som stöder hanterade identiteter för Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Innan logikappen kan använda en hanterad identitet följer du stegen i [Autentisera åtkomst till Azure-resurser med hjälp av hanterade identiteter i Azure Logic Apps](../logic-apps/create-managed-service-identity.md). De här stegen aktiverar den hanterade identiteten i logikappen och konfigurerar identitetens åtkomst till Azure-målresursen.

1. Innan en Azure-funktion kan använda en hanterad identitet aktiverar du först [autentisering för Azure-funktioner](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. Ange dessa egenskapsvärden i utlösaren eller åtgärden där du vill använda den hanterade identiteten:

   | Egenskap (designer) | Egenskap (JSON) | Krävs | Värde | Beskrivning |
   |---------------------|-----------------|----------|-------|-------------|
   | **Autentisering** | `type` | Ja | **Hanterad identitet** <br>eller <br>`ManagedServiceIdentity` | Den autentiseringstyp som ska användas |
   | **Hanterad identitet** | `identity` | Ja | * **Systemtilldelade hanterade identitet** <br>eller <br>`SystemAssigned` <p><p>* <*användartilldelade identitetsnamn*> | Den hanterade identitet som ska användas |
   | **Målgrupp** | `audience` | Ja | <*mål-resurs-ID*> | Resurs-ID:t för den målresurs som du vill komma åt. <p>Gör till `https://storage.azure.com/` exempel åtkomsttoken för autentisering giltiga för alla lagringskonton. Du kan dock också ange en url `https://fabrikamstorageaccount.blob.core.windows.net` för rottjänsten, till exempel för ett visst lagringskonto. <p>**Egenskapen** **Målgrupp** kan vara dold i vissa utlösare eller åtgärder. Om du vill göra den här egenskapen synlig öppnar du listan **Lägg till ny parameter** i utlösaren eller åtgärden och väljer **Målgrupp**. <p><p>**Viktigt**: Se till att det här målresurs-ID:t *exakt matchar* det värde som Azure AD förväntar sig, inklusive eventuella nödvändiga efterföljande snedstreck. Så `https://storage.azure.com/` kräver resurs-ID för alla Azure Blob Storage-konton ett avslutande snedstreck. Resurs-ID:t för ett visst lagringskonto kräver dock inte ett efterföljande snedstreck. Information om hur du hittar dessa resurs-ID:er finns i [Azure-tjänster som stöder Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   När du använder [säkra parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en Azure [Resource Manager-mall för automatisering av distribution,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du använda uttryck för att komma åt dessa parametervärden vid körning. I det här exemplet anger `type` `ManagedServiceIdentity` HTTP-åtgärdsdefinitionen autentiseringen som och använder [funktionen parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parametervärdena:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>Nästa steg

* [Automatisera distribution för Azure Logic Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Övervaka logikappar](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Diagnostisera fel och problem med logikappar](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Automatisera distributionen av logikappar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
