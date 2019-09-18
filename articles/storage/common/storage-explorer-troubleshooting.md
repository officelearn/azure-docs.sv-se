---
title: Azure Storage Explorer fel söknings guide | Microsoft Docs
description: Översikt över fel söknings tekniker för Azure Storage Explorer
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 1a856fc3af3d0ebd53ca7de8d3df301207845107
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036359"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage Explorer fel söknings guide

Microsoft Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage data på Windows, macOS och Linux. Appen kan ansluta till lagrings konton som finns i Azure, nationella moln och Azure Stack.

I den här guiden sammanfattas lösningar för problem som ofta visas i Storage Explorer.

## <a name="rbac-permissions-issues"></a>Problem med RBAC-behörighet

Rollbaserad åtkomst kontroll [RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) möjliggör mycket detaljerad åtkomst hantering av Azure-resurser genom att kombinera uppsättningar med behörigheter i _roller_. Här följer några strategier för att få RBAC fungerar optimalt i Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Hur gör jag för att åtkomst till mina resurser i Storage Explorer?

Om du har problem med att komma åt lagrings resurser via RBAC kanske du inte har tilldelats lämpliga roller. I följande avsnitt beskrivs de behörigheter som Storage Explorer för närvarande kräver åtkomst till dina lagrings resurser. Kontakta administratören för Azure-kontot om du inte är säker på att du har lämpliga roller eller behörigheter.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Läs Lista/Hämta lagrings kontona behörighets problem

Du måste ha behörighet att lista lagrings konton. För att få den här behörigheten måste du tilldelas rollen _läsare_ .

#### <a name="list-storage-account-keys"></a>Lista lagrings konto nycklar

Storage Explorer kan också använda konto nycklar för att autentisera begär Anden. Du kan få åtkomst till konto nycklar via mer kraftfulla roller, till exempel _deltagar_ rollen.

