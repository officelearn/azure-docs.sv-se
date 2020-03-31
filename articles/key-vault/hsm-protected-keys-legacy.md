---
title: Så här genererar och överför HSM-skyddade nycklar för Azure Key Vault - Azure Key Vault | Microsoft-dokument
description: Använd den här artikeln för att planera för, generera och sedan överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. Även känd som BYOK eller ta med din egen nyckel.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 0d51e1aaf1d9b0472245b9a7f29148517a0a7b93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77429311"
---
# <a name="import-hsm-protected-keys-for-key-vault-legacy"></a>Importera HSM-skyddade nycklar för Key Vault (äldre)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

När du använder Azure Key Vault för extra säkerhet kan du importera eller generera nycklar i maskinvarusäkerhetsmoduler (HSM) som aldrig lämnar HSM-gränsen. Det här scenariot kallas ofta *BYOK* (Bring Your Own Key). Azure Key Vault använder nCipher nShield-familjen av HSM -enheter (FIPS 140-2-nivå 2 validerat) för att skydda dina nycklar.

Använd informationen i det här avsnittet för att planera för, generera och sedan överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault.

Den här funktionen är inte tillgänglig för Azure China.

> [!NOTE]
> Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](key-vault-overview.md)  
> En självstudiekurs för att komma igång, som inkluderar att skapa ett nyckelvalv för HSM-skyddade nycklar, finns i [Vad är Azure Key Vault?](key-vault-overview.md).

Mer information om hur du genererar och överför en HSM-skyddad nyckel via Internet:

* Du genererar nyckeln från en offlinearbetsstation, vilket minskar angreppsytan.
* Nyckeln krypteras med en KEY Exchange-nyckel (KEK), som förblir krypterad tills den överförs till Azure Key Vault HSM-moduler. Endast den krypterade versionen av nyckeln lämnar den ursprungliga arbetsstationen.
* Verktyguppsättningen anger egenskaper för din klientnyckel som binder din nyckel till Azure Key Vault-säkerhetsvärlden. Så efter att Azure Key Vault HSM-modulerna har fått och dekrypterat din nyckel kan endast dessa HSM:er använda den. Det går inte att exportera nyckeln. Den här bindningen tillämpas av nCipher HSM:er.
* Nyckeln Exchange-nyckel (KEK) som används för att kryptera din nyckel genereras i Azure Key Vault HSM-moduler och kan inte exporteras. HSM:erna genomdriver att det inte kan finnas någon klartextversion av KEK utanför HSM:erna. Dessutom innehåller verktygsuppsättningen attestering från nCipher att KEK inte kan exporteras och genererades inuti en äkta HSM som tillverkades av nCipher.
* Verktygsuppsättningen innehåller attestering från nCipher att Azure Key Vault-säkerhetsvärlden också genererades på en äkta HSM som tillverkas av nCipher. Detta intyg bevisar för dig att Microsoft använder äkta maskinvara.
* Microsoft använder separata KEK:er och separata säkerhetsvärldar i varje geografisk region. Den här separationen säkerställer att din nyckel endast kan användas i datacenter i den region där du krypterade den. En nyckel från en europeisk kund kan till exempel inte användas i datacenter i Nordamerika eller Asien.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Mer information om nCipher HSM och Microsoft-tjänster

nCipher Security, ett Entrust Datacard-företag, är ledande på den allmänna HSM-marknaden och stärker världsledande organisationer genom att leverera förtroende, integritet och kontroll till deras affärskritiska information och applikationer. nCiphers kryptografiska lösningar säkrar ny teknik – moln, IoT, blockchain, digitala betalningar – och hjälper till att uppfylla nya efterlevnadsmandat, med samma beprövade teknik som globala organisationer är beroende av idag för att skydda mot hot mot deras känsliga data, nätverkskommunikation och företagsinfrastruktur. nCipher ger förtroende för affärskritiska program, säkerställer dataintegriteten och ger kunderna fullständig kontroll – idag, i morgon, hela tiden.

Microsoft har samarbetat med nCipher Security för att förbättra den senaste tekniken för HSM:er. Med dessa förbättringar kan du få de vanliga fördelarna med värdtjänster utan att behöva lämna ifrån dig kontrollen över dina nycklar. Framför allt medför förbättringarna att Microsoft kan hantera HSM:erna så att du inte behöver göra det. Som en molntjänst skalas Azure Key Vault upp med kort varsel för att uppfylla organisationens användningstoppar. Samtidigt skyddas din nyckel i Microsofts HSM:er. Du behåller dock kontrollen över nyckelns livscykel eftersom du genererar nyckeln och överför den till Microsofts HSM:er.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementera BYOK (Bring Your Own Key) för Azure Key Vault

