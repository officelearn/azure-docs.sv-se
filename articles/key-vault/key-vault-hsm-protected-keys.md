---
title: "Generera och överför HSM-skyddade nycklar för Azure Key Vault | Microsoft Docs"
description: "Använd den här artikeln för att planera för, generera och överföra din egen HSM-skyddade nycklar som ska användas med Azure Key Vault. Kallas även BYOK eller egna nycklar."
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: barclayn
ms.openlocfilehash: 6c49b086fd35a855fa8e32fa576c5b52d16f1d04
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Generera och överför HSM-skyddade nycklar för Azure Key Vault
## <a name="introduction"></a>Introduktion
För ytterligare säkerhet när du använder Azure Key Vault kan importera eller generera nycklar i maskinvarusäkerhetsmoduler (HSM) som lämnar aldrig HSM-gräns. Det här scenariot kallas ofta till *egna nycklar*, eller BYOK. HSM-modulerna är FIPS 140-2 Level 2-verifierade. Azure Key Vault använder Thales nShield familj av HSM för att skydda dina nycklar.

Använd informationen i det här avsnittet för att hjälpa dig att planera för, generera och överföra din egen HSM-skyddade nycklar som ska användas med Azure Key Vault.

Den här funktionen är inte tillgänglig för Azure Kina.

> [!NOTE]
> Mer information om Azure Key Vault finns [vad är Azure Key Vault?](key-vault-whatis.md)  
>
> En komma igång-kursen, vilket innefattar att skapa nyckelvalvet för HSM-skyddade nycklar, se [Kom igång med Azure Key Vault](key-vault-get-started.md).
>
>

Mer information om generera och överför en HSM-skyddad nyckel via Internet:

* Du kan generera nyckeln från en arbetsstation som offline, vilket minskar risken för angrepp.
* Nyckeln är krypterad med en KeyExchange-nyckel (KEK), som är krypterad tills den överförs till Azure Key Vault HSM. Den krypterade versionen av din nyckel lämnar den ursprungliga arbetsstationen.
* Verktygsuppsättningen anger egenskaperna för din klientnyckel som binder din nyckel till Azure Key Vault-säkerhetsvärlden. Så när Azure Key Vault HSM tagit emot och dekrypterat din nyckel, kan bara dessa HSM: er använda den. Din nyckel kan inte exporteras. Den här bindningen är påtvingad av Thales HSM: er.
* Den KeyExchange-nyckel (KEK) som används för att kryptera nyckeln genereras i Azure Key Vault HSM och kan inte exporteras. De HSM: erna genomdriver att det kan finnas någon klartextversion av KEK utanför HSM: er. Dessutom innehåller verktygsuppsättningen intyg från Thales om att KEK inte kan exporteras och genereras inuti en äkta HSM som har tillverkats av Thales.
* Verktygsuppsättningen innehåller intyg från Thales om att Azure Key Vault-säkerhetsvärld genererades i en äkta HSM tillverkad av Thales. Detta bevisar för dig att Microsoft använder äkta maskinvara.
* Microsoft använder separata KeyExchange-nycklar och separata Säkerhetsvärldar i varje geografisk region. Den här separationen säkerställer att din nyckel kan användas i datacenter i den region där den krypterades. Exempelvis kan en nyckel från en europeisk kund inte användas i datacenter i Nordamerika eller Asien.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Mer information om Thales HSM: er och Microsoft-tjänster
Thales e-Security är en ledande global leverantör krypteringen och cybersäkerhetslösningar till finansiella tjänster, avancerad teknik, tillverkning, myndigheter och informationsteknik. Thales lösningar som används av fyra av fem största energi- och aerospace företag med en 40 år spåra post för att skydda företagets och myndigheter information. Sina lösningar används också av 22 NATO-länder och skyddar mer än 80 procent av transaktioner över hela världen.

