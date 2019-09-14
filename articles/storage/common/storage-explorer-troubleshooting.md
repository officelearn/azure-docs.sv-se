---
title: Azure Storage Explorer fel söknings guide | Microsoft Docs
description: Översikt över fel söknings tekniker för Azure Storage Explorer
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3a95d52f2a0aa07094f91b0653fcb94ff1f88d44
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959004"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage Explorer fel söknings guide

Microsoft Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage data på Windows, macOS och Linux. Appen kan ansluta till lagrings konton som finns i Azure, nationella moln och Azure Stack.

Den här guiden sammanfattar lösningar för vanliga problem som visas i Storage Explorer.

## <a name="role-based-access-control-permission-issues"></a>Rollbaserade Access Control behörighets problem

[Rollbaserad åtkomst kontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) ger detaljerad åtkomst hantering av Azure-resurser genom att kombinera uppsättningar med behörigheter i _roller_. Här följer några förslag som du kan följa för att få RBAC-arbete i Storage Explorer.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Vad behöver jag för att kunna se mina resurser i Storage Explorer?

Om du har problem med att komma åt lagrings resurser med RBAC kan det bero på att du inte har tilldelats lämpliga roller. I följande avsnitt beskrivs de behörigheter som Storage Explorer för närvarande kräver åtkomst till dina lagrings resurser.

Kontakta administratören för Azure-kontot om du är osäker på om du har lämpliga roller eller behörigheter.

#### <a name="read-listget-storage-accounts"></a>Läs Lista/Hämta lagrings konto (n)

Du måste ha behörighet att lista lagrings konton. Du kan få den här behörigheten genom att tilldela rollen "läsare".

#### <a name="list-storage-account-keys"></a>Lista nycklar för lagringskonto

Storage Explorer kan också använda konto nycklar för att autentisera begär Anden. Du kan få åtkomst till nycklar med mer kraftfulla roller, t. ex. rollen deltagare.

