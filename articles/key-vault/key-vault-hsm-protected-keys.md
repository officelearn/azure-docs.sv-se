---
title: Så här genererar och överför HSM-skyddade nycklar för Azure Key Vault | Microsoft Docs
description: Använd den här artikeln hjälper dig att planera för, generera och överföra din egen HSM-skyddade nycklar som ska användas med Azure Key Vault. Kallas även BYOK eller egna nycklar.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: barclayn
ms.openlocfilehash: f027ced7d6e317bfdf101cb792d9f2f2b7612242
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247752"
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Så här genererar och överför HSM-skyddade nycklar för Azure Key Vault

För extra trygghet, när du använder Azure Key Vault, kan du importera eller generera nycklar i maskinvarusäkerhetsmoduler (HSM) som aldrig lämnar HSM: ens gränser. Det här scenariot kallas ofta att *egna nycklar*, eller BYOK. HSM-modulerna är FIPS 140-2 Level 2-verifierade. Azure Key Vault använder Thales nShield-familj av HSM: er för att skydda dina nycklar.

Använd informationen i det här avsnittet hjälper dig att planera för, generera och överföra din egen HSM-skyddade nycklar som ska användas med Azure Key Vault.

Den här funktionen är inte tillgänglig för Azure Kina.

> [!NOTE]
> Läs mer om Azure Key Vault [vad är Azure Key Vault?](key-vault-whatis.md)  
> En introduktionskurs som innefattar att skapa key vault för HSM-skyddade nycklar, se [Kom igång med Azure Key Vault](key-vault-get-started.md).

Mer information om generera och överföra en HSM-skyddad nyckel via Internet:

* Du kan generera nyckeln från en arbetsstation som offline, vilket minskar risken för angrepp.
* Nyckeln är krypterad med en nyckel Exchange-nyckel (KEK), som är krypterad tills den överförs till Azure Key Vault HSM. Den krypterade versionen av nyckeln lämnar den ursprungliga arbetsstationen.
* Verktygsuppsättningen anger egenskaperna för din klientnyckel som binder din nyckel till Azure Key Vault-säkerhetsvärlden. Så när Azure Key Vault HSM tagit emot och dekrypterat din nyckel, kan bara dessa HSM: er använda den. Din nyckel kan inte exporteras. Den här bindningen är påtvingad av Thales HSM: er.
* Den nyckel Exchange-nyckel (KEK) som används för att kryptera din nyckel genereras i Azure Key Vault HSM och kan inte exporteras. De HSM: erna genomdriver att det kan finnas någon klartextversion av KEK utanför HSM: erna. Dessutom innehåller verktygsuppsättningen intyg från Thales om att KEK inte kan exporteras och genereras inuti en äkta HSM som har tillverkats av Thales.
* Verktygsuppsättningen innehåller intyg från Thales om att Azure Key Vault-säkerhetsvärld genererades i en äkta HSM tillverkad av Thales. Den här attestering bevisar för dig att Microsoft använder äkta maskinvara.
* Microsoft använder separata KeyExchange-nycklar och separata Säkerhetsvärldar i varje geografisk region. Den här separationen säkerställer att din nyckel kan användas endast i datacenter i den region där den krypterades. Exempelvis kan en nyckel från en europeisk kund inte användas i datacenter i Nordamerika eller Asien.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Mer information om Thales HSM: er och Microsoft-tjänster

Thales e-Security är en ledande global leverantör av datakryptering och cybersäkerhetslösningar till finansiella tjänster, avancerad teknik, tillverkning, myndigheter och informationsteknik. Thales lösningar som används av fyra av fem största energi och flygindustrin företag med en 40 år bevisade framgångar med att skydda företagets och myndighets-information. Sina lösningar används också av 22 NATO-länder och skyddar mer än 80 procent av transaktioner över hela världen.

Microsoft har samarbetat med Thales för att göra avancerade för HSM: er. Dessa förbättringar kan du få de vanliga fördelarna med värdtjänster utan att behöva lämna ifrån dig kontrollen över dina nycklar. Mer specifikt att medför förbättringarna Microsoft hantera HSM: erna så att du inte behöver. Som en molntjänst skalar Azure Key Vault upp med kort varsel för att uppfylla din organisations användningstoppar. På samma gång, skyddas din nyckel i Microsofts HSM: du behålla kontrollen över nyckelns livscykel eftersom du generera nyckeln och överföra den till Microsofts HSM: er.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementera ta med din egen nyckel (BYOK) för Azure Key Vault