Microsoft har samarbetat med Thales för att göra-objekt för HSM. Dessa förbättringar kan du få de vanliga fördelarna med värdtjänster utan att behöva lämna ifrån dig kontrollen över dina nycklar. Mer specifikt låta dessa förbättringar Microsoft hantera HSM: erna så att du inte behöver. Azure Key Vault skala med kort varsel för att uppfylla din organisations användningstoppar som en molntjänst. På samma gång, skyddas din nyckel i Microsofts HSM: du behålla kontrollen över nyckelns livscykel eftersom du genererar nyckeln och överför den till Microsofts HSM: er.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementera byok (BYOK) för Azure Key Vault
Använd följande information och procedurer om du ska generera en egen HSM-skyddad nyckel och sedan överföra det till Azure Key Vault – ta din egen nyckel (BYOK)-scenario.

## <a name="prerequisites-for-byok"></a>Krav för BYOK
I tabellen nedan finns en lista över förutsättningarna för att överföra din egen nyckel (BYOK) för Azure Key Vault.

| Krav | Mer information |
| --- | --- |
| En prenumeration på Azure |Du behöver en Azure-prenumeration för att skapa ett Azure Key Vault: [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/) |
| Azure Key Vault Premium tjänstnivån att stödja HSM-skyddade nycklar |Mer information om tjänstnivåer och funktioner för Azure Key Vault finns i [priser för Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) webbplats. |
| Thales HSM, smartkort och hjälpprogram |Du måste ha åtkomst till en maskinvarusäkerhetsmodul och grundläggande operativa kunskaper om Thales HSM: er. Se [maskinvarusäkerhetsmodul](https://www.thales-esecurity.com/msrms/buy) för listan över kompatibla modeller eller för att köpa en HSM om du inte har någon. |
| Följande maskinvara och programvara:<ol><li>Ett offline x64 arbetsstation med minst Windows-operativsystemet Windows 7 och Thales nShield-programvara som är minst version 11.50.<br/><br/>Om den här arbetsstationen kör Windows 7, måste du [installera Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>En arbetsstation som är ansluten till Internet och har en minsta Windows-operativsystemet Windows 7 och [Azure PowerShell](/powershell/azure/overview) **minimiversionen 1.1.0** installerad.</li><li>En USB-enhet eller annan bärbar lagringsenhet som har minst 16 MB ledigt utrymme.</li></ol> |Av säkerhetsskäl rekommenderar vi att den första arbetsstationen inte är ansluten till ett nätverk. Men är den här rekommendationen inte programmässigt tvingande.<br/><br/>Observera att i instruktionerna som följer den här arbetsstationen kallas den frånkopplade arbetsstationen.</p></blockquote><br/>Om din klientnyckel är avsedd för ett produktionsnätverk, rekommenderar vi dessutom att du använder andra, separat arbetsstation för att hämta verktygen och överföra klientnyckeln. Men i testsyfte kan du använda samma arbetsstation som den första.<br/><br/>Observera att i instruktionerna som följer den här andra arbetsstationen kallas den Internetanslutna arbetsstationen.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generera och överför din nyckel till Azure Key Vault HSM
Du använder följande fem steg för att generera och överför din nyckel till en Azure Key Vault HSM:

* [Steg 1: Förbered din Internetanslutna arbetsstation](#step-1-prepare-your-internet-connected-workstation)
* [Steg 2: Förbered din frånkopplade arbetsstation](#step-2-prepare-your-disconnected-workstation)
* [Steg 3: Skapa din nyckel](#step-3-generate-your-key)
* [Steg 4: Förbereda din nyckel för överföring](#step-4-prepare-your-key-for-transfer)
* [Steg 5: Överför din nyckel till Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Steg 1: Förbered din Internetanslutna arbetsstation
Gör följande på en arbetsstation som är ansluten till Internet för det här första steget.

### <a name="step-11-install-azure-powershell"></a>Steg 1.1: Installera Azure PowerShell
Hämta och installera Azure PowerShell-modulen som innehåller cmdlets för att hantera Azure Key Vault från den Internetanslutna arbetsstationen. Detta kräver en lägsta version av 0.8.13.

Installationsanvisningar finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Steg 1.2: Hämta ditt Azure prenumerations-ID
Starta en Azure PowerShell-session och logga in på ditt Azure-konto med hjälp av följande kommando:

        Add-AzureAccount
Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Använd sedan den [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) kommando:

        Get-AzureSubscription
Leta upp ID för den prenumeration som du ska använda för Azure Key Vault utdata. Du behöver den här prenumerations-ID senare.

Stäng inte Azure PowerShell-fönster.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Steg 1.3: Hämta BYOK-verktygen för Azure Key Vault
Gå till Microsoft Download Center och [hämta Azure Key Vault BYOK-verktygen](http://www.microsoft.com/download/details.aspx?id=45345) för ett geografiskt område eller instans av Azure. Använd följande information för att identifiera paketnamnet att ladda ned och dess motsvarande hash för SHA-256:

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
**USA: S regering DOD:**

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
**Storbritannien:**

KeyVault-BYOK-verktyg-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

- - -

Om du vill validera integriteten för din hämtade BYOK-verktyg från Azure PowerShell-sessionen använder den [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) cmdlet.

    Get-FileHash KeyVault-BYOK-Tools-*.zip

Denna verktygsuppsättning omfattar följande:

* Ett paket för KeyExchange-nyckel (KEK) som har ett namn som börjar med **BYOK-KEK - pkg-.**
* Ett säkerhetsvärldspaket som har ett namn som börjar med **BYOK-SecurityWorld - pkg-.**
* Python-skriptet **verifykeypackage.py.**
* En kommandorad körbara filen **KeyTransferRemote.exe** och tillhörande DLL-filer.
* Ett Visual C++ Redistributable Package med namnet **vcredist_x64.exe.**

Kopiera paketet till en USB-enhet eller annan bärbar lagringsenhet.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Steg 2: Förbered din frånkopplade arbetsstation
Gör följande på den arbetsstation som inte är ansluten till ett nätverk (Internet eller ditt interna nätverk) för den här andra steget.

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Steg 2.1: Förbered den frånkopplade arbetsstationen med Thales HSM
Installera hjälpprogrammet nCipher (Thales) på en Windows-dator och sedan kopplar en Thales HSM till datorn.

Se till att Thales-verktygen finns i din sökväg (**%nfast_home%\bin**). Skriv till exempel följande:

        set PATH=%PATH%;"%nfast_home%\bin"

Mer information finns i användarhandboken som medföljer Thales HSM.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Steg 2.2: Installera BYOK-verktygen på den frånkopplade arbetsstationen
Kopiera BYOK-verktygspaketet från USB-enhet eller annan bärbar lagringsenhet och gör sedan följande:

1. Extrahera filerna från det Hämta paketet till en annan mapp.
2. Kör vcredist_x64.exe från denna mapp.
3. Följ instruktionerna för att installera Visual C++-körtidskomponenter för Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Steg 3: Skapa din nyckel
Gör följande på den frånkopplade arbetsstationen för den här tredje steget. För att slutföra det här steget måste din HSM vara i läget för initiering. 


### <a name="step-31-change-the-hsm-mode-to-i"></a>Steg 3.1: Ändra HSM-läge till 'I'
Om du använder Thales nShield gränsen, för att ändra läget: 1. Använd knappen läge för att markera det nödvändiga läget. 2. Tryck och håll Rensa-knapp för några sekunder inom några sekunder. Om läget ändras, slutar blinka Indikator för det nya läget och fortsätter att lysa. Indikator för Status kan flash oregelbundet under några sekunder och sedan blinkar regelbundet när enheten är klar. Annars kan enheten fortfarande i det aktuella läget med det aktuella läget Indikator upplysta.

### <a name="step-32-create-a-security-world"></a>Steg 3.2: Skapa en säkerhetsvärld
Starta en kommandotolk och kör Thales nya världen programmet.

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Det här programmet skapar en **Säkerhetsvärld** filen i % NFAST_KMDATA%\local\world, vilket motsvarar mappen C:\ProgramData\nCipher\Key Management Data\local. Du kan använda olika värden för kvorumet men i vårt exempel uppmanas du att ange tre tomma kort och PIN-koder för vart och ett. Därefter ger två valfria kort fullständig åtkomst till säkerhetsvärlden. Dessa kort blir den **Administratörskortsuppsättningen** för den nya säkerhetsvärlden.

Gör något av följande:

* Säkerhetskopiera världfilen. Säkra och skydda världfilen, Administratörskorten och sina PIN-koder och se till att ingen enskild person har tillgång till mer än ett kort.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Steg 3.3: Ändra HSM läget ' o-
Om du använder Thales nShield gränsen, för att ändra läget: 1. Använd knappen läge för att markera det nödvändiga läget. 2. Tryck och håll Rensa-knapp för några sekunder inom några sekunder. Om läget ändras, slutar blinka Indikator för det nya läget och fortsätter att lysa. Indikator för Status kan flash oregelbundet under några sekunder och sedan blinkar regelbundet när enheten är klar. Annars kan enheten fortfarande i det aktuella läget med det aktuella läget Indikator upplysta.


### <a name="step-34-validate-the-downloaded-package"></a>Steg 3.4: Validera det Hämta paketet
Det här steget är valfritt men rekommenderas så att du kan validera följande:

* KeyExchange-nyckeln som ingår i verktygsuppsättningen har genererats från en äkta Thales HSM.
* Hash för Säkerhetsvärld som ingår i verktygsuppsättningen har genererats från en äkta Thales HSM.
* KeyExchange-nyckeln kan inte exporteras.

> [!NOTE]
> Om du vill validera det Hämta paketet HSM måste vara ansluten, påslagen, och måste ha en säkerhetsvärld (som den du nyss skapat).
>
>

Att validera det Hämta paketet:

1. Kör skriptet verifykeypackage.PY genom att skriva ett av följande, beroende på din geografiskt område eller en instans av Azure:

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
   * För [Azure Government](https://azure.microsoft.com/features/gov/), som använder US government instans av Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * För USA: S regering DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * För Kanada:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Tyskland:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * För Indien:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
     > [!TIP]
     > Thales-programvaran innehåller python på %NFAST_HOME%\python\bin
     >
     >
2. Bekräfta att du ser följande, vilket betyder att valideringen har lyckats: **resultat: lyckades**

Det här skriptet validerar undertecknarkedjan upp till Thales rotnyckel. Hashen för den här rotnyckeln är inbäddad i skriptet och dess värde bör vara **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Du kan också bekräfta det här värdet separat genom att besöka den [Thales webbplats](http://www.thalesesec.com/).

Nu är du redo att skapa en ny nyckel.

### <a name="step-35-create-a-new-key"></a>Steg 3.5: Skapa en ny nyckel
Generera en nyckel med hjälp av Thales **generatekey** program.

Kör följande kommando för att generera nyckeln:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Följ dessa instruktioner när du kör det här kommandot:

* Parametern *skydda* måste anges till värdet **modulen**, som visas. Detta skapar en modulskyddad nyckel. BYOK-verktygen har inte stöd för OCS-skyddade nycklar.
* Ersätt värdet för *contosokey* för den **ident** och **plainname** med valfritt strängvärde. Om du vill minimera de administrativa kostnaderna och minska risken för fel, rekommenderar vi att du använder samma värde för båda. Den **ident** värdet måste innehålla endast siffror, bindestreck och gemener.
* Pubexp lämnas tomt (standard) i det här exemplet, men du kan ange specifika värden. Mer information finns i Thales-dokumentationen.

Det här kommandot skapar en fil för Tokeniserad nyckel i mappen %NFAST_KMDATA%\local med ett namn som börjar med **key_simple_**, följt av den **ident** som angavs i kommandot. Till exempel: **key_simple_contosokey**. Den här filen innehåller en krypterad nyckel.

Säkerhetskopiera filen för Tokeniserad nyckel på en säker plats.

> [!IMPORTANT]
> När du senare överför din nyckel till Azure Key Vault kan inte Microsoft exportera den här nyckeln till dig så att det är mycket viktigt att säkerhetskopiera din nyckel och säkerhetsvärld på ett säkert sätt. Kontakta Thales för vägledning och bästa praxis för säkerhetskopiering av din nyckel.
>
>

Du är nu redo att överföra din nyckel till Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Steg 4: Förbereda din nyckel för överföring
Gör följande på den frånkopplade arbetsstationen för det här fjärde steget.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Steg 4.1: Skapa en kopia av din nyckel med minskade behörigheter

Öppna en ny kommandotolk och ändra katalogen till den plats där du unzipped BYOK zip-filen. Kör något av följande, beroende på din geografiskt område eller en instans av Azure för att minska behörigheterna för din nyckel från en kommandotolk:

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
* För [Azure Government](https://azure.microsoft.com/features/gov/), som använder US government instans av Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* För USA: S regering DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* För Kanada:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Tyskland:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* För Indien:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1

När du kör det här kommandot ersätter *contosokey* med samma värde som du angav i **steg 3.5: skapa en ny nyckel** från den [generera din nyckel](#step-3-generate-your-key) steg.

Du uppmanas att ansluta dina säkerhet world admin-kort.

När kommandot har slutförts visas **resultat: lyckades** och kopia av din nyckel med minskade behörigheter finns i filen key_xferacid_<contosokey>.

Du kan undersöker en ACL: er med hjälp av följande kommandon med hjälp av Thales-verktygen:

* aclprint.PY:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  När du kör kommandona, ersätter contosokey med samma värde som du angav i **steg 3.5: skapa en ny nyckel** från den [generera din nyckel](#step-3-generate-your-key) steg.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Steg 4.2: Kryptera nyckeln med hjälp av Microsofts KeyExchange-nyckel
Kör något av följande kommandon, beroende på din geografiskt område eller en instans av Azure:

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
* För [Azure Government](https://azure.microsoft.com/features/gov/), som använder US government instans av Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För USA: S regering DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Kanada:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Tyskland:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Indien:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Följ dessa instruktioner när du kör det här kommandot:

* Ersätt *contosokey* med den identifierare som du använde för att generera nyckeln i **steg 3.5: skapa en ny nyckel** från den [generera din nyckel](#step-3-generate-your-key) steg.
* Ersätt *SubscriptionID* med ID: T för Azure-prenumeration som innehåller nyckelvalvet. Detta värde hämtades tidigare i **steg 1.2: hämta ditt Azure-prenumeration ID** från den [Förbered din Internetanslutna arbetsstation](#step-1-prepare-your-internet-connected-workstation) steg.
* Ersätt *ContosoFirstHSMKey* med en etikett som används för utdata-filnamn.

När detta är klar visas **resultat: lyckades** och det finns en ny fil i den aktuella mappen med följande namn: keytransferpackage*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Steg 4.3: Kopiera ditt nyckelöverföringspaket till den Internetanslutna arbetsstationen
Använd en USB-enhet eller annan bärbar lagringsenhet för att kopiera utdatafilen från föregående steg (KeyTransferPackage ContosoFirstHSMkey.byok) till din Internetanslutna arbetsstation.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Steg 5: Överför din nyckel till Azure Key Vault
För det här sista steget, på den Internetanslutna arbetsstationen använder den [Lägg till AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) för att ladda upp nyckelöverföringspaketet som du kopierade från den frånkopplade arbetsstationen till Azure Key Vault HSM:

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Om överföringen lyckas du se egenskaperna för den nyckel som du just lagt till.

## <a name="next-steps"></a>Nästa steg
Du kan nu använda den här HSM-skyddad nyckel i nyckelvalvet. Mer information finns i **om du vill använda en maskinvarusäkerhetsmodul (HSM)** under den [komma igång med Azure Key Vault](key-vault-get-started.md) kursen.
