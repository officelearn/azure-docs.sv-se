---
title: Viktig information om Microsoft Azure Storage Explorer
description: Viktig information för Microsoft Azure Storage Explorer
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 986da8980a569583ef454833957ace85dd1bfbb6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351064"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Viktig information om Microsoft Azure Storage Explorer

Den här artikeln innehåller de senaste versionsanvisningarna för Azure Storage Explorer samt versionsanteckningar för tidigare versioner. 

[Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) är en fristående app som gör att du enkelt kan arbeta med Azure Storage-data på Windows, macOS och Linux.

Om du vill hämta tidigare versioner av Storage Explorer kan du besöka [sidan Releases](https://github.com/microsoft/AzureStorageExplorer/releases) i vår GitHub-repo.

## <a name="version-1110"></a>Version 1.11.0
11/4/2019

### <a name="new"></a>Ny
* Åtgärder för Blobbar, ADLS Gen2 och Hanterade diskar använder den integrerade AzCopy. Mer specifikt görs följande åtgärder med AzCopy:
   * Blobar
      * Öppna för redigering + Ladda upp
      * Ladda upp, inklusive dra & släppa
      * Ladda ned
      * Kopiera & klistra in #1249
      * Ta bort
   * ADLS Gen2 Blobbar
      * Ladda upp, inklusive dra & släppa
      * Ladda ned
      * Kopiera & klistra in
      * Ta bort, inklusive borttagning av mappar
   * Managed Disks
      * Ladda upp
      * Ladda ned
      * Kopiera & klistra in

   Dessutom har flera ofta efterfrågade funktioner lagts till i den integrerade AzCopy-upplevelsen:
   * Konfliktlösningar – du uppmanas att göra ändringar under överföringar för att lösa konflikter. #1455
   * Ladda upp som sidblobar – du kan välja om AzCopy laddar upp VHD- och VHDX-filer som sidblobar. #1164 och #1601
   * Konfigurerbara AzCopy-parametrar - Flera inställningar har lagts till för att justera AzCopys prestanda och resursanvändning. Se mer information nedan.

* Om du vill aktivera ADLS Gen2- och Blobs flerprotokollsåtkomst och ytterligare förbättra ADLS Gen2-upplevelser har vi lagt till följande funktioner för ADLS Gen2-kontona:
   * Sök med egna namn för att ange behörigheter för ACL
   * Visa dolda behållare, till exempel $logs och $web
   * Skaffa och bryta behållarlån
   * Förvärva och bryta Blob-lån #848
   * Hantera principer för åtkomst till behållare
   * Konfigurera åtkomstnivåer för Blob
   * Kopiera & klistra in blobbar

* I den här versionen förhandsgranskar vi ytterligare 17 språk. Du kan växla till ett språk som du väljer på inställningssidan under "Program" → "Nationella inställningar" → "Språk (förhandsgranskning)". Vi arbetar fortfarande hårt med att översätta ytterligare strängar och förbättra översättningskvaliteten. Om du har någon feedback om en översättning, eller om du märker en sträng som ännu inte är översatt, [vänligen öppna ett problem på GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* I varje utgåva försöker vi gå in på några inställningar för att möjliggöra finsvarvning av Storage Explorer. I den här versionen har vi lagt till inställningar för att ytterligare konfigurera AzCopy samt för att dölja tjänstnoder:
   * AzCopy bandbredd gräns - hjälper till att kontrollera hur mycket av nätverket AzCopy använder. Du hittar den här inställningen på "Transfers" → "AzCopy" → "Maximal överföringshastighet". #1099
   * AzCopy MD5 check - låter dig konfigurera om och hur strikt AzCopy kontrollerar MD5 hashar på nedladdning. Du hittar den här inställningen på "Transfers" → "AzCopy" → "Kontrollera MD5".
   * AzCopy samtidighet och minne buffertstorlek - som standard AzCopy kommer att analysera din maskin för att fastställa rimliga standardvärden för dessa inställningar. Men om du stöter på prestandaproblem kan dessa avancerade inställningar användas för att ytterligare skräddarsy hur AzCopy körs på din dator. Du hittar dessa inställningar under "Transfers" → "AzCopy". #994
   * Visa och dölja tjänstnoder – de här inställningarna ger dig möjlighet att visa eller dölja någon av de Azure-tjänster som Storage Explorer stöder. Du hittar dessa inställningar under avsnittet "Tjänster". #1877

* När du skapar en ögonblicksbild av en hanterad disk anges nu ett standardnamn. #1847
* När du ansluter med Azure AD visas "(ADLS Gen2)" bredvid noden om du bifogar en ADLS Gen2 Blob-behållare. #1861

### <a name="fixes"></a>Korrigeringar
* När du kopierar, laddar upp eller hämtar stora diskar kan Storage Explorer ibland inte återkalla åtkomsten till de diskar som är involverade i åtgärden. Problemet har åtgärdats. #2048
* Tabellstatistiken misslyckades när en partitionsnyckelfråga visades. Problemet har åtgärdats. #1886

### <a name="known-issues"></a>Kända problem
* Lagringsutforskaren 1.11.0 kräver nu en DFS-slutpunkt (till exempel "myaccount.dfs.core.windows.net") för att ansluta till ADLS Gen2-behållare. Tidigare versioner av Storage Explorer gjorde att du kan använda en blob-slutpunkt. Dessa bilagor kanske inte längre fungerar efter uppgradering till 1.11.0. Om du stöter på det här problemet kan du sätta tillbaka med DFS-slutpunkten.
* Numeriska inställningar kontrolleras inte om de ligger i ett giltigt intervall.#2140
* Det kan misslyckas med att kopiera blob-behållare från ett lagringskonto till ett annat i trädvyn. Vi undersöker frågan.#2124
* Inställningen Automatisk uppdatering påverkar ännu inte alla åtgärder i Blob Explorer.
* Hanterade diskfunktioner stöds inte i Azure Stack.
* Om en disköverföring eller inklistring misslyckas och en ny disk skapades före felet tas inte disken bort.
* Beroende på när du avbryter en diskuppladdning eller inklistring är det möjligt att lämna den nya disken i ett skadat tillstånd. Om detta inträffar måste du antingen ta bort den nya disken eller manuellt anropa disk-API:erna för att ersätta innehållet på disken så att den inte längre är skadad.
* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanteringslager för att komma åt dina lagringsresurser. Mer information finns i [felsökningsguiden.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om du inte gör det avblockerar du dig, kommenterar du det här problemet.
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron. Om du vill lösa problemet när du laddar upp till eller hämtar från en blob-behållare kan du använda den experimentella AzCopy-funktionen.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Om du försöker använda dessa funktioner när du arbetar med Azure Stack-resurser kan det leda till oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
   * Managed Disks
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För att köra Storage Explorer på Linux måste vissa beroenden installeras först. Mer information finns i [felsökningsguiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) för Storage Explorer.

## <a name="previous-releases"></a>Tidigare versioner

* [Version 1.10.1](#version-1101)
* [Version 1.10.0](#version-1100)
* [Version 1.9.0](#version-190)
* [Version 1.8.1](#version-181)
* [Version 1.8.0](#version-180)
* [Version 1.7.0](#version-170)
* [Version 1.6.2](#version-162)
* [Version 1.6.1](#version-161)
* [Version 1.6.0](#version-160)
* [Version 1.5.0](#version-150)
* [Version 1.4.4](#version-144)
* [Version 1.4.3](#version-143)
* [Version 1.4.2](#version-142)
* [Version 1.4.1](#version-141)
* [Version 1.3.0](#version-130)
* [Version 1.2.0](#version-120)
* [Version 1.1.0](#version-110)
* [Version 1.0.0](#version-100)
* [Version 0.9.6](#version-096)
* [Version 0.9.5](#version-095)
* [Version 0.9.4 och 0.9.3](#version-094-and-093)
* [Version 0.9.2](#version-092)
* [Version 0.9.1 och 0.9.0](#version-091-and-090)
* [Version 0.8.16](#version-0816)
* [Version 0.8.14](#version-0814)
* [Version 0.8.13](#version-0813)
* [Version 0.8.12 och 0.8.11 och 0.8.10](#version-0812-and-0811-and-0810)
* [Version 0.8.9 och 0.8.8](#version-089-and-088)
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

## <a name="version-1101"></a>Version 1.10.1
9/19/2019

### <a name="hotfix"></a>Snabbkorrigeringen
* Vissa användare påträffade ett fel i 1.10.0 när de försökte visa sina data i sina ADLS Gen 1-konton. Det här felet hindrade explorer-panelen från att återges korrekt. Problemet har åtgärdats. #1853 #1865

### <a name="new"></a>Ny
* Storage Explorer har nu ett dedikerat inställningsgränssnitt. Du kan komma åt den antingen från Redigera → Inställningar eller genom att klicka på ikonen Inställningar (växeln) i det vänstra vertikala verktygsfältet. Den här funktionen är det första steget vi tar för att tillhandahålla en mängd olika [användarbestasta filer.](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate) Med början i den här versionen stöds följande inställningar:
  * Tema
  * Proxy
  * Utloggning vid #6
  * Aktivera inloggning av enhetskodflöde
  * Automatisk uppdatering #1526
  * Aktivera AzCopy
  * AzCopy SAS varaktighet Om det finns andra inställningar som du vill se [tillagda, öppna ett problem på GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) som beskriver den inställning du vill se.
* Storage Explorer stöder nu hanterade diskar. Du kan:
  * Ladda upp en lokal virtuell hårddisk till en ny disk
  * Ladda ned en disk
  * Kopiera och klistra in diskar mellan resursgrupper och regioner
  * Ta bort diskar
  * Skapa en ögonblicksbild av en disk

Uppladdning, nedladdning och korsregionöverskridande kopiering av diskar drivs av AzCopy v10.
* Storage Explorer kan nu installeras via Snap Store på Linux. När du installerar via Snap-butiken installeras alla beroenden åt dig, inklusive .NET Core! För närvarande har vi verifierat att Storage Explorer fungerar bra på Ubuntu och CentOS. Om du stöter på problem med att installera från Snap-butiken på andra Linux-distributioner kan du [öppna ett problem på GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Mer information om hur du installerar från Snap-butiken finns i vår [komma igång-guide.](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux) #68
* Två huvudändringar har gjorts för att bifoga med Azure Active Directory (Azure AD) som är avsedda att göra funktionen mer användbar för ADLS Gen2-användare:
  * Nu väljer du den klient som resursen du kopplar finns i. Det innebär att du inte längre behöver ha RBAC-åtkomst till resursens prenumeration.
  * Om du bifogar en ADLS Gen2 Blob-behållare kan du nu koppla till en viss sökväg i behållaren.
* När du hanterar ACL:er för ADLS Gen2-filer och mappar visar Storage Explorer nu de egna namnen för entiteter i åtkomstkontrollistan. #957
* När du lägger till via OID i en ADLS Gen2 ACL validerar Storage Explorer nu att OID:n tillhör en giltig entitet i din klientorganisation. #1603
* Kortkommandona för att navigera mellan flikar använder nu fler standardkombinationer för tangentkombinationer. #1018
* Mitten klicka på en flik kommer nu att stänga den. #1348
* Om en AzCopy-överföring innehåller hopp och inga fel visas nu en varningsikon för att markera att hopp har inträffat. #1490
* Den integrerade AzCopy har uppdaterats till version 10.2.1. Dessutom kan du nu visa den version av AzCopy som är installerad i dialogrutan Om. #1343

### <a name="fixes"></a>Korrigeringar
* Många användare har stött på olika "kan inte läsa version av odefinierade" eller "kan inte läsa anslutningen av odefinierade" fel när du arbetar med bifogade lagringskonton. Även om vi fortfarande fortsätter att undersöka orsaken till det här problemet, i 1.10.0 har vi förbättrat felhanteringen runt lastning bifogade lagringskonton. #1626, #985 och #1532
* Det var möjligt för explorer trädet (vänster sida) för att komma in i ett tillstånd där fokus skulle hoppa till den övre noden upprepade gånger. Problemet har åtgärdats. #1596
* När du hanterar ögonblicksbilder av en blob läser skärmläsare inte tidsstämpeln som är associerad med ögonblicksbilden. Problemet har åtgärdats. #1202
* Proxyinställningen på macOS angavs inte i tid för att autentiseringsprocessen skulle kunna använda dem. Problemet har åtgärdats. #1567
* Om ett lagringskonto i ett suveränt moln bifogades med namn och nyckel skulle AzCopy inte fungera. Problemet har åtgärdats. #1544
* När du ansluter via en anslutningssträng tar Storage Explorer nu bort efterföljande blanksteg. #1387

### <a name="known-issues"></a>Kända problem
* Inställningen Automatisk uppdatering påverkar ännu inte alla åtgärder i Blob Explorer.
* Hanterade diskfunktioner stöds inte i Azure Stack.
* Om en disköverföring eller inklistring misslyckas och en ny disk skapades före felet tas inte disken bort.
* Beroende på när du avbryter en diskuppladdning eller inklistring är det möjligt att lämna den nya disken i ett skadat tillstånd. Om detta inträffar måste du antingen ta bort den nya disken eller manuellt anropa disk-API:erna för att ersätta innehållet på disken så att den inte längre är skadad.
* Beroende på när du avbryter en diskuppladdning eller inklistring är det möjligt att lämna den nya disken i ett skadat tillstånd. Om detta inträffar måste du antingen ta bort den nya disken eller manuellt anropa disk-API:erna för att ersätta innehållet på disken så att den inte längre är skadad.
* När du utför en icke-AzCopy Blob-hämtning verifieras inte MD5 för stora filer. Detta beror på ett fel i Lagring SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanteringslager för att komma åt dina lagringsresurser. Mer information finns i [felsökningsguiden.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om du inte gör det avblockerar du dig, kommenterar du det här problemet.
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron. Om du vill lösa problemet när du laddar upp till eller hämtar från en blob-behållare kan du använda den experimentella AzCopy-funktionen.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Om du försöker använda dessa funktioner när du arbetar med Azure Stack-resurser kan det leda till oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
   * Managed Disks
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För att köra Storage Explorer på Linux måste vissa beroenden installeras först. Mer information finns i [felsökningsguiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) för Storage Explorer.


## <a name="version-1100"></a>Version 1.10.0
9/12/2019

### <a name="new"></a>Ny

* Storage Explorer har nu ett dedikerat inställningsgränssnitt. Du kan komma åt den antingen från Redigera → Inställningar eller genom att klicka på ikonen Inställningar (växeln) i det vänstra vertikala verktygsfältet. Den här funktionen är det första steget vi tar för att tillhandahålla en mängd olika [användarbestasta filer.](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate) Med början i den här versionen stöds följande inställningar:
    * Tema
    * Proxy
    * Utloggning vid [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * Aktivera inloggning av enhetskodflöde
    * Automatisk uppdatering [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * Aktivera AzCopy
    * AzCopy SAS varaktighet

    Om det finns andra inställningar som du vill se till, [öppna ett problem på GitHub som beskriver den inställning du vill se](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Storage Explorer stöder nu hanterade diskar. Du kan:
    * Ladda upp en lokal virtuell hårddisk till en ny disk
    * Ladda ned en disk
    * Kopiera och klistra in diskar mellan resursgrupper och regioner
    * Ta bort diskar
    * Skapa en ögonblicksbild av en disk

    Uppladdning, nedladdning och korsregionöverskridande kopiering av diskar drivs av AzCopy v10.
* Storage Explorer kan nu installeras via Snap Store på Linux. När du installerar via Snap-butiken installeras alla beroenden åt dig, inklusive .NET Core! För närvarande har vi verifierat att Storage Explorer fungerar bra på Ubuntu och CentOS. Om du stöter på problem med att installera från Snap-butiken på andra Linux-distributioner kan du [öppna ett problem på GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Mer information om hur du installerar från Snap-butiken finns i vår [komma igång-guide.](https://aka.ms/storageexplorer/snapinformation) [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Två stora ändringar har gjorts för att bifoga med Azure Active Directory (Azure AD) som är avsedda att göra funktionen mer användbar för ADLS Gen2-användare: * Du väljer nu den klient som resursen du bifogar finns i. Det innebär att du inte längre behöver ha RBAC-åtkomst till resursens prenumeration.
        * Om du bifogar en ADLS Gen2 Blob Container kan du nu koppla till en viss sökväg i behållaren.
* När du hanterar ACL:er för ADLS Gen2-filer och mappar visar Storage Explorer nu de egna namnen för entiteter i åtkomstkontrollistan. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* När du lägger till via OID i en ADLS Gen2 ACL validerar Storage Explorer nu att OID:n tillhör en giltig entitet i din klientorganisation. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Kortkommandona för att navigera mellan flikar använder nu fler standardkombinationer för tangentkombinationer. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Mitten klicka på en flik kommer nu att stänga den. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Om en AzCopy-överföring innehåller hopp och inga fel visas nu en varningsikon för att markera att hopp har inträffat. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* Den integrerade AzCopy har uppdaterats till version 10.2.1. Dessutom kan du nu visa den version av AzCopy som är installerad i dialogrutan Om. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Korrigeringar

* Många användare har stött på olika "kan inte läsa version av odefinierade" eller "kan inte läsa anslutningen av odefinierade" fel när du arbetar med bifogade lagringskonton. Även om vi fortfarande fortsätter att undersöka orsaken till det här problemet, i 1.10.0 har vi förbättrat felhanteringen runt lastning bifogade lagringskonton. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)och [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Det var möjligt för explorer trädet (vänster sida) för att komma in i ett tillstånd där fokus skulle hoppa till den övre noden upprepade gånger. Problemet har åtgärdats. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* När du hanterar ögonblicksbilder av en blob läser skärmläsare inte tidsstämpeln som är associerad med ögonblicksbilden. Problemet har åtgärdats. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* Proxyinställningen på macOS angavs inte i tid för att autentiseringsprocessen skulle kunna använda dem. Problemet har åtgärdats. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Om ett lagringskonto i ett suveränt moln bifogades med namn och nyckel skulle AzCopy inte fungera. Problemet har åtgärdats. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* När du ansluter via en anslutningssträng tar Storage Explorer nu bort efterföljande blanksteg. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Kända problem

* Inställningen Automatisk uppdatering påverkar ännu inte alla åtgärder i Blob Explorer.
* Hanterade diskfunktioner stöds inte i Azure Stack.
* Om en disköverföring eller inklistring misslyckas och en ny disk skapades före felet tas inte disken bort.
* Beroende på när du avbryter en diskuppladdning eller inklistring är det möjligt att lämna den nya disken i ett skadat tillstånd. Om detta inträffar måste du antingen ta bort den nya disken eller manuellt anropa disk-API:erna för att ersätta innehållet på disken så att den inte längre är skadad.
* När du utför en icke-AzCopy Blob-hämtning verifieras inte MD5 för stora filer. Detta beror på ett fel i Lagring SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanteringslager för att komma åt dina lagringsresurser. Mer information finns i [felsökningsguiden.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om du inte gör det avblockerar du dig, kommenterar du det här problemet.
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron. Om du vill lösa problemet när du laddar upp till eller hämtar från en blob-behållare kan du använda den experimentella AzCopy-funktionen.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Om du försöker använda dessa funktioner när du arbetar med Azure Stack-resurser kan det leda till oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
   * Managed Disks
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För att köra Storage Explorer på Linux måste vissa beroenden installeras först. Mer information finns i [felsökningsguiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) för Storage Explorer.

## <a name="version-190"></a>Version 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Ladda ned Azure Storage Explorer 1.9.0
- [Utforskaren för Azure Storage 1.9.0 för Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Utforskaren för Azure Storage 1.9.0 för Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.9.0 för Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Ny

* Du kan nu bifoga Blob-behållare via Azure AD (RBAC- eller ACL-behörigheter). Den här funktionen är avsedd att hjälpa användare som har åtkomst till behållare men inte lagringskonton som behållarna finns i. Mer information om den här funktionen finns i vår komma igångguide.
* Förvärva och bryta hyresavtalet arbetar nu med RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Hantera åtkomstprinciper och ange offentlig åtkomstnivå fungerar nu med RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Ta bort blob mappar fungerar nu med RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* Ändra blob-åtkomstnivå fungerar nu med RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Du kan nu snabbt återställa Snabbåtkomst via "Hjälp" → "Reset". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Förhandsgranskningsfunktioner

* Inloggning av enhetskodflöde är nu tillgängligt för förhandsgranskning. Om du vill aktivera det går du till "Förhandsgranska" → "Använd inloggning med enhetskodflöde". Vi uppmuntrar alla användare som har haft problem med tomma inloggningsfönster att prova den här funktionen, eftersom det kan visa sig vara en mer tillförlitlig form av inloggning.
* Storage Explorer integrerat med AzCopy är för närvarande tillgänglig för förhandsversion. För att aktivera det, gå till "Preview" → "Använd AzCopy för förbättrad Blob Ladda upp och ladda ner". Blob överföringar kompletteras med AzCopy bör vara snabbare och mer högpresterande.

### <a name="fixes"></a>Korrigeringar

* Fast att inte kunna läsa in mer än 50 prenumerationer för ett konto. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Fast "Logga in"-knappen fungerar inte på infobar som visas när en direktlänk misslyckas. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Fast att inte ladda upp .app-filer på macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Fast "Försök alla" fungerar inte för en misslyckad blob byta namn. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Fast "Avbryt" fungerar inte när du öppnar en blob. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Fixade flera stavnings- och verktygsproblem i hela produkten. Stort tack till alla som rapporterade dessa frågor! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Kända problem

* När du utför en icke-AzCopy Blob-hämtning verifieras inte MD5 för stora filer. Detta beror på ett fel i Lagring SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanteringslager för att komma åt dina lagringsresurser. Mer information finns i [felsökningsguiden.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Det kan misslyckas att försöka komma åt ADLS Gen2 Blobbar när bakom en proxy.
* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om du inte gör det avblockerar du dig, kommenterar du det här problemet.
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron. Om du vill lösa problemet när du laddar upp till eller hämtar från en blob-behållare kan du använda den experimentella AzCopy-funktionen.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Om du försöker använda dessa funktioner när du arbetar med Azure Stack-resurser kan det leda till oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För att köra Storage Explorer på Linux måste vissa beroenden installeras först. Mer information finns i [felsökningsguiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) för Storage Explorer.

## <a name="version-181"></a>Version 1.8.1
5/13/2019

### <a name="hotfixes"></a>Snabbkorrigeringar
* I vissa fall returnerar inte nästa sida med resurser om du klickar på "Läs in mer" på resursnivå. Problemet har åtgärdats. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* På Windows skulle AzCopy-nedladdningar misslyckas om en enskild fil eller mapp hämtades och namnet på filen eller mappen hade ett tecken som var ogiltigt för en Windows-sökväg. Problemet har åtgärdats. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* I ytterst sällsynta fall, när du utför ett namnbyte av en filresurs eller ett namnbyte i en filresurs, om kopiorna för namnbytet misslyckades, eller om Storage Explore inte kunde bekräfta att kopiorna med Azure lyckades, fanns det en risk för att Storage Explorer skulle ta bort originalfiler innan kopian var klar. Problemet har åtgärdats.

### <a name="new"></a>Ny

* Den integrerade AzCopy-versionen har uppdaterats till version 10.1.0.
* Ctrl/Cmd+R kan nu användas för att uppdatera den för närvarande fokuserade redigeraren. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Azure Stack Storage API-versionen har ändrats till 2017-04-17.
* Dialogrutan Hantera åtkomst för ADLS Gen2 håller nu masken synkroniserad på ett sätt som liknar andra POSIX-behörighetsverktyg. Användargränssnittet varnar dig också om en ändring görs som gör att behörigheterna för en användare eller grupp överskrider maskens gränser. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* För AzCopy-uppladdningar är flaggan för att beräkna och ange MD5-hash nu aktiverad. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Förhandsgranskningsfunktioner

* Inloggning av enhetskodflöde är nu tillgängligt för förhandsgranskning. Om du vill aktivera det går du till "Förhandsgranska" → "Använd inloggning med enhetskodflöde". Vi uppmuntrar alla användare som har haft problem med tomma inloggningsfönster att prova den här funktionen, eftersom det kan visa sig vara en mer tillförlitlig form av inloggning.
* Storage Explorer integrerat med AzCopy är för närvarande tillgänglig för förhandsversion. För att aktivera det, gå till "Preview" → "Använd AzCopy för förbättrad Blob Ladda upp och ladda ner". Blob överföringar kompletteras med AzCopy bör vara snabbare och mer högpresterande.

### <a name="fixes"></a>Korrigeringar

* Dialogrutan Åtkomstprinciper anger inte längre ett utgångsdatum för lagringsåtkomstprinciper som inte har någon förfallodatum. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Vissa ändringar har gjorts i dialogrutan Generera SAS för att se till att principer för lagrad åtkomst används korrekt när en SAS genereras. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* När du försöker ladda upp en fil som inte är 512-bytejusterad till en sidblob, visar Storage Explorer nu ett mer relevant fel. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Det gick inte att kopiera en Blob-behållare som använde ett visningsnamn. Nu används det faktiska namnet på Blob-behållaren. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Det gick inte att försöka utföra vissa åtgärder på en ADLS Gen2-mapp som hade unicode-tecken i sitt namn. Alla åtgärder bör nu fungera. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Kända problem

* När du utför en icke-AzCopy Blob-hämtning verifieras inte MD5 för stora filer. Detta beror på ett fel i Lagring SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanteringslager för att komma åt dina lagringsresurser. Mer information finns i [felsökningsguiden.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Det kan misslyckas att försöka komma åt ADLS Gen2 Blobbar när bakom en proxy.
* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om du inte gör det avblockerar du dig, kommenterar du det här problemet.
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron. Om du vill lösa problemet när du laddar upp till eller hämtar från en blob-behållare kan du använda den experimentella AzCopy-funktionen.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Om du försöker använda dessa funktioner när du arbetar med Azure Stack-resurser kan det leda till oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För att köra Storage Explorer på Linux måste vissa beroenden installeras först. Mer information finns i [felsökningsguiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) för Storage Explorer.

## <a name="version-180"></a>Version 1.8.0
2019-05-01

### <a name="new"></a>Ny

* Den integrerade AzCopy-versionen har uppdaterats till version 10.1.0.
* Ctrl/Cmd+R kan nu användas för att uppdatera den för närvarande fokuserade redigeraren. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Azure Stack Storage API-versionen har ändrats till 2017-04-17.
* Dialogrutan Hantera åtkomst för ADLS Gen2 håller nu masken synkroniserad på ett sätt som liknar andra POSIX-behörighetsverktyg. Användargränssnittet varnar dig också om en ändring görs som gör att behörigheterna för en användare eller grupp överskrider maskens gränser. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* För AzCopy-uppladdningar är flaggan för att beräkna och ange MD5-hash nu aktiverad. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Förhandsgranskningsfunktioner

* Inloggning av enhetskodflöde är nu tillgängligt för förhandsgranskning. Om du vill aktivera det går du till "Förhandsgranska" → "Använd inloggning med enhetskodflöde". Vi uppmuntrar alla användare som har haft problem med tomma inloggningsfönster att prova den här funktionen, eftersom det kan visa sig vara en mer tillförlitlig form av inloggning.
* Storage Explorer integrerat med AzCopy är för närvarande tillgänglig för förhandsversion. För att aktivera det, gå till "Preview" → "Använd AzCopy för förbättrad Blob Ladda upp och ladda ner". Blob överföringar kompletteras med AzCopy bör vara snabbare och mer högpresterande.

### <a name="fixes"></a>Korrigeringar

* Dialogrutan Åtkomstprinciper anger inte längre ett utgångsdatum för lagringsåtkomstprinciper som inte har någon förfallodatum. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Vissa ändringar har gjorts i dialogrutan Generera SAS för att se till att principer för lagrad åtkomst används korrekt när en SAS genereras. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* När du försöker ladda upp en fil som inte är 512-bytejusterad till en sidblob, visar Storage Explorer nu ett mer relevant fel. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Det gick inte att kopiera en Blob-behållare som använde ett visningsnamn. Nu används det faktiska namnet på Blob-behållaren. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Det gick inte att försöka utföra vissa åtgärder på en ADLS Gen2-mapp som hade unicode-tecken i sitt namn. Alla åtgärder bör nu fungera. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Kända problem

* När du utför en icke-AzCopy Blob-hämtning verifieras inte MD5 för stora filer. Detta beror på ett fel i Lagring SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanteringslager för att komma åt dina lagringsresurser. Mer information finns i [felsökningsguiden.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Det kan misslyckas att försöka komma åt ADLS Gen2 Blobbar när bakom en proxy.
* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om du inte gör det avblockerar du dig, kommenterar du det här problemet.
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron. Om du vill lösa problemet när du laddar upp till eller hämtar från en blob-behållare kan du använda den experimentella AzCopy-funktionen.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Om du försöker använda dessa funktioner när du arbetar med Azure Stack-resurser kan det leda till oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För att köra Storage Explorer på Linux måste vissa beroenden installeras först. Mer information finns i [felsökningsguiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) för Storage Explorer.

## <a name="version-170"></a>Version 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Ladda ned Azure Storage Explorer 1.7.0
- [Utforskaren för Azure Storage 1.7.0 för Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Utforskaren för Azure Storage 1.7.0 för Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.7.0 för Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Ny

* Du kan nu ändra ägar- och ägargruppen när du hanterar åtkomst för en ADLS Gen2-behållare, fil eller mapp.
* I Windows är det nu en inkrementell installation att uppdatera Storage Explorer inifrån produkten. Detta bör resultera i en snabbare uppdateringsupplevelse. Om du föredrar en ren installation kan du ladda ner [installationsprogrammet](https://azure.microsoft.com/features/storage-explorer/) själv och sedan installera manuellt. #1089

### <a name="preview-features"></a>Förhandsgranskningsfunktioner

* Inloggning av enhetskodflöde är nu tillgängligt för förhandsgranskning. Om du vill aktivera det går du till "Förhandsgranska" → "Använd inloggning med enhetskodflöde". Vi uppmuntrar alla användare som har haft problem med tomma inloggningsfönster att prova den här funktionen, eftersom det kan visa sig vara en mer tillförlitlig form av inloggning. #938
* Storage Explorer integrerat med AzCopy är för närvarande tillgänglig för förhandsversion. För att aktivera det, gå till "Preview" → "Använd AzCopy för förbättrad Blob Ladda upp och ladda ner". Blob överföringar kompletteras med AzCopy bör vara snabbare och mer högpresterande.

### <a name="fixes"></a>Korrigeringar

* Du kan nu välja den blobtyp som du vill ladda upp som när AzCopy är aktiverat. #1111
* Tidigare, om du hade aktiverat statiska webbplatser för ett ADLS Gen2 Storage-konto och sedan bifogat det med namn och nyckel, skulle Storage Explorer inte ha upptäckt att hierarkiskt namnområde var aktiverat. Problemet har åtgärdats. #1081
* I blob-redigeraren bröts sortering efter antingen återstående kvarhållningsdagar eller status. Problemet har åtgärdats. #1106
* Efter 1.5.0 väntade Storage Explorer inte längre på att serversidans kopior skulle slutföras innan du rapporterar att det ska lyckas under ett namnbyte eller en kopia & klistra in. Problemet har åtgärdats. #976
* När du använder den experimentella AzCopy-funktionen kunde kommandot som kopierades efter att ha klickat på "Kopiera kommando till Urklipp" inte alltid kunnat köras på egen hand. Nu kopieras alla kommandon som behövs för att köra överföringen manuellt. #1079
* Tidigare var ADLS Gen2-blobbar inte tillgängliga om du låg bakom en proxy. Detta berodde på ett fel i ett nytt nätverksbibliotek som används av Storage SDK. I 1.7.0 har ett försök att minska problemet gjorts, men vissa personer kan fortsätta att se problem. En fullständig korrigering kommer att släppas i en framtida uppdatering. #1090
* I 1.7.0 kommer dialogrutan för spara filer nu korrekt ihåg den senaste platsen du sparade en fil på. #16
* På egenskapspanelen visades SKU-nivån för ett lagringskonto som kontots slag. Problemet har åtgärdats. #654
* Ibland var det omöjligt att bryta hyresavtalet för en blob, även om du angav namnet på bloben korrekt. Problemet har åtgärdats. #1070

### <a name="known-issues"></a>Kända problem

* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanteringslager för att komma åt dina lagringsresurser. Mer information finns i [felsökningsguiden.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Det kan misslyckas att försöka komma åt ADLS Gen2 Blobbar när bakom en proxy.
* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Se #537 för mer information.
* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om du inte gör det avblockerar du dig, kommenterar du det här problemet.
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron. Om du vill lösa problemet när du laddar upp till eller hämtar från en blob-behållare kan du använda den experimentella AzCopy-funktionen.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Om du försöker använda dessa funktioner när du arbetar med Azure Stack-resurser kan det leda till oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Version 1.6.2
1/9/2019

### <a name="hotfixes"></a>Snabbkorrigeringar
* I 1.6.1 lades entiteter till ADLS Gen2 ACLs av ObjectId som inte var användare alltid som grupper. Nu läggs endast grupper till som grupper och entiteter som företagsprogram och huvudnamn läggs till som användare. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Om ett ADLS Gen2 Storage-konto inte hade några behållare och var kopplat till namn och nyckel, skulle Storage Explorer inte identifiera att lagringskontot var ADLS Gen2. Problemet har åtgärdats. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* I 1.6.0 skulle konflikter under kopiering och inklistring inte uppmana till en lösning. I stället skulle den konfliktfyllda kopian helt enkelt misslyckas. Nu, på den första konflikten, kommer du att bli tillfrågad hur du vill att det ska lösas. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* På grund av API-begränsningar har all validering av ObjectIds i dialogrutan Hantera åtkomst inaktiverats. Valideringen sker nu endast för användares UPN-nätverk. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* I dialogrutan ADLS Gen2 Manage Access kunde inte behörigheterna för en grupp ändras. Problemet har åtgärdats. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Lagt dra och släpp ladda upp stöd till ADLS Gen2 editor. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* URL-egenskapen i egenskapsdialogrutan för ADLS Gen2-filer och mappar saknades ibland ett /. Problemet har åtgärdats. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Om det går att hämta de aktuella behörigheterna för en ADLS Gen2-behållare, fil eller mapp, visas nu felet korrekt i aktivitetsloggen. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Den tillfälliga sökvägen som skapats för att öppna filer har förkortats för att minska risken för att skapa en sökväg som är längre än MAX_PATH i Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Dialogrutan Anslut visas nu korrekt när det inte finns några inloggade användare och inga resurser har kopplats. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* I 1.6.0 skulle sparande av egenskaper för icke-HNS Blobbar och filer koda värdet för varje egenskap. Detta resulterade i onödig kodning av värden som endast innehöll ASCII-tecken. Nu kodas värden endast om de innehåller icke-ASCII-tecken. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Det gick inte att överföra en mapp till en icke-HNS Blob-behållare om en SAS användes och SAS inte hade läsbehörighet. Problemet har åtgärdats. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Avbryta en AzCopy överföring fungerade inte. Problemet har åtgärdats. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy skulle misslyckas när du försöker hämta en mapp från en ADLS Gen2 Blob-behållare om mappen hade blanksteg i sitt namn. Problemet har åtgärdats. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* CosmosDB-redigeraren bröts på 1.6.0. Det är nu fast. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Ny

* Du kan nu använda Storage Explorer för att komma åt dina Blob-data via [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Om du är inloggad och Storage Explorer inte kan hämta nycklarna till ditt lagringskonto används en OAuth-token för att autentisera när du interagerar med dina data.
* Storage Explorer stöder nu ADLS Gen2 Storage-konton. När Storage Explorer upptäcker att hierarkiskt namnområde är aktiverat för ett lagringskonto visas "(ADLS Gen2 Preview)" bredvid namnet på ditt lagringskonto. Storage Explorer kan identifiera om hierarkiskt namnområde är aktiverat när du är inloggad eller om du har kopplat ditt lagringskonto med namn och nyckel. För ADLS Gen2 Storage-konton kan du använda Storage Explorer för att:
  * Skapa och ta bort behållare
  * Hantera behållaregenskaper och behörigheter (vänster sida)
  * Visa och navigera data inuti behållare
  * Skapa nya mappar
  * Ladda upp, ladda ned, byta namn på och ta bort filer och mappar
  * Hantera fil- och mappegenskaper och behörigheter (höger sida).
    
    Andra vanliga Blob-funktioner, till exempel Mjuk borttagning och Ögonblicksbilder, är för närvarande inte tillgängliga. Hantering av behörigheter är också endast tillgängligt när du är inloggad. Dessutom, när du arbetar i ett ADLS Gen2 Storage-konto, kommer Storage Explorer använda AzCopy för alla uppladdningar och nedladdningar och standard för att använda namn och nyckel autentiseringsuppgifter för alla åtgärder om sådana finns.
* Efter stark feedback från användaren kan pauslån återigen användas för att bryta lån på flera blobbar samtidigt.

### <a name="known-issues"></a>Kända problem

* När du hämtar från ett ADLS Gen2 Storage-konto, om en av de filer som överförs redan finns, kommer AzCopy ibland att krascha. Detta kommer att åtgärdas i en kommande snabbkorrigering.
* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om du inte gör det avblockerar du dig, kommenterar du det här problemet.
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron. Om du vill lösa problemet när du laddar upp till eller hämtar från en blob-behållare kan du använda den experimentella AzCopy-funktionen.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Om du försöker använda dessa funktioner när du arbetar med Azure Stack-resurser kan det leda till oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Version 1.6.1
12/18/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* På grund av API-begränsningar har all validering av ObjectIds i dialogrutan Hantera åtkomst inaktiverats. Valideringen sker nu endast för användares UPN-nätverk. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* I dialogrutan ADLS Gen2 Manage Access kunde inte behörigheterna för en grupp ändras. Problemet har åtgärdats. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Lagt dra och släpp ladda upp stöd till ADLS Gen2 editor. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* URL-egenskapen i egenskapsdialogrutan för ADLS Gen2-filer och mappar saknades ibland ett /. Problemet har åtgärdats. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Om det går att hämta de aktuella behörigheterna för en ADLS Gen2-behållare, fil eller mapp, visas nu felet korrekt i aktivitetsloggen. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Den tillfälliga sökvägen som skapats för att öppna filer har förkortats för att minska risken för att skapa en sökväg som är längre än MAX_PATH i Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Dialogrutan Anslut visas nu korrekt när det inte finns några inloggade användare och inga resurser har kopplats. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* I 1.6.0 skulle sparande av egenskaper för icke-HNS Blobbar och filer koda värdet för varje egenskap. Detta resulterade i onödig kodning av värden som endast innehöll ASCII-tecken. Nu kodas värden endast om de innehåller icke-ASCII-tecken. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Det gick inte att överföra en mapp till en icke-HNS Blob-behållare om en SAS användes och SAS inte hade läsbehörighet. Problemet har åtgärdats. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Avbryta en AzCopy överföring fungerade inte. Problemet har åtgärdats. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy skulle misslyckas när du försöker hämta en mapp från en ADLS Gen2 Blob-behållare om mappen hade blanksteg i sitt namn. Problemet har åtgärdats. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* CosmosDB-redigeraren bröts på 1.6.0. Det är nu fast. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Ny

* Du kan nu använda Storage Explorer för att komma åt dina Blob-data via [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Om du är inloggad och Storage Explorer inte kan hämta nycklarna till ditt lagringskonto används en OAuth-token för att autentisera när du interagerar med dina data.
* Storage Explorer stöder nu ADLS Gen2 Storage-konton. När Storage Explorer upptäcker att hierarkiskt namnområde är aktiverat för ett lagringskonto visas "(ADLS Gen2 Preview)" bredvid namnet på ditt lagringskonto. Storage Explorer kan identifiera om hierarkiskt namnområde är aktiverat när du är inloggad eller om du har kopplat ditt lagringskonto med namn och nyckel. För ADLS Gen2 Storage-konton kan du använda Storage Explorer för att:
  * Skapa och ta bort behållare
  * Hantera behållaregenskaper och behörigheter (vänster sida)
  * Visa och navigera data inuti behållare
  * Skapa nya mappar
  * Ladda upp, ladda ned, byta namn på och ta bort filer och mappar
  * Hantera fil- och mappegenskaper och behörigheter (höger sida).
    
    Andra vanliga Blob-funktioner, till exempel Mjuk borttagning och Ögonblicksbilder, är för närvarande inte tillgängliga. Hantering av behörigheter är också endast tillgängligt när du är inloggad. Dessutom, när du arbetar i ett ADLS Gen2 Storage-konto, kommer Storage Explorer använda AzCopy för alla uppladdningar och nedladdningar och standard för att använda namn och nyckel autentiseringsuppgifter för alla åtgärder om sådana finns.
* Efter stark feedback från användaren kan pauslån återigen användas för att bryta lån på flera blobbar samtidigt.

### <a name="known-issues"></a>Kända problem

* När du hämtar från ett ADLS Gen2 Storage-konto, om en av de filer som överförs redan finns, kommer AzCopy ibland att krascha. Detta kommer att åtgärdas i en kommande snabbkorrigering.
* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om du inte gör det avblockerar du dig, kommenterar du det här problemet.
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron. Om du vill lösa problemet när du laddar upp till eller hämtar från en blob-behållare kan du använda den experimentella AzCopy-funktionen.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Om du försöker använda dessa funktioner när du arbetar med Azure Stack-resurser kan det leda till oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Version 1.6.0
2018-12-05

### <a name="new"></a>Ny

* Du kan nu använda Storage Explorer för att komma åt dina Blob-data via [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Om du är inloggad och Storage Explorer inte kan hämta nycklarna till ditt lagringskonto används en OAuth-token för att autentisera när du interagerar med dina data.
* Storage Explorer stöder nu ADLS Gen2 Storage-konton. När Storage Explorer upptäcker att hierarkiskt namnområde är aktiverat för ett lagringskonto visas "(ADLS Gen2 Preview)" bredvid namnet på ditt lagringskonto. Storage Explorer kan identifiera om hierarkiskt namnområde är aktiverat när du är inloggad eller om du har kopplat ditt lagringskonto med namn och nyckel. För ADLS Gen2 Storage-konton kan du använda Storage Explorer för att:
  * Skapa och ta bort behållare
  * Hantera behållaregenskaper och behörigheter (vänster sida)
  * Visa och navigera data inuti behållare
  * Skapa nya mappar
  * Ladda upp, ladda ned, byta namn på och ta bort filer och mappar
  * Hantera fil- och mappegenskaper och behörigheter (höger sida).
    
    Andra vanliga Blob-funktioner, till exempel Mjuk borttagning och Ögonblicksbilder, är för närvarande inte tillgängliga. Hantering av behörigheter är också endast tillgängligt när du är inloggad. Dessutom, när du arbetar i ett ADLS Gen2 Storage-konto, kommer Storage Explorer använda AzCopy för alla uppladdningar och nedladdningar och standard för att använda namn och nyckel autentiseringsuppgifter för alla åtgärder om sådana finns.
* Efter stark feedback från användaren kan pauslån återigen användas för att bryta lån på flera blobbar samtidigt.

### <a name="known-issues"></a>Kända problem

* När du hämtar från ett ADLS Gen2 Storage-konto, om en av de filer som överförs redan finns, kommer AzCopy ibland att krascha. Detta kommer att åtgärdas i en kommande snabbkorrigering.
* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om du inte gör det avblockerar du dig, kommenterar du det här problemet.
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron. Om du vill lösa problemet när du laddar upp till eller hämtar från en blob-behållare kan du använda den experimentella AzCopy-funktionen.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Om du försöker använda dessa funktioner när du arbetar med Azure Stack-resurser kan det leda till oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Version 1.5.0
10/29/2018

### <a name="new"></a>Ny

* Du kan nu använda [AzCopy v10 (Preview)](https://github.com/Azure/azure-storage-azcopy) för att ladda upp och ladda ner Blobbar. För att aktivera den här funktionen gå till "Experimentell" menyn och klicka sedan på "Använd AzCopy för förbättrad Blob Ladda upp och ladda ner". När det är aktiverat används AzCopy i följande scenarier:
   * Ladda upp mappar och filer till blob behållare, antingen via verktygsfältet eller dra och släpp.
   * Nedladdning av mappar och filer, antingen via verktygsfältet eller snabbmenyn.

* Dessutom, när du använder AzCopy:
   * Du kan kopiera azkopi-kommandot som används för att köra överföringen till Urklipp. Klicka bara på "Kopiera AzCopy Command to Urklipp" i aktivitetsloggen.
   * Du måste uppdatera blob-redigeraren manuellt efter uppladdningen.
   * Det går inte att överföra filer till tilläggsblobar och vhd-filer laddas upp som sidblobar och alla andra filer laddas upp som blockblobar.
   * Fel och konflikter som uppstår under uppladdning eller nedladdning kommer inte att visas förrän en uppladdning eller nedladdning är klar.

Slutligen kommer stöd för att använda AzCopy med filaktier att komma i framtiden.
* Storage Explorer använder nu Electron version 2.0.11.
* Att bryta arrenden kan nu endast utföras på en blob i taget. Dessutom måste du ange namnet på bloben vars lån du bryter. Den här ändringen gjordes för att minska risken för att ett leasingavtal av misstag skulle brytas, särskilt för virtuella datorer. #394
* Om du stöter på inloggningsproblem kan du nu prova att återställa autentiseringen. Gå till "Hjälp"-menyn och klicka på "Återställ" för att komma åt den här funktionen. #419

### <a name="fix"></a>Snabbkorrigering

* Efter stark feedback från användaren har standardnoden emulator nod återaktiverats. Du kan fortfarande lägga till ytterligare emulatoranslutningar via dialogrutan Anslut, men om emulatorn är konfigurerad för att använda standardportarna kan du också använda noden "Emulator * Standardportar" under "Lokala & bifogade/lagringskonton". #669
* Storage Explorer låter dig inte längre ange blobmetadatavärden som har inledande eller avslutande blanktecken. #760
* Knappen "Logga in" var alltid aktiverad på samma sidor i dialogrutan Anslut. Det är nu inaktiverat när det är lämpligt. #761
* Snabbåtkomst genererar inte längre ett fel i konsolen när inga Snabbåtkomstobjekt har lagts till.

### <a name="known-issues"></a>Kända problem

* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om du inte gör det avblockerar du dig, kommenterar du det här problemet.
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron. Om du vill lösa problemet när du laddar upp till eller hämtar från en blob-behållare kan du använda den experimentella AzCopy-funktionen.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Om du försöker använda dessa funktioner när du arbetar med Azure Stack-resurser kan det leda till oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Version 1.4.4
10/15/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Api-versionen för Azure Resource Management har återställts för att avblockera Azure US Government-användare. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Laddar spinners använder nu CSS-animeringar för att minska mängden GPU som används av Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Ny
* Externa resurstillbehör, till exempel för SAS-anslutningar och emulatorer, har förbättrats avsevärt. Nu kan du:
   * Anpassa visningsnamnet för den resurs som du bifogar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Koppla till flera lokala emulatorer med olika portar. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Lägg till kopplade resurser i Snabbåtkomst. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer stöder nu Mjuk borttagning. Du kan:
   * Konfigurera en princip för mjuk borttagning genom att högerklicka på noden Blob Containers för ditt lagringskonto.
   * Visa mjuka borttagna blobbar i Blob Editor genom att välja "Aktiva och borttagna blobbar" i listrutan bredvid navigeringsfältet.
   * Ta bort mjuka borttagna blobbar.

### <a name="fixes"></a>Korrigeringar
* Åtgärden Konfigurera CORS-inställningar är inte längre tillgänglig på Premium Storage-konton eftersom Premium Storage-konton inte stöder CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Det finns nu en egenskap för signature för delad åtkomst för SAS-anslutna tjänster. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Åtgärden "Ange standardåtkomstnivå" är nu tillgänglig för Blob- och GPV2-lagringskonton som har fästs i Snabbåtkomst. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Ibland kunde Storage Explorer inte visa klassiska lagringskonton. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage Emulator eller Azurite, måste du låta dem lyssna efter anslutningar på sina standardportar. Annars kan Storage Explorer inte ansluta till dem.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om detta inte avblockeras kan du kommentera [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Version 1.4.3
10/11/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Api-versionen för Azure Resource Management har återställts för att avblockera Azure US Government-användare. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Laddar spinners använder nu CSS-animeringar för att minska mängden GPU som används av Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Ny
* Externa resurstillbehör, till exempel för SAS-anslutningar och emulatorer, har förbättrats avsevärt. Nu kan du:
   * Anpassa visningsnamnet för den resurs som du bifogar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Koppla till flera lokala emulatorer med olika portar. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Lägg till kopplade resurser i Snabbåtkomst. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer stöder nu Mjuk borttagning. Du kan:
   * Konfigurera en princip för mjuk borttagning genom att högerklicka på noden Blob Containers för ditt lagringskonto.
   * Visa mjuka borttagna blobbar i Blob Editor genom att välja "Aktiva och borttagna blobbar" i listrutan bredvid navigeringsfältet.
   * Ta bort mjuka borttagna blobbar.

### <a name="fixes"></a>Korrigeringar
* Åtgärden Konfigurera CORS-inställningar är inte längre tillgänglig på Premium Storage-konton eftersom Premium Storage-konton inte stöder CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Det finns nu en egenskap för signature för delad åtkomst för SAS-anslutna tjänster. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Åtgärden "Ange standardåtkomstnivå" är nu tillgänglig för Blob- och GPV2-lagringskonton som har fästs i Snabbåtkomst. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Ibland kunde Storage Explorer inte visa klassiska lagringskonton. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage Emulator eller Azurite, måste du låta dem lyssna efter anslutningar på sina standardportar. Annars kan Storage Explorer inte ansluta till dem.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om detta inte avblockeras kan du kommentera [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Version 1.4.2
09/24/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Uppdatera Azure Resource Management API-version till 2018-07-01 för att lägga till stöd för nya Azure Storage-kontotyper. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Ny
* Externa resurstillbehör, till exempel för SAS-anslutningar och emulatorer, har förbättrats avsevärt. Nu kan du:
   * Anpassa visningsnamnet för den resurs som du bifogar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Koppla till flera lokala emulatorer med olika portar. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Lägg till kopplade resurser i Snabbåtkomst. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer stöder nu Mjuk borttagning. Du kan:
   * Konfigurera en princip för mjuk borttagning genom att högerklicka på noden Blob Containers för ditt lagringskonto.
   * Visa mjuka borttagna blobbar i Blob Editor genom att välja "Aktiva och borttagna blobbar" i listrutan bredvid navigeringsfältet.
   * Ta bort mjuka borttagna blobbar.

### <a name="fixes"></a>Korrigeringar
* Åtgärden Konfigurera CORS-inställningar är inte längre tillgänglig på Premium Storage-konton eftersom Premium Storage-konton inte stöder CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Det finns nu en egenskap för signature för delad åtkomst för SAS-anslutna tjänster. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Åtgärden "Ange standardåtkomstnivå" är nu tillgänglig för Blob- och GPV2-lagringskonton som har fästs i Snabbåtkomst. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Ibland kunde Storage Explorer inte visa klassiska lagringskonton. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage Emulator eller Azurite, måste du låta dem lyssna efter anslutningar på sina standardportar. Annars kan Storage Explorer inte ansluta till dem.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om detta inte avblockeras kan du kommentera [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Version 1.4.1
08/28/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Vid första lanseringen kunde Storage Explorer inte generera nyckeln som användes för att kryptera känsliga data. Detta skulle orsaka problem när du använder Snabbåtkomst och bifoga resurser. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Om ditt konto inte krävde MFA för sin hemklient, men gjorde för vissa andra klienter, skulle Storage Explorer inte kunna lista prenumerationer. Nu, efter att ha loggat in med ett sådant konto, kommer Storage Explorer be dig att ange dina autentiseringsuppgifter igen och utföra MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Storage Explorer kunde inte koppla resurser från Azure Germany och Azure US Government. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Om du loggade in på två konton med samma e-postadress kan Storage Explorer ibland misslyckas med att visa dina resurser i trädvyn. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* På långsammare Windows-maskiner tar välkomstskärmen ibland en avsevärd tid att visas. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Anslutningsdialogrutan visas även om det fanns kopplade konton eller tjänster. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Ny
* Externa resurstillbehör, till exempel för SAS-anslutningar och emulatorer, har förbättrats avsevärt. Nu kan du:
   * Anpassa visningsnamnet för den resurs som du bifogar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Koppla till flera lokala emulatorer med olika portar. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Lägg till kopplade resurser i Snabbåtkomst. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer stöder nu Mjuk borttagning. Du kan:
   * Konfigurera en princip för mjuk borttagning genom att högerklicka på noden Blob Containers för ditt lagringskonto.
   * Visa mjuka borttagna blobbar i Blob Editor genom att välja "Aktiva och borttagna blobbar" i listrutan bredvid navigeringsfältet.
   * Ta bort mjuka borttagna blobbar.

### <a name="fixes"></a>Korrigeringar
* Åtgärden Konfigurera CORS-inställningar är inte längre tillgänglig på Premium Storage-konton eftersom Premium Storage-konton inte stöder CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Det finns nu en egenskap för signature för delad åtkomst för SAS-anslutna tjänster. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Åtgärden "Ange standardåtkomstnivå" är nu tillgänglig för Blob- och GPV2-lagringskonton som har fästs i Snabbåtkomst. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Ibland kunde Storage Explorer inte visa klassiska lagringskonton. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage Emulator eller Azurite, måste du låta dem lyssna efter anslutningar på sina standardportar. Annars kan Storage Explorer inte ansluta till dem.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om detta inte avblockeras kan du kommentera [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Version 1.3.0
07/09/2018

### <a name="new"></a>Ny
* Det går nu att komma åt $web behållare som används av statiska webbplatser. På så sätt kan du enkelt ladda upp och hantera filer och mappar som används av din webbplats. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* Appfältet på macOS har omorganiserats. Ändringarna inkluderar en Arkiv-meny, några genvägsändringar och flera nya kommandon under appmenyn. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Myndighetsslutpunkten för inloggning till Azure US Government har ändrats tillhttps://login.microsoftonline.us/
* Hjälpmedel: När en skärmläsare är aktiv fungerar tangentbordsnavigeringen nu med de tabeller som används för att visa objekt till höger. Du kan använda piltangenterna för att navigera i rader och kolumner, Ange för att anropa standardåtgärder, snabbmenytangenten för att öppna snabbmenyn för ett objekt och Skift eller Kontrollera för att flervälja. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Korrigeringar
*  På vissa maskiner tog barnprocesser lång tid att starta. När detta skulle hända, en "underordnad process misslyckades med att starta i tid" fel skulle visas. Den tid som avsatts för att en barnprocess ska starta har nu ökats från 20 till 90 sekunder. Om du fortfarande påverkas av det här problemet kan du kommentera det länkade GitHub-problemet. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* När du använder en SAS som inte hade läsbehörighet gick det inte att ladda upp en stor blob. Logiken för överföring har ändrats för att fungera i det här scenariot. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Om du anger den offentliga åtkomstnivån för en behållare tas alla åtkomstprinciper bort och vice versa. Nu bevaras offentlig åtkomstnivå och åtkomstprinciper när någon av de två ställs in. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" trunkerades i dialogrutan Egenskaper. Problemet har åtgärdats. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* Prefixet "Microsoft Azure Storage Explorer -" saknades i dialogrutan Skapa ny katalog. Problemet har åtgärdats. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Hjälpmedel: Dialogrutan Lägg till entitet var svår att navigera i när voiceover användes. Förbättringar har gjorts. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Hjälpmedel: Bakgrundsfärgen för knappen Dölj/expandera för fönstret Åtgärder och Egenskaper var inkonsekvent med liknande gränssnittskontroller i temat Svart hög kontrast. Färgen har ändrats. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Tillgänglighet: I svart hög kontrast var fokusstylingen för X-knappen i dialogrutan Egenskaper inte synlig. Problemet har åtgärdats. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Tillgänglighet: Flikarna Åtgärder och Egenskaper saknade flera ariavärden som resulterade i en undergruppsskärmsläsning. De saknade ariavärdena har nu lagts till. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Tillgänglighet: Kollapsade trädnoder på vänster sida fick inte ett aria-utökat värde av false. Problemet har åtgärdats. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Kända problem
* Att koppla från en resurs som är ansluten via SAS URI, till exempel en blob-behållare, kan orsaka ett fel som förhindrar att andra bilagor visas korrekt. Du kan undvika det här problemet genom att uppdatera gruppnoden. Mer information [finns i det här problemet.](https://github.com/Microsoft/AzureStorageExplorer/issues/537)
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om detta inte avblockeras kan du kommentera [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner och försök att använda dem när du arbetar med Azure Stack kan resultera i oväntade fel:
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Version 1.2.0
06/12/2018

### <a name="new"></a>Ny
* Om Storage Explorer inte kan läsa in prenumerationer från endast en delmängd av dina klienter visas alla inlästa prenumerationer tillsammans med ett felmeddelande specifikt för de klienter som misslyckades. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* I Windows, när en uppdatering är tillgänglig, kan du nu välja att "Uppdatera på Stäng". När det här alternativet har valts körs installationsprogrammet för uppdateringen när du har stängt Storage Explorer. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Återställningsögonblicksbild har lagts till på snabbmenyn i fildelningsredigeraren när en ögonblicksbild av filresursen visas. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Knappen Rensa kö är nu alltid aktiverad. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Stöd för inloggning till ADFS Azure Stack har återaktiverats. Azure Stack version 1804 eller mer krävs. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Korrigeringar
* Om du visade ögonblicksbilder för en filresurs vars namn var ett prefix för en annan filresurs i samma lagringskonto, visas även ögonblicksbilderna för den andra filresursen. Det här problemet har åtgärdats. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* När du bifogas via SAS, återställa en fil från en ögonblicksbild filresurs skulle resultera i ett fel. Det här problemet har åtgärdats. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* När ögonblicksbilder visades för en blob aktiverades åtgärden Befordra ögonblicksbild när basbloben och en enda ögonblicksbild valdes. Åtgärden är nu bara aktiverad om en enda ögonblicksbild har valts. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Om ett enda jobb (till exempel att hämta en blob) startades och senare misslyckades, skulle det inte automatiskt försöka igen förrän du startade ett annat jobb av samma typ. Alla jobb ska nu försöka igen automatiskt, oavsett hur många jobb du har köat.
* Redigerare som öppnats för nyligen skapade blob-behållare i GPV2- och Blob Storage-konton har ingen kolumn på åtkomstnivå. Det här problemet har åtgärdats. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* En accessnivåkolumn visas ibland inte när ett lagringskonto eller en blob-behållare har bifogats via SAS. Kolumnen visas nu alltid, men med ett tomt värde om det inte finns någon åtkomstnivå inställd. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Inställningen av åtkomstnivån för en nyligen uppladdad blockblob inaktiverades. Det här problemet har åtgärdats. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Om knappen "Håll fliken öppen" anropades med tangentbordet skulle tangentbordsfokus gå förlorad. Nu kommer fokus att gå vidare till fliken som hölls öppen. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* För en fråga i Frågeverktyget gav VoiceOver inte en användbar beskrivning av den aktuella operatorn. Det är nu mer beskrivande. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Sidnumreringslänkarna för de olika redaktörerna var inte beskrivande. De har ändrats för att vara mer beskrivande. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* I dialogrutan Lägg till entitet tillkännagav VoiceOver inte vilken kolumn ett indataelement ingick i. Namnet på den aktuella kolumnen ingår nu i beskrivningen av elementet. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Alternativknappar och kryssrutor hade ingen synlig kantlinje när de fokuserade. Det här problemet har åtgärdats. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage Emulator eller Azurite, måste du låta dem lyssna efter anslutningar på sina standardportar. Annars kan Storage Explorer inte ansluta till dem.
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om detta inte avblockeras kan du kommentera [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Version 1.1.0
05/09/2018

### <a name="new"></a>Ny
* Storage Explorer stöder nu användningen av Azurite. Anslutningen till Azurite är hårdkodad till standardutvecklande slutpunkter.
* Storage Explorer stöder nu åtkomstnivåer för endast Blob-konton och GPV2-lagringskonton. Läs mer om Åtkomstnivåer [här](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
* En starttid krävs inte längre när du skapar en SAS.

### <a name="fixes"></a>Korrigeringar
* Hämtningen av abonnemang för konton i den amerikanska regeringen bröts. Det här problemet har åtgärdats. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* Utgångstiden för åtkomstprinciper sparades inte korrekt. Det här problemet har åtgärdats. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* När du skapade en SAS-URL för ett objekt i en behållare lades inte namnet på objektet till i URL:en. Det här problemet har åtgärdats. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* När du skapar en SAS, utgångstider som är i det förflutna skulle ibland vara standardvärdet. Detta berodde på att Storage Explorer använde den senast använda start- och utgångstiden som standardvärden. Nu, varje gång du öppnar SAS-dialogrutan, skapas en ny uppsättning standardvärden. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Vid kopiering mellan lagringskonton genereras en 24-timmars SAS.When copy between Storage Accounts, a 24-hour SAS is generated. Om kopian varade i mer än 24 timmar misslyckas kopian. Vi har ökat SAS till sista 1 vecka för att minska risken för en kopia misslyckas på grund av en utgångna SAS. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* För vissa aktiviteter klicka på "Avbryt" skulle inte alltid fungera. Det här problemet har åtgärdats. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* För vissa aktiviteter var överföringshastigheten fel. Det här problemet har åtgärdats. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* Stavningen av "Föregående" på Visa-menyn var felaktig. Det stavas nu ordentligt. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Den sista sidan i Windows-installationsprogrammet hade en "Nästa"-knapp. Den har ändrats till knappen "Slutför". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Tabbfokus var inte synligt för knappar i dialogrutor när du använder hc black-temat. Det är nu synligt. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* Höljet för "Automatisk lösning" för åtgärder i aktivitetsloggen var felaktig. Det är nu korrekt. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* När du tar bort en entitet från en tabell visade dialogrutan där du om bekräftelse visade en felikon. Dialogrutan använder nu en varningsikon. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Kända problem
* Om du använder VS för Mac och någonsin har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Om du vill lösa problemet tar du bort innehållet i ~/. IdentityService/AadConfigurations. Om detta inte avblockeras kan du kommentera [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte genomfört alla lagrings-API:er. På grund av detta kan det finnas oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* Det går inte att ladda upp från OneDrive-mappen på grund av ett fel i NodeJS. Felet har åtgärdats, men ännu inte integrerats i Electron.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Version 1.0.0
04/16/2018

### <a name="new"></a>Ny
* Förbättrad autentisering som gör att Storage Explorer kan använda samma kontoarkiv som Visual Studio 2017. Om du vill använda den här funktionen måste du logga in på dina konton igen och ställa in dina filtrerade prenumerationer på ett liknande sätt.
* För Azure Stack-konton som backas upp av AAD hämtar Storage Explorer nu Azure Stack-prenumerationer när "Target Azure Stack" är aktiverat. Du behöver inte längre skapa en anpassad inloggningsmiljö.
* Flera genvägar har lagts till för att möjliggöra snabbare navigering. Dessa inkluderar växla olika paneler och flytta mellan redaktörer. Mer information finns på Visa-menyn.
* Feedback från Storage Explorer finns nu på GitHub. Du kan nå vår problemsida genom att klicka på feedbackknappen längst ned till vänster eller genom att gå till [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues). Ge gärna förslag, rapportera problem, ställa frågor eller lämna någon annan form av feedback.
* Om du stöter på problem med TLS/SSL-certifikat och inte kan hitta det felaktiga certifikatet `--ignore-certificate-errors` kan du nu starta Storage Explorer från kommandoraden med flaggan. När den här flaggan startas ignoreras TLS/SSL-certifikatfel.
* Det finns nu ett "Download"-alternativ i snabbmenyn för blob- och filobjekt.
* Förbättrat stöd för hjälpmedel och skärmläsare. Om du förlitar dig på hjälpmedelsfunktioner läser du vår [tillgänglighetsdokumentation](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) för mer information.
* Storage Explorer använder nu Electron 1.8.3

### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
* Storage Explorer har bytt till ett nytt autentiseringsbibliotek. Som en del av övergången till biblioteket måste du logga in på nytt på dina konton och ställa in dina filtrerade prenumerationer på nytt
* Den metod som används för att kryptera känsliga data har ändrats. Detta kan resultera i att vissa snabbåtkomstobjekt behöver läggas till på nytt och/eller att vissa av er har bifogade resurser som behöver sättas fast igen.

### <a name="fixes"></a>Korrigeringar
* Vissa användare bakom proxyservrar skulle ha grupp blob uppladdningar eller nedladdningar avbryts av en "Det går inte att lösa" felmeddelande. Det här problemet har åtgärdats.
* Om inloggning behövdes när du använde en direktlänk skulle det dyka upp en tom dialogruta om du klickar på "Logga in"-prompten. Det här problemet har åtgärdats.
* På Linux, om Storage Explorer inte kan starta på grund av en GPU-processkrasch, kommer du nu att informeras om kraschen, tillsagd att använda växeln "--disable-gpu" och Storage Explorer startar sedan automatiskt om med växeln aktiverad.
* Ogiltiga åtkomstprinciper var svåra att identifiera i dialogrutan Åtkomstprinciper. Ogiltiga åtkomstprincip-ID:er beskrivs nu i rött för mer synlighet.
* Aktivitetsloggen skulle ibland ha stora områden med blanktecken mellan de olika delarna av en aktivitet. Det här problemet har åtgärdats.
* Om du lämnade en tidsstämpelsats i ett ogiltigt tillstånd och sedan försökte ändra en annan sats i tabellfrågeredigeraren, skulle redigeraren låsas. Redigeraren återställer nu tidsstämpelklausulen till sitt senaste giltiga tillstånd när en ändring av en annan sats identifieras.
* Om du pausade medan du skrev i sökfrågan i trädvyn börjar sökningen och fokus skulle stjälas från textrutan. Nu måste du uttryckligen börja söka genom att trycka på "Enter"-tangenten eller genom att klicka på startsökknappen.
* Kommandot Hämta signatur för delad åtkomst inaktiveras ibland när du högerklickar på en fil i en filresurs. Det här problemet har åtgärdats.
* Om resursträdsnoden med fokus filtrerades bort under sökningen kunde du inte ta en flik i resursträdet och använda piltangenterna för att navigera i resursträdet. Nu, om den fokuserade resursträdnoden är dold, kommer den första noden i resursträdet att fokuseras automatiskt.
* En extra avgränsare visas ibland i verktygsfältet i redigeraren. Det här problemet har åtgärdats.
* Textrutan för brödsmulor svämmar ibland över. Det här problemet har åtgärdats.
* Redaktionen för Blob- och Fildelning uppdateras ibland ständigt när många filer laddas upp samtidigt. Det här problemet har åtgärdats.
* Funktionen Mappstatistik hade inget syfte i vyn Hantering av ögonblicksbilder av filresurs. Det har nu inaktiverats.
* På Linux visades inte Arkiv-menyn. Det här problemet har åtgärdats.
* När du överför en mapp till en filresurs laddades som standard endast innehållet i mappen upp. Nu är standardbeteendet att ladda upp innehållet i mappen till en matchande mapp i filresursen.
* Ordningen på knapparna i flera dialogrutor hade återförts. Det här problemet har åtgärdats.
* Olika säkerhetsrelaterade korrigeringar.

### <a name="known-issues"></a>Kända problem
* I sällsynta fall kan trädets fokus fastna på Snabbåtkomst. Om du vill ta upp fokus kan du uppdatera alla.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Version 0.9.6
02/28/2018

### <a name="fixes"></a>Korrigeringar
* Ett problem förhindrade att förväntade blobbar/filer listades i redigeraren. Det här problemet har åtgärdats.
* Ett problem orsakade att växling mellan ögonblicksbildvyer visade objekt felaktigt. Det här problemet har åtgärdats.

### <a name="known-issues"></a>Kända problem
* Storage Explorer stöder inte ADFS-konton.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Panelen kontoinställningar kan visa att du behöver ange autentiseringsuppgifter igen för att filtrera prenumerationer.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Version 0.9.5
02/06/2018

### <a name="new"></a>Ny

* Stöd för ögonblicksbilder av filresurser:
    * Skapa och hantera ögonblicksbilder för dina filresurser.
    * Växla enkelt vyer mellan ögonblicksbilder av dina filresurser medan du utforskar.
    * Återställ tidigare versioner av dina filer.
* Förhandsgranska stöd för Azure Data Lake Store:
    * Anslut till ADLS-resurserna för flera konton.
    * Anslut till och dela ADLS-resurser med ADL-urier.
    * Utför enkla fil-/mappåtgärder rekursivt.
    * Fäst enskilda mappar i Snabbåtkomst.
    * Visa mappstatistik.

### <a name="fixes"></a>Korrigeringar
* Förbättringar av startprestanda.
* Olika buggfixar.

### <a name="known-issues"></a>Kända problem
* Storage Explorer stöder inte ADFS-konton.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Panelen kontoinställningar kan visa att du behöver ange autentiseringsuppgifter igen för att filtrera prenumerationer.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Version 0.9.4 och 0.9.3
01/21/2018

### <a name="new"></a>Ny
* Ditt befintliga storage explorer-fönster används igen när:
    * Öppna direkta länkar som genereras i Storage Explorer.
    * Öppna Storage Explorer från portalen.
    * Öppnar Storage Explorer från Azure Storage VS-kodtillägg (kommer snart).
* Lade till möjligheten att öppna ett nytt Storage Explorer-fönster från Storage Explorer.
    * För Windows finns det ett "Nytt fönster" under Arkiv-menyn och i snabbmenyn i aktivitetsfältet.
    * För Mac finns det ett "nytt fönster" alternativ under App-menyn.

### <a name="fixes"></a>Korrigeringar
* Åtgärdade ett säkerhetsproblem. Vänligen uppgradera till 0.9.4 så snart som möjligt.
* Gamla aktiviteter städades inte upp på lämpligt sätt. Detta påverkade resultatet för långvariga jobb. De håller nu på att saneras på rätt sätt.
* Åtgärder som involverar ett stort antal filer och kataloger skulle ibland leda till att Storage Explorer fryser. Begäranden till Azure för filresurser begränsas nu för att begränsa användningen av systemresurser.

### <a name="known-issues"></a>Kända problem
* Storage Explorer stöder inte ADFS-konton.
* Kortkommandon för "Visa Utforskaren" och "Visa kontohantering" ska vara Ctrl/Cmd+Skift+E respektive Ctrl/Cmd+Skift+A.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Panelen kontoinställningar kan visa att du behöver ange autentiseringsuppgifter igen för att filtrera prenumerationer.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer --disable-gpu
    ```

* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Version 0.9.2
11/01/2017

### <a name="hotfixes"></a>Snabbkorrigeringar
* Oväntade dataändringar var möjliga när Edm.DateTime-värden redigerades för tabellentiteter beroende på den lokala tidszonen. Redigeraren använder nu en oformaterad textruta som ger exakt och konsekvent kontroll över Edm.DateTime-värden.
* Ladda upp/ladda ned en grupp blobbar när den är kopplad till namn och nyckel startar inte. Det här problemet har åtgärdats.
* Tidigare Lagringsutforskaren skulle bara uppmana dig att omauktorisera ett inaktuellt konto om en eller flera av kontots prenumerationer har valts. Nu kommer Storage Explorer att fråga dig även om kontot är helt filtrerat ut.
* Slutpunktsdomänen för Azure US Government var fel. Det har åtgärdats.
* Knappen Använd på panelen Hantera konton var ibland svår att klicka på. Detta bör inte längre hända.

### <a name="new"></a>Ny
* Förhandsgranska stöd för Azure Cosmos DB:
    * [Online-dokumentation](./cosmos-db/storage-explorer.md)
    * Skapa databaser och samlingar
    * Manipulera data
    * Fråga, skapa eller ta bort dokument
    * Uppdatera lagrade procedurer, användardefinierade funktioner eller utlösare
    * Använda anslutningssträngar för att ansluta till och hantera dina databaser
* Förbättrade prestanda för att ladda upp/ladda ner många små blobbar.
* Lade till en "Försök alla" om det finns fel i en blob-uppladdningsgrupp eller blob-hämtningsgrupp.
* Storage Explorer pausar nu iterationen under blob-uppladdning/hämtning om nätverksanslutningen har gått förlorad. Du kan sedan återuppta iterationen när nätverksanslutningen har återupprättats.
* Lade till möjligheten att "Stäng alla", "Stäng andra" och "Stäng" flikar via snabbmenyn.
* Storage Explorer använder nu inbyggda dialogrutor och inbyggda snabbmenyer.
* Storage Explorer är nu mer tillgänglig. Bland förbättringarna finns:
    * Förbättrat stöd för skärmläsare för NVDA i Windows och för VoiceOver på Mac
    * Förbättrad hög kontrast teman
    * Tangentbord tabbing och tangentbord fokus fixar

### <a name="fixes"></a>Korrigeringar
* Om du försökte öppna eller hämta en blob med ett ogiltigt Windows-filnamn misslyckas åtgärden. Storage Explorer identifierar nu om ett blob-namn är ogiltigt och frågar om du vill antingen koda det eller hoppa över blobben. Storage Explorer identifierar också om ett filnamn verkar vara kodat och fråga dig om du vill avkoda det innan du laddar upp det.
* Under blob-uppladdningen uppdateras ibland inte redigeraren för målblobbbehållaren korrekt. Det här problemet har åtgärdats.
* Stödet för flera former av anslutningssträngar och SAS-URI:er gick tillbaka. Vi har tagit itu med alla kända frågor, men skicka feedback om du stöter på ytterligare problem.
* Uppdateringsmeddelandet bröts för vissa användare i 0.9.0. Det här problemet har åtgärdats, och för dem som påverkas av felet kan du manuellt hämta den senaste versionen av Storage Explorer [här](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Kända problem
* Storage Explorer stöder inte ADFS-konton.
* Kortkommandon för "Visa Utforskaren" och "Visa kontohantering" ska vara Ctrl/Cmd+Skift+E respektive Ctrl/Cmd+Skift+A.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Panelen kontoinställningar kan visa att du behöver ange autentiseringsuppgifter igen för att filtrera prenumerationer.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer --disable-gpu
    ```

* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Version 0.9.1 och 0.9.0
10/20/2017
### <a name="new"></a>Ny
* Förhandsgranska stöd för Azure Cosmos DB:
    * [Online-dokumentation](./cosmos-db/storage-explorer.md)
    * Skapa databaser och samlingar
    * Manipulera data
    * Fråga, skapa eller ta bort dokument
    * Uppdatera lagrade procedurer, användardefinierade funktioner eller utlösare
    * Använda anslutningssträngar för att ansluta till och hantera dina databaser
* Förbättrade prestanda för att ladda upp/ladda ner många små blobbar.
* Lade till en "Försök alla" om det finns fel i en blob-uppladdningsgrupp eller blob-hämtningsgrupp.
* Storage Explorer pausar nu iterationen under blob-uppladdning/hämtning om nätverksanslutningen har gått förlorad. Du kan sedan återuppta iterationen när nätverksanslutningen har återupprättats.
* Lade till möjligheten att "Stäng alla", "Stäng andra" och "Stäng" flikar via snabbmenyn.
* Storage Explorer använder nu inbyggda dialogrutor och inbyggda snabbmenyer.
* Storage Explorer är nu mer tillgänglig. Bland förbättringarna finns:
    * Förbättrat stöd för skärmläsare för NVDA i Windows och för VoiceOver på Mac
    * Förbättrad hög kontrast teman
    * Tangentbord tabbing och tangentbord fokus fixar

### <a name="fixes"></a>Korrigeringar
* Om du försökte öppna eller hämta en blob med ett ogiltigt Windows-filnamn misslyckas åtgärden. Storage Explorer identifierar nu om ett blob-namn är ogiltigt och frågar om du vill antingen koda det eller hoppa över blobben. Storage Explorer identifierar också om ett filnamn verkar vara kodat och fråga dig om du vill avkoda det innan du laddar upp det.
* Under blob-uppladdningen uppdateras ibland inte redigeraren för målblobbbehållaren korrekt. Det här problemet har åtgärdats.
* Stödet för flera former av anslutningssträngar och SAS-URI:er gick tillbaka. Vi har tagit itu med alla kända frågor, men skicka feedback om du stöter på ytterligare problem.
* Uppdateringsmeddelandet bröts för vissa användare i 0.9.0. Det här problemet har åtgärdats, och för dem som påverkas av felet kan du manuellt hämta den senaste versionen av Storage Explorer [här](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Kända problem
* Storage Explorer stöder inte ADFS-konton.
* Kortkommandon för "Visa Utforskaren" och "Visa kontohantering" ska vara Ctrl/Cmd+Skift+E respektive Ctrl/Cmd+Skift+A.
* När du riktar in azure stack kan det misslyckas med att överföra vissa filer som tilläggsblobar.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta beror på att vi använder avbryt filter arbete runt beskrivs här.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Panelen kontoinställningar kan visa att du behöver ange autentiseringsuppgifter igen för att filtrera prenumerationer.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Elektronskalet som används av Storage Explorer har problem med viss GPU (grafikprocessorenhet) maskinvaruacceleration. Om Storage Explorer visar ett tomt (tomt) huvudfönster kan du prova att starta Storage Explorer `--disable-gpu` från kommandoraden och inaktivera GPU-acceleration genom att lägga till växeln:

    ```
    ./StorageExplorer --disable-gpu
    ```

* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Version 0.8.16
8/21/2017

### <a name="new"></a>Ny
* När du öppnar en blob uppmanar Storage Explorer dig att ladda upp den nedladdade filen om en ändring upptäcks
* Förbättrad inloggning från Azure Stack
* Förbättrad prestanda för att ladda upp/ladda ner många små filer samtidigt


### <a name="fixes"></a>Korrigeringar
* För vissa blob-typer kan det ibland leda till att överföringen startas om om om du väljer att "ersätta" under en uppladdningskonflikt.
* I version 0.8.15, uppladdningar skulle ibland stall på 99%.
* När du laddar upp filer till en filresurs, om du valde att ladda upp till en katalog som ännu inte fanns, skulle din uppladdning misslyckas.
* Storage Explorer genererade felaktigt tidsstämplar för signaturer och tabellfrågor med delad åtkomst.


### <a name="known-issues"></a>Kända problem
* Det fungerar inte att använda en namn- och nyckelanslutningssträng. Det kommer att fastställas i nästa utgåva. Tills dess kan du använda bifoga med namn och nyckel.
* Om du försöker öppna en fil med ett ogiltigt Windows-filnamn resulterar hämtningen i ett fel på en fil.
* När du har klickat på "Avbryt" på en aktivitet kan det ta ett tag innan aktiviteten avbryts. Detta är en begränsning av Azure Storage Node-biblioteket.
* När du har slutfört en blob-uppladdning uppdateras fliken som initierade överföringen. Detta är en förändring från tidigare beteende, och kommer också att orsaka att du tas tillbaka till roten av behållaren du befinner dig i.
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma det beslutet.
* Panelen kontoinställningar kan visa att du behöver ange autentiseringsuppgifter igen för att filtrera prenumerationer.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* För användare på Ubuntu 14.04 måste du se till att GCC är uppdaterat - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17.04 måste du installera GConf - detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Version 0.8.14
06/22/2017

### <a name="new"></a>Ny

* Uppdaterad Electron version till 1.7.2 för att dra nytta av flera kritiska säkerhetsuppdateringar
* Nu kan du snabbt komma åt felsökningsguiden online från hjälpmenyn
* [Felsökningsguide för][2] Lagringsutforskaren
* [Instruktioner][3] för hur du ansluter till en Azure Stack-prenumeration

### <a name="known-issues"></a>Kända problem

* Knapparna i dialogrutan för bekräftelse av borttagningsmappen registreras inte med musklick på Linux. komma runt är att använda Enter-tangenten
* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma beslutet
* Om du har fler än tre grupper av blobbar eller filer som laddas upp samtidigt kan det orsaka fel
* Panelen kontoinställningar kan visa att du behöver ange autentiseringsuppgifter igen för att kunna filtrera prenumerationer
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Ubuntu 14,04 installera behöver GCC version uppdateras eller uppgraderas - steg för att uppgradera är nedan:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Version 0.8.13
05/12/2017

#### <a name="new"></a>Ny

* [Felsökningsguide för][2] Lagringsutforskaren
* [Instruktioner][3] för hur du ansluter till en Azure Stack-prenumeration

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: Filuppladdningen hade stor chans att orsaka ett fel i minnet
* Åtgärdat: Nu kan du logga in med PIN/Smartcard
* Åtgärdat: Öppna i Portal fungerar nu med Azure China 21Vianet, Azure Germany, Azure US Government och Azure Stack
* Åtgärdat: När du laddar upp en mapp till en blob-behållare uppstår ibland ett fel i "Olaglig åtgärd"
* Åtgärdat: Markera alla inaktiverades när ögonblicksbilder hanterades
* Åtgärdat: Metadata för basbloben kan skrivas över när egenskaperna för dess ögonblicksbilder har visas

#### <a name="known-issues"></a>Kända problem

* Om du väljer fel PIN/Smartcard-certifikat måste du starta om för att Storage Explorer ska glömma beslutet
* När zoomas in eller ut kan zoomnivån tillfälligt återställas till standardnivån
* Om du har fler än tre grupper av blobbar eller filer som laddas upp samtidigt kan det orsaka fel
* Panelen kontoinställningar kan visa att du behöver ange autentiseringsuppgifter igen för att kunna filtrera prenumerationer
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Ubuntu 14,04 installera behöver GCC version uppdateras eller uppgraderas - steg för att uppgradera är nedan:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Version 0.8.12 och 0.8.11 och 0.8.10
04/07/2017

#### <a name="new"></a>Ny

* Storage Explorer stängs nu automatiskt när du installerar en uppdatering från uppdateringsmeddelandet
* Snabb åtkomst på plats ger en förbättrad upplevelse för att arbeta med dina resurser som används ofta
* I Blob Container-redigeraren kan du nu se vilken virtuell dator en leasad blob tillhör
* Du kan nu dölja den vänstra sidopanelen
* Discovery körs nu samtidigt som nedladdning
* Använd Statistik i eredererarna Blob Container, Fildelning och Tabell för att se storleken på din resurs eller markering
* Du kan nu logga in på Azure Active Directory (AAD) baserade Azure Stack-konton.
* Du kan nu ladda upp arkivfiler över 32 MB till Premium-lagringskonton
* Förbättrat hjälpmedelsstöd
* Du kan nu lägga till betrodda Base-64-kodade X.509 TLS/SSL-certifikat genom att gå till Redigera -&gt; SSL-certifikat -&gt; Importcertifikat

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: när ett konto har uppdaterats uppdateras uppdateras trädvyn ibland inte automatiskt
* Åtgärdat: om du skapar en SAS för emulatorköer och tabeller skulle det leda till en ogiltig URL
* Fast: Premium lagringskonton kan nu utökas medan en proxy är aktiverad
* Åtgärdat: knappen tillämpa på kontohanteringssidan skulle inte fungera om du hade valt 1 eller 0 konton
* Åtgärdat: uppladdning av blobbar som kräver konfliktlösningar kan misslyckas - fast i 0.8.11
* Fast: skicka feedback bröts i 0.8.11 - fast i 0.8.12

#### <a name="known-issues"></a>Kända problem

* När du har uppgraderat till 0.8.10 måste du uppdatera alla dina autentiseringsuppgifter.
* När zoomas in eller ut kan zoomnivån tillfälligt återställas till standardnivån.
* Om du har fler än tre grupper av blobbar eller filer som laddas upp samtidigt kan det orsaka fel.
* Panelen kontoinställningar kan visa att du behöver ange autentiseringsuppgifter igen för att filtrera prenumerationer.
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack för närvarande inte stöder filresurser visas fortfarande en nod för filresurser under ett bifogat Azure Stack-lagringskonto.
* Ubuntu 14,04 installera behöver GCC version uppdateras eller uppgraderas - steg för att uppgradera är nedan:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Version 0.8.9 och 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Ny

* Storage Explorer 0.8.9 hämtar automatiskt den senaste versionen för uppdateringar.
* Snabbkorrigering: om du använder en portalgenererad SAS URI för att koppla ett lagringskonto skulle resultera i ett fel.
* Du kan nu skapa, hantera och befordra blob-ögonblicksbilder.
* Nu kan du logga in på Azure China 21Vianet-, Azure Germany- och Azure US Government-konton.
* Nu kan du ändra zoomnivån. Använd alternativen på Visa-menyn för att zooma in, zooma ut och återställa zoom.
* Unicode-tecken stöds nu i användarmetadata för blobbar och filer.
* Förbättringar av tillgängligheten.
* Nästa versions viktig information kan visas från uppdateringsmeddelandet. Du kan också visa de aktuella versionsanteckningarna på Hjälp-menyn.

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: versionsnumret visas nu korrekt på Kontrollpanelen i Windows
* Åtgärdat: sökningen är inte längre begränsad till 50 000 noder
* Åtgärdat: ladda upp till en filresurs som snurrades för alltid om målkatalogen inte redan fanns
* Fast: förbättrad stabilitet för långa uppladdningar och nedladdningar

#### <a name="known-issues"></a>Kända problem

* När zoomas in eller ut kan zoomnivån tillfälligt återställas till standardnivån.
* Snabbåtkomst fungerar bara med prenumerationsbaserade objekt. Lokala resurser eller resurser som är kopplade via nyckel eller SAS-token stöds inte i den här versionen.
* Det kan ta snabb åtkomst några sekunder att navigera till målresursen, beroende på hur många resurser du har.
* Om du har fler än tre grupper av blobbar eller filer som laddas upp samtidigt kan det orsaka fel.

12/16/2016
### <a name="version-087"></a>Version 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Ny

* Du kan välja hur du vill lösa konflikter i början av en uppdaterings-, hämtnings- eller kopieringssession i fönstret Aktiviteter
* Hovra över en flik för att se lagringsresursens fullständiga sökväg
* När du klickar på en flik synkroniseras den med dess plats i navigeringsfönstret på vänster sida

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: Storage Explorer är nu en betrodd app på Mac
* Fast: Ubuntu 14,04 stöds igen
* Åtgärdat: Ibland blinkar användargränssnittet för lägg till konto när prenumerationer läses in
* Åtgärdat: Ibland visas inte alla lagringsresurser i navigeringsfönstret på vänster sida
* Åtgärdat: Åtgärdsfönstret visade ibland tomma åtgärder
* Åtgärdat: Fönsterstorleken från den senast stängda sessionen behålls nu
* Åtgärdat: Du kan öppna flera flikar för samma resurs med snabbmenyn

#### <a name="known-issues"></a>Kända problem

* Snabbåtkomst fungerar bara med prenumerationsbaserade objekt. Lokala resurser eller resurser som är kopplade via nyckel eller SAS-token stöds inte i den här versionen
* Det kan ta snabbåtkomst några sekunder att navigera till målresursen, beroende på hur många resurser du har
* Om du har fler än tre grupper av blobbar eller filer som laddas upp samtidigt kan det orsaka fel
* Sökhandtag sökning över ungefär 50.000 noder - efter detta kan prestanda påverkas eller kan orsaka ohanterade undantag
* För första gången med lagringsutforskaren på macOS kan du se flera uppmaningar som ber om användarens behörighet att komma åt nyckelring. Vi föreslår att du väljer Tillåt alltid så att uppmaningen inte visas igen

11/18/2016
### <a name="version-086"></a>Version 0.8.6

#### <a name="new"></a>Ny

* Du kan nu fästa de vanligaste tjänsterna på Snabbåtkomst för enkel navigering
* Du kan nu öppna flera redigerare på olika flikar. Klicka en gång om för att öppna en tillfällig flik. dubbelklicka för att öppna en permanent flik. Du kan också klicka på den tillfälliga fliken för att göra det till en permanent flik
* Vi har gjort märkbara prestanda- och stabilitetsförbättringar för uppladdningar och nedladdningar, särskilt för stora filer på snabba maskiner
* Tomma "virtuella" mappar kan nu skapas i blob-behållare
* Vi har återinfört scoped sökning med vår nya förbättrade substring sökning, så du har nu två alternativ för att söka:
    * Global sökning - skriv bara in en sökterm i söktextrutan
    * Scoped sökning - klicka på förstoringsglas ikonen bredvid en nod, sedan lägga till en sökterm i slutet av sökvägen, eller högerklicka och välj "Sök härifrån"
* Vi har lagt till olika teman: Ljus (standard), Mörk, Svart hög kontrast och vit med hög kontrast. Gå till&gt; Redigera – Teman för att ändra inställningarna för teman
* Du kan ändra Blob- och filegenskaper
* Vi stöder nu kodade (base64) och okodade kömeddelanden
* På Linux krävs nu ett 64-bitars OPERATIVSYSTEM. För den här versionen stöder vi endast 64-bitars Ubuntu 16.04.1 LTS
* Vi har uppdaterat vår logotyp!

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: Problem med skärmfrysning
* Åtgärdat: Ökad säkerhet
* Åtgärdat: Ibland kan dubblettanslutna konton visas
* Åtgärdat: En blob med en odefinierad innehållstyp kan generera ett undantag
* Åtgärdat: Det gick inte att öppna frågepanelen på en tom tabell
* Åtgärdat: Varierar buggar i Sök
* Åtgärdat: Ökade antalet resurser som laddades från 50 till 100 när du klickar på "Ladda mer"
* Åtgärdat: Vid första körningen, om ett konto är inloggad, väljer vi nu alla prenumerationer för det kontot som standard

#### <a name="known-issues"></a>Kända problem

* Den här versionen av Storage Explorer körs inte på Ubuntu 14.04
* Om du vill öppna flera flikar för samma resurs klickar du inte kontinuerligt på samma resurs. Klicka på en annan resurs och gå sedan tillbaka och klicka sedan på den ursprungliga resursen för att öppna den igen på en annan flik
* Snabbåtkomst fungerar bara med prenumerationsbaserade objekt. Lokala resurser eller resurser som är kopplade via nyckel eller SAS-token stöds inte i den här versionen
* Det kan ta snabbåtkomst några sekunder att navigera till målresursen, beroende på hur många resurser du har
* Om du har fler än tre grupper av blobbar eller filer som laddas upp samtidigt kan det orsaka fel
* Sökhandtag sökning över ungefär 50.000 noder - efter detta kan prestanda påverkas eller kan orsaka ohanterade undantag

10/03/2016
### <a name="version-085"></a>Version 0.8.5

#### <a name="new"></a>Ny

* Kan nu använda Portalgenererade SAS-nycklar för att koppla till lagringskonton och resurser

#### <a name="fixes"></a>Korrigeringar

* Fast: ras tillstånd under sökning ibland orsakade noder att bli icke-utbyggbara
* Åtgärdat: "Använd HTTP" fungerar inte när du ansluter till lagringskonton med kontonamn och -nyckel
* Åtgärdat: SAS-nycklar (speciellt portalgenererade) returnerar ett "avslutande snedstreck" fel
* Åtgärdat: problem med tabellimport
    * Ibland har partitionsnyckel och radnyckel återförts
    * Det går inte att läsa "null"-partitionsnycklar

#### <a name="known-issues"></a>Kända problem

* Sökhandtag sökning över cirka 50.000 noder - efter detta kan prestanda påverkas
* Azure Stack stöder för närvarande inte filer, så om du försöker expandera filer visas ett fel

09/12/2016
### <a name="version-084"></a>Version 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Ny

* Generera direkta länkar till lagringskonton, behållare, köer, tabeller eller filresurser för delning och enkel åtkomst till dina resurser – stöd för Windows och Mac OS
* Sök efter blob-behållare, tabeller, köer, filresurser eller lagringskonton från sökrutan
* Du kan nu gruppera satser i tabellfrågebyggaren
* Byta namn på och kopiera/klistra in blob-behållare, filresurser, tabeller, blobbar, blob-mappar, filer och kataloger från SAS-bifogade konton och behållare
* Byta namn på och kopiera blob-behållare och filresurser bevarar nu egenskaper och metadata

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: det går inte att redigera tabellentiteter om de innehåller booleska eller binära egenskaper

#### <a name="known-issues"></a>Kända problem

* Sökhandtag sökning över cirka 50.000 noder - efter detta kan prestanda påverkas

08/03/2016
### <a name="version-083"></a>Version 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Ny

* Byta namn på behållare, tabeller, filresurser
* Förbättrad frågebyggare
* Möjlighet att spara och läsa in frågor
* Direkta länkar till lagringskonton eller behållare, köer, tabeller eller filresurser för delning och enkelt åtkomst till dina resurser (Endast Windows - macOS-stöd kommer snart!)
* Förmåga att hantera och konfigurera CORS-regler

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: Microsoft-konton kräver återautentisering var 8–12:e timme

#### <a name="known-issues"></a>Kända problem

* Ibland kan användargränssnittet verka fryst - om du maximerar fönstret kan du lösa problemet
* macOS-installation kan kräva förhöjda behörigheter
* Panelen Kontoinställningar kan visa att du behöver ange autentiseringsuppgifter igen för att kunna filtrera prenumerationer
* Om du byter namn på filresurser, blob-behållare och tabeller bevaras inte metadata eller andra egenskaper i behållaren, till exempel filresurskvot, offentlig åtkomstnivå eller åtkomstprinciper
* Om du byter namn på blobbar (individuellt eller inuti en omdöpt blob-behållare) bevaras inte ögonblicksbilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte
* Det fungerar inte att kopiera eller byta namn på resurser i SAS-kopplade konton

07/07/2016
### <a name="version-082"></a>Version 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Ny

* Lagringskonton grupperas efter prenumerationer. utvecklingslagring och resurser som bifogas via nyckel- eller SAS visas under (Lokal och Bifogad) nod
* Logga ut från konton på panelen "Azure-kontoinställningar"
* Konfigurera proxyinställningar för att aktivera och hantera inloggning
* Skapa och bryta blob-lån
* Öppna blob-behållare, köer, tabeller och filer med ett enda klick

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: kömeddelanden som infogats med .NET- eller Java-bibliotek avkodas inte korrekt från base64
* Åtgärdat: $metrics tabeller visas inte för Blob Storage-konton
* Fast: tabellnod fungerar inte för lokal (utveckling) lagring

#### <a name="known-issues"></a>Kända problem

* macOS-installation kan kräva förhöjda behörigheter

06/15/2016
### <a name="version-080"></a>Version 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Ny

* Stöd för fildelning: visa, ladda upp, ladda ned, kopiera filer och kataloger, SAS-urier (skapa och ansluta)
* Förbättrad användarupplevelse för anslutning till lagring med SAS-urier eller kontonycklar
* Exportera tabellfrågeresultat
* Tabellkolumn ändra ordning och anpassning
* Visa $logs blob-behållare och $metrics tabeller för lagringskonton med aktiverade mått
* Förbättrat export- och importbeteende, innehåller nu egenskapsvärdetyp

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: uppladdning eller nedladdning av stora blobbar kan resultera i ofullständiga uppladdningar/nedladdningar
* Åtgärdat: om du redigerar, lägger till eller importerar en entitet med ett numeriskt strängvärde ("1") konverteras den till dubbel
* Åtgärdat: Det gick inte att expandera tabellnoden i den lokala utvecklingsmiljön

#### <a name="known-issues"></a>Kända problem

* $metrics tabeller visas inte för Blob Storage-konton
* Kömeddelanden som läggs till programmässigt kanske inte visas korrekt om meddelandena kodas med Base64-kodning

05/17/2016
### <a name="version-07201605090"></a>Version 0.7.20160509.0

#### <a name="new"></a>Ny

* Bättre felhantering för appkrascher

#### <a name="fixes"></a>Korrigeringar

* Fast bugg där InfoBar-meddelanden ibland inte visas när inloggningsuppgifter krävdes

#### <a name="known-issues"></a>Kända problem

* Tabeller: Lägga till, redigera eller importera en entitet som har en egenskap med ett tvetydigt numeriskt värde, till exempel "1" eller "1.0", och användaren försöker skicka den som en `Edm.String`, kommer värdet att komma tillbaka via klient-API:et som en Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Version 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Ny

* Tabellstöd: visa, fråga, exportera, importera och CRUD-åtgärder för entiteter
* Köstöd: visa, lägga till, dequeueing meddelanden
* Generera SAS-URI:er för lagringskonton
* Ansluta till lagringskonton med SAS-URI:er
* Uppdatera meddelanden för framtida uppdateringar av Storage Explorer
* Uppdaterat utseende och känsla

#### <a name="fixes"></a>Korrigeringar

* Prestanda- och tillförlitlighetsförbättringar

### <a name="known-issues-amp-mitigations"></a>Kända &amp; problem mildrande åtgärder

* Nedladdning av stora blob-filer fungerar inte korrekt - vi rekommenderar att du använder AzCopy medan vi tar itu med detta problem
* Kontouppgifter hämtas inte eller cachelagras om hemmappen inte kan hittas eller inte kan skrivas till
* Om vi lägger till, redigerar eller importerar en entitet som har en egenskap med ett tvetydigt numeriskt värde, till exempel `Edm.String`"1" eller "1.0", och användaren försöker skicka den som en kommer värdet tillbaka via klient-API:et som en Edm.Double
* När du importerar CSV-filer med flerradiga poster kan data hackas eller förvrängas

02/03/2016

### <a name="version-07201601291"></a>Version 0.7.20160129.1

#### <a name="fixes"></a>Korrigeringar

* Förbättrad övergripande prestanda vid uppladdning, nedladdning och kopiering av blobbar

01/14/2016

### <a name="version-07201601050"></a>Version 0.7.20160105.0

#### <a name="new"></a>Ny

* Linux-stöd (paritetsfunktioner till OSX)
* Lägga till blob-behållare med SAS-tangenten (Shared Access Signatures)
* Lägga till lagringskonton för Azure China 21Vianet
* Lägga till lagringskonton med anpassade slutpunkter
* Öppna och visa innehållet text och bildblobar
* Visa och redigera blobegenskaper och metadata

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: att ladda upp eller ladda ned ett stort antal blobbar (500+) kan ibland leda till att appen har en vit skärm
* Åtgärdat: När du ställer in offentlig åtkomstnivå för blob-behållare uppdateras inte det nya värdet förrän du ställer in fokus på behållaren igen. Dialogrutan är också alltid standard för "Ingen offentlig åtkomst" och inte det faktiska aktuella värdet.
* Bättre stöd för tangentbord/hjälpmedel och användargränssnitt
* Text i brödsmulor radbryts när den är lång med tomt utrymme
* SAS-dialogrutan stöder validering av indata
* Lokal lagring fortsätter att vara tillgänglig även om användaruppgifterna har upphört att gälla
* När en öppnad blob-behållare tas bort stängs blob explorer på höger sida

#### <a name="known-issues"></a>Kända problem

* Linux installera behöver GCC version uppdateras eller uppgraderas - steg för att uppgradera är nedan:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Version 0.7.20151116.0

#### <a name="new"></a>Ny

* macOS- och Windows-versioner
* Logga in för att visa dina lagringskonton – använd ditt Organisationskonto, Microsoft-konto, 2FA osv.
* Lagring av lokal utveckling (använd lagringsemulator, endast Windows)
* Azure Resource Manager och klassisk resurssupport
* Skapa och ta bort blobbar, köer eller tabeller
* Sök efter specifika blobbar, köer eller tabeller
* Utforska innehållet i blob-behållare
* Visa och navigera genom kataloger
* Ladda upp, hämta och ta bort blobbar och mappar
* Visa och redigera blobegenskaper och metadata
* Generera SAS-nycklar
* Hantera och skapa SAP (Stored Access Policies)
* Sök efter blobbar efter prefix
* Dra 'n släpp filer för att ladda upp eller ladda ner

#### <a name="known-issues"></a>Kända problem

* När du ställer in offentlig åtkomstnivå för blob-behållare uppdateras inte det nya värdet förrän du ställer in fokus på behållaren igen
* När du öppnar dialogrutan för att ange den offentliga åtkomstnivån visas alltid "Ingen offentlig åtkomst" som standard och inte det faktiska aktuella värdet
* Det går inte att byta namn på hämtade blobbar
* Aktivitetsloggposter fastnar ibland i ett pågående tillstånd när ett fel inträffar och felet visas inte
* Ibland kraschar eller blir helt vit när man försöker ladda upp eller ladda ner ett stort antal blobbar
* Ibland avbryter en kopiering fungerar inte
* När du skapar en behållare (blob/kö/tabell) kan du inte ange fokus på den nya typen av information om du anger ett ogiltigt namn och fortsätter att skapa ett annat under en annan behållartyp.
* Det går inte att skapa en ny mapp eller byta namn på mapp




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
