---
title: "Anpassa OS säkerhetskonfigurationer i Azure Security Center (förhandsversion) | Microsoft Docs"
description: "Den här artikeln visar hur du anpassar security center bedömningar"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2018
ms.author: terrylan
ms.openlocfilehash: d42dd4ba150a28109d6bb3f7c2281d07b21a366e
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2018
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Anpassa OS säkerhetskonfigurationer i Azure Security Center (förhandsgranskning)

Den här genomgången visar hur du anpassar bedömning av konfigurationen för OS i Azure Security Center.

## <a name="what-are-os-security-configurations"></a>Vad är OS säkerhetskonfigurationer?

Azure Security Center Övervakare säkerhetskonfigurationer genom att använda en uppsättning över 150 rekommenderade regler för härdning av Operativsystemet, inklusive regler rör brandväggar, granskning, lösenordsprinciper och mycket mer. Om en dator har en sårbar konfiguration, genererar en säkerhetsrekommendation Security Center.

Organisationer kan genom att anpassa regler kan styra vilka konfigurationsalternativ som är mer lämpliga för deras miljö. Du kan ange en princip för anpassad bedömning och tillämpa den på alla tillämpliga datorer i prenumerationen.

> [!NOTE]
> - Anpassning av OS-säkerhetskonfiguration är för närvarande tillgänglig för Windows Server 2008, 2008 R2, 2012 och 2012 R2 endast operativsystem.
> - Konfigurationen gäller för alla virtuella datorer och datorer som är anslutna till alla arbetsytor under den valda prenumerationen.
> - Operativsystemanpassning security configuration är bara tillgängliga på standardnivån Security Center.
>
>

Du kan anpassa konfigurationsregler för OS-säkerhet genom att aktivera och inaktivera en specifik regel, ändra önskade inställningen för en befintlig regel eller lägga till en ny regel som baseras på stöds regeltyper (registret, granskning och säkerhetsprincip). För närvarande måste önskade vara ett exakt värde.

Nya regler måste vara i samma format och struktur som andra befintliga regler av samma typ.

> [!NOTE]
> Om du vill anpassa OS säkerhetskonfigurationer, du måste tilldelas rollen för *Prenumerationsägaren*, *prenumeration deltagare*, eller *säkerhetsadministratör*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Anpassa standardkonfiguration för OS-säkerhet

Om du vill anpassa den OS standardkonfigurationen för säkerhet i Security Center, gör du följande:

1.  Öppna instrumentpanelen för **Security Center**.

2.  I den vänstra rutan, Välj **säkerhetsprincip**.  
    Den **Security Center - säkerhetsprincip** öppnas.

    ![Lista över säkerhetsprinciper](media/security-center-customize-os-security-config/open-security-policy.png)

3.  Välj den prenumeration som du vill utföra anpassning av.

4. Under **princip komponenter**väljer **redigera säkerhetskonfigurationer**.  
    Den **redigera säkerhetskonfigurationer** öppnas. 
    
    ![Fönstret ”Redigera säkerhetskonfigurationer”](media/security-center-customize-os-security-config/blade.png)

