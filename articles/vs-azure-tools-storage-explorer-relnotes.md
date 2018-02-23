---
title: "Viktig information för Microsoft Azure Lagringsutforskaren (förhandsversion)"
description: "Viktig information för Microsoft Azure Lagringsutforskaren (förhandsversion)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: cawa
ms.openlocfilehash: d23ddfb881695b2310d379a9112e6ab8305c0cce
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="microsoft-azure-storage-explorer-preview-release-notes"></a>Viktig information för Microsoft Azure Lagringsutforskaren (förhandsversion)

Den här artikeln innehåller versionen viktig information för Azure Lagringsutforskaren 0.9.5 (förhandsgranskning) samt viktig information för tidigare versioner.

[Microsoft Azure Lagringsutforskaren (förhandsversion)](./vs-azure-tools-storage-manage-with-storage-explorer.md) är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, macOS och Linux.

## <a name="version-095"></a>Version 0.9.5
02/06/2018

### <a name="download-azure-storage-explorer-095-preview"></a>Hämta Azure Lagringsutforskaren 0.9.5 (förhandsgranskning)
- [Azure Lagringsutforskaren 0.9.5 (förhandsversion) för Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Lagringsutforskaren (förhandsversion) för 0.9.5 för Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Lagringsutforskaren (förhandsversion) för 0.9.5 för Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Ny

* Stöd för filresurser ögonblicksbilder:
    * Skapa och hantera ögonblicksbilder för dina resurser.
    * Växla mellan ögonblicksbilder av din filresurser du undersöker.
    * Återställ tidigare versioner av dina filer.
* Förhandsgranska stöd för Azure Data Lake Store:
    * Ansluta till ADLS-resurser över flera konton.
    * Ansluta till och dela ADLS-resurser genom att använda ADL URI: er.
    * Utföra grundläggande filmapp operations rekursivt.
    * Fäst enskilda mappar för Snabbåtkomst.
    * Visa statistik över mappen.

### <a name="fixes"></a>Korrigeringar
* Prestandaförbättringar för start.
* Olika felkorrigeringar.

### <a name="known-issues"></a>Kända problem
* Lagringsutforskaren stöder inte ADFS-konton.
* När måldatorn Azure Stack misslyckas överföring av filer tilläggsblobar.
* När du klickar på ”Avbryt” för en uppgift, kan det ta ett tag att avbryta aktiviteten. Det beror på att vi använder Avbryt filter lösningen som beskrivs här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Panelen konto inställningar kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer.
* Byta namn på blobbar (individuellt eller i en bytt namn till blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras vid ett byte av namn.
* Även om Azure-stacken inte stöder filresurser, visas en filresurser nod fortfarande under ett bifogade Stack för Azure storage-konto.
* Electron-gränssnittet som används av Lagringsutforskaren har problem med vissa maskinvaruacceleration GPU (graphics bearbetning unit). Om Lagringsutforskaren visas en tom (tom) huvudfönstret, kan du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växel:

```
./StorageExplorer.exe --disable-gpu
```

* Du behöver kontrollera GCC är uppdaterad – kan du göra det genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu 14.04:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – kan du göra det genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094--093"></a>Version 0.9.4 / 0.9.3
01/21/2018

### <a name="download-azure-storage-explorer-094-preview"></a>Hämta Azure Lagringsutforskaren 0.9.4 (förhandsgranskning)
* [Hämta Azure Lagringsutforskaren 0.9.4 (förhandsversion) för Windows](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Hämta Azure Lagringsutforskaren (förhandsversion) för 0.9.4 för Mac](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Hämta Azure Lagringsutforskaren (förhandsversion) för 0.9.4 för Linux](https://go.microsoft.com/fwlink/?LinkId=809308)

### <a name="new"></a>Ny
* Din befintliga lagring Utforskaren ska återanvändas när:
    * Öppna Direktlänkar genereras i Lagringsutforskaren.
    * Öppna Lagringsutforskaren från portalen.
    * Öppna Lagringsutforskaren från Azure Storage VS anknytning (kommer snart).
* Utökad möjlighet att öppna ett nytt Lagringsutforskaren-fönster från Lagringsutforskaren.
    * Det finns ett 'nytt fönster och' alternativ under Arkiv-menyn och på snabbmenyn i Aktivitetsfältet i Windows.
    * För Mac finns det ett 'nytt fönster och' alternativet App-menyn.

### <a name="fixes"></a>Korrigeringar
* Fast ett säkerhetsproblem. Uppgradera till 0.9.4 när tidigaste dig.
* Gamla aktiviteter har inte korrekt rensas. Detta påverkade prestanda för tidskrävande jobb. De är nu rensas på rätt sätt.
* Åtgärder som inbegriper stort antal filer och kataloger kan ibland orsaka Lagringsutforskaren att låsa. Begäranden till Azure för filresurser är nu trottled att begränsa system Resursanvändning.

### <a name="known-issues"></a>Kända problem
* Lagringsutforskaren stöder inte ADFS-konton.
* Kortkommandon för ”Visa Explorer” och ”visa kontohantering” ska vara Ctrl / Cmd + SKIFT + E och Ctrl / Cmd + SKIFT + A respektive.
* När måldatorn Azure Stack misslyckas överföring av filer tilläggsblobar.
* När du klickar på ”Avbryt” för en uppgift, kan det ta ett tag att avbryta aktiviteten. Det beror på att vi använder Avbryt filter lösningen som beskrivs här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Panelen konto inställningar kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer.
* Byta namn på blobbar (individuellt eller i en bytt namn till blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras vid ett byte av namn.
* Även om Azure-stacken inte stöder filresurser, visas en filresurser nod fortfarande under ett bifogade Stack för Azure storage-konto.
* Electron-gränssnittet som används av Lagringsutforskaren har problem med vissa maskinvaruacceleration GPU (graphics bearbetning unit). Om Lagringsutforskaren visas en tom (tom) huvudfönstret, kan du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växel:
```
./StorageExplorer --disable-gpu
```
* Du behöver kontrollera GCC är uppdaterad – kan du göra det genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu 14.04:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – kan du göra det genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="previous-releases"></a>Tidigare versioner

* [Version 0.9.2](#version-092)
* [Version 0.9.1 / 0.9.0](#version-091)
* [Version 0.8.16](#version-0816)
* [Version 0.8.14](#version-0814)
* [Version 0.8.13](#version-0813)
* [Version 0.8.12 / 0.8.11 / 0.8.10](#version-0812--0811--0810)
* [Version 0.8.9 / 0.8.8](#version-089--088)
* [Version 0.8.7](#version-087)
* [Version 0.8.6](#version-086)
* [Version 0.8.5](#version-085)
* [Version 0.8.4](#version-084)
* [Version 0.8.3](#version-083)
* [Version 0.8.2](#version-082)
* [Version 0.8.0](#version-080)
* [Version 0.7.20160509.0](#version-07201605090)
* [Version 0.7.20160325.0](#version-07201603250)
* [Version 0.7.20160129.1](#version-07201601291)
* [Version 0.7.20160105.0](#version-07201601050)
* [Version 0.7.20151116.0](#version-07201511160)

## <a name="version-092"></a>Version 0.9.2
11/01/2017

### <a name="hotfixes"></a>Snabbkorrigeringar
* Oväntade data har möjligt när du redigerar Edm.DateTime värden för tabellentiteter beroende på den lokala tidszonen. Nu används en oformaterad textruta med exakt och konsekvent kontroll över Edm.DateTime värden.
* Överför/hämta en uppsättning blobbar när kopplade till namn och nyckel startar inte. Problemet har åtgärdats.
* Tidigare Lagringsutforskaren endast ombeds du att autentiseras en inaktuella konto om en eller flera av kontots prenumerationer har valts. Nu uppmanas Lagringsutforskaren du även om kontot fullständigt har filtrerats ut.
* Domänen slutpunkter för Azure som tillhör amerikanska myndigheter var fel. Den har korrigerats.
* Verkställ-knappen på panelen hantera konton har ibland svårt att klicka på. Detta ska inte längre inträffa.

### <a name="new"></a>Ny
* Förhandsgranska stöd för Azure Cosmos DB:
    * [Onlinedokumentation](./cosmos-db/storage-explorer.md)
    * Skapa databaser och samlingar
    * Ändra data
    * Fråga, skapa eller ta bort dokument
    * Uppdatera lagrade procedurer, användardefinierade funktioner eller utlösare
    * Använd anslutningssträngar för att ansluta till och hantera dina databaser
* Bättre prestanda för att ladda upp/hämta många små blobbar.
* Lägga till en ”försök alla”-åtgärd om det finns fel i en blob överför eller blob download gruppen.
* Lagringsutforskaren pausar nu iteration under blob överför/hämta om nätverksanslutningen har brutits. Du kan sedan återuppta iteration när nätverksanslutningen har återupprättats.
* Lägga till möjligheten att ”Stäng alla”, ”Stäng andra” och ”Stäng” flikar via snabbmenyn.
* Lagringsutforskaren använder nu inbyggd dialogrutor och inbyggda snabbmenyer.
* Lagringsutforskaren är nu mer tillgängligt. Förbättringarna innefattar:
    * Förbättrat stöd för skärmläsare, för NVDA i Windows och VoiceOver på Mac
    * Förbättrad hög kontrast teman
    * Tabbar och tangentbordet tangentbordsfokus åtgärdas

### <a name="fixes"></a>Korrigeringar
* Om du försöker öppna eller hämta en blob med ett ogiltigt namn på Windows-filen misslyckas åtgärden. Lagringsutforskaren nu identifierar om en blobbnamnet är ogiltigt och be om du vill koda den eller hoppa över blob. Lagringsutforskaren identifierar även om ett filnamn verkar vara kodad och be dig om att avkoda innan du laddar upp.
* Under blob filöverföringen redigerare mål blob-behållaren skulle ibland inte korrekt att uppdatera. Problemet har åtgärdats.
* Stöd för flera typer av anslutningssträngar och SAS-URI: er regressed. Vi har löst alla kända problem men skicka feedback om du får ytterligare problem.
* Meddelanden om uppdateringar bröts för vissa användare i 0.9.0. Det här problemet har åtgärdats och dem som berörs av felet, du manuellt kan hämta den senaste versionen av Lagringsutforskaren [här](https://azure.microsoft.com/en-us/features/storage-explorer/).

### <a name="known-issues"></a>Kända problem
* Lagringsutforskaren stöder inte ADFS-konton.
* Kortkommandon för ”Visa Explorer” och ”visa kontohantering” ska vara Ctrl / Cmd + SKIFT + E och Ctrl / Cmd + SKIFT + A respektive.
* När måldatorn Azure Stack misslyckas överföring av filer tilläggsblobar.
* När du klickar på ”Avbryt” för en uppgift, kan det ta ett tag att avbryta aktiviteten. Det beror på att vi använder Avbryt filter lösningen som beskrivs här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Panelen konto inställningar kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer.
* Byta namn på blobbar (individuellt eller i en bytt namn till blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras vid ett byte av namn.
* Även om Azure-stacken inte stöder filresurser, visas en filresurser nod fortfarande under ett bifogade Stack för Azure storage-konto.
* Electron-gränssnittet som används av Lagringsutforskaren har problem med vissa maskinvaruacceleration GPU (graphics bearbetning unit). Om Lagringsutforskaren visas en tom (tom) huvudfönstret, kan du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växel:
```
./StorageExplorer --disable-gpu
```
* Du behöver kontrollera GCC är uppdaterad – kan du göra det genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu 14.04:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – kan du göra det genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091--090-preview"></a>Version 0.9.1 / 0.9.0 (förhandsgranskning)
10/20/2017
### <a name="new"></a>Ny
* Förhandsgranska stöd för Azure Cosmos DB:
    * [Onlinedokumentation](./cosmos-db/storage-explorer.md)
    * Skapa databaser och samlingar
    * Ändra data
    * Fråga, skapa eller ta bort dokument
    * Uppdatera lagrade procedurer, användardefinierade funktioner eller utlösare
    * Använd anslutningssträngar för att ansluta till och hantera dina databaser
* Bättre prestanda för att ladda upp/hämta många små blobbar.
* Lägga till en ”försök alla”-åtgärd om det finns fel i en blob överför eller blob download gruppen.
* Lagringsutforskaren pausar nu iteration under blob överför/hämta om nätverksanslutningen har brutits. Du kan sedan återuppta iteration när nätverksanslutningen har återupprättats.
* Lägga till möjligheten att ”Stäng alla”, ”Stäng andra” och ”Stäng” flikar via snabbmenyn.
* Lagringsutforskaren använder nu inbyggd dialogrutor och inbyggda snabbmenyer.
* Lagringsutforskaren är nu mer tillgängligt. Förbättringarna innefattar:
    * Förbättrat stöd för skärmläsare, för NVDA i Windows och VoiceOver på Mac
    * Förbättrad hög kontrast teman
    * Tabbar och tangentbordet tangentbordsfokus åtgärdas

### <a name="fixes"></a>Korrigeringar
* Om du försöker öppna eller hämta en blob med ett ogiltigt namn på Windows-filen misslyckas åtgärden. Lagringsutforskaren nu identifierar om en blobbnamnet är ogiltigt och be om du vill koda den eller hoppa över blob. Lagringsutforskaren identifierar även om ett filnamn verkar vara kodad och be dig om att avkoda innan du laddar upp.
* Under blob filöverföringen redigerare mål blob-behållaren skulle ibland inte korrekt att uppdatera. Problemet har åtgärdats.
* Stöd för flera typer av anslutningssträngar och SAS-URI: er regressed. Vi har löst alla kända problem men skicka feedback om du får ytterligare problem.
* Meddelanden om uppdateringar bröts för vissa användare i 0.9.0. Det här problemet har åtgärdats och dem som berörs av felet, du manuellt kan hämta den senaste versionen av Lagringsutforskaren [här](https://azure.microsoft.com/en-us/features/storage-explorer/)

### <a name="known-issues"></a>Kända problem
* Lagringsutforskaren stöder inte ADFS-konton.
* Kortkommandon för ”Visa Explorer” och ”visa kontohantering” ska vara Ctrl / Cmd + SKIFT + E och Ctrl / Cmd + SKIFT + A respektive.
* När måldatorn Azure Stack misslyckas överföring av filer tilläggsblobar.
* När du klickar på ”Avbryt” för en uppgift, kan det ta ett tag att avbryta aktiviteten. Det beror på att vi använder Avbryt filter lösningen som beskrivs här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Panelen konto inställningar kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer.
* Byta namn på blobbar (individuellt eller i en bytt namn till blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras vid ett byte av namn.
* Även om Azure-stacken inte stöder filresurser, visas en filresurser nod fortfarande under ett bifogade Stack för Azure storage-konto.
* Electron-gränssnittet som används av Lagringsutforskaren har problem med vissa maskinvaruacceleration GPU (graphics bearbetning unit). Om Lagringsutforskaren visas en tom (tom) huvudfönstret, kan du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växel:
```
./StorageExplorer --disable-gpu
```
* Du behöver kontrollera GCC är uppdaterad – kan du göra det genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu 14.04:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – kan du göra det genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Version 0.8.16
8/21/2017

### <a name="new"></a>Ny
* När du öppnar en blob uppmanas Lagringsutforskaren du att ladda upp den hämta filen om en ändring identifieras
* Förbättrad Azure Stack inloggning
* Bättre prestanda för att ladda upp/hämta många små filer på samma gång


### <a name="fixes"></a>Korrigeringar
* För vissa blob-typer leder om du väljer att ”replace” under en överför hamnar i konflikt ibland överföringen startas.
* I version 0.8.15 skulle överföringar ibland av stopp vid 99%.
* När överföringen av filer till en filresurs, om du vill överföra till en katalog som inte ännu finns, misslyckas överföra.
* Lagringsutforskaren felaktigt generera tidsstämplar för signaturer för delad åtkomst och tabellen.


### <a name="known-issues"></a>Kända problem
* Med hjälp av ett namn och nyckel anslutningssträngen fungerar inte för närvarande. Den kommer att åtgärdas i nästa version. Fram till dess kan du använda bifoga med namn och nyckel.
* Om du försöker öppna en fil med ett ogiltigt Windows-filnamn resulterar nedladdningen i en fil hittades inte.
* När du klickar på ”Avbryt” för en uppgift, kan det ta ett tag att avbryta aktiviteten. Detta är en begränsning i biblioteket för Azure Storage-nod.
* Fliken som initierade överföringen uppdateras när du har slutfört en blob-överföring. Detta är en förändring från tidigare beteende och medför även du tas tillbaka till roten i behållaren.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Panelen konto inställningar kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer.
* Byta namn på blobbar (individuellt eller i en bytt namn till blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras vid ett byte av namn.
* Även om Azure-stacken inte stöder filresurser, visas en filresurser nod fortfarande under ett bifogade Stack för Azure storage-konto.
* Du behöver kontrollera GCC är uppdaterad – kan du göra det genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu 14.04:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – kan du göra det genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Version 0.8.14
06/22/2017

### <a name="new"></a>Ny

* Uppdaterade Electron versionen till 1.7.2 för att kunna dra nytta av flera viktiga säkerhetsuppdateringar
* Du kan nu snabbt komma åt online felsökningsguiden från Hjälp-menyn
* Lagringsutforskaren felsökning [Guide][2]
* [Instruktioner] [ 3] om hur du ansluter till en Azure-Stack-prenumeration

### <a name="known-issues"></a>Kända problem

* Knapparna i bekräftelsedialogrutan ta bort mappen registrera inte med musklickningar på Linux. Lösning är att använda på RETUR
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer beslutet
* Med mer än 3 grupper av blobbar eller filer som överför samtidigt kan orsaka fel
* Panelen konto inställningar kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer
* Byta namn på blobbar (individuellt eller i en bytt namn till blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras vid ett byte av namn.
* Även om Azure-stacken inte stöder filresurser, visas en filresurser nod fortfarande under ett bifogade Stack för Azure storage-konto.
* Ubuntu 14.04 installera måste gcc version uppdateras eller uppgraderas – steg för att uppgradera nedan:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Version 0.8.13
05/12/2017

#### <a name="new"></a>Ny

* Lagringsutforskaren felsökning [Guide][2]
* [Instruktioner] [ 3] om hur du ansluter till en Azure-Stack-prenumeration

#### <a name="fixes"></a>Korrigeringar

* Fast: Ladda upp filen hunnit hög orsaka en out-of-minnesfel
* Fast: Du kan nu logga in med PIN-kod/smartkort
* Fast: Öppna i portalen nu fungerar med Azure Kina, Tyskland Azure, Azure som tillhör amerikanska myndigheter och Azure-stacken
* Fast: Går inte att överföra en mapp till en blobbbehållare, ”otillåten åtgärd” skulle ibland uppstå fel
* Fast: Markera alla inaktiverades vid hantering av ögonblicksbilder
* Fast: Metadata för grundläggande blob kan komma att skrivas över när du visar egenskaperna för dess ögonblicksbilder

#### <a name="known-issues"></a>Kända problem

* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer beslutet
* När du har zoomat in eller ut kan zoomnivån tillfälligt återställas till Standardnivå
* Med mer än 3 grupper av blobbar eller filer som överför samtidigt kan orsaka fel
* Panelen konto inställningar kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer
* Byta namn på blobbar (individuellt eller i en bytt namn till blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras vid ett byte av namn.
* Även om Azure-stacken inte stöder filresurser, visas en filresurser nod fortfarande under ett bifogade Stack för Azure storage-konto.
* Ubuntu 14.04 installera måste gcc version uppdateras eller uppgraderas – steg för att uppgradera nedan:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812--0811--0810"></a>Version 0.8.12 / 0.8.11 / 0.8.10
04/07/2017

#### <a name="new"></a>Ny

* Lagringsutforskaren stängs nu automatiskt när du installerar en uppdatering från meddelanden om uppdateringar
* Lokalt Snabbåtkomst ger en förbättrad upplevelse för att arbeta med resurserna används ofta
* I Redigeraren för Blob-behållare kan du nu se vilken virtuell dator som tillhör en lånade blob
* Du kan nu komprimera panelen vänster
* Identifiering nu körs samtidigt som nedladdning
* Använda statistik i Blob-behållaren och filresursen tabell redigerare för att se storleken på ditt val eller resurs
* Du kan nu logga in till Azure Active Directory (AAD) baserad Azure Stack-konton.
* Du kan överföra arkivfiler över 32MB till Premium storage-konton
* Förbättrat stöd för hjälpmedel
* Nu kan du lägga till betrodda Base64-kodat X.509-SSL-certifikat genom att gå till redigera -&gt; SSL-certifikat -&gt; Importera certifikat

#### <a name="fixes"></a>Korrigeringar

* Fast: när du har uppdaterat en kontoautentiseringsuppgifter trädvyn kan ibland uppdateras inte automatiskt
* Fast: Generera en SAS för emulatorn köer och tabeller skulle leda till en ogiltig URL
* Fast: premium storage-konton kan nu utökas när en proxy är aktiverat
* Fast: Verkställ-knappen på sidan för hantering av konton fungerar inte om du hade 1 eller 0 konton som har valts
* Fast: överföring av blobbar som kräver konfliktlösningar kan misslyckas - fast i 0.8.11
* Fast: skicka feedback har brutits i 0.8.11 - fast i 0.8.12

#### <a name="known-issues"></a>Kända problem

* Efter uppgraderingen till 0.8.10, behöver du uppdatera alla dina autentiseringsuppgifter.
* När du har zoomat in eller ut kan tillfälligt zoomnivån återställa till Standardnivå.
* Med mer än 3 grupper av blobbar eller filer som överför samtidigt kan orsaka fel.
* Panelen konto inställningar kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer.
* Byta namn på blobbar (individuellt eller i en bytt namn till blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras vid ett byte av namn.
* Även om Azure-stacken inte stöder filresurser, visas en filresurser nod fortfarande under ett bifogade Stack för Azure storage-konto.
* Ubuntu 14.04 installera måste gcc version uppdateras eller uppgraderas – steg för att uppgradera nedan:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089--088"></a>Version 0.8.9 / 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Ny

* Lagringsutforskaren 0.8.9 kommer automatiskt att hämta den senaste versionen för uppdateringar.
* Snabbkorrigering: med hjälp av en portal resulterar genererade SAS-URI att koppla ett lagringskonto i ett fel.
* Du kan nu skapa, hantera och främja blob ögonblicksbilder.
* Du kan nu logga in på Azure Kina, Azure Tyskland och Azure som tillhör amerikanska myndigheter konton.
* Du kan nu ändra zoomnivån. Använd alternativen i menyn Visa för att Zooma In, Zooma ut och Återställ Zoom.
* Unicode-tecken stöds nu i Användarmetadata för blobbar och filer.
* Hjälpmedelsförbättringar.
* I nästa version viktig information kan visas från meddelanden om uppdateringar. Du kan också visa aktuella viktig information från Hjälp-menyn.

#### <a name="fixes"></a>Korrigeringar

* Fast: versionsnumret nu visas korrekt på Kontrollpanelen i Windows
* Fast: sökning är inte längre begränsad till 50 000 noder
* Fast: överföra till en filresurs som alltid roterade målkatalogen inte redan finns
* Fast: förbättrad stabilitet för lång överföring

#### <a name="known-issues"></a>Kända problem

* När du har zoomat in eller ut kan tillfälligt zoomnivån återställa till Standardnivå.
* Snabbåtkomst fungerar bara med prenumerationen baserat objekt. Lokala resurser eller resurser som är anslutna via en nyckel eller SAS-token stöds inte i den här versionen.
* Snabbåtkomst kan det ta ett par sekunder att navigera till målresursen, beroende på hur många resurser som du har.
* Med mer än 3 grupper av blobbar eller filer som överför samtidigt kan orsaka fel.

12/16/2016
### <a name="version-087"></a>Version 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Ny

* Du kan välja hur du löser konflikter i början av en uppdatering, hämta eller kopiera session i fönstret aktiviteter
* Hovra över en flik om du vill se den fullständiga sökvägen till resursen lagring
* När du klickar på en flik, synkronisering och dess plats i navigeringsfönstret vänster

#### <a name="fixes"></a>Korrigeringar

* Fast: Lagringsutforskaren är nu ett betrott app på Mac
* Fast: Ubuntu 14.04 igen stöds
* Fast: Ibland Lägg till konto UI blinkar när inläsning av prenumerationer
* Fast: Ibland inte alla lagringsresurser ingick i navigeringsfönstret vänster
* Fast: Åtgärdsfönstret ibland visas tom åtgärder
* Fast: Fönsterstorlek från den senaste stängd sessionen nu sparas
* Fast: Du kan öppna flera flikar för samma resurs med hjälp av snabbmenyn

#### <a name="known-issues"></a>Kända problem

* Snabbåtkomst fungerar bara med prenumerationen baserat objekt. Lokala resurser eller resurser som är anslutna via en nyckel eller SAS-token stöds inte i den här versionen
* Det kan ta Snabbåtkomst en några sekunder att navigera till målresursen, beroende på hur många resurser som du har
* Med mer än 3 grupper av blobbar eller filer som överför samtidigt kan orsaka fel
* Referenser för sökning efter det att söka i ungefär 50 000 noder - prestanda kan påverkas eller kan orsaka undantag
* Du kan se flera prompter som ber om användarens tillstånd att komma åt nyckelringar för första gången med Lagringsutforskaren på macOS. Vi rekommenderar att du väljer Tillåt alltid så uppmaningen inte visas igen

11/18/2016
### <a name="version-086"></a>Version 0.8.6

#### <a name="new"></a>Ny

* Du kan nu PIN-kod används oftast Snabbåtkomst-tjänster för enkel navigering
* Nu kan du öppna flera redigerare på olika flikar. Enskild klickar för att öppna en tillfällig flik; Dubbelklicka om du vill öppna en permanent flik. Du kan också klicka på fliken tillfälligt så att den permanenta fliken
* Vi har gjort märkbar prestanda och stabilitetsförbättringar för överföring och hämtning, särskilt för stora filer på snabb datorer
* Nu kan skapa tomma ”virtuell” mappar i blob-behållare
* Vi har nytt introducerades bred sökning med vår nya förbättrad sökningen så att du nu har två alternativ för att söka efter:
    * Globala Sök - bara ange en sökterm i textrutan Sök
    * Bred sökning - Klicka på förstoringsglasikonen bredvid en nod och sedan lägga till en sökterm i slutet av sökvägen, eller högerklicka och välj ”Sök från hit”
* Vi har lagt till olika teman: ljus (standard), mörkt, hög kontrast svart och hög kontrast vit. Gå till redigera -&gt; teman för att ändra inställningarna teman
* Du kan ändra Blob och filegenskaper
* Vi stöder nu kodade (base64) och kodat Kömeddelanden
* På Linux för, ett 64-bitars operativsystem är nu krävs. Den här versionen stöder vi endast 64-bitars Ubuntu 16.04.1 LTS
* Vi har uppdaterat vår logotypen!

#### <a name="fixes"></a>Korrigeringar

* Fast: Skärmen låser problem
* Fast: Förbättrad säkerhet
* Fast: Ibland dubbla bifogade konton kan visas
* Fast: En blob med en odefinierad innehållstyp generera ett undantag
* Fast: Öppna panelen fråga på en tom tabell var inte möjligt
* Fast: Varierar buggar i sökningen
* Fast: Öka antalet resurser som lästs in från 50 till 100 när du klickar på ”mer belastning”
* Fast: Första körningen om ett konto som har loggat in, vi nu välja alla prenumerationer för kontot som standard

#### <a name="known-issues"></a>Kända problem

* Den här versionen av Lagringsutforskaren körs inte på Ubuntu 14.04
* Om du vill öppna flera flikar för samma resurs kontinuerligt Klicka inte på samma resurs. Klicka på en annan resurs och sedan gå tillbaka och klicka sedan på den ursprungliga resursen att öppna den igen i en annan flik
* Snabbåtkomst fungerar bara med prenumerationen baserat objekt. Lokala resurser eller resurser som är anslutna via en nyckel eller SAS-token stöds inte i den här versionen
* Det kan ta Snabbåtkomst en några sekunder att navigera till målresursen, beroende på hur många resurser som du har
* Med mer än 3 grupper av blobbar eller filer som överför samtidigt kan orsaka fel
* Referenser för sökning efter det att söka i ungefär 50 000 noder - prestanda kan påverkas eller kan orsaka undantag

10/03/2016
### <a name="version-085"></a>Version 0.8.5

#### <a name="new"></a>Ny

* Kan nu använda Portal-genererade SAS nycklar för att ansluta till Lagringskonton och resurser

#### <a name="fixes"></a>Korrigeringar

* Fast: konkurrenstillstånd under sökningen ibland orsakade noder blir inte är expanderbara
* Fast: ”Använd HTTP” fungerar inte när du ansluter till Storage-konton med kontonamn och nyckel
* Fast: SAS-nycklar (som är särskilt Portal-genererade) returnerar ett ”avslutande snedstreck” fel
* Fast: importera tabell problem
    * Ibland har partitionsnyckel och radnyckel återförts
    * Det gick inte att läsa ”null” partitionsnycklar

#### <a name="known-issues"></a>Kända problem

* Referenser för sökning efter det att söka i ungefär 50 000 noder - kan prestanda påverkas
* Azure-stacken stöder inte filer, så försök att expandera filer visas ett fel

09/12/2016
### <a name="version-084"></a>Version 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Ny

* Generera Direktlänkar till storage-konton, behållare, köer, tabeller eller filresurser för att dela och stöd för enkel åtkomst till dina resurser - Windows- och Mac OS x
* Sök efter din blob-behållare, tabeller, köer, filresurser eller storage-konton från sökrutan
* Nu kan du gruppera-satser i tabellen Frågebyggaren
* Byt namn på och kopiera och klistra in blob-behållare, filresurser, tabeller, BLOB, blob mappar, filer och kataloger från i SAS-anslutna konton och behållare
* Byta namn på och kopiera blob-behållare och filresurser bevara nu egenskaper och metadata

#### <a name="fixes"></a>Korrigeringar

* Fast: Det går inte att redigera tabellentiteter om de innehåller boolean eller binära egenskaper

#### <a name="known-issues"></a>Kända problem

* Referenser för sökning efter det att söka i ungefär 50 000 noder - kan prestanda påverkas

08/03/2016
### <a name="version-083"></a>Version 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Ny

* Byt namn på behållare, tabeller, filresurser
* Bättre frågan builder upplevelse
* Möjlighet att spara och läsa in frågor
* Direkta länkar till storage-konton eller behållare, köer, tabeller eller filresurser för delning och lätt att komma åt dina resurser (endast för Windows - macOS stöder kommer snart!)
* Möjlighet att hantera och konfigurera CORS-regler

#### <a name="fixes"></a>Korrigeringar

* Fast: Microsoft Accounts kräver omautentisering var 8 – 12: e timme

#### <a name="known-issues"></a>Kända problem

* Ibland Användargränssnittet verka fryst – maximerar fönstret hjälper dig att lösa problemet
* installera macOS kan kräva förhöjd behörighet
* Konto inställningar panelen kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer
* Byta namn på filresurser, blob-behållare och tabeller bevaras inte metadata eller andra egenskaper för behållare, till exempel filresurskvoten, offentliga åtkomstnivå eller åtkomstprinciper
* Byta namn på blobbar (individuellt eller i en bytt namn till blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras vid ett byte av namn
* Kopiera eller byta namn på resurser fungerar inte i SAS-anslutna konton

07/07/2016
### <a name="version-082"></a>Version 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Ny

* Storage-konton är grupperade efter prenumerationer; utveckling lagring och resurser som är anslutna via SAS eller nyckeln visas under noden (lokala och bifogad)
* Logga ut från konton ”Azure kontoinställningar” Kontrollpanelen
* Konfigurera proxyinställningar för att aktivera och hantera inloggning
* Skapa och ta bort blob-lån
* Öppna blob-behållare, köer, tabeller och filer med enkelklickning

#### <a name="fixes"></a>Korrigeringar

* Fast: Kömeddelanden infogas med .NET eller Java-bibliotek är inte korrekt avkoda från base64
* Fast: $metrics tabeller visas inte för Blob Storage-konton
* Fast: tabeller noden fungerar inte för lokal lagring för (utveckling)

#### <a name="known-issues"></a>Kända problem

* installera macOS kan kräva förhöjd behörighet

06/15/2016
### <a name="version-080"></a>Version 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Ny

* Stöd för resursen: visa, överföra, hämta, kopiera filer och kataloger, SAS-URI: er (skapa och ansluta)
* Förbättrad användarupplevelse för anslutning till lagring med SAS URI: er eller konto nycklar
* Exportera tabell frågeresultat
* Tabell kolumnordning och anpassning
* Visa $logs blob-behållare och $metrics tabeller för Storage-konton med aktiverad
* Förbättrad exportera och importera beteende, innehåller nu egenskapsvärdetypen

#### <a name="fixes"></a>Korrigeringar

* Fast: överföra eller hämta stora blobbar kan resultera i ofullständiga överföringar/hämtningar
* Fast: redigera, lägga till eller importera en entitet med ett numeriskt värde (”1”) konverteras till dubbla
* Fast: Det gick inte att expandera noden tabellen i den lokala utvecklingsmiljön

#### <a name="known-issues"></a>Kända problem

* $metrics tabeller är inte synliga för Blob Storage-konton
* Kömeddelanden som lagts till via programmering kan inte visas korrekt om meddelanden är kodad med Base64-kodning

05/17/2016
### <a name="version-07201605090"></a>Version 0.7.20160509.0

#### <a name="new"></a>Ny

* Bättre felhantering för appen kraschar

#### <a name="fixes"></a>Korrigeringar

* Fast bugg där Informationsfältet meddelanden ibland inte visas när behövde inloggningsuppgifter

#### <a name="known-issues"></a>Kända problem

* Tabeller: Lägga till, redigera eller importera en entitet som har en egenskap med ett ambiguously numeriska värde, till exempel ”1” eller ”1.0”, och användaren försöker skicka den som en `Edm.String`, värdet kommer tillbaka till klienten API som en Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Version 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Ny

* Tabell stöd: visa, frågor, exportera, importera och CRUD-åtgärder för entiteter
* Kö stöd: visa, lägga till dequeueing meddelanden
* Generera SAS URI: er för Storage-konton
* Ansluta till Lagringskonton med SAS URI: er
* Meddelanden om uppdateringar för framtida uppdateringar Lagringsutforskaren
* Uppdaterade känsla och utseende

#### <a name="fixes"></a>Korrigeringar

* Förbättringar av prestanda och tillförlitlighet

### <a name="known-issues-amp-mitigations"></a>Kända problem &amp; åtgärder

* Hämta stora blob filer fungerar inte - vi rekommenderar att du använder AzCopy medan vi löser problemet
* Autentiseringsuppgifter att inte hämta och cachelagras om arbetsmappen hittas inte eller kan inte skrivas till
* Om vi lägger till, redigera eller importera en entitet som har en egenskap med ett ambiguously numeriska värde, till exempel ”1” eller ”1.0”, och användaren försöker skicka den som en `Edm.String`, värdet kommer tillbaka till klienten API som en Edm.Double
* När du importerar CSV-filer med flera rader innehåller kan data få hackas eller kodade

02/03/2016

### <a name="version-07201601291"></a>Version 0.7.20160129.1

#### <a name="fixes"></a>Korrigeringar

* Förbättrad prestanda när du laddar upp, hämta och kopiera BLOB

01/14/2016

### <a name="version-07201601050"></a>Version 0.7.20160105.0

#### <a name="new"></a>Ny

* Linux-support (OSX paritet funktioner)
* Lägga till blob-behållare med delad åtkomst signaturer (SAS)-nyckel
* Lägg till Lagringskonton för Azure Kina
* Lägg till Storage-konton med anpassade slutpunkter
* Öppna och visa innehållet text- och blobbar
* Visa och redigera blob-egenskaper och metadata

#### <a name="fixes"></a>Korrigeringar

* Fast: överföra eller hämta ett stort antal blobbar (500 +) kan ibland orsaka att appen har en vit skärm
* Fast: när blob-behållaren offentliga åtkomstnivå kan det nya värdet uppdateras inte förrän du har angett fokus på behållaren igen. Dessutom dialogrutan alltid som standard ”ingen offentlig åtkomst” och inte det faktiska aktuella värdet.
* Stöd för bättre övergripande tangentbord/tillgänglighet och UI
* Spår historik texten ska radbrytas när den är lång med ett blanksteg
* SAS-dialogrutan har stöd för verifiering av indata
* Lokal lagring fortsätter att fungera även om användarens autentiseringsuppgifter har upphört att gälla
* När du tar bort en öppen blobbehållare är blob-Utforskaren på höger sida stängd

#### <a name="known-issues"></a>Kända problem

* Linux-installationen måste gcc version uppdateras eller uppgraderas – steg för att uppgradera nedan:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Version 0.7.20151116.0

#### <a name="new"></a>Ny

* macOS och versioner av Windows
* Logga in att visa dina Lagringskonton – använda din organisations konto, Account, 2FA osv.
* Lokal utveckling lagring (Använd storage-emulatorn endast för Windows)
* Stöd för Azure Resource Manager och klassisk resurs
* Skapa och ta bort blobbar, köer och tabeller
* Sök efter specifika blobbar, köer och tabeller
* Utforska innehållet i blob-behållare
* Visa och navigera genom kataloger
* Ladda upp, hämta och ta bort blobbar och mappar
* Visa och redigera blob-egenskaper och metadata
* Generera en SAS-nycklar
* Hantera och skapa lagras åtkomst principer SAP)
* Sök efter blobbar efter prefix
* Dra 'n släppa filer att överföra eller hämta

#### <a name="known-issues"></a>Kända problem

* När du ställer in blob-behållaren offentliga åtkomstnivå, uppdateras inte det nya värdet förrän du har angett fokus på behållaren igen
* När du öppnar dialogrutan för att ställa in allmän åtkomst visas alltid ”ingen offentlig åtkomst” som standard och inte faktiska aktuella värde
* Det går inte att byta namn på hämtade blobbar
* Aktiviteten loggposter kommer ibland ”fastna” i en pågående tillstånd när ett fel inträffar och felet inte visas
* Ibland kraschar eller stängs helt vit när du försöker överföra eller hämta ett stort antal blobbar
* Ibland avbryter en kopieringsåtgärd fungerar inte
* När du skapar en behållare (tabell-blob/kön), om du vill ange ett ogiltigt namn och fortsätta med att skapa en annan under en annan Behållartyp kan inte du ange fokus på den nya typen
* Det går inte att skapa en ny mapp eller byta namn på mappen




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