> [!NOTE]
> Åtkomst nycklar beviljar obegränsade behörigheter till alla som innehar dem. Därför rekommenderar vi inte att du skickar ut de här nycklarna till konto användare. Om du behöver återkalla åtkomst nycklar kan du återskapa dem från [Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Data roller

Du måste tilldelas minst en roll som beviljar åtkomst för att läsa data från resurser. Om du till exempel vill lista eller hämta blobbar behöver du minst rollen _Storage BLOB data Reader_ .

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Varför behöver jag en hanterings skikt roll för att se mina resurser i Storage Explorer?

Azure Storage har två åtkomst nivåer: _hantering_ och _data_. Prenumerationer och lagrings konton nås via hanterings skiktet. Behållare, blobbar och andra data resurser nås via data lagret. Om du till exempel vill hämta en lista över dina lagrings konton från Azure skickar du en begäran till hanterings slut punkten. Om du vill ha en lista över BLOB-behållare i ett konto skickar du en begäran till lämplig tjänst slut punkt.

RBAC-roller kan innehålla behörigheter för hantering eller åtkomst till data lager. Rollen läsare, till exempel beviljar skrivskyddad åtkomst till hanterings skikts resurser.

Utan att tala med rollen läsare får du inga data lager behörigheter och behöver inte komma åt data skiktet.

Storage Explorer gör det enkelt att komma åt dina resurser genom att samla in nödvändig information för att ansluta till dina Azure-resurser. Om du till exempel vill visa dina BLOB-behållare, Storage Explorer skickar en begäran "lista över behållare" till BLOB-tjänstens slut punkt. Storage Explorer söker i listan över prenumerationer och lagrings konton som du har åtkomst till för att hämta slut punkten. För att hitta dina prenumerationer och lagrings konton behöver Storage Explorer även åtkomst till hanterings skiktet.

Om du inte har en roll som beviljar behörigheter för hanterings lager kan Storage Explorer inte hämta den information som krävs för att ansluta till data lagret.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Vad händer om jag inte kan hämta de behörigheter för hanterings skikt jag behöver från min administratör?

Vi har för närvarande ingen RBAC-relaterad lösning för det här problemet. Som en lösning kan du begära en SAS-URI för att [ansluta till din resurs](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fel: Självsignerat certifikat i certifikat kedjan (och liknande fel)

Certifikat fel inträffar vanligt vis i någon av följande situationer:

- Appen är ansluten via en _transparent proxy_, vilket innebär att en server (till exempel din företags server) fångar upp HTTPS-trafik, dekrypterar den och sedan krypterar den med hjälp av ett självsignerat certifikat.
- Du kör ett program som matar in ett självsignerat SSL-certifikat i de HTTPS-meddelanden som du får. Exempel på program som injicerar certifikat är antivirus-och kontroll program för nätverks trafik.

När Storage Explorer ser ett självsignerat eller ej betrott certifikat, vet det inte längre om det mottagna HTTPS-meddelandet har ändrats. Om du har en kopia av det självsignerade certifikatet kan du instruera Storage Explorer att lita på det genom att följa dessa steg:

1. Hämta en kopia av certifikatet med bas-64-kodad X. 509 (. cer).
2. Gå till **Redigera** > **SSL-certifikat** > **Importera certifikat**och Använd sedan fil väljaren för att söka efter, välja och öppna. CER-filen.

Det här problemet kan även uppstå om det finns flera certifikat (rot och mellanliggande). För att åtgärda det här felet måste båda certifikaten läggas till.

Om du är osäker på var certifikatet kommer från följer du de här stegen för att hitta det:

1. Installera OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): Alla ljusa versioner bör vara tillräckliga.
    * Mac och Linux: Ska inkluderas i ditt operativ system.
2. Kör OpenSSL.
    * Windows: Öppna installations katalogen, Välj **/bin/** och dubbelklicka sedan på **openssl. exe**.
    * Mac och Linux: Kör `openssl` från en Terminal.
3. Kör `s_client -showcerts -connect microsoft.com:443`.
4. Leta efter självsignerade certifikat. Om du är osäker på vilka certifikat som är självsignerade måste du anteckna var `("s:")` och en av dem och `("i:")` utfärdaren.
5. När du hittar självsignerade certifikat för var och en, kopierar och klistrar du in allt från ( `-----BEGIN CERTIFICATE-----` och `-----END CERTIFICATE-----` inkluderar) till en ny. cer-fil.
6. Öppna Storage Explorer och gå till **Redigera** > **SSL-certifikat** > **Importera certifikat**. Använd sedan fil väljaren för att söka efter, välja och öppna CER-filerna som du skapade.

Om du inte hittar några självsignerade certifikat genom att följa dessa steg kan du kontakta oss via feedback-verktyget. Du kan också öppna Storage Explorer från kommando raden med hjälp `--ignore-certificate-errors` av flaggan. Vid öppning med den här flaggan ignorerar Storage Explorer certifikat fel.

## <a name="sign-in-issues"></a>Inloggningsproblem

### <a name="blank-sign-in-dialog-box"></a>Dialog rutan tom inloggning

Tomma inloggnings dialog rutor inträffar oftast när Active Directory Federation Services (AD FS) (AD FS) begär Storage Explorer att utföra en omdirigering, vilket inte stöds av Electron. För att undvika det här problemet kan du försöka använda enhets kod flödet för inloggning. Det gör du genom att följa dessa steg:

1. På menyn går du till **förhands granskning** > **Använd enhets kod inloggning**.
2. Öppna dialog rutan **Anslut** (antingen via plugin-ikonen på vänster lodrätt fält eller genom att välja **Lägg till konto** på konto panelen).
3. Välj den miljö som du vill logga in på.
4. Välj **Logga**in.
5. Följ anvisningarna på nästa panel.

Om du inte kan logga in på det konto som du vill använda eftersom standard webbläsaren redan är inloggad på ett annat konto gör du något av följande:

- Kopiera länken och koden manuellt till en privat session i webbläsaren.
- Kopiera länken och koden manuellt till en annan webbläsare.

### <a name="reauthentication-loop-or-upn-change"></a>Reautentisering av loop eller UPN-ändring

Följ dessa steg om du befinner dig i en loop för autentisering eller har ändrat UPN för något av dina konton:

1. Ta bort alla konton och Stäng Storage Explorer.
2. Ta bort. IdentityService-mappen från din dator. I Windows finns mappen på `C:\users\<username>\AppData\Local`. För Mac och Linux kan du hitta mappen i roten i din användar katalog.
3. Om du kör Mac eller Linux måste du också ta bort posten Microsoft. Developer. IdentityService från operativ systemets nyckel lager. I Mac är nyckel lagringen *gnome nyckel rings* program. I Linux kallas programmet vanligt vis för _nyckel_ring, men namnet kan variera beroende på din distribution.

### <a name="conditional-access"></a>Villkorad åtkomst

På grund av en begränsning i Azure AD-biblioteket som används av Storage Explorer, stöds inte villkorlig åtkomst när Storage Explorer används på Windows 10, Linux eller macOS.

## <a name="mac-keychain-errors"></a>Fel i Mac-nyckelring

MacOS-nyckel ringen kan ibland ange ett tillstånd som orsakar problem med biblioteket för Storage Explorer autentisering. Följ dessa steg om du vill hämta nyckel ringen ur det här läget:

1. Stäng Storage Explorer.
2. Öppna nyckel ringen (tryck på kommando + blank steg, Skriv **nyckel Ring**och tryck på RETUR).
3. Välj nyckel ringen för inloggning.
4. Välj hänglås ikonen för att låsa nyckel ringen. (Hänglåset blir låst när processen är klar. Det kan ta några sekunder, beroende på vilka appar som är öppna).

    ![Hänglås ikon](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Öppna Storage Explorer.
6. Du uppmanas att ange ett meddelande som "service Hub vill ha åtkomst till nyckel ringen". Ange lösen ordet för ditt Mac-administratörskonto och välj **Tillåt alltid** (eller **Tillåt** om **Tillåt alltid** är tillgängligt).
7. Försök att logga in.

### <a name="general-sign-in-troubleshooting-steps"></a>Allmän inloggning fel söknings steg

* Om du använder macOS och inloggnings fönstret aldrig visas i dialog rutan **väntar på autentisering** , kan du prova [de här stegen](#mac-keychain-errors).
* Starta om Storage Explorer.
* Om Authentication-fönstret är tomt väntar du minst en minut innan du stänger dialog rutan autentisering.
* Kontrol lera att inställningarna för proxy och certifikat är korrekt konfigurerade för både din dator och Storage Explorer.
* Om du kör Windows och har åtkomst till Visual Studio 2019 på samma dator och inloggnings uppgifterna, kan du försöka logga in i Visual Studio 2019. När du har loggat in på Visual Studio 2019 kan du öppna Storage Explorer och se ditt konto i konto panelen.

Om ingen av dessa metoder fungerar [öppnar du ett ärende i GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Saknade prenumerationer och brutna klienter

Om du inte kan hämta dina prenumerationer när du har loggat in, kan du prova följande fel söknings metoder:

* Kontrol lera att ditt konto har åtkomst till de prenumerationer som du förväntar dig. Du kan kontrol lera åtkomsten genom att logga in på portalen för den Azure-miljö som du försöker använda.
* Kontrol lera att du har loggat in via rätt Azure-miljö (Azure, Azure Kina 21Vianet, Azure Germany, Azure amerikanska myndigheter eller anpassad miljö).
* Om du är bakom en proxyserver ser du till att du har konfigurerat Storage Explorer proxyn korrekt.
* Försök att ta bort och lägga till kontot på nytt.
* Om länken "Mer information" visas kontrollerar du vilka fel meddelanden som rapporteras för de klienter som inte fungerar. Om du inte är säker på hur du ska svara på fel meddelanden kan du [öppna ett ärende i GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Det går inte att ta bort ett kopplat konto eller en lagrings resurs

Om du inte kan ta bort ett kopplat konto eller en lagrings resurs via användar gränssnittet kan du manuellt ta bort alla anslutna resurser genom att ta bort följande mappar:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Stäng Storage Explorer innan du tar bort mapparna.

> [!NOTE]
> Om du någonsin har importerat några SSL-certifikat säkerhetskopierar du innehållet `certs` i katalogen. Senare kan du använda säkerhets kopieringen för att importera SSL-certifikat igen.

## <a name="proxy-issues"></a>Problem med proxy

Kontrol lera först att följande information som du har angett är korrekt:

* Proxy-URL och port nummer
* Användar namn och lösen ord om proxyn kräver dem

> [!NOTE]
> Storage Explorer stöder inte AutoConfig-installationsfiler för att konfigurera proxyinställningar.

### <a name="common-solutions"></a>Vanliga lösningar

Om du fortfarande har problem kan du prova följande fel söknings metoder:

* Om du kan ansluta till Internet utan att använda proxyservern kontrollerar du att Storage Explorer fungerar utan proxyinställningar aktiverade. I så fall kan det finnas ett problem med proxyinställningarna. Arbeta med administratören för att identifiera problemen.
* Kontrol lera att andra program som använder proxyservern fungerar som förväntat.
* Kontrol lera att du kan ansluta till portalen för den Azure-miljö som du försöker använda.
* Verifiera att du kan ta emot svar från dina tjänst slut punkter. Ange en av dina slut punkts-URL: er i webbläsaren. Om du kan ansluta bör du ta emot InvalidQueryParameterValue eller ett liknande XML-svar.
* Om någon annan också använder Storage Explorer med proxyservern, kontrollerar du att de kan ansluta. Om de kan det kan du behöva kontakta din Proxy Server-administratör.

### <a name="tools-for-diagnosing-issues"></a>Verktyg för att diagnostisera problem

Om du har nätverks verktyg, till exempel Fiddler för Windows, kan du diagnostisera problemen på följande sätt:

* Om du måste arbeta via proxyservern kan du behöva konfigurera nätverks verktyget för att ansluta via proxyservern.
* Kontrol lera port numret som används av nätverks verktyget.
* Ange den lokala värd-URL: en och nätverks verktygets port nummer som proxyinställningar i Storage Explorer. När du gör detta korrekt startar nätverks verktyget loggning av nätverks begär Anden som görs av Storage Explorer till hanterings-och tjänst slut punkter. Ange `https://cawablobgrs.blob.core.windows.net/` till exempel för din BLOB-slutpunkt i en webbläsare och du får ett svar som liknar följande:

  ![Kodexempel](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Detta svar föreslår att resursen finns, även om du inte har åtkomst till den.

### <a name="contact-proxy-server-admin"></a>Kontakta proxyserverns administratör

Om proxyinställningarna är korrekta kan du behöva kontakta Proxy Server-administratören för att:

* Kontrol lera att proxyn inte blockerar trafik till Azure-hantering eller resurs slut punkter.
* Verifiera autentiseringsprotokollet som används av proxyservern. Storage Explorer stöder för närvarande inte NTLM-proxyservrar.

## <a name="unable-to-retrieve-children-error-message"></a>Fel meddelandet "det går inte att hämta underordnade"

Om du är ansluten till Azure via en proxyserver kontrollerar du att proxyinställningarna är korrekta. Om du beviljas åtkomst till en resurs från ägaren av prenumerationen eller kontot kontrollerar du att du har Läs-eller List behörigheter för den resursen.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Anslutnings strängen har inte fullständiga konfigurations inställningar

Om du får det här fel meddelandet är det möjligt att du inte har de behörigheter som krävs för att hämta nycklarna för ditt lagrings konto. För att bekräfta att detta är fallet går du till portalen och letar upp ditt lagrings konto. Du kan göra detta genom att högerklicka på noden för ditt lagrings konto och välja **Öppna i portalen**. Gå sedan till bladet **åtkomst nycklar** . Om du inte har behörighet att Visa nycklar visas meddelandet "du har inte åtkomst". För att undvika det här problemet kan du antingen hämta konto nyckeln från någon annan och ansluta via namnet och nyckeln, eller så kan du be någon om en SAS till lagrings kontot och använda det för att ansluta lagrings kontot.

Om du ser konto nycklarna kan du ange ett problem i GitHub så att vi kan hjälpa dig att lösa problemet.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Ett fel uppstod när en ny anslutning lades till: TypeError: Det går inte att läsa egenskap ' version ' av odefinierad

Om du får det här fel meddelandet när du försöker lägga till en anpassad anslutning kan de anslutnings data som lagras i den lokala Autentiseringshanteraren vara skadade. Undvik det här problemet genom att försöka ta bort dina skadade lokala anslutningar och sedan lägga till dem på nytt:

1. Starta Storage Explorer. I menyn går du till **Hjälp** > att**Växla utvecklarverktyg**.
2. I det öppnade fönstret, på fliken **program** , går du till **lokal lagring** (vänster sida) > **File://** .
3. Beroende på vilken typ av anslutning du har problem med, letar du efter nyckeln och kopierar dess värde till en text redigerare. Värdet är en matris med dina anpassade anslutnings namn, som följande:
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
4. När du har sparat dina aktuella anslutnings namn ställer du in värdet i Utvecklarverktyg `[]`till.

Om du vill bevara de anslutningar som inte är skadade kan du använda följande steg för att hitta de skadade anslutningarna. Om du inte gör något med att förlora alla befintliga anslutningar kan du hoppa över de här stegen och följa de plattformsspecifika instruktionerna för att rensa dina anslutnings data.

1. I en text redigerare, Lägg till varje anslutnings namn i Utvecklarverktyg igen och kontrol lera sedan om anslutningen fortfarande fungerar.
2. Om en anslutning fungerar som den ska, är den inte skadad och du kan på ett säkert sätt lämna den där. Om en anslutning inte fungerar tar du bort dess värde från Utvecklarverktyg och spelar in det så att du kan lägga till den igen senare.
3. Upprepa tills du har undersökt alla dina anslutningar.

När du har gått igenom alla dina anslutningar, för alla anslutnings namn som inte läggs tillbaka, måste du rensa deras skadade data (om det finns några) och lägga till dem igen med hjälp av standard stegen i Storage Explorer:

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

1. På **Start** -menyn söker du efter **Autentiseringshanteraren** och öppnar den.
2. Gå till **Windows-autentiseringsuppgifter**.
3. Under **allmänna autentiseringsuppgifter**söker du `<connection_type_key>/<corrupted_connection_name>` efter poster som har nyckeln `StorageExplorer_CustomConnections_Accounts_v1/account1`(till exempel).
4. Ta bort dessa poster och Lägg till anslutningarna på nytt.

# <a name="macostabmacos"></a>[macOS](#tab/macOS)

1. Öppna Spotlight (kommando + blank steg) och Sök efter **nyckel rings åtkomst**.
2. Leta efter poster som har `<connection_type_key>/<corrupted_connection_name>` nyckeln ( `StorageExplorer_CustomConnections_Accounts_v1/account1`till exempel).
3. Ta bort dessa poster och Lägg till anslutningarna på nytt.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

Hantering av lokala autentiseringsuppgifter varierar beroende på Linux-distributionen. Om din Linux-distribution inte tillhandahåller ett inbyggt GUI-verktyg för lokal hantering av autentiseringsuppgifter, kan du installera ett verktyg från tredje part för att hantera dina lokala autentiseringsuppgifter. Du kan till exempel använda [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), ett gui-verktyg med öppen källkod för att hantera lokala autentiseringsuppgifter för Linux.

1. Öppna det lokala hanterings verktyget för autentiseringsuppgifter och hitta dina sparade autentiseringsuppgifter.
2. Leta efter poster som har `<connection_type_key>/<corrupted_connection_name>` nyckeln ( `StorageExplorer_CustomConnections_Accounts_v1/account1`till exempel).
3. Ta bort dessa poster och Lägg till anslutningarna på nytt.
---

Om du fortfarande stöter på det här felet när du har kört dessa steg, eller om du vill dela det som du misstänker har skadat anslutningarna, [öppnar du ett problem](https://github.com/microsoft/AzureStorageExplorer/issues) på vår GitHub-sida.

## <a name="issues-with-sas-url"></a>Problem med SAS-URL

Om du ansluter till en tjänst via en SAS-URL och råkar ut för ett fel:

* Kontrol lera att URL: en ger de behörigheter som krävs för att läsa eller lista resurser.
* Kontrol lera att URL: en inte har upphört att gälla.
* Om SAS-URL: en baseras på en åtkomst princip kontrollerar du att åtkomst principen inte har återkallats.

Om du av misstag har kopplat med en ogiltig SAS-URL och inte kan koppla från, följer du dessa steg:

1. När du kör Storage Explorer trycker du på F12 för att öppna fönstret Utvecklarverktyg.
2. På fliken **program** väljer du **lokal lagring** > **File://** i trädet till vänster.
3. Hitta nyckeln som är associerad med tjänst typen för den problematiska SAS-URI: n. Om t. ex. den felaktiga SAS-URI: n är för en BLOB-behållare, `StorageExplorer_AddStorageServiceSAS_v1_blob`letar du efter nyckeln med namnet.
4. Värdet för nyckeln ska vara en JSON-matris. Hitta objektet som är associerat med den felaktiga URI: n och ta sedan bort det.
5. Tryck på CTRL + R för att läsa in Storage Explorer igen.

## <a name="linux-dependencies"></a>Linux-beroenden

Storage Explorer 1.10.0 och senare är tillgängligt som en fäst från snapin Store. Den Storage Explorer Snap installerar alla dess beroenden automatiskt och uppdateras när en ny version av Snap är tillgänglig. Installation av Storage Explorer Snap är den rekommenderade installations metoden.

Storage Explorer kräver att du använder en lösen ords hanterare, som du kan behöva ansluta manuellt innan Storage Explorer fungerar som den ska. Du kan ansluta Storage Explorer till systemets lösen ords hanterare genom att köra följande kommando:

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
> Storage Explorer version 1.7.0 och tidigare kräver .NET Core 2,0. Om du har en nyare version av .NET Core installerad måste du [uppdatera Storage Explorer](#patching-storage-explorer-for-newer-versions-of-net-core). Om du kör Storage Explorer 1.8.0 eller senare bör du kunna använda upp till .NET Core 2,2. Versioner utöver 2,2 har inte verifierats för att fungera för tillfället.

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

1. Ladda ned Storage Explorer.
2. Installera [.net Core runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Kör följande kommando:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14,04](#tab/1404)

1. Ladda ned Storage Explorer.
2. Installera [.net Core runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Kör följande kommando:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Korrigera Storage Explorer för nyare versioner av .NET Core

För Storage Explorer 1.7.0 eller tidigare kan du behöva korrigera den version av .NET Core som används av Storage Explorer:

1. Hämta version 1.5.43 av StreamJsonRpc [från NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Leta upp länken "Ladda ned paket" på höger sida av sidan.
2. När du har laddat ned paketet ändrar du dess fil `.nupkg` namns tillägg från till `.zip`.
3. Packa upp paketet.
4. Öppna mappen `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopiera `StreamJsonRpc.dll` till följande platser i mappen Storage Explorer:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Öppna i Utforskaren" från Azure Portal fungerar inte

Om knappen **Öppna i Explorer** på Azure Portal inte fungerar kontrollerar du att du använder en kompatibel webbläsare. Följande webbläsare har testats för kompatibilitet:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Nästa steg

Om ingen av dessa lösningar fungerar för dig [öppnar du ett ärende i GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Du kan också göra detta genom att välja knappen **rapportera ärende till GitHub** i det nedre vänstra hörnet.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)
