---
title: Anpassa OS-säkerhetskonfigurationer i Azure Security Center (för hands version) | Microsoft Docs
description: Den här artikeln visar hur du anpassar Security Center-utvärderingar
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
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: 8216aee1c27fd5bcb722648aa6380044e1431452
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662481"
---
# <a name="customize-os-security-configurations-in-azure-security-center-retired"></a>Anpassa OS-säkerhetskonfigurationer i Azure Security Center (dras tillbaka)

Den här genom gången visar hur du anpassar utvärderingar av säkerhets konfiguration för operativ system (för hands version) i Azure Security Center.

> [!NOTE]
> Möjligheten att anpassa OS-säkerhetskonfigurationer (förhands gransknings funktion) har dragits tillbaka den 31 juli 2019. Mer information och alternativa tjänster finns i [dra tillbaka Security Center funktioner (2019 juli)](security-center-features-retirement-july2019.md#menu_securityconfigurations).

## <a name="what-are-os-security-configurations"></a>Vad är säkerhets konfiguration för operativ system?

Azure Security Center övervakar säkerhetskonfigurationer med hjälp av en uppsättning på [fler än 150 rekommenderade regler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) för att härda operativsystemet, inklusive regler för brandväggar, granskning, lösenordsprinciper med mera. Om en sårbar konfiguration hittas på en dator, genererar Security Center en säkerhetsrekommendation.

Genom att anpassa reglerna kan organisationer styra vilka konfigurations alternativ som är lämpliga för deras miljö. Du kan ange en anpassad utvärderings princip och sedan tillämpa den på alla tillämpliga datorer i prenumerationen.

> [!NOTE]
> - För närvarande är anpassning av säkerhets konfigurationen för operativ systemet tillgängligt för Windows Server-versionerna 2008, 2008 R2, 2012, 2012 R2 och 2016 operativ system.
> - Konfigurationen gäller för alla virtuella datorer och datorer som är anslutna till alla arbets ytor under den valda prenumerationen.
> - Anpassning av säkerhets konfiguration för operativ system är bara tillgängligt på Security Center standard nivå.
>
>

Du kan anpassa säkerhets konfigurations reglerna för operativ systemet genom att aktivera och inaktivera en speciell regel, ändra den önskade inställningen för en befintlig regel eller lägga till en ny regel som baseras på de regel typer som stöds (register, gransknings princip och säkerhets princip). För närvarande måste den önskade inställningen vara ett exakt värde.

Nya regler måste ha samma format och struktur som andra befintliga regler av samma typ.

> [!NOTE]
> Om du vill anpassa OS-säkerhetskonfigurationer måste du ha tilldelats rollen som *prenumerations ägare*, *prenumerations deltagare*eller *säkerhets administratör*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Anpassa standard säkerhets konfigurationen för operativ systemet

Om du vill anpassa standard säkerhets konfigurationen för operativ systemet i Security Center gör du följande:

1.  Öppna instrumentpanelen för **Security Center**.

2.  Välj **pris & inställningar**i det vänstra fönstret.

    ![Lista över säkerhets principer](media/security-center-customize-os-security-config/manual-provision.png)

4. Välj lämplig prenumeration och välj **Redigera**säkerhetskonfigurationer.  

    ![Fönstret "redigera säkerhetskonfigurationer"](media/security-center-customize-os-security-config/blade.png)

5. Följ stegen för att ladda ned, redigera och ladda upp den ändrade filen.

   > [!NOTE]
   > Som standard är konfigurations filen som du hämtar i *JSON* -format. Anvisningar om hur du ändrar den här filen finns i [Anpassa konfigurations filen](#customize-the-configuration-file).
   >

6. Om du vill genomföra ändringen väljer du **Spara**. Annars lagras inte principen.

    ![Knappen Spara](media/security-center-customize-os-security-config/save-successfully.png)

   När du har sparat filen tillämpas konfigurationen på alla virtuella datorer och datorer som är anslutna till arbets ytorna under prenumerationen. Processen tar vanligt vis några minuter, men det kan ta längre tid, beroende på infrastrukturens storlek.

Du kan när som helst återställa den aktuella princip konfigurationen till dess standard tillstånd. Det gör du genom att välja **Återställ**i fönstret **Redigera säkerhets konfigurations regler för operativ system** . Bekräfta det här alternativet genom att välja **Ja** i popup-fönstret bekräftelse.

![Bekräftelse fönstret för återställning](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Anpassa konfigurations filen

I anpassnings filen har varje OS-version som stöds en uppsättning regler, eller ruleset. Varje ruleset har ett eget namn och ett unikt ID, som visas i följande exempel:

![Ruleset namn och ID](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Den här exempel filen har redigerats i Visual Studio, men du kan också använda anteckningar om du har plugin-programmet JSON Viewer installerat.
>
>

När du redigerar anpassnings filen kan du ändra en eller flera regler. Varje ruleset innehåller ett *regel* avsnitt som är indelat i tre kategorier: Register, gransknings princip och säkerhets princip, som du ser här:

![Tre ruleset-kategorier](media/security-center-customize-os-security-config/rules-section.png)

Varje kategori har en egen uppsättning attribut. Du kan ändra följande attribut:

- **expectedValue**: Det här attributets fält data typ måste matcha de värden som stöds per *regeltyp*, till exempel:

  - **baselineRegistryRules**: Värdet måste matcha [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) som definieras i regeln.

  - **baselineAuditPolicyRules**: Använd något av följande sträng värden:

    - *Lyckade och misslyckade*

    - *Resultatet*

  - **baselineSecurityPolicyRules**: Använd något av följande sträng värden:

    - *Ingen*

    - Lista över tillåtna användar grupper, till exempel: *Administratörer*, *ansvariga för säkerhets kopiering*

-   **tillstånd**: Strängen kan innehålla alternativen inaktiverade eller *aktiverade*. I den här versionen är strängen Skift läges känslig.

Detta är de enda fält som kan konfigureras. Om du strider mot fil formatet eller storleken kan du inte spara ändringen. Du får ett fel meddelande som anger att du måste ladda upp en giltig JSON-konfigurationsfil.

En lista över andra möjliga fel finns i [felkoder](#error-codes).

Följande tre avsnitt innehåller exempel på föregående regler. Attributen *expectedValue* och *State* kan ändras.

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

Vissa regler är duplicerade för olika operativ system typer. Duplicerade regler har samma *originalId* -attribut.

## <a name="create-custom-rules"></a>Skapa anpassade roller

Du kan också skapa nya regler. Tänk på följande begränsningar innan du skapar en ny regel:

-   Schema versionen, *baselineId* och *baselineName* kan inte ändras.

-   Ruleset kan inte tas bort.

-   Det går inte att lägga till ruleset.

-   Det högsta tillåtna antalet regler (inklusive standard regler) är 1000.

Nya anpassade regler markeras med en ny anpassad källa (! = "Microsoft"). Fältet *ruleId* kan vara null eller tomt. Om det är tomt genererar Microsoft en. Om den inte är tom måste den ha ett giltigt GUID som är unikt för alla regler (standard och anpassad). Granska följande begränsningar för Core-fälten:

-   **originalId**: Kan vara null eller tomt. Om *originalId* inte är tomt bör det vara ett giltigt GUID.

-   **cceId**: Kan vara null eller tomt. Om *cceId* inte är tomt måste det vara unikt.

-   **ruleType**: (Välj en) Registry, AuditPolicy eller SECURITYPOLICY.

-   **Allvarlighets grad**: (Välj en) okänd, kritisk, varning eller information.

-   **analyzeOperation**: Måste vara *lika med*.

-   **auditPolicyId**: Måste vara ett giltigt GUID.

-   **regValueType**: (Välj en) int-, Long-, String-eller MultipleString-värde.

> [!NOTE]
> Hive måste vara *LocalMachine*.
>
>

Exempel på en ny anpassad regel:

**Register**:
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
**Säkerhets princip**:
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
**Gransknings princip**:
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

## <a name="file-upload-failures"></a>Fil överförings problem

Om den skickade konfigurations filen är ogiltig på grund av fel i värden eller formatering, visas ett fel meddelande, till exempel **Spara-åtgärden misslyckades**. Du kan hämta en detaljerad errors. csv-rapport för att åtgärda och åtgärda felen innan du skickar en korrigerad konfigurations fil igen.

Exempel på en felfil:

![Fel fil exempel](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Felkoder

Alla potentiella fel visas i följande tabell:

| **Fels**                                | **Beskrivning**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfigurationSchemaVersionError  | Egenskapen *schema schema* hittades ogiltig eller tom. Värdet måste anges till *{0}* .                                                         |
| BaselineInvalidStringError               | Egenskapen *{0}* får inte innehålla  *\\n*.                                                                                                         |
| BaselineNullRuleError                    | Listan över bas linje konfigurations regler innehåller en regel med värdet *Null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID: *{0}* t är inte unikt.                                                                                                                  |
| BaselineRuleEmptyProperty                | Egenskapen *{0}* saknas eller är ogiltig.                                                                                                       |
| BaselineRuleIdNotInDefault               | Regeln har en käll egenskap *Microsoft* men den hittades inte i Microsoft standard ruleset.                                                   |
| BaselineRuleIdNotUniqueError             | Regel identifieraren är inte unik.                                                                                                                       |
| BaselineRuleInvalidGuid                  | Egenskapen *{0}* hittades ogiltig. Värdet är inte ett giltigt GUID.                                                                             |
| BaselineRuleInvalidHive                  | Hive måste vara LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Regel namnet är inte unikt.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | Det gick inte att hitta regeln i den nya konfigurationen. Det går inte att ta bort regeln.                                                                     |
| BaselineRuleNotFoundError                | Det gick inte att hitta regeln i standard bas linje ruleset.                                                                                        |
| BaselineRuleNotInPlace                   | Regeln matchar en standard regel med typ {0} och visas i {1} listan.                                                                       |
| BaselineRulePropertyTooLong              | Egenskapen *{0}* är för lång. Högsta tillåtna längd: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | Det förväntade värdet *{0}* matchar inte den register värde typ som har definierats.                                                              |
| BaselineRulesetAdded                     | Det gick inte att hitta *{0}* ruleset med identifieraren i standard konfigurationen. Det går inte att lägga till ruleset.                                               |
| BaselineRulesetIdMustBeUnique            | Den aktuella bas linjens ruleset *{0}* måste vara unik.                                                                                           |
| BaselineRulesetNotFound                  | Det gick inte att *{0}* hitta ruleset *{1}* med ID och namn i den aktuella konfigurationen. Det går inte att ta bort ruleset.                                |
| BaselineRuleSourceNotMatch               | Regeln med identifierare *{0}* har redan definierats.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Standard regel typen är *{0}* .                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Regelns faktiska typ är *{0}* , men egenskapen *ruleType* är. *{1}*                                                                          |
| BaselineRuleUnpermittedChangesError      | Endast egenskaperna *expectedValue* och *State* får ändras.                                                                       |
| BaselineTooManyRules                     | Det maximala antalet tillåtna anpassade regler är {0} regler. Den aktuella konfigurationen innehåller {1} regler, {2} standard regler och {3} anpassade regler. |
| ErrorNoConfigurationStatus               | Ingen konfigurations status hittades. Ange önskad konfigurations status: *Standard* eller *anpassad*.                                    |
| ErrorNonEmptyRulesetOnDefault            | Konfigurations statusen är inställd på default. *BaselineRulesets* -listan måste vara null eller tom.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | Den aktuella konfigurations statusen är *anpassad* men *baselineRulesets* -egenskapen är null eller tom.                                             |
| ErrorParsingBaselineConfig               | Den aktuella konfigurationen är ogiltig. Ett eller flera av de definierade värdena har ett null-värde eller en ogiltig typ.                                  |
| ErrorParsingIsDefaultProperty            | Angivet *configurationStatus* -värde *{0}* är ogiltigt. Värdet kan vara *standard* eller *anpassat*.                                         |
| InCompatibleViewVersion                  | View-versionen *{0}* stöds *inte* för den här typen av arbets yta.                                                                                   |
| InvalidBaselineConfigurationGeneralError | Den aktuella bas linje konfigurationen påträffades med en eller flera typ verifierings fel.                                                          |
| ViewConversionError                      | Vyn är en äldre version än vad arbets ytan stöder. Det gick inte att {0}konvertera vyn:.                                                                 |

Om du inte har tillräcklig behörighet kan du få ett allmänt fel meddelande som visas här:

![Fel meddelandet "Det gick inte att spara åtgärden"](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Nästa steg
Den här artikeln beskrivs hur du anpassar utvärderingen av säkerhets konfiguration i Security Center i. Mer information om konfigurations reglerna och reparationen finns i:

- [Security Center vanliga konfigurations identifierare och bas linje regler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Security Center använder vanliga konfigurations uppräkning (CCE) för att tilldela unika identifierare till konfigurations regler. Mer information finns i [CCE](https://nvd.nist.gov/config/cce/index).
- Information om hur du löser säkerhets risker när din operativ system konfiguration inte matchar de rekommenderade säkerhets konfigurations reglerna finns i [åtgärda](security-center-remediate-os-vulnerabilities.md)säkerhetskonfigurationer.
