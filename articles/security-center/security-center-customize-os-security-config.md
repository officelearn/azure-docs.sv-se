---
title: Anpassa OS-säkerhetskonfigurationer i Azure Security Center (förhandsversion) | Microsoft Docs
description: Den här artikeln visar hur du anpassar security center utvärderingar
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: rkarlin
ms.openlocfilehash: 15707e9854e79b3aa05ff9024fc5b3c7a38fa8f2
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106893"
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Anpassa OS-säkerhetskonfigurationer i Azure Security Center (förhandsversion)

Den här genomgången visar hur du anpassar OS configuration säkerhetsutvärderingar i Azure Security Center.

## <a name="what-are-os-security-configurations"></a>Vad är OS-säkerhetskonfigurationer?

Azure Security Center övervakar säkerhetskonfigurationer genom att använda en uppsättning [över 150 rekommenderas regler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) för att härda Operativsystemet, inklusive regler relaterade till brandväggar, granskning, lösenordsprinciper med mera. Om en dator har en sårbara konfiguration, genererar Security Center en säkerhetsrekommendation.

Organisationer kan genom att anpassa regler kan styra vilka konfigurationsalternativ passar bättre för deras miljö. Du kan ange en princip för anpassad bedömning och tillämpa den på alla tillämpliga datorer i prenumerationen.

> [!NOTE]
> - Anpassning av OS-säkerhetskonfiguration är för närvarande tillgänglig för Windows Server 2008, 2008 R2, 2012, 2012 R2 och 2016 operating system.
> - Det gäller konfigurationen för alla virtuella datorer och datorer som är anslutna till alla arbetsytor under den valda prenumerationen.
> - Operativsystemanpassning security configuration är endast tillgänglig på Security Center standard-nivån.
>
>

Du kan anpassa konfigurationsregler för OS-säkerhet genom att aktivera och inaktivera en specifik regel, ändra önskade inställningen för en befintlig regel eller att lägga till en ny regel som baseras på stöds regeltyper (register, granskningsprincip och säkerhetsprincip). För närvarande måste på önskad inställning vara ett exakt värde.

Nya regler måste finnas i samma format och struktur som andra befintliga regler och av samma typ.

> [!NOTE]
> Om du vill anpassa OS-säkerhetskonfigurationer, måste du vara tilldelad rollen *Prenumerationsägaren*, *Prenumerationsdeltagare*, eller *säkerhetsadministratör*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Anpassa standardkonfiguration för OS-säkerhet

Om du vill anpassa den OS standardkonfigurationen för säkerhet i Security Center, gör du följande:

1.  Öppna instrumentpanelen för **Security Center**.

2.  I den vänstra rutan väljer **säkerhetsprincip**.      

    ![Security Policy list](media/security-center-customize-os-security-config/manual-provision.png)

3.  Klicka på raden i den prenumeration som du vill anpassa och **redigera inställningar för**.

4. Välj **redigera säkerhetskonfigurationerna**.  

    ![Fönstret ”Redigera säkerhetskonfigurationerna”](media/security-center-customize-os-security-config/blade.png)