5. I den högra rutan, följer du stegen för att hämta, redigera och ladda upp den ändrade filen.

   > [!NOTE]
   > Som standard är den konfigurationsfil som du hämtar i *json* format. Anvisningar om hur du ändrar den här filen finns i [anpassa konfigurationsfilen](#customize-the-configuration-file).
   >

   När filen har sparats, används konfigurationen på alla virtuella datorer och datorer som är anslutna till alla arbetsytor för den här prenumerationen. Processen vanligtvis tar några minuter, men kan ta längre tid beroende på storleken för infrastruktur. 

6. För att genomföra ändringen, Välj **spara**. Annars kan lagras inte.

    ![Knappen Spara](media/security-center-customize-os-security-config/save-successfully.png)

När som helst kan du återställa den aktuella principkonfigurationen till sitt ursprungsläge. Gör i den **redigera OS säkerhetsregler configuration** väljer **återställa**. Bekräfta det här alternativet genom att välja **Ja** i popup-bekräftelsefönstret.

![Återställ bekräftelsefönstret](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Anpassa konfigurationsfilen

I anpassningsfilen har varje OS-version som stöds en uppsättning regler eller RuleSet-metod. Varje regeluppsättning har ett eget namn och unikt ID som visas i följande exempel:

![Regeluppsättning namn och ID](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Det här exemplet-filen har redigerats i Visual Studio, men du kan också använda anteckningar om du har de JSON-visningsprogram plugin-program installerat.
>
>

När du redigerar anpassningsfilen kan ändra du en regel eller alla. Varje regeluppsättning innehåller en *regler* avsnitt som är indelade i tre kategorier: registret, granskning och säkerhetsprincip som visas här:

![Tre ruleset-kategorier](media/security-center-customize-os-security-config/rules-section.png)

Varje kategori har en egen uppsättning attribut. Du kan ändra följande attribut:

- **expectedValue**: det här attributet har datatypen måste matcha värdena som stöds per *regeltyp*, till exempel:

  - **baselineRegistryRules**: värdet måste matcha den [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) som har definierats i regeln.

  - **baselineAuditPolicyRules**: Använd någon av följande strängvärden:

    - *Lyckade och misslyckade*

    - *Lyckades*

  - **baselineSecurityPolicyRules**: Använd någon av följande strängvärden:

    - *Ingen*

    - Lista över tillåtna användargrupper, till exempel: *administratörer*, *ansvariga för säkerhetskopiering*

-   **tillstånd**: strängen kan innehålla alternativen *inaktiverad* eller *aktiverad*. För den här privat förhandsversionen är strängen skiftlägeskänsliga.

Dessa är de enda fält som kan konfigureras. Om du bryter mot filformatet eller storlek kan du inte spara ändringen. Följande felmeddelande visas när filen inte kan bearbetas:

![Säkerhet konfiguration felmeddelande](media/security-center-customize-os-security-config/invalid-json.png)

En lista över andra eventuella fel, se [felkoder](#error-codes).

Följande tre avsnitt innehåller exempel på föregående regler. Den *expectedValue* och *tillstånd* attribut kan ändras.

**baselineRegistryRules**
```
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
```
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
```
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

Vissa regler dupliceras för olika typer av OS. Dubbla regler har samma *originalId* attribut.

## <a name="create-custom-rules"></a>Skapa anpassade regler

Du kan också skapa nya regler. Innan du skapar en ny regel, Tänk på följande begränsningar:

-   Schemaversionen *baselineId* och *baselineName* kan inte ändras.

-   RuleSet-metod kan inte tas bort.

-   RuleSet-metod kan inte läggas till.

-   Det maximala antalet regler för tillåtna (inklusive standardregler) är 1000.

Nya regler som är markerade med en ny anpassad källa (! = ”Microsoft”). Den *ruleId* fältet kan vara null eller tomt. Om den är tom, genererar en Microsoft. Om den inte är tom måste den ha ett giltigt GUID som är unikt för alla regler (standard och anpassad). Granska följande begränsningar för fälten kärnor:

-   **originalId**: kan vara null eller tomt. Om *originalId* är inte tom, ska det vara ett giltigt GUID.

-   **cceId**: kan vara null eller tomt. Om *cceId* är inte tom, det måste vara unikt.

-   **ruleType**: (Välj ett) registret, AuditPolicy eller SecurityPolicy.

-   **Allvarlighetsgrad**: (Välj ett) Okänd, kritisk, varning eller information.

-   **analyzeOperation**: måste vara *är lika med*.

-   **auditPolicyId**: måste vara ett giltigt GUID.

-   **regValueType**: (Välj ett) Int, Long, sträng eller MultipleString.

> [!NOTE]
> Hive måste vara *LocalMachine*.
>
>

Exempel på en ny anpassad regel:

**Registret**:
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule”, "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
    }
```
**Säkerhetsprincip**:
```
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
```
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

## <a name="file-upload-failures"></a>Filen Överför fel

Om den överförda konfigurationsfilen är ogiltig på grund av fel i värden eller formatering, visas ett felmeddelande. Du kan hämta en rapport för CSV-detaljerade fel för att reparera och åtgärda felen innan du kan skicka en korrigerade konfigurationsfil.

![”Spara-åtgärd misslyckades” felmeddelande](media/security-center-customize-os-security-config/invalid-configuration.png)

Exempel på en felfil:

![Exempel på fel](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Felkoder

Alla eventuella fel visas i följande tabell:

| **Fel**                                | **Beskrivning**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | Egenskapen *schemaVersion* hittades ogiltigt eller tomt. Värdet måste anges till *{0}*.                                                         |
| BaselineInvalidStringError               | Egenskapen *{0}* får inte innehålla  *\\ n* .                                                                                                         |
| BaselineNullRuleError                    | Grundläggande konfiguration regler innehåller en regel med värdet *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID *{0}* är inte unikt.                                                                                                                  |
| BaselineRuleEmptyProperty                | Egenskapen *{0}* är ogiltig eller saknas.                                                                                                       |
| BaselineRuleIdNotInDefault               | Regeln har ett source-egenskapen *Microsoft* men hittades inte i Microsoft standard regeluppsättning.                                                   |
| BaselineRuleIdNotUniqueError             | Regel-Id är inte unikt.                                                                                                                       |
| BaselineRuleInvalidGuid                  | Egenskapen *{0}* hittades ogiltig. Värdet är inte ett giltigt GUID.                                                                             |
| BaselineRuleInvalidHive                  | Strukturen måste vara LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Regelnamnet är inte unikt.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | Regeln hittades inte i den nya konfigurationen. Det går inte att ta bort regeln.                                                                     |
| BaselineRuleNotFoundError                | Regeln hittades inte i baslinje RuleSet-metod.                                                                                        |
| BaselineRuleNotInPlace                   | Regeln matchar en standardregel med typen {0} och i {1}-listan.                                                                       |
| BaselineRulePropertyTooLong              | Egenskapen *{0}* är för långt. Högsta tillåtna längd: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | Det förväntade värdet *{0}* matchar inte registervärdetypen som har definierats.                                                              |
| BaselineRulesetAdded                     | RuleSet-metod med ID *{0}* hittades inte i standardkonfigurationen. Den RuleSet-metod kan inte läggas till.                                               |
| BaselineRulesetIdMustBeUnique            | Den givna baslinjen ruleset *{0}* måste vara unika.                                                                                           |
| BaselineRulesetNotFound                  | RuleSet-metod med id *{0}* och namnet *{1}* hittades inte i den angivna konfigurationen. Den RuleSet-metod kan inte tas bort.                                |
| BaselineRuleSourceNotMatch               | Regeln med ID *{0}* har redan definierats.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Standardtypen för regeln är *{0}*.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Den faktiska typen av regeln är *{0}*, men *ruleType* egenskapen är *{1}*.                                                                          |
| BaselineRuleUnpermittedChangesError      | Endast *expectedValue* och *tillstånd* egenskaper ska kunna ändras.                                                                       |
| BaselineTooManyRules                     | Det maximala antalet tillåtna anpassade regler är {0} regler. Den angivna konfigurationen innehåller regler för {1}, {2} standardregler och {3} anpassade regler. |
| ErrorNoConfigurationStatus               | Inga Konfigurationsstatus hittades. Ange status för önskad konfiguration: *standard* eller *anpassad*.                                    |
| ErrorNonEmptyRulesetOnDefault            | Konfigurationen har angetts till standardvärdet. Den *BaselineRulesets* listan måste vara null eller tomt.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | Angivna konfigurationens status är *anpassad* men *baselineRulesets* egenskapen är null eller tomt.                                             |
| ErrorParsingBaselineConfig               | Den angivna konfigurationen är ogiltig. En eller flera av de definierade värdena ha ett null-värde eller en ogiltig datatyp.                                  |
| ErrorParsingIsDefaultProperty            | Den angivna *configurationStatus* värdet *{0}* är ogiltig. Värdet kan vara endast *standard* eller *anpassad*.                                         |
| InCompatibleViewVersion                  | Visa version *{0}* är *inte* stöds på den här typen av arbetsyta.                                                                                   |
| InvalidBaselineConfigurationGeneralError | Den givna baslinje konfigurationen hittades med en eller flera verifieringsfel för typen.                                                          |
| ViewConversionError                      | Vyn är en äldre version än arbetsytan stöder. Visa konvertering misslyckades: {0}.                                                                 |

Om du inte har tillräcklig behörighet kan få du ett allmänt fel som visas här:

![”Spara-åtgärd misslyckades” felmeddelande](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Nästa steg
Den här artikeln beskrivs hur du anpassar bedömning av konfigurationen för OS i Security Center. Mer information om konfigurationsregler och reparationen finns:

- [Security Center vanliga configuration identifierare och baslinjeregler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Security Center använder common configuration enumeration (CCE) för att tilldela konfigurationsregler unika identifierare. Mer information finns i [CCE](https://nvd.nist.gov/config/cce/index).
- För att lösa problem när Operativsystemets konfiguration inte matchar de rekommenderade konfigurationen säkerhetsreglerna, se [reparera säkerhetskonfigurationer](security-center-remediate-os-vulnerabilities.md).
