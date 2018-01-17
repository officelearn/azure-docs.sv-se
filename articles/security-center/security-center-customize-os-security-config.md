---
title: "Anpassa OS säkerhetskonfigurationer i Azure Security Center [förhandsgranskning] | Microsoft Docs"
description: "Den här artikeln lär du dig hur du anpassar security center bedömningar"
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
ms.openlocfilehash: 3af59e1b38e70494dd9dc17e2682d31cf7b7d361
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="customizing-os-security-configurations-in-azure-security-center-preview"></a>Anpassa OS säkerhetskonfigurationer i Azure Security Center [förhandsgranskning]

Lär dig hur du anpassar OS säkerhetskonfiguration bedömningar i Azure Security Center med den här genomgången.

## <a name="what-are-os-security-configurations"></a>Vad är OS säkerhetskonfigurationer?

Azure Security Center Övervakare säkerhetskonfigurationer med hjälp av en uppsättning över 150 rekommenderade regler för härdning av Operativsystemet, inklusive regler rör brandväggar, granskning, lösenordsprinciper och mycket mer. Om en dator har en sårbar konfiguration, skapas en säkerhetsrekommendation.

Anpassning av reglerna kan hjälpa organisationer att styra vilka konfigurationsalternativ som är mer lämpliga för deras miljö. Den här funktionen kan du ange en princip för anpassad bedömning och tillämpa den på alla tillämpliga datorer i prenumerationen.

> [!NOTE]
> - För närvarande är OS säkerhetskonfiguration anpassning tillgänglig för Windows Server 2008, 2008R2, 2012, endast 2012 R2-operativsystem.
- Konfigurationen som gäller för alla virtuella datorer och datorer som är ansluten till alla arbetsytor under den valda prenumerationen.
- OS-säkerhetskonfiguration anpassning är bara tillgängliga på standardnivån för Security Center.
>
>

Hur du anpassar OS säkerhetskonfiguration regler?

Du kan anpassa OS säkerhetskonfiguration regler genom att aktivera och inaktivera en specifik regel, ändra önskade inställningen för en befintlig regel och lägger till en ny regel som baseras på stöds regeltyper (registret, granskning och säkerhetsprincip). För närvarande måste önskade vara ett exakt värde.

Nya regler måste finnas i samma format och struktur som andra befintliga regler av samma typ.

> [!NOTE]
> Du måste tilldelas rollen prenumeration ägare, deltagare i prenumeration eller säkerhetsadministratör om du vill anpassa OS säkerhetskonfigurationer.
>
>

## <a name="customize-security-configuration"></a>Anpassa säkerhetskonfiguration

Så här anpassar standard OS säkerhetskonfiguration i Security Center:

1.  Öppna instrumentpanelen **Security Center**.

2.  Välj under huvudmenyn Security Center **säkerhetsprincip**.  **Security Center - säkerhetsprincip** öppnas.

3.  Välj den prenumeration som du vill utföra anpassning av.

    ![](media/security-center-customize-os-security-config/open-security-policy.png)

4. Under **princip komponenter**väljer **redigera säkerhetskonfigurationer**.