Använd följande information och procedurer om du ska generera din egen HSM-skyddad nyckel och sedan överföra den till Azure Key Vault – scenariot bring your own key (BYOK).

## <a name="prerequisites-for-byok"></a>Krav för BYOK

Se följande tabell för en lista över förutsättningar för att ta med din egen nyckel (BYOK) för Azure Key Vault.

| Krav | Mer information |
| --- | --- |
| En prenumeration på Azure |Om du vill skapa ett Azure Key Vault behöver du en Azure-prenumeration: [Registrera dig för kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/) |
| Azure Key Vault Premium-tjänstnivån för att stödja HSM-skyddade nycklar |Mer information om tjänstnivåer och funktioner för Azure Key Vault finns på webbplatsen [för prissättning av Azure Key Vault.](https://azure.microsoft.com/pricing/details/key-vault/) |
| nCipher nShield HSMs, smartcards och supportprogram |Du måste ha tillgång till en nCipher Hardware Security Module och grundläggande operativ kunskap om nCipher nShield HSMs. Se [nCipher nShield Hardware Security Module](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) för listan över kompatibla modeller, eller för att köpa en HSM om du inte har någon. |
| Följande maskin- och programvara:<ol><li>En offline x64-arbetsstation med ett minsta Windows-operativsystem för Windows 7 och nCipher nShield-programvara som är minst version 11.50.<br/><br/>Om den här arbetsstationen kör Windows 7 måste du [installera Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>En arbetsstation som är ansluten till Internet och har ett minsta Windows-operativsystem med Windows 7 och [Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) **minimum version 1.1.0** installerat.</li><li>En USB-enhet eller annan bärbar lagringsenhet som har minst 16 MB ledigt utrymme.</li></ol> |Av säkerhetsskäl rekommenderar vi att den första arbetsstationen inte är ansluten till ett nätverk. Denna rekommendation tillämpas dock inte programmässigt.<br/><br/>I instruktionerna som följer kallas den här arbetsstationen den frånkopplade arbetsstationen.</p></blockquote><br/>Om klientnyckeln är för ett produktionsnätverk rekommenderar vi dessutom att du använder en andra, separat arbetsstation för att hämta verktygsuppsättningen och överföra klientnyckeln. För testsyften kan du emellertid använda samma arbetsstation som den första.<br/><br/>I instruktionerna som följer kallas den här andra arbetsstationen för den Internetanslutna arbetsstationen.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generera och överföra din nyckel till Azure Key Vault HSM

Du kommer att använda följande fem steg för att generera och överföra din nyckel till en Azure Key Vault HSM:

* [Steg 1: Förbered din Internetanslutna arbetsstation](#step-1-prepare-your-internet-connected-workstation)
* [Steg 2: Förbered din frånkopplade arbetsstation](#step-2-prepare-your-disconnected-workstation)
* [Steg 3: Generera din nyckel](#step-3-generate-your-key)
* [Steg 4: Förbered din nyckel för överföring](#step-4-prepare-your-key-for-transfer)
* [Steg 5: Överför nyckeln till Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Steg 1: Förbered din Internetanslutna arbetsstation

Gör följande procedurer på arbetsstationen som är ansluten till Internet för det här första steget.

### <a name="step-11-install-azure-powershell"></a>Steg 1.1: Installera Azure PowerShell

Hämta och installera Azure PowerShell-modulen som innehåller cmdlets för att hantera Azure Key Vault från den Internetanslutna arbetsstationen. Installationsinstruktioner finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Steg 1.2: Hämta ditt Azure-prenumerations-ID

Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:

```Powershell
   Connect-AzAccount
```
Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Använd sedan kommandot [Get-AzSubscription:](/powershell/module/az.accounts/get-azsubscription)

```powershell
   Get-AzSubscription
```
Leta reda på ID:et för prenumerationen som du ska använda för Azure Key Vault från utdata. Du behöver det här prenumerations-ID:t senare.

Stäng inte Azure PowerShell-fönstret.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Steg 1.3: Hämta BYOK-verktygsuppsättningen för Azure Key Vault

Gå till Microsoft Download Center och [hämta Byok-verktygsuppsättningen För Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45345) för din geografiska region eller instans av Azure. Använd följande information för att identifiera paketnamnet att ladda ner och motsvarande SHA-256-pakethh:

---
**USA:**

KeyVault-BYOK-Verktyg-USA.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Europa:**

KeyVault-BYOK-Verktyg-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Asien:**

KeyVault-BYOK-Verktyg-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Latinamerika:**

KeyVault-BYOK-Verktyg-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F2538922DD1B01A4FACB619

---
**Japan:**

KeyVault-BYOK-Verktyg-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Verktyg-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA053444ED136F

---
**Sydafrika:**

KeyVault-BYOK-Verktyg-SouthAfrica.zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**Uae:**

KeyVault-BYOK-Verktyg-UAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Australien:**

KeyVault-BYOK-Verktyg-Australien.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure-regeringen:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Verktyg-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**AMERIKANSKA regeringen DOD:**

KeyVault-BYOK-Verktyg-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Kanada:**

KeyVault-BYOK-Verktyg-Kanada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Tyskland:**

KeyVault-BYOK-Verktyg-Tyskland.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Tyskland Public:**

KeyVault-BYOK-Verktyg-Tyskland-Public.zip

54534936D0A0C99C8117DB724C34A50FD204CFCBD75C78972B785865364A29

---
**Indien:**

KeyVault-BYOK-Verktyg-Indien.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Frankrike:**

KeyVault-BYOK-Verktyg-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Storbritannien:**

KeyVault-BYOK-Verktyg-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Schweiz:**

KeyVault-BYOK-Verktyg-Schweiz.zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


Om du vill verifiera integriteten för din nedladdade BYOK-verktygsuppsättning använder du cmdleten [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) från din Azure PowerShell-session.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Verktygsuppsättningen innehåller:

* Ett KEK-paket (Key Exchange Key) som har ett namn som börjar med **BYOK-KEK-pkg-.**
* Ett Security World-paket som har ett namn som börjar med **BYOK-SecurityWorld-pkg-.**
* Ett pythonskript som heter **verifykeypackage.py.**
* En körbar kommandoradsfil med namnet **KeyTransferRemote.exe** och tillhörande DLL-filer.
* Ett visuellt C++ redistributable-paket med namnet **vcredist_x64.exe.**

Kopiera paketet till en USB-enhet eller annan bärbar lagringsenhet.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Steg 2: Förbered din frånkopplade arbetsstation

I det andra steget gör du följande procedurer på arbetsstationen som inte är ansluten till ett nätverk (antingen Internet eller det interna nätverket).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Steg 2.1: Förbered den frånkopplade arbetsstationen med nCipher nShield HSM

Installera nCipher-supportprogrammet på en Windows-dator och bifoga sedan en nCipher nShield HSM till den datorn.

Kontrollera att nCipher-verktygen finns i sökvägen (**%nfast_home%\bin**). Ange till exempel följande:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Mer information finns i användarhandboken som medföljer nShield HSM.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Steg 2.2: Installera BYOK-verktygsuppsättningen på den frånkopplade arbetsstationen

Kopiera BYOK-verktygspaketet från USB-enheten eller en annan bärbar lagringsenhet och gör sedan följande:

1. Extrahera filerna från det hämtade paketet till valfri mapp.
2. Kör vcredist_x64.exe från denna mapp.
3. Följ instruktionerna för att installera Visual C++-körningskomponenterna för Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Steg 3: Generera din nyckel

För det här tredje steget gör du följande procedurer på den frånkopplade arbetsstationen. För att slutföra det här steget måste din HSM vara i initieringsläge. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Steg 3.1: Ändra HSM-läget till "I"

Om du använder nCipher nShield Edge, för att ändra läge: 1. Använd knappen Läge för att markera önskat läge. 2. Inom några sekunder håller du ned knappen Rensa i några sekunder. Om läget ändras slutar det nya lägets lysdiod att blinka och förblir tänd. Statuslampan kan blinka oregelbundet i några sekunder och blinkar sedan regelbundet när enheten är klar. Annars förblir enheten i aktuellt läge, med lämplig lysdiod i läge lyser.

### <a name="step-32-create-a-security-world"></a>Steg 3.2: Skapa en säkerhetsvärld

Starta en kommandotolk och kör nCipher nya världen programmet.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Det här programmet skapar en **säkerhetsvärldsfil** i %NFAST_KMDATA%\local\world, vilket motsvarar mappen C:\ProgramData\nCipher\Key Management Data\local. Du kan använda olika värden för kvorumet men i vårt exempel uppmanas du att ange tre tomma kort och PIN-koder för vart och ett. Sedan ger två kort full tillgång till säkerhetsvärlden. Dessa kort blir **administratörskortsuppsättningen** för den nya säkerhetsvärlden.

> [!NOTE]
> Om din HSM inte stöder den nyare cypher-sviten DLf3072s256mRijndael kan du ersätta --chiffer-suite= DLf3072s256mRijndael med --chiffer-suite=DLf1024s160mRijndael
> 
> Säkerhet världen skapas med new-world.exe som levereras med nCipher programvara version 12.50 är inte kompatibel med denna BYOK förfarande. Det finns två alternativ tillgängliga:
> 1) Nedgradera nCipher-programvaruversionen till 12.40.2 för att skapa en ny säkerhetsvärld.
> 2) Kontakta nCipher-supporten och be dem att tillhandahålla en snabbkorrigering för 12,50-programversion, som gör att du kan använda 12.40.2-versionen av new-world.exe som är kompatibel med den här BYOK-proceduren.

Gör något av följande:

* Säkerhetskopiera världfilen. Säkra och skydda världfilen, administratörskorten och tillhörande PIN-koder och se till att ingen enskild person får åtkomst till mer än ett kort.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Steg 3.3: Ändra HSM-läget till "O"

Om du använder nCipher nShield Edge, för att ändra läge: 1. Använd knappen Läge för att markera önskat läge. 2. Inom några sekunder håller du ned knappen Rensa i några sekunder. Om läget ändras slutar det nya lägets lysdiod att blinka och förblir tänd. Statuslampan kan blinka oregelbundet i några sekunder och blinkar sedan regelbundet när enheten är klar. Annars förblir enheten i aktuellt läge, med lämplig lysdiod i läge lyser.

### <a name="step-34-validate-the-downloaded-package"></a>Steg 3.4: Validera det nedladdade paketet

Det här steget är valfritt men rekommenderas så att du kan validera följande:

* Key Exchange Key som ingår i verktygsuppsättningen har genererats från en äkta nCipher nShield HSM.
* Hash av Security World som ingår i verktygsuppsättningen har genererats i en äkta nCipher nShield HSM.
* KeyExchange-nyckeln kan inte exporteras.

> [!NOTE]
> För att validera det hämtade paketet måste HSM:en vara ansluten, påslagen, och ha en säkerhetsvärld (som den du nyss skapat).

Så här validerar du det nedladdade paketet:

1. Kör verifykeypackage.py skriptet genom att skriva något av följande, beroende på din geografiska region eller instans av Azure:

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
   * För Sydafrika:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * För UAE:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * För Australien:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * För [Azure Government](https://azure.microsoft.com/features/gov/), som använder den amerikanska myndighetsinstansen av Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * För amerikanska regeringen DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * För Kanada:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * För Tyskland:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * För Tyskland Public:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * För Indien:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * För Frankrike:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * För Storbritannien:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
   * För Schweiz:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1

     > [!TIP]
     > NCipher nShield-programvaran innehåller python på %NFAST_HOME%\python\bin
     >
     >
2. Bekräfta att du ser följande, vilket indikerar lyckad validering: **Resultat: FRAMGÅNG**

Det här skriptet validerar undertecknarkedjan upp till nShield-rotnyckeln. Hashen för den här rotnyckeln är inbäddad i skriptet och dess värde bör vara **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Du kan också bekräfta detta värde separat genom att besöka [nCipher webbplats](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation).

Du är nu redo att skapa en ny nyckel.

### <a name="step-35-create-a-new-key"></a>Steg 3.5: Skapa en ny nyckel

Generera en nyckel med hjälp av **generatekey-programmet** nCipher nShield.

Kör följande kommando för att generera nyckeln:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Använd följande anvisningar när du kör det här kommandot:

* Parametern *protect* måste anges till värdet **module**, så som det visas. Detta skapar en modulskyddad nyckel. BYOK-verktygen har inte stöd för OCS-skyddade nycklar.
* Ersätt värdet för *contosokey* för **ident** och **plainname** med valfritt strängvärde. För att minimera administrativa omkostnader och minska risken för fel rekommenderar vi att du använder samma värde för båda. Det **identalet** får bara innehålla siffror, streck och gemener.
* Pubexp lämnas tomt (standard) i det här exemplet, men du kan ange specifika värden. Mer information finns i [nCipher-dokumentationen.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

Med det här kommandot skapas en tokeniserad nyckelfil i mappen %NFAST_KMDATA%\lokal med ett namn som börjar med **key_simple_**, följt av den **ident** som angavs i kommandot. Till exempel: **key_simple_contosokey**. Den här filen innehåller en krypterad nyckel.

Säkerhetskopiera filen för tokeniserad nyckel till en säker plats.

> [!IMPORTANT]
> När du senare överför nyckeln till Azure Key Vault kan Microsoft inte exportera den här nyckeln tillbaka till dig så det blir oerhört viktigt att du säkerhetskopierar din nyckel- och säkerhetsvärld på ett säkert sätt. Kontakta [nCipher](https://www.ncipher.com/about-us/contact-us) för vägledning och metodtips för säkerhetskopiering av nyckeln.
>


Du är nu redo att överföra din nyckel till Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Steg 4: Förbered din nyckel för överföring

För det här fjärde steget gör du följande procedurer på den frånkopplade arbetsstationen.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Steg 4.1: Skapa en kopia av din nyckel med nedsatt behörighet

Öppna en ny kommandotolk och ändra den aktuella katalogen till den plats där du packade upp ZIP-filen BYOK. Om du vill minska behörigheterna för din nyckel kör du något av följande från en kommandotolk, beroende på ditt geografiska område eller din instans av Azure:

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
* För Sydafrika:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* För UAE:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* För Australien:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* För [Azure Government](https://azure.microsoft.com/features/gov/), som använder den amerikanska myndighetsinstansen av Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* För amerikanska regeringen DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* För Kanada:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* För Tyskland:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* För Tyskland Public:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* För Indien:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* För Frankrike:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* För Storbritannien:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* För Schweiz:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

När du kör det här kommandot ersätter du *contosokey* med samma värde som du angav i **steg 3.5: Skapa en ny nyckel** från steget Generera [nyckeln.](#step-3-generate-your-key)

Du ombeds att koppla in din säkerhet världen admin kort.

När kommandot är klart visas **Resultat: FRAMGÅNG** och kopian av nyckeln med reducerade behörigheter\<finns i filen med namnet key_xferacId_ contosokey>.

Du kan inspektera ACLS med hjälp av följande kommandon med hjälp av nCipher nShield verktyg:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  När du kör dessa kommandon ersätter du contosokey med samma värde som du angav i **steg 3.5: Skapa en ny nyckel** från steget Generera [nyckeln.](#step-3-generate-your-key)

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Steg 4.2: Kryptera din nyckel med hjälp av Microsofts nyckel för nyckelutbyte

Kör något av följande kommandon, beroende på din geografiska region eller instans av Azure:

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
* För Sydafrika:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För UAE:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Australien:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För [Azure Government](https://azure.microsoft.com/features/gov/), som använder den amerikanska myndighetsinstansen av Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För amerikanska regeringen DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Kanada:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Tyskland:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Tyskland Public:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Indien:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Frankrike:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Storbritannien:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* För Schweiz:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Använd följande anvisningar när du kör det här kommandot:

* Ersätt *contosokey* med identifieraren som du använde för att generera nyckeln i **steg 3.5: Skapa en ny nyckel** från steget Generera [nyckeln.](#step-3-generate-your-key)
* Ersätt *SubscriptionID* med ID:et för Azure-prenumerationen som innehåller ditt nyckelvalv. Du har hämtat det här värdet tidigare i **steg 1.2: Hämta ditt Azure-prenumerations-ID** från steget [Förbered din Internet-anslutna arbetsstation.](#step-1-prepare-your-internet-connected-workstation)
* Ersätt *ContosoFirstHSMKey* med en etikett som används för ditt utdatafilnamn.

När detta är klart visas **Resultat: FRAMGÅNG** och det finns en ny fil i den aktuella mappen som har följande namn: KeyTransferPackage-*ContosoFirsTHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Steg 4.3: Kopiera ditt nyckelöverföringspaket till den Internetanslutna arbetsstationen

Använd en USB-enhet eller annan bärbar lagring för att kopiera utdatafilen från föregående steg (KeyTransferPackage-ContosoFirstHSMkey.byok) till din Internetanslutna arbetsstation.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Steg 5: Överför nyckeln till Azure Key Vault

I det här sista steget använder du cmdleten [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) för att ladda upp nyckelöverföringspaketet som du kopierade från den frånkopplade arbetsstationen till Azure Key Vault HSM:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Om överföringen lyckas visas egenskaperna för nyckeln som du just har lagt till.

## <a name="next-steps"></a>Nästa steg

Du kan nu använda den här HSM-skyddade nyckeln i nyckelvalvet. Mer information finns i den här pris- och [funktionsjämna jämförelsen](https://azure.microsoft.com/pricing/details/key-vault/).