Använd följande information och procedurer om du ska skapa dina egna HSM-skyddad nyckel och sedan överföra det till Azure Key Vault – ta med din egen nyckel (BYOK)-scenario.

## <a name="prerequisites-for-byok"></a>Förutsättningar för BYOK

I tabellen nedan finns en lista över förutsättningar för att ta med din egen nyckel (BYOK) för Azure Key Vault.

| Krav | Mer information |
| --- | --- |
| En prenumeration på Azure |Om du vill skapa ett Azure Key Vault behöver du en Azure-prenumeration: [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/) |
| Azure Key Vault Premium-tjänstnivån för HSM-skyddade nycklar |Mer information om tjänstnivåer och funktioner för Azure Key Vault finns i den [priser för Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) webbplats. |
| Thales HSM, smartkort och hjälpprogram |Du måste ha åtkomst till en maskinvarusäkerhetsmodul och grundläggande operativa kunskaper om Thales HSM: er. Se [maskinvarusäkerhetsmodul](https://www.thales-esecurity.com/msrms/buy) lista över kompatibla modeller eller för att köpa en HSM om du inte har något. |
| Följande maskinvara och programvara:<ol><li>Ett offline x64 arbetsstation med minst Windows-operativsystemet Windows 7 och Thales nShield-programvara som är minst version 11.50.<br/><br/>Om den här arbetsstationen kör Windows 7, måste du [installera Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>En arbetsstation som är ansluten till Internet med en minsta Windows-operativsystemet Windows 7 och [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.7.0) **lägsta version 1.1.0** installerad.</li><li>En USB-enhet eller annan bärbar lagringsenhet som har minst 16 MB ledigt utrymme.</li></ol> |Av säkerhetsskäl rekommenderar vi att den första arbetsstationen inte är ansluten till ett nätverk. Men är den här rekommendationen inte programmässigt tvingande.<br/><br/>Observera att i instruktionerna som följer den här arbetsstationen kallas den frånkopplade arbetsstationen.</p></blockquote><br/>Om din klientnyckel är avsedd för ett produktionsnätverk, rekommenderar vi dessutom att du använder en andra, separat arbetsstation för att hämta verktygen och överföra klientnyckeln. Men i testsyfte kan du använda samma arbetsstation som den första.<br/><br/>Observera att i instruktionerna som följer den här andra arbetsstationen kallas den Internetanslutna arbetsstationen.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generera och överför din nyckel till Azure Key Vault HSM

Du vill använda följande fem steg för att generera och överför din nyckel till en Azure Key Vault HSM:

* [Steg 1: Förbered din Internetanslutna arbetsstation](#step-1-prepare-your-internet-connected-workstation)
* [Steg 2: Förbered din frånkopplade arbetsstation](#step-2-prepare-your-disconnected-workstation)
* [Steg 3: Generera nyckeln](#step-3-generate-your-key)
* [Steg 4: Förbered din nyckel för överföring](#step-4-prepare-your-key-for-transfer)
* [Steg 5: Överför nyckeln till Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Steg 1: Förbered din Internetanslutna arbetsstation

För det här första steget, gör du följande procedurer på en arbetsstation som är ansluten till Internet.

### <a name="step-11-install-azure-powershell"></a>Steg 1.1: Installera Azure PowerShell

Hämta och installera Azure PowerShell-modulen som innehåller cmdletar för att hantera Azure Key Vault från den Internetanslutna arbetsstationen. Detta kräver en lägsta version av 0.8.13.

Installationsanvisningar finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Steg 1.2: Hämta ditt Azure-prenumerations-ID

Starta en Azure PowerShell-session och logga in på ditt Azure-konto med hjälp av följande kommando:

```Powershell
   Add-AzureRMAccount
```
Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Använd sedan den [Get-AzureSubscription](/powershell/module/servicemanagement/azure/get-azuresubscription?view=azuresmps-3.7.0) kommando:

```powershell
   Get-AzureRMSubscription
```
Leta upp ID för den prenumeration som du ska använda för Azure Key Vault i utdata. Du behöver den här prenumerations-ID senare.

Stäng inte Azure PowerShell-fönster.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Steg 1.3: Hämta BYOK-verktygen för Azure Key Vault

Gå till Microsoft Download Center och [ladda ned Azure Key Vault BYOK-verktygsuppsättningen](https://www.microsoft.com/download/details.aspx?id=45345) för geografisk region eller instans av Azure. Använd följande information för att identifiera paketets namn att ladda ned och dess motsvarande SHA-256 paketets hashvärde:

- - -
**USA:**

KeyVault-BYOK-verktyg-Förenade States.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

- - -
**Europa:**

KeyVault-BYOK-verktyg-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

- - -
**Asien:**

KeyVault-BYOK-verktyg-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

- - -
**Latinamerika:**

KeyVault-BYOK-verktyg-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

- - -
**Japan:**

KeyVault-BYOK-verktyg-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

- - -
**Korea:**

KeyVault-BYOK-verktyg-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

- - -
**Australien:**

KeyVault-BYOK-verktyg-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

- - -
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-verktyg-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

- - -
**Amerikanska myndigheter DOD:**

KeyVault-BYOK-verktyg-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

- - -
**Kanada:**

KeyVault-BYOK-verktyg-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

- - -
**Tyskland:**

KeyVault-BYOK-verktyg-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

- - -
**Indien:**

KeyVault-BYOK-verktyg-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

- - -
**Frankrike:**

KeyVault-BYOK-verktyg-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

- - -
**Storbritannien:**

KeyVault-BYOK-verktyg-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

- - -

Om du vill validera integriteten för din hämtade BYOK-verktygen, Azure PowerShell-session och använda den [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) cmdlet.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Denna verktygsuppsättning omfattar följande:

* Ett paket för nyckeln Exchange-nyckel (KEK) som har ett namn som börjar med **BYOK-KEK - pkg-.**
* Ett säkerhetsvärldspaket som har ett namn som börjar med **BYOK-SecurityWorld - pkg-.**
* Python-skriptet **verifykeypackage.py.**
* En kommandorad körbara filen **KeyTransferRemote.exe** och tillhörande DLL-filer.
* Ett Visual C++ Redistributable Package med namnet **vcredist_x64.exe.**

Kopiera paketet till en USB-enhet eller annan bärbar lagringsenhet.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Steg 2: Förbered din frånkopplade arbetsstation

För den här andra steget, gör du följande procedurer på en arbetsstation som inte är ansluten till ett nätverk (Internet eller ditt interna nätverk).

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Steg 2.1: Förbered den frånkopplade arbetsstationen med Thales HSM

Installera support-mjukvaran hjälpprogrammet (Thales) på en Windows-dator och Anslut en Thales HSM till datorn.

Se till att Thales-verktygen finns i din sökväg (**%nfast_home%\bin**). Skriv till exempel följande:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Mer information finns i användarhandboken som medföljer Thales HSM.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Steg 2.2: Installera BYOK-verktygen på den frånkopplade arbetsstationen

Kopiera BYOK-verktygspaketet från USB-enhet eller annan bärbar lagringsenhet och gör sedan följande:

1. Extrahera filerna från det Hämta paketet till valfri mapp.
2. Kör vcredist_x64.exe från denna mapp.
3. Följ anvisningarna för att installera Visual C++-körtidskomponenter för Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Steg 3: Generera nyckeln

För den här tredje steget, gör du följande procedurer på den frånkopplade arbetsstationen. För att slutföra det här steget måste din HSM vara i läget för initiering. 


### <a name="step-31-change-the-hsm-mode-to-i"></a>Steg 3.1: Ändra HSM-läge till ”I”

Om du använder Thales nShield Edge, för att ändra läget: 1. Du kan använda knappen läge för att markera läget som krävs. 2. Inom några sekunder, tryck och håll Rensa-knapp för några sekunder. Om läget ändras kan det nya läget LED slutar blinka och fortsätter att lysa. Status-LED kan flash oregelbundet under några sekunder och sedan blinkar regelbundet när enheten är klar. I annat fall enheten finns kvar i det aktuella läget med det aktuella läget LED tänd.

### <a name="step-32-create-a-security-world"></a>Steg 3.2: Skapa en säkerhetsvärld

Starta en kommandotolk och kör programmet Thales ny värld.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3
   ```

Det här programmet skapar en **Säkerhetsvärlden** i % NFAST_KMDATA%\local\world, vilket motsvarar mappen C:\ProgramData\nCipher\Key Management Data\local. Du kan använda olika värden för kvorumet men i vårt exempel uppmanas du att ange tre tomma kort och PIN-koder för vart och ett. Därefter ger två valfria kort fullständig åtkomst till säkerhetsvärlden. Dessa kort blir den **Administratörskortsuppsättningen** för den nya säkerhetsvärlden.

Gör något av följande:

* Säkerhetskopiera världfilen. Säkra och skydda världfilen, Administratörskorten och tillhörande PIN-koder och se till att ingen enskild person har tillgång till mer än ett kort.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Steg 3.3: Ändra HSM-läge till ' formatmönster

Om du använder Thales nShield Edge, för att ändra läget: 1. Du kan använda knappen läge för att markera läget som krävs. 2. Inom några sekunder, tryck och håll Rensa-knapp för några sekunder. Om läget ändras kan det nya läget LED slutar blinka och fortsätter att lysa. Status-LED kan flash oregelbundet under några sekunder och sedan blinkar regelbundet när enheten är klar. I annat fall enheten finns kvar i det aktuella läget med det aktuella läget LED tänd.

### <a name="step-34-validate-the-downloaded-package"></a>Steg 3.4: Validera det Hämta paketet

Det här steget är valfritt men rekommenderas så att du kan validera följande:

* KeyExchange-nyckeln som ingår i verktygsuppsättningen har genererats från en äkta Thales HSM.
* Hash för Säkerhetsvärld som ingår i verktygsuppsättningen har genererats från en äkta Thales HSM.
* KeyExchange-nyckeln är inte kan exporteras.

> [!NOTE]
> Om du vill validera det Hämta paketet HSM måste vara ansluten, påslagen, och måste ha en säkerhetsvärld (till exempel det som du nyss skapat).

Verifiera det Hämta paketet:

1. Kör skriptet verifykeypackage.py genom att ange någon av följande, beroende på dina geografiska region eller instans av Azure:

   * För Nordamerika:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * För Europa:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * För Asien:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * För Latinamerika:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * För Japan:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * För Korea:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * För Australien:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * För [Azure Government](https://azure.microsoft.com/features/gov/), som använder US government-instans av Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * För amerikanska myndigheter DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * För Kanada:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * För Tyskland:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * För Indien:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Frankrike:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * För Storbritannien:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1

     > [!TIP]
     > Thales-programvaran innehåller python på %NFAST_HOME%\python\bin
     >
     >
2. Kontrollera att du ser följande, som anger lyckad validering: **resultat: lyckades**

Det här skriptet validerar undertecknarkedjan upp till Thales rotnyckel. Hash för den här rotnyckeln är inbäddad i skriptet och dess värde bör vara **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Du kan också bekräfta det här värdet separat genom att besöka den [Thales webbplats](http://www.thalesesec.com/).

Du är nu redo att skapa en ny nyckel.

### <a name="step-35-create-a-new-key"></a>Steg 3.5: Skapa en ny nyckel

Generera en nyckel med hjälp av Thales **generatekey** program.

Kör följande kommando för att generera nyckeln:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Använd dessa anvisningar när du kör det här kommandot:

* Parametern *skydda* måste anges till värdet **modulen**som visas. Detta skapar en modulskyddad nyckel. BYOK-verktygen har inte stöd för OCS-skyddade nycklar.
* Ersätt värdet för *contosokey* för den **ident** och **plainname** med valfritt strängvärde. För att minimera de administrativa kostnaderna och minska risken för fel, rekommenderar vi att du använder samma värde för båda. Den **ident** värdet måste innehålla endast siffror, bindestreck och gemena bokstäver.
* Pubexp lämnas tomt (standard) i det här exemplet, men du kan ange specifika värden. Mer information finns i Thales-dokumentationen.

Det här kommandot skapar en fil för Tokeniserad nyckel i mappen %NFAST_KMDATA%\local med ett namn som börjar med **key_simple_**, följt av den **ident** som har angetts i kommandot. Till exempel: **key_simple_contosokey**. Den här filen innehåller en krypterad nyckel.

Säkerhetskopiera filen för Tokeniserad nyckel på en säker plats.

> [!IMPORTANT]
> När du senare överför din nyckel till Azure Key Vault kan inte Microsoft exportera den här nyckeln till dig, så det är mycket viktigt att säkerhetskopiera din nyckel och säkerhetsvärld på ett säkert sätt. Kontakta Thales för vägledning och bästa praxis för att säkerhetskopiera din nyckel.
>


Du är nu redo att överföra din nyckel till Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Steg 4: Förbered din nyckel för överföring

För den här fjärde steget, gör du följande procedurer på den frånkopplade arbetsstationen.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Steg 4.1: Skapa en kopia av din nyckel med minskade behörigheter

Öppna en kommandotolk och ändra den aktuella katalogen till den plats där du har packat BYOK zip-filen. Kör något av följande, beroende på dina geografiska region eller instans av Azure för att minska behörigheterna för din nyckel från en kommandotolk:

* För Nordamerika:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* För Europa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* För Asien:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* För Latinamerika:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* För Japan:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* För Korea:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* För Australien:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* För [Azure Government](https://azure.microsoft.com/features/gov/), som använder US government-instans av Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* För amerikanska myndigheter DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* För Kanada:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* För Tyskland:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* För Indien:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Frankrike:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* För Storbritannien:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1

När du kör det här kommandot ersätter *contosokey* med samma värde som du angav i **steg 3.5: skapa en ny nyckel** från den [generera nyckeln](#step-3-generate-your-key) steg.

Du uppmanas att plugin-säkerhet världen admin kort.

När kommandot har slutförts visas **resultat: lyckades** och kopia av din nyckel med minskade behörigheter finns i filen key_xferacid_<contosokey>.

Du kan inspekterar ACL: er med följande kommandon med hjälp av Thales-verktygen:

* aclprint.PY:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  När du kör dessa kommandon ersätter contosokey med samma värde som du angav i **steg 3.5: skapa en ny nyckel** från den [generera nyckeln](#step-3-generate-your-key) steg.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Steg 4.2: Kryptera nyckeln med hjälp av Microsofts KeyExchange-nyckel

Kör något av följande kommandon, beroende på dina geografiska region eller instans av Azure:

* För Nordamerika:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Europa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Asien:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Latinamerika:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Japan:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Korea:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Australien:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För [Azure Government](https://azure.microsoft.com/features/gov/), som använder US government-instans av Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För amerikanska myndigheter DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Kanada:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Tyskland:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Indien:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Frankrike:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Storbritannien:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Använd dessa anvisningar när du kör det här kommandot:

* Ersätt *contosokey* med den identifierare som du använde för att generera nyckeln i **steg 3.5: skapa en ny nyckel** från den [generera nyckeln](#step-3-generate-your-key) steg.
* Ersätt *SubscriptionID* med ID: T för Azure-prenumerationen som innehåller ditt nyckelvalv. Det här värdet som du hämtade tidigare i **steg 1.2: hämta ditt Azure-prenumerations-ID** från den [Förbered din Internetanslutna arbetsstation](#step-1-prepare-your-internet-connected-workstation) steg.
* Ersätt *ContosoFirstHSMKey* med en etikett som används för ditt utdata-filnamn.

När detta är klar visas **resultat: lyckades** och det finns en ny fil i den aktuella mappen med följande namn: keytransferpackage*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Steg 4.3: Kopiera ditt nyckelöverföringspaket till den Internetanslutna arbetsstationen

Använd en USB-enhet eller annan bärbar lagringsenhet för att kopiera utdatafilen från föregående steg (KeyTransferPackage ContosoFirstHSMkey.byok) till din Internetanslutna arbetsstation.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Steg 5: Överför nyckeln till Azure Key Vault

Det här sista steget på den Internetanslutna arbetsstationen använder de [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-add-azurekeyvaultkey) cmdlet för att ladda upp nyckelöverföringspaketet som du kopierade från den frånkopplade arbetsstationen till Azure Key Vault HSM:

   ```powershell
        Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Om överföringen lyckas visas visas egenskaperna för den nyckel som du just lade till.

## <a name="next-steps"></a>Nästa steg

Du kan nu använda den här HSM-skyddad nyckel i ditt nyckelvalv. Mer information finns i den **om du vill använda en maskinvarusäkerhetsmodul (HSM)** i avsnittet den [komma igång med Azure Key Vault](key-vault-get-started.md) självstudien.