6.  **Redigera säkerhetskonfigurationer** öppnas. Följ stegen på skärmen för att hämta, redigera och överföra den ändrade filen markerat.

    ![](media/security-center-customize-os-security-config/blade.png)

  > [!NOTE]
  > Som standard är den konfigurationsfil som du hämtar i *json* format. Anvisningar om hur du ändrar den här filen finns i [anpassa konfigurationsfilen](#customize-the-configuration-file).
  >

7. När du har sparat filen, konfigurationen tillämpas på alla virtuella datorer och datorer som är anslutna till alla arbetsytor under den valda prenumerationen. Den här processen kan ta lite tid, vanligtvis några minuter, men kan det kan ta längre tid eftersom den är beroende av infrastruktur-storlek. Välj **spara** för att genomföra ändringen, annars inte lagras.

    ![](media/security-center-customize-os-security-config/save-successfully.png)

När som helst kan du återställa den aktuella principkonfigurationen till standardläget för principen genom att välja den **återställa** alternativet i **redigera OS säkerhetskonfiguration regler**. Om du väljer det här alternativet visas följande popup-avisering. Välj **Ja** att bekräfta.

![](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Anpassa konfigurationsfilen

Varje OS-version som stöds har en uppsättning regler (ruleset) i anpassning. Varje uppsättning regler som har ett eget namn och unikt ID som visas i följande exempel:

![](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Den här filen har redigerats med Visual Studio, men du kan också använda anteckningar så länge som du har JSON Viewer plugin-programmet installerat.
>
>

När du redigerar filen, kan du ändra en regel eller alla. Varje regeluppsättning innehåller en *regler* avsnitt som innehåller regler, uppdelade efter 3 Regelkategori: registret, granskning och säkerhetsprincip som visas nedan:

![](media/security-center-customize-os-security-config/rules-section.png)

Varje kategori har en egen uppsättning attribut. För befintliga regler kan du göra ändringar i de följande:

- expectedValue: det här attributet har datatypen måste matcha värdena som stöds per varje regeltyp, till exempel:

  - baselineRegistryRules: värdet måste matcha den [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) definierats i regeln.

  - baselineAuditPolicyRules: värdet måste vara ett strängvärde, något av följande:

    - Lyckade och misslyckade 

    - Lyckades

  - baselineSecurityPolicyRules: värdet måste vara ett strängvärde, något av följande:

    - ”Ingen”

    - Lista över tillåtna användargrupper, till exempel: ”administratörer, ansvariga för säkerhetskopiering”

-   status: sträng som kan innehålla alternativ ”inaktiverad” eller ”Enabled”. För den här privat förhandsversionen är strängen skiftlägeskänsliga.

Dessa är de enda fält som kan konfigureras. Om du bryter mot filformatet eller storlek kan du inte spara ändringen. Följande felmeddelande visas när filen inte kan bearbetas:

![](media/security-center-customize-os-security-config/invalid-json.png)

Se [felkoder](#error-codes) lista över eventuella fel.

Nedan finner du några exempel på dessa regler. Attribut 'expectedValue' och 'state' kan ändras:

**Regler avsnitt:** baselineRegistryRules
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

**Regler avsnitt:** baselineAuditPolicyRules
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
},
```

**Regler avsnitt:** baselineSecurityPolicyRules
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
},
```

Det finns vissa regler som är duplicerade för olika typer av OS. Dubbla regler har samma 'originalId'.

## <a name="adding-a-new-custom-rule"></a>Lägga till en ny anpassad regel

Du kan också skapa en ny regel. Innan du skapar en ny regel, Tänk på följande begränsningar:

-   Schemaversionen *baselineId* och *baselineName* kan inte ändras.

-   RuleSet-metod kan inte tas bort.

-   RuleSet-metod kan inte läggas till.

-   Det maximala antalet regler för tillåtna (inklusive standardregler) är 1000 regler.

Nya regler som är markerade med en ny anpassad källa (! = ”Microsoft”). Den *ruleId* fältet kan vara null eller tomt. Om den är tom, genererar en Microsoft. Om den inte är tom måste den ha ett giltigt GUID som är unikt över alla regler (standard och anpassad). Granska villkoren nedan om fälten kärnor:

-   *originalId* -kan vara null eller tomt. Om *originalId* är inte tom, ska det vara ett giltigt GUID.

-   *cceId* -kan vara null eller tomt. Om *cceId* är inte tom, det måste vara unikt.

-   *ruleType* – en av: registret, AuditPolicy eller SecurityPolicy.

-   *Allvarlighetsgrad* – en av: Okänd, kritisk, varning eller information.

-   *analyzeOperation* -måste vara lika med.

-   *auditPolicyId* -måste vara giltigt GUID.

-   *regValueType* -måste vara något av: Int, Long, sträng eller MultipleString.

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
**Granskningsprincip:**
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

Om överförda konfigurationsfilen är ogiltig på grund av fel i värden eller formatering, visas ett felmeddelande. Du kan hämta en rapport för csv detaljerade fel för att reparera och åtgärda felen innan du skickar på nytt korrigerade konfigurationsfilen.

![](media/security-center-customize-os-security-config/invalid-configuration.png)

Exempel på filen fel:

![](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Felkoder

I listan nedan innehåller alla potentiella fel:

| **Fel**                                | **Beskrivning**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | Egenskap 'schemaVersion' hittades ogiltigt eller tomt. Värdet måste anges till '{0}'.                                                         |
| BaselineInvalidStringError               | Egenskapen '{0}' kan inte innehålla '\\n'.                                                                                                         |
| BaselineNullRuleError                    | Grundläggande konfiguration regellistan innehåller en regel med värdet 'null'.                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID: {0} är inte unikt.                                                                                                                  |
| BaselineRuleEmptyProperty                | Egenskapen:: {0} är ogiltig eller saknas.                                                                                                       |
| BaselineRuleIdNotInDefault               | Regeln har ett source-egenskapen 'Microsoft' men hittades inte i Microsoft standard RuleSet-metod.                                                   |
| BaselineRuleIdNotUniqueError             | Regel-Id är inte unikt.                                                                                                                       |
| BaselineRuleInvalidGuid                  | Egenskap '{0}' hittades ogiltig. Värdet är inte ett giltigt Guid.                                                                             |
| BaselineRuleInvalidHive                  | Hive måste vara LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Regelnamnet är inte unikt.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | Regeln hittades inte i den nya konfigurationen. Det går inte att ta bort regeln.                                                                     |
| BaselineRuleNotFoundError                | Regeln hittades inte i grunduppsättning regler.                                                                                        |
| BaselineRuleNotInPlace                   | Regeln matchar en standardregel med typen {0} och i {1}-listan.                                                                       |
| BaselineRulePropertyTooLong              | Egenskap: {0} är för långt. Högsta tillåtna längd: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | Förväntat värde: {0} matchar inte registervärdetypen som har definierats.                                                              |
| BaselineRulesetAdded                     | Ange regler med id: {0} hittades inte i standardkonfigurationen. Ange regler kan inte läggas till.                                               |
| BaselineRulesetIdMustBeUnique            | Den givna baslinje regler uppsättningen '{0}' måste vara unika.                                                                                           |
| BaselineRulesetNotFound                  | Regler som angetts med id: {0} och namnet {1} hittades inte i den angivna konfigurationen. Ange regler kan inte tas bort.                                |
| BaselineRuleSourceNotMatch               | Regeln med id: {0} har redan definierats.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Standardtypen för regeln är: {0}.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Den faktiska typen av regeln är: {0}, men ruleType egenskapen är: {1}.                                                                          |
| BaselineRuleUnpermittedChangesError      | Endast 'expectedValue' och 'tillstånd' egenskaper kan ändras.                                                                       |
| BaselineTooManyRules                     | Största tillåtna antalet anpassade regler är {0} regler. Den angivna konfigurationen innehåller {1} regler. ({2} standardregler + {3} anpassade regler. |
| ErrorNoConfigurationStatus               | Inga Konfigurationsstatus hittades. Ange läget önskad Konfigurationsstatus - 'Default' eller 'Custom'.                                    |
| ErrorNonEmptyRulesetOnDefault            | Konfigurationen status är inställd på standardvärdet. BaselineRulesets listan måste vara null eller tomt.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | Angivna konfigurationens status är 'Custom' men egenskapen 'baselineRulesets' är null eller tomt.                                             |
| ErrorParsingBaselineConfig               | Den angivna konfigurationen är ogiltig. En eller flera av de värden som definieras har ett null-värde eller en ogiltig datatyp.                                  |
| ErrorParsingIsDefaultProperty            | Det angivna configurationStatus värdet: {0} är ogiltigt. Värdet kan vara endast 'Default' eller 'Custom'.                                         |
| InCompatibleViewVersion                  | Visa version: {0} stöds inte på den här typen av arbetsyta.                                                                                   |
| InvalidBaselineConfigurationGeneralError | Den givna baslinje konfigurationen hittades med en eller flera verifieringsfel för typen.                                                          |
| ViewConversionError                      | Visa är en äldre version som stöds i den arbetsytan. Visa konvertering misslyckades: {0}                                                                 |

Om du inte har tillräcklig behörighet kan du få ett allmänt fel-fel:

![](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Nästa steg
Den här artikeln visades hur du anpassar OS säkerhetskonfiguration bedömningar i Security Center. Mer information om konfigurationsregler och reparationen finns:

- [Security Center vanliga Configuration identifierare och Baslinjeregler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Security Center använder CCE (Common Configuration uppräkning) för att tilldela unika identifierare för konfigurationsregler. Besök den [CCE](https://nvd.nist.gov/config/cce/index) webbplats för mer information.
- [Åtgärda säkerhetskonfigurationer](security-center-remediate-os-vulnerabilities.md) visar hur du löser säkerhetsproblem när Operativsystemets konfiguration inte matchar de rekommenderade konfigurationen säkerhetsreglerna.