5. Följ stegen för att hämta, redigera och ladda upp den ändrade filen.

   > [!NOTE]
   > Konfigurationsfilen som du laddar ned är som standard i *json* format. Anvisningar om hur du ändrar den här filen finns i [anpassa konfigurationsfilen](#customize-the-configuration-file).
   >

6. För att genomföra ändringen, Välj **spara**. I annat fall lagras inte principen.

    ![Knappen Spara](media/security-center-customize-os-security-config/save-successfully.png)

   När du har har sparat filen, tillämpas konfigurationen på alla virtuella datorer och datorer som är anslutna till arbetsytor under prenumerationen. Processen tar några minuter vanligtvis, men kan ta längre tid beroende på storleken för infrastruktur.

När som helst kan du återställa aktuell principkonfiguration till sitt ursprungsläge. Gör, i den **redigera OS reglerna för säkerhetskonfigurationen** väljer **återställa**. Bekräfta det här alternativet genom att välja **Ja** i popup-bekräftelsefönstret.

![Återställ bekräftelsefönstret](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Anpassa konfigurationsfilen

Har en uppsättning regler eller RuleSet-metod i anpassning, varje OS-version som stöds. Varje regeluppsättning har ett eget namn och unikt ID, som visas i följande exempel:

![Regeluppsättning namn och ID](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Den här exempel-filen har redigerats i Visual Studio, men du kan också använda anteckningar om du har den JSON-visare plugin-programmet installerat.
>
>

När du redigerar anpassningsfilen kan ändra du en regel eller alla. Varje regeluppsättning innehåller en *regler* avsnitt som är uppdelad i tre kategorier: Registret, granskningsprincip och Security Policy, som visas här:

![Tre ruleset-kategorier](media/security-center-customize-os-security-config/rules-section.png)

Varje kategori har en egen uppsättning attribut. Du kan ändra följande attribut:

- **expectedValue**: Det här attributet fälts datatyp måste matcha värdena som stöds per *regeltyp*, till exempel:

  - **baselineRegistryRules**: Värdet måste matcha den [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) som definieras i regeln.

  - **baselineAuditPolicyRules**: Använd någon av följande strängvärden:

    - *Lyckade och misslyckade*

    - *Lyckades*

  - **baselineSecurityPolicyRules**: Använd någon av följande strängvärden:

    - *Ingen*

    - Lista över tillåtna användargrupper, till exempel: *Administratörer*, *ansvariga för säkerhetskopiering*

-   **tillstånd**: Strängen kan innehålla alternativ *inaktiverad* eller *aktiverad*. För den här privata förhandsversionen är strängen skiftlägeskänsligt.

Det här är de enda fält som kan konfigureras. Om du bryter mot file format eller storlek, kan du inte spara ändringen. Du får ett fel som säger att du behöver ladda upp en giltig JSON-konfigurationsfil.

En lista över andra potentiella fel finns i [felkoder](#error-codes).

Följande tre avsnitt innehåller exempel på föregående regler. Den *expectedValue* och *tillstånd* attribut kan ändras.

**baselineRegistryRules**
```json
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "expectedValue": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Disabled"

    }
```

**baselineAuditPolicyRules**
```json
    {
    "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
    "ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
    "originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
    "cceId": "CCE-11001-5",
    "ruleName": "Audit Policy: Account Management: Other Account Management Events",
    "ruleType": "AuditPolicy",
    "expectedValue": "Success and Failure",
    "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

**baselineSecurityPolicyRules**
```json
    {
    "sectionName": "Privilege Rights",
    "settingName": "SeIncreaseWorkingSetPrivilege",
    "ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
    "originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
    "cceId": "CCE-10548-6",
    "ruleName": "Increase a process working set",
    "ruleType": "SecurityPolicy",
    "expectedValue": "Administrators, Local Service",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

Vissa regler dupliceras för de olika typerna av OS. Duplicerade regler har samma *originalId* attribut.

## <a name="create-custom-rules"></a>Skapa anpassade regler

Du kan också skapa nya regler. Innan du skapar en ny regel, Tänk på följande begränsningar:

-   Schemaversionen *baselineId* och *baselineName* kan inte ändras.

-   Regeluppsättning tas inte bort.

-   Regeluppsättning kan inte läggas till.

-   Det maximala antalet regler för tillåtna (inklusive standardregler) är 1 000.

Nya regler som har markerats med en ny anpassad källa (! = ”Microsoft”). Den *ruleId* fält kan vara null eller tomt. Om den är tom, genererar en av Microsoft. Om den inte är tom, måste den ha ett giltigt GUID som är unikt i alla regler (standard och anpassade). Granska följande begränsningar för core fält:

-   **originalId**: Kan vara null eller tomt. Om *originalId* är inte tom, bör det vara ett giltigt GUID.

-   **cceId**: Kan vara null eller tomt. Om *cceId* är inte tom, det måste vara unikt.

-   **Regeltyp**: (Välj ett alternativ) registret, AuditPolicy eller SecurityPolicy.

-   **Allvarlighetsgrad**: (Välj ett alternativ) Okänd, kritisk, varning eller information.

-   **analyzeOperation**: Måste vara *är lika med*.

-   **auditPolicyId**: Måste vara ett giltigt GUID.

-   **regValueType**: (Välj ett alternativ) Int, Long, String eller MultipleString.

> [!NOTE]
> Hive måste vara *LocalMachine*.
>
>

Exempel på en ny anpassad regel:

**Registret**:
```json
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule", "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
    }
```
**Säkerhetsprincip**:
```json
   {
   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**Granskningsprincip**:
```json
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>Filöverföringsfel

Om den överförda konfigurationsfilen är ogiltig på grund av fel i värden eller formatering, ett fel visas, till exempel **spara-åtgärden misslyckades**. Du kan hämta en rapport för CSV-detaljerade fel för att åtgärda och åtgärda felen innan du skicka en korrigerad konfigurationsfil.

Exempel på en felfil:

![Exempel på fel](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Felkoder

Alla eventuella fel visas i följande tabell:

| **Fel**                                | **Beskrivning**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfigurationSchemaVersionError  | Egenskapen *schemaVersion* hittades ogiltigt eller tomt. Värdet måste anges till *{0}*.                                                         |
| BaselineInvalidStringError               | Egenskapen *{0}* får inte innehålla  *\\n*.                                                                                                         |
| BaselineNullRuleError                    | Regler för baslinjen konfigurationslistan innehåller en regel med värdet *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID *{0}* är inte unikt.                                                                                                                  |
| BaselineRuleEmptyProperty                | Egenskapen *{0}* är ogiltig eller saknas.                                                                                                       |
| BaselineRuleIdNotInDefault               | Regeln har ett source-egenskapen *Microsoft* men hittades inte i Microsoft standard regeluppsättning.                                                   |
| BaselineRuleIdNotUniqueError             | Regel-Id är inte unikt.                                                                                                                       |
| BaselineRuleInvalidGuid                  | Egenskapen *{0}* hittades ogiltig. Värdet är inte ett giltigt GUID.                                                                             |
| BaselineRuleInvalidHive                  | Hive måste vara LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Regelnamnet är inte unikt.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | Regeln hittades inte i den nya konfigurationen. Regeln kan inte tas bort.                                                                     |
| BaselineRuleNotFoundError                | Regeln hittades inte i baslinje regeluppsättning.                                                                                        |
| BaselineRuleNotInPlace                   | Regeln matchar en standardregel med typen {0} och visas i {1} lista.                                                                       |
| BaselineRulePropertyTooLong              | Egenskapen *{0}* är för långt. Högsta tillåtna längd: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | Det förväntade värdet *{0}* matchar inte registervärdetypen som har definierats.                                                              |
| BaselineRulesetAdded                     | Regeluppsättning med ID *{0}* hittades inte i standardkonfigurationen. Den RuleSet-metod kan inte läggas till.                                               |
| BaselineRulesetIdMustBeUnique            | Den givna baslinjen ruleset *{0}* måste vara unikt.                                                                                           |
| BaselineRulesetNotFound                  | Regeluppsättning med id *{0}* och namnet *{1}* hittades inte i den angivna konfigurationen. Den RuleSet-metod kan inte tas bort.                                |
| BaselineRuleSourceNotMatch               | Regeln med ID *{0}* har redan definierats.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Typ av regel är *{0}*.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Den faktiska typen av regeln är *{0}*, men *regeltyp* egenskapen är *{1}*.                                                                          |
| BaselineRuleUnpermittedChangesError      | Endast *expectedValue* och *tillstånd* egenskaper ska kunna ändras.                                                                       |
| BaselineTooManyRules                     | Det maximala antalet tillåtna anpassade regler är {0} regler. Den angivna konfigurationen innehåller {1} regler, {2} standardregler, och {3} anpassade regler. |
| ErrorNoConfigurationStatus               | Inga Konfigurationsstatus hittades. Tillstånd för status för önskad konfiguration: *Standard* eller *anpassad*.                                    |
| ErrorNonEmptyRulesetOnDefault            | Configuration-status är inställd på standardvärdet. Den *BaselineRulesets* listan måste vara null eller tom.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | Den angivna konfigurationsstatusen är *anpassade* men *baselineRulesets* egenskapen är null eller tomt.                                             |
| ErrorParsingBaselineConfig               | Den angivna konfigurationen är ogiltig. En eller flera av de definierade värdena har ett null-värde eller en ogiltig typ.                                  |
| ErrorParsingIsDefaultProperty            | Den angivna *configurationStatus* värdet *{0}* är ogiltig. Värdet kan bara vara *standard* eller *anpassad*.                                         |
| InCompatibleViewVersion                  | Visa version *{0}* är *inte* stöds på den här typen av arbetsyta.                                                                                   |
| InvalidBaselineConfigurationGeneralError | Den givna baslinje konfigurationen hittades med en eller flera verifieringsfel för typen.                                                          |
| ViewConversionError                      | Vyn är en äldre version än vad som har stöd för arbetsytan. Visa konvertering misslyckades: {0}.                                                                 |

Om du inte har tillräcklig behörighet, kan du få ett allmänt fel-fel som visas här:

![”Spara åtgärden misslyckades” felmeddelande](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Nästa steg
Den här artikeln beskrivs hur du anpassar OS configuration säkerhetsutvärderingar i Security Center. Mer information om konfigurationsregler och reparation finns:

- [Security Center vanliga Konfigurationsidentifierare och grundkonfigurationer](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Security Center använder common configuration enumeration (CCE) för att tilldela unika identifierare till konfigurationsregler. Mer information finns i [CCE](https://nvd.nist.gov/config/cce/index).
- För att lösa säkerhetsproblem när Operativsystemets konfiguration inte matchar de rekommendera säkerhetsreglerna för konfiguration, se [åtgärda säkerhetskonfigurationer](security-center-remediate-os-vulnerabilities.md).