> [!NOTE]
> Åtkomst nycklar beviljar obegränsade behörigheter till alla som innehar dem. Därför rekommenderar vi vanligt vis inte att de blir tillgängliga för användare. Om du behöver återkalla åtkomst nycklar kan du återskapa dem från [Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Data roller

Du måste tilldelas minst en roll som ger åtkomst till Läs data från resurser. Om du till exempel behöver lista eller hämta blobbar behöver du minst ha rollen "Storage BLOB data Reader".

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Varför behöver jag en hanterings skikt roll för att se mina resurser i Storage Explorer?

Azure Storage har två åtkomst nivåer: _hantering_ och _data_. Prenumerationer och lagrings konton nås via hanterings skiktet. Behållare, blobbar och andra data resurser nås via data lagret. Om du till exempel vill hämta en lista över dina lagrings konton från Azure skickar du en begäran till hanterings slut punkten. Om du vill ha en lista över BLOB-behållare i ett konto skickar du en begäran till lämplig tjänst slut punkt.

RBAC-roller kan innehålla behörigheter för hantering eller åtkomst till data lager. Rollen "läsare" ger till exempel till gång till skrivskyddade åtkomst hanterings lager resurser.

Det är strikt att tala om att rollen "läsare" inte har några data lager behörigheter och inte behövs för åtkomst till data skiktet.

Storage Explorer gör det enkelt att komma åt dina resurser genom att samla in nödvändig information för att ansluta till dina Azure-resurser åt dig. Om du till exempel vill visa dina BLOB-behållare, Storage Explorer skicka en lista med behållare till BLOB-tjänstens slut punkt. Storage Explorer söker i listan över prenumerationer och lagrings konton som du har åtkomst till för att hämta slut punkten. Men för att hitta dina prenumerationer och lagrings konton behöver Storage Explorer också åtkomst till hanterings lagret.

Om du inte har någon roll som beviljar behörigheter för hanterings lager kan Storage Explorer inte hämta den information som krävs för att ansluta till data lagret.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Vad händer om jag inte kan hämta de behörigheter för hanterings skikt jag behöver från min administratör?

Vi har inte någon RBAC-relaterad lösning än för tillfället. Som en lösning kan du begära en SAS-URI för att [ansluta till din resurs](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fel: Självsignerat certifikat i certifikat kedjan (och liknande fel)

Certifikat fel orsakas av någon av följande två situationer:

1. Appen är ansluten via en "transparent proxy", vilket innebär att en server (till exempel din företags server) fångar upp HTTPS-trafik, dekrypterar den och sedan krypterar den med hjälp av ett självsignerat certifikat.
2. Du kör ett program som matar in ett självsignerat SSL-certifikat i de HTTPS-meddelanden som du får. Exempel på program som använder sig av att injicera certifikat är program vara mot virus-och nätverks trafik kontroll.

När Storage Explorer ser ett självsignerat eller ej betrott certifikat, kan det inte längre veta om det mottagna HTTPS-meddelandet har ändrats. Om du har en kopia av det självsignerade certifikatet kan du instruera Storage Explorer att lita på det genom att utföra följande steg:

1. Hämta en kopia av en bas-64-kodad X. 509 (. cer) av certifikatet
2. Klicka på **redigera** → **SSL-certifikat** → **Importera certifikat**och Använd sedan fil väljaren för att hitta, välja och öppna. CER-filen

Det här problemet kan också vara resultatet av flera certifikat (rot och mellanliggande). Båda certifikaten måste läggas till för att lösa problemet.

Om du är osäker på var certifikatet kommer från kan du prova de här stegen för att hitta det:

1. Installera öppen SSL
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (vilken som helst av de ljusa versionerna bör vara tillräckliga)
    * Mac och Linux: bör ingå i ditt operativ system
2. Kör öppen SSL
    * Windows: öppna installations katalogen, klicka på **/bin/** och dubbelklicka sedan på **openssl. exe**.
    * Mac och Linux: kör **openssl** från en Terminal.
3. Kör `s_client -showcerts -connect microsoft.com:443`
4. Leta efter självsignerade certifikat. Om du är osäker på vilka certifikat som är självsignerade kan du söka efter var ämnet `("s:")` och `("i:")` utfärdaren är samma.
5. När du har hittat självsignerade certifikat, för var och en, kopiera och klistra in allt från och inklusive **-----BEGIN certificate-----** för att **-----slut certifikat-----** till en ny. cer-fil.
6. Öppna Storage Explorer, klicka på **redigera** → **SSL-certifikat** → **Importera certifikat**och Använd sedan fil väljaren för att hitta, välja och öppna CER-filerna som du skapade.

Om du inte hittar några självsignerade certifikat med hjälp av föregående steg, kontaktar du oss via feedback-verktyget för mer hjälp. Du kan också välja att starta Storage Explorer från kommando raden med `--ignore-certificate-errors` flaggan. När den startas med den här flaggan kommer Storage Explorer att ignorera certifikat fel.

## <a name="sign-in-issues"></a>Inloggningsproblem

### <a name="blank-sign-in-dialog"></a>Tom inloggnings dialog ruta

Tomma inloggnings dialog rutor orsakas oftast av ADFS som efterfrågar Storage Explorer att utföra en omdirigering, vilket inte stöds av Electron. För att undvika det här problemet kan du försöka använda enhets kod flödet för inloggning. Det gör du genom att utföra följande steg:

1. Hoppmeny För hands version – > "Använd enhets kod inloggning".
2. Öppna dialog rutan Anslut (antingen via plugin-ikonen i det vänstra lodräta fältet eller "Lägg till konto" i konto panelen).
3. Välj vilken miljö som du vill logga in på.
4. Klicka på knappen "logga in".
5. Följ anvisningarna på nästa panel.

Om du upptäcker att du har problem med att logga in på det konto som du vill använda, eftersom din standard webbläsare redan har loggat in på ett annat konto, kan du antingen:

1. Kopiera länken och koden manuellt till en privat session i webbläsaren.
2. Kopiera länken och koden manuellt till en annan webbläsare.

### <a name="reauthentication-loop-or-upn-change"></a>Reautentisering av loop eller UPN-ändring

Om du befinner dig i en loop för autentisering eller har ändrat UPN för något av dina konton kan du prova följande steg:

1. Ta bort alla konton och Stäng Storage Explorer
2. Ta bort. IdentityService-mappen från din dator. I Windows finns mappen på `C:\users\<username>\AppData\Local`. För Mac och Linux kan du hitta mappen i roten i din användar katalog.
3. Om du använder Mac eller Linux måste du också ta bort posten Microsoft. Developer. IdentityService från ditt OS-nyckel arkiv. I Mac är nyckel arkivet "gnome nyckel Ring". För Linux kallas programmet vanligt vis "nyckel Ring", men namnet kan vara olika beroende på din distribution.

### <a name="conditional-access"></a>Villkorad åtkomst

Villkorlig åtkomst stöds inte när Storage Explorer används på Windows 10, Linux eller macOS. Detta beror på en begränsning i AAD-biblioteket som används av Storage Explorer.

## <a name="mac-keychain-errors"></a>Fel i Mac-nyckelring

MacOS-nyckel ringen kan ibland komma in i ett tillstånd som orsakar problem med Storage Explorerens autentiseringspaket. Prova följande steg för att hämta nyckel ringen ur det här läget:

1. Stäng Storage Explorer.
2. Öppna nyckel ringen (**cmd + blank steg**, Skriv in nyckel ringen och tryck på RETUR).
3. Välj nyckel ringen för inloggning.
4. Klicka på hänglås ikonen för att låsa nyckel ringen (hänglåset animeras till en låst plats när det är klart, det kan ta några sekunder beroende på vilka appar som är öppna).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Starta Storage Explorer.
6. Ett popup-fönster bör visas som säger att "service Hub vill komma åt nyckel ringen". När det gör det anger du lösen ordet för Mac-administratörskontot och klickar på **Tillåt alltid** (eller **Tillåt** om **Tillåt alltid** är inte tillgängligt).
7. Försök att logga in.

### <a name="general-sign-in-troubleshooting-steps"></a>Allmän inloggning fel söknings steg

* Om du använder macOS och inloggnings fönstret visas aldrig över "väntar på autentisering..." och prova sedan [de här stegen](#mac-keychain-errors)
* Starta om Storage Explorer
* Om Authentication-fönstret är tomt väntar du minst en minut innan du stänger dialog rutan autentisering.
* Kontrol lera att inställningarna för proxy och certifikat är korrekt konfigurerade för både din dator och Storage Explorer.
* Om du använder Windows och har åtkomst till Visual Studio 2019 på samma dator och loggar in, kan du försöka logga in på Visual Studio 2019. När du har loggat in på Visual Studio 2019 kan du öppna Storage Explorer och se ditt konto i konto panelen.

Om ingen av dessa metoder fungerar [öppnar du ett problem på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Saknade prenumerationer och brutna klienter

Om du inte kan hämta dina prenumerationer när du har loggat in, kan du prova följande fel söknings metoder:

* Kontrol lera att ditt konto har åtkomst till de prenumerationer som du förväntar dig. Du kan kontrol lera åtkomsten genom att logga in på portalen för den Azure-miljö som du försöker använda.
* Kontrol lera att du har loggat in med rätt Azure-miljö (Azure, Azure Kina 21Vianet, Azure Germany, Azure amerikanska myndigheter eller anpassad miljö).
* Om du är bakom en proxyserver ser du till att du har konfigurerat Storage Explorer proxyservern korrekt.
* Försök att ta bort och lägga till kontot igen.
* Om länken "Mer information" visas kan du se vilka fel meddelanden som rapporteras för de klienter som inte fungerar. Om you'ren't vet vad som ska utföras med de fel meddelanden som visas, är det kostnads fritt att [öppna ett problem på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-attached-account-or-storage-resource"></a>Det går inte att ta bort det anslutna kontot eller lagrings resursen

Om du inte kan ta bort ett kopplat konto eller en lagrings resurs via användar gränssnittet kan du manuellt ta bort alla anslutna resurser genom att ta bort följande mappar:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Stäng Storage Explorer innan du tar bort ovanstående mappar.

> [!NOTE]
> Om du någonsin har importerat några SSL-certifikat ska du säkerhetskopiera innehållet `certs` i katalogen. Senare kan du använda säkerhets kopieringen för att importera SSL-certifikat igen.

## <a name="proxy-issues"></a>Problem med proxy

Kontrol lera först att följande information som du har angett är korrekt:

* Proxy-URL och port nummer
* Användar namn och lösen ord om proxyn kräver det

> [!NOTE]
> Storage Explorer stöder inte AutoConfig-installationsfiler för att konfigurera proxyinställningar.

### <a name="common-solutions"></a>Vanliga lösningar

Om du fortfarande har problem kan du prova följande fel söknings metoder:

* Om du kan ansluta till Internet utan att använda proxyservern kontrollerar du att Storage Explorer fungerar utan proxyinställningar aktiverade. I så fall kan det finnas ett problem med proxyinställningarna. Arbeta med proxy-administratören för att identifiera problemen.
* Kontrol lera att andra program som använder proxyservern fungerar som förväntat.
* Kontrol lera att du kan ansluta till portalen för den Azure-miljö som du försöker använda
* Verifiera att du kan ta emot svar från dina tjänst slut punkter. Ange en av dina slut punkts-URL: er i webbläsaren. Om du kan ansluta bör du få ett InvalidQueryParameterValue eller liknande XML-svar.
* Om någon annan också använder Storage Explorer med proxyservern, kontrollerar du att de kan ansluta. Om de kan ansluta kan du behöva kontakta proxyserverns administratör.

### <a name="tools-for-diagnosing-issues"></a>Verktyg för att diagnostisera problem

Om du har nätverks verktyg, till exempel Fiddler för Windows, kan du diagnostisera problemen på följande sätt:

* Om du måste arbeta via proxyservern kan du behöva konfigurera nätverks verktyget för att ansluta via proxyservern.
* Kontrol lera port numret som används av nätverks verktyget.
* Ange den lokala värd-URL: en och nätverks verktygets port nummer som proxyinställningar i Storage Explorer. När du är färdig startar nätverks verktyget loggning av nätverks förfrågningar som görs av Storage Explorer till hanterings-och tjänst slut punkter. Ange https://cawablobgrs.blob.core.windows.net/ till exempel för din BLOB-slutpunkt i en webbläsare och du får ett svar som liknar följande, vilket innebär att resursen finns, även om du inte kan komma åt den.

![Kod exempel](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Kontakta proxyserverns administratör

Om proxyinställningarna är korrekta kan du behöva kontakta proxyserverns administratör och

* Kontrol lera att proxyn inte blockerar trafik till Azure-hantering eller resurs slut punkter.
* Verifiera autentiseringsprotokollet som används av proxyservern. Storage Explorer stöder för närvarande inte NTLM-proxyservrar.

## <a name="unable-to-retrieve-children-error-message"></a>Fel meddelandet "det går inte att hämta underordnade"

Om du är ansluten till Azure via en proxyserver kontrollerar du att proxyinställningarna är korrekta. Om du beviljas åtkomst till en resurs från ägaren av prenumerationen eller kontot kontrollerar du att du har Läs-eller List behörigheter för den resursen.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Anslutnings strängen har inte fullständiga konfigurations inställningar

Om du får det här fel meddelandet är det möjligt att du inte har de behörigheter som krävs för att hämta nycklarna för ditt lagrings konto. Om du vill kontrol lera om så är fallet går du till portalen och letar upp ditt lagrings konto. Du kan snabbt göra detta genom att högerklicka på noden för ditt lagrings konto och klicka på "öppna i portalen". När du har gjort det går du till bladet "åtkomst nycklar". Om du inte har behörighet att Visa nycklar visas en sida med meddelandet "du har inte åtkomst". För att undvika det här problemet kan du antingen hämta konto nyckeln från någon annan och bifoga med namn och nyckel, eller så kan du be någon om en SAS till lagrings kontot och använda det för att ansluta lagrings kontot.

Om du ser konto nycklarna kan du ange ett problem på GitHub så att vi kan hjälpa dig att lösa problemet.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Ett fel uppstod när en ny anslutning lades till: TypeError: Det går inte att läsa egenskap ' version ' av odefinierad

Om du får det här fel meddelandet när du försöker lägga till en anpassad anslutning, är det möjligt att anslutnings data som lagras i den lokala Autentiseringshanteraren är skadade.
För att undvika det här problemet kan du prova att ta bort dina skadade lokala anslutningar och sedan lägga till dem igen.

1. Starta Storage Explorer. I den översta menyn går du till hjälp → växla Utvecklarverktyg.
2. I fönstret öppnas går du till fliken program → lokal lagring (vänster sida) → file://
3. Beroende på vilken typ av anslutningar som du har problem med, letar du efter dess nyckel och kopierar dess värde till en text redigerare. Värdet är en matris med dina anpassade anslutnings namn.
    * Lagringskonton
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blob-containrar
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Filresurser
        * `StorageExplorer_CustomConnections_Files_v1`
    * Köer
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabeller
        * `StorageExplorer_CustomConnections_Tables_v1`
4. När du har sparat dina aktuella anslutnings namn ställer du in värdet i Utvecklarverktyg `[]`.

Om du vill bevara de anslutningar som inte är skadade kan du utföra följande steg för att hitta de skadade anslutningarna. Om du inte gör något med att förlora alla befintliga anslutningar kan du hoppa över följande steg och följa plattforms instruktionerna för att rensa dina anslutnings data.

1. I text redigeraren, Lägg till varje anslutnings namn tillbaka till Utvecklarverktyg och kontrol lera om anslutningen fortfarande fungerar.
2. Om en anslutning fungerar korrekt är den inte skadad och du kan på ett säkert sätt lämna den där. Om en anslutning inte fungerar tar du bort dess värde från Utvecklarverktyg och registrerar det så att du kan lägga till det igen senare.
3. Upprepa tills du har undersökt alla dina anslutningar.

När du har gått igenom alla dina anslutningar, för alla anslutnings namn som inte läggs tillbaka, måste du rensa deras skadade data (om det finns några) och lägga till dem igen med hjälp av vanliga steg med hjälp av Storage Explorer.

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

1. Öppna Credential Manager genom att öppna Start-menyn och söka efter "Autentiseringshanteraren".
2. Gå till Windows-autentiseringsuppgifter i det öppnade fönstret.
3. Under "allmänna autentiseringsuppgifter" Letar du `<connection_type_key>/<corrupted_connection_name>` `StorageExplorer_CustomConnections_Accounts_v1/account1`efter poster med nyckel (till exempel).
4. Ta bort dessa poster och Lägg till anslutningarna igen.

# <a name="macostabmacos"></a>[macOS](#tab/macOS)

1. Öppna Spotlight (kommando rads fält) och Sök efter "nyckel rings åtkomst".
2. Leta efter poster med nyckel `<connection_type_key>/<corrupted_connection_name>` ( `StorageExplorer_CustomConnections_Accounts_v1/account1`till exempel).
3. Ta bort dessa poster och Lägg till anslutningarna igen.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

Hantering av lokala autentiseringsuppgifter varierar beroende på Linux-distribution. Om din Linux-distribution inte tillhandahåller ett inbyggt GUI-verktyg för lokal hantering av autentiseringsuppgifter, kan du installera ett verktyg från tredje part för att hantera dina lokala autentiseringsuppgifter. Du kan till exempel använda [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), ett gui-verktyg med öppen källkod för att hantera lokala autentiseringsuppgifter för Linux.

1. Öppna det lokala hanterings verktyget för autentiseringsuppgifter och hitta dina sparade autentiseringsuppgifter.
2. Leta efter poster med nyckel `<connection_type_key>/<corrupted_connection_name>` ( `StorageExplorer_CustomConnections_Accounts_v1/account1`till exempel).
3. Ta bort dessa poster och Lägg till anslutningarna igen.
---

Om du fortfarande stöter på det här felet när du har utfört de här stegen, eller om du vill dela med dig av vad du tycker är skadat anslutningarna, [öppnar du ett ärende](https://github.com/microsoft/AzureStorageExplorer/issues) på vår GitHub-sida.

## <a name="issues-with-sas-url"></a>Problem med SAS-URL

Om du ansluter till en tjänst med en SAS-URL och det här felet uppstår:

* Kontrol lera att URL: en ger de behörigheter som krävs för att läsa eller lista resurser.
* Kontrol lera att URL: en inte har upphört att gälla.
* Om SAS-URL: en baseras på en åtkomst princip kontrollerar du att åtkomst principen inte har återkallats.

Om du av misstag har kopplat med en ogiltig SAS-URL och inte kan koppla från, följer du dessa steg:

1. När du kör Storage Explorer trycker du på F12 för att öppna fönstret utvecklarverktyg.
2. Klicka på fliken program och sedan på lokal lagring > file://i trädet till vänster.
3. Hitta nyckeln som är associerad med tjänst typen för den problematiska SAS-URI: n. Om t. ex. den felaktiga SAS-URI: n är för en BLOB-behållare, `StorageExplorer_AddStorageServiceSAS_v1_blob`letar du efter nyckeln med namnet.
4. Värdet för nyckeln ska vara en JSON-matris. Hitta objektet som är associerat med den felaktiga URI: n och ta bort det.
5. Tryck på CTRL + R för att läsa in Storage Explorer igen.

## <a name="linux-dependencies"></a>Linux-beroenden

Storage Explorer 1.10.0 och senare är tillgängligt som en fäst från snapin Store. Med Storage Explorer Snap installeras alla dess beroenden automatiskt och uppdateras när en ny version av Snap är tillgänglig. Installation av Storage Explorer Snap är den rekommenderade installations metoden.

Storage Explorer kräver att du använder en lösen ords hanterare, som kan behöva anslutas manuellt innan Storage Explorer fungerar som den ska. Du kan ansluta Storage Explorer till systemets lösen ords hanterare med följande kommando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Du kan också hämta programmet som en. tar. gz-fil, men du måste installera beroenden manuellt.

> [!IMPORTANT]
> Storage Explorer som anges i hämtningen. tar. gz stöds bara för Ubuntu-distributioner. Andra distributioner har inte verifierats och kan kräva alternativa eller ytterligare paket.

Dessa paket är de vanligaste kraven för Storage Explorer i Linux:

* [.NET Core 2,0-körning](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` eller `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Storage Explorer version 1.7.0 och tidigare kräver .NET Core 2,0. Om du har en senare version av .NET Core installerad måste du [korrigera Storage Explorer](#patching-storage-explorer-for-newer-versions-of-net-core). Om du kör Storage Explorer 1.8.0 eller senare bör du kunna använda upp till .NET Core 2,2. Versioner utöver 2,2 har inte verifierats för att fungera för tillfället.

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19,04](#tab/1904)

1. Ladda ned Storage Explorer.
2. Installera [.net Core runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Kör följande kommando:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18,04](#tab/1804)

1. Ladda ned Storage Explorer.
2. Installera [.net Core runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Kör följande kommando:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16,04](#tab/1604)

1. Ladda ned Storage Explorer
2. Installera [.net Core runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Kör följande kommando:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14,04](#tab/1404)

1. Ladda ned Storage Explorer
2. Installera [.net Core runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Kör följande kommando:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Korrigera Storage Explorer för nyare versioner av .NET Core

För Storage Explorer 1.7.0 eller äldre kan du behöva korrigera den version av .NET Core som används av Storage Explorer.

1. Hämta version 1.5.43 av StreamJsonRpc [från NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Leta upp länken "Ladda ned paket" på höger sida av sidan.
2. När du har laddat ned paketet ändrar du dess `.nupkg` fil `.zip`namns tillägg från till.
3. Packa upp paketet.
4. Öppna mappen `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopiera `StreamJsonRpc.dll` till följande platser i Storage Explorer-mappen:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Öppna i Explorer från Azure Portal fungerar inte

Om knappen "öppna i Utforskaren" på Azure Portal inte fungerar för dig kontrollerar du att du använder en kompatibel webbläsare. Följande webbläsare har testats för kompatibilitet.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Nästa steg

Om ingen av lösningarna fungerar för dig kan du [öppna ett ärende på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Du kan också snabbt komma åt GitHub genom att använda knappen rapportera problem med GitHub i det nedre vänstra hörnet.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)
