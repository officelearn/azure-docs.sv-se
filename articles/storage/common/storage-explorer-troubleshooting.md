---
title: Felsökningsguide för Azure Storage Explorer | Microsoft-dokument
description: Översikt över felsökningstekniker för Azure Storage Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: db36033ea524603416f16db27f40d5eefb8bf613
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437105"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Felsökningsguide för Azure Storage Explorer

Microsoft Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage-data på Windows, macOS och Linux. Appen kan ansluta till lagringskonton som finns på Azure, nationella moln och Azure Stack.

Den här guiden sammanfattar lösningar på problem som ofta visas i Storage Explorer.

## <a name="rbac-permissions-issues"></a>Rbac-behörighetsproblem

Rollbaserad åtkomstkontroll [RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) möjliggör mycket detaljerad åtkomsthantering av Azure-resurser genom att kombinera uppsättningar med behörigheter i _roller_. Här är några strategier för att få RBAC att fungera optimalt i Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Hur kommer jag åt mina resurser i Storage Explorer?

Om du har problem med att komma åt lagringsresurser via RBAC kanske du inte har tilldelats rätt roller. I följande avsnitt beskrivs de behörigheter som Storage Explorer kräver för närvarande för åtkomst till dina lagringsresurser. Kontakta administratören för Ditt Azure-konto om du inte är säker på att du har rätt roller eller behörigheter.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"Läs: Fråga om behörigheter för lagringskonto

Du måste ha behörighet att lista lagringskonton. För att få den här behörigheten måste du tilldelas rollen _Läsare._

#### <a name="list-storage-account-keys"></a>Lista lagringskontonycklar

Storage Explorer kan också använda kontonycklar för att autentisera begäranden. Du kan få åtkomst till kontonycklar via mer kraftfulla roller, till exempel _rollen Deltagare._

> [!NOTE]
> Åtkomstnycklar ger obegränsad behörighet till alla som innehar dem. Därför rekommenderar vi inte att du delar ut dessa nycklar till kontoanvändare. Om du behöver återkalla åtkomstnycklar kan du återskapa dem från [Azure-portalen](https://portal.azure.com/).

#### <a name="data-roles"></a>Dataroller

Du måste tilldelas minst en roll som ger åtkomst till läsa data från resurser. Om du till exempel vill lista eller hämta blobbar behöver du åtminstone rollen _Storage Blob Data Reader._

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Varför behöver jag en roll för hanteringslagret för att se mina resurser i Storage Explorer?

Azure Storage har två lager av åtkomst: _hantering_ och _data_. Prenumerationer och lagringskonton nås via hanteringslagret. Behållare, blobbar och andra dataresurser nås via datalagret. Om du till exempel vill få en lista över dina lagringskonton från Azure skickar du en begäran till hanteringsslutpunkten. Om du vill ha en lista över blob-behållare i ett konto skickar du en begäran till rätt tjänstslutpunkt.

RBAC-roller kan innehålla behörigheter för hantering eller åtkomst till datalager. Rollen Läsare ger till exempel skrivskyddad åtkomst till resurser för hanteringslager.

Kort sagt ger reader-rollen inga behörigheter för datalager och är inte nödvändig för att komma åt datalagret.

Storage Explorer gör det enkelt att komma åt dina resurser genom att samla in nödvändig information för att ansluta till dina Azure-resurser. Om du till exempel vill visa blob-behållarna skickar Storage Explorer en "listbehållare"-begäran till blob-tjänstslutpunkten. För att få den slutpunkten söker Storage Explorer igenom listan över prenumerationer och lagringskonton som du har åtkomst till. För att hitta dina prenumerationer och lagringskonton behöver Storage Explorer också åtkomst till hanteringslagret.

Om du inte har en roll som ger behörigheter för hanteringslager kan Storage Explorer inte hämta den information som behövs för att ansluta till datalagret.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Vad händer om jag inte kan få de behörigheter för hanteringslager som jag behöver från administratören?

Vi har för närvarande ingen RBAC-relaterad lösning för det här problemet. Som en lösning kan du begära att en SAS URI [bifogas till din resurs](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri).

### <a name="recommended-built-in-rbac-roles"></a>Rekommenderade inbyggda RBAC-roller

Det finns flera inbyggda RBAC-roller som kan ge de behörigheter som behövs för att använda Storage Explorer. Några av dessa roller är:
- [Ägare](/azure/role-based-access-control/built-in-roles#owner): Hantera allt, inklusive åtkomst till resurser. **Obs:** den här rollen ger dig nyckelåtkomst.
- [Deltagare](/azure/role-based-access-control/built-in-roles#contributor): Hantera allt, exklusive åtkomst till resurser. **Obs:** den här rollen ger dig nyckelåtkomst.
- [Läsare:](/azure/role-based-access-control/built-in-roles#reader)Läs och lista resurser.
- [Storage Account Contributor](/azure/role-based-access-control/built-in-roles#storage-account-contributor): Fullständig hantering av lagringskonton. **Obs:** den här rollen ger dig nyckelåtkomst.
- [Lagring blob dataägare:](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)Fullständig åtkomst till Azure Storage blob behållare och data.
- [Storage Blob Data Contributor](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor): Läs, skriva och ta bort Azure Storage-behållare och blobbar.
- [Lagring blob dataläsare:](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)Läs och lista Azure Storage behållare och blobbar.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fel: Självsignerat certifikat i certifikatkedjan (och liknande fel)

Certifikatfel uppstår vanligtvis i någon av följande situationer:

- Appen är ansluten via en _transparent proxy_, vilket innebär att en server (till exempel företagsservern) fångar upp HTTPS-trafik, dekrypterar den och krypterar den sedan med hjälp av ett självsignerat certifikat.
- Du kör ett program som injicerar ett självsignerat TLS/SSL-certifikat i de HTTPS-meddelanden som du får. Exempel på program som injicerar certifikat är antivirus- och nätverkstrafikinspektionsprogram.

När Storage Explorer ser ett självsignerat eller ej anförtrott certifikat vet den inte längre om det mottagna HTTPS-meddelandet har ändrats. Om du har en kopia av det självsignerade certifikatet kan du instruera Storage Explorer att lita på det genom att följa dessa steg:

1. Skaffa en Base-64-kodad X.509 (.cer) kopia av certifikatet.
2. Gå till Importera certifikat för **redigering av** > **SSL-certifikat** > **Import Certificates**och använd sedan filväljaren för att hitta, markera och öppna CER-filen.

Det här problemet kan också uppstå om det finns flera certifikat (rot och mellanliggande). För att åtgärda det här felet måste båda certifikaten läggas till.

Om du är osäker på var certifikatet kommer ifrån gör du så här:

1. Installera OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): Alla ljusversioner bör vara tillräckligt.
    * Mac och Linux: Bör ingå i ditt operativsystem.
2. Kör OpenSSL.
    * Windows: Öppna installationskatalogen, välj **/bin/** och dubbelklicka sedan på **openssl.exe**.
    * Mac och Linux: Kör `openssl` från en terminal.
3. Kör `s_client -showcerts -connect microsoft.com:443`.
4. Leta efter självsignerade certifikat. Om du är osäker på vilka certifikat som är självsignerade bör du notera var som helst som ämnet `("s:")` och utfärdaren `("i:")` är desamma.
5. När du hittar självsignerade certifikat, för var och en, `-----BEGIN CERTIFICATE-----` kopiera `-----END CERTIFICATE-----` och klistra in allt från (och inklusive) till en ny .cer-fil.
6. Öppna Storage Explorer och gå till **Importera** > certifikat för**redigering av SSL-certifikat** > **Import Certificates**. Använd sedan filväljaren för att hitta, markera och öppna .cer-filerna som du skapade.

Om du inte hittar några självsignerade certifikat genom att följa dessa steg kontaktar du oss via feedbackverktyget. Du kan också öppna Storage Explorer från `--ignore-certificate-errors` kommandoraden med hjälp av flaggan. När den öppnas med den här flaggan ignorerar Storage Explorer certifikatfel.

## <a name="sign-in-issues"></a>Inloggningsproblem

### <a name="blank-sign-in-dialog-box"></a>Dialogrutan Tom inloggning

Tomma inloggningsdialogrutor inträffar oftast när AD FS (Active Directory Federation Services) uppmanar Storage Explorer att utföra en omdirigering, som inte stöds av Electron. Du kan lösa problemet genom att försöka använda Enhetskodflöde för inloggning. Det gör du på följande sätt:

1. Öppna **Inställningar**i den vänstra lodräta verktygsraden . Gå till > **Programskyltning på**inställningspanelen . **Application** Aktivera **Logga in för användning av enhetskodflöde**.
2. Öppna dialogrutan **Anslut** (antingen via kontaktikonen i det vertikala fältet till vänster eller genom att välja **Lägg till konto** på kontopanelen).
3. Välj den miljö du vill logga in på.
4. Välj **Logga in**.
5. Följ instruktionerna på nästa panel.

Om du inte kan logga in på det konto som du vill använda eftersom standardwebbläsaren redan är inloggad på ett annat konto gör du något av följande:

- Kopiera länken och koden manuellt till en privat session i webbläsaren.
- Kopiera länken och koden manuellt till en annan webbläsare.

### <a name="reauthentication-loop-or-upn-change"></a>Reauthentication loop eller UPN förändring

Om du är i en omautentiseringsslinga eller har ändrat UPN för ett av dina konton gör du så här:

1. Ta bort alla konton och stäng sedan Storage Explorer.
2. Ta bort . IdentityService-mapp från datorn. I Windows finns mappen `C:\users\<username>\AppData\Local`på . För Mac och Linux kan du hitta mappen i roten i din användarkatalog.
3. Om du kör Mac eller Linux måste du också ta bort posten Microsoft.Developer.IdentityService från operativsystemets nyckelbutik. På Mac är keystore *Gnome Keychain* ansökan. I Linux kallas programmet vanligtvis _Nyckelring_, men namnet kan variera beroende på din distribution.

### <a name="conditional-access"></a>Villkorlig åtkomst

På grund av en begränsning i Azure AD-biblioteket som används av Storage Explorer stöds villkorlig åtkomst inte när Storage Explorer används på Windows 10, Linux eller macOS.

## <a name="mac-keychain-errors"></a>Mac-nyckelringsfel

MacOS-nyckelringen kan ibland ange ett tillstånd som orsakar problem för autentiseringsbiblioteket för Storage Explorer. Så här tar du bort nyckelringen från det här tillståndet:

1. Stäng Lagringsutforskaren.
2. Öppna Nyckelring (tryck på Kommando+Blanksteg, skriv **nyckelring**och tryck på Retur).
3. Välj nyckelringen "logga in".
4. Välj hänglåsikonen för att låsa nyckelringen. (Hänglåset visas låst när processen är klar. Det kan ta några sekunder, beroende på vilka appar du har öppna).

    ![Ikon för Hänglås](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Öppna Storage Explorer.
6. Du uppmanas att använda ett meddelande som "Service-hubben vill komma åt nyckelringen". Ange lösenordet för ditt Mac-administratörskonto och välj **Tillåt alltid** (eller **Tillåt** om **Alltid Tillåt** inte är tillgängligt).
7. Försök logga in.

### <a name="general-sign-in-troubleshooting-steps"></a>Felsökningssteg för allmän inloggning

* Om du använder macOS och inloggningsfönstret aldrig visas under dialogrutan **Väntar på autentisering** provar du [de här stegen](#mac-keychain-errors).
* Starta om Lagringsutforskaren.
* Om autentiseringsfönstret är tomt väntar du minst en minut innan du stänger autentiseringsdialogrutan.
* Kontrollera att proxy- och certifikatinställningarna är korrekt konfigurerade för både datorn och Lagringsutforskaren.
* Om du kör Windows och har tillgång till Visual Studio 2019 på samma dator och till inloggningsuppgifterna kan du prova att logga in på Visual Studio 2019. Efter en lyckad inloggning till Visual Studio 2019 kan du öppna Storage Explorer och se ditt konto på kontopanelen.

Om ingen av dessa metoder fungerar [öppnar du ett problem i GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Saknade prenumerationer och trasiga klienter

Om du inte kan hämta dina prenumerationer när du har loggat in kan du prova följande felsökningsmetoder:

* Kontrollera att ditt konto har åtkomst till de prenumerationer du förväntar dig. Du kan verifiera din åtkomst genom att logga in på portalen för Den Azure-miljö som du försöker använda.
* Kontrollera att du har loggat in via rätt Azure-miljö (Azure, Azure China 21Vianet, Azure Germany, Azure US Government eller Custom Environment).
* Om du ligger bakom en proxyserver kontrollerar du att du har konfigurerat Storage Explorer-proxyn korrekt.
* Försök att ta bort och lägga till kontot igen.
* Om det finns en "Mer information"-länk kontrollerar du vilka felmeddelanden som rapporteras för de klienter som inte fungerar. Om du inte är säker på hur du ska svara på felmeddelandena får du gärna [öppna ett problem i GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Det går inte att ta bort ett bifogat konto eller en lagringsresurs

Om du inte kan ta bort ett bifogat konto eller en lagringsresurs via användargränssnittet kan du manuellt ta bort alla kopplade resurser genom att ta bort följande mappar:

* Windows:`%AppData%/StorageExplorer`
* Macos:`/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux:`~/.config/StorageExplorer`

> [!NOTE]
> Stäng Storage Explorer innan du tar bort dessa mappar.

> [!NOTE]
> Om du någon gång har importerat SSL-certifikat `certs` säkerhetskopierar du innehållet i katalogen. Senare kan du använda säkerhetskopian för att importera dina SSL-certifikat igen.

## <a name="proxy-issues"></a>Proxyproblem

Kontrollera först att följande information du har angett är korrekt:

* Proxy-URL:en och portnumret
* Användarnamn och lösenord om proxyn kräver dem

> [!NOTE]
> Storage Explorer stöder inte automatiska proxy-config-filer för att konfigurera proxyinställningar.

### <a name="common-solutions"></a>Vanliga lösningar

Om du fortfarande har problem kan du prova följande felsökningsmetoder:

* Om du kan ansluta till internet utan att använda proxyn kontrollerar du att Storage Explorer fungerar utan att proxyinställningarna är aktiverade. Om så är fallet kan det vara problem med proxyinställningarna. Arbeta med administratören för att identifiera problemen.
* Kontrollera att andra program som använder proxyservern fungerar som förväntat.
* Kontrollera att du kan ansluta till portalen för Den Azure-miljö som du försöker använda.
* Kontrollera att du kan få svar från tjänstslutpunkterna. Ange en av slutpunktsadresserna i webbläsaren. Om du kan ansluta bör du få InvalidQueryParameterValue eller ett liknande XML-svar.
* Om någon annan också använder Storage Explorer med proxyservern kontrollerar du att de kan ansluta. Om de kan kan du behöva kontakta din proxyserver admin.

### <a name="tools-for-diagnosing-issues"></a>Verktyg för att diagnostisera problem

Om du har nätverksverktyg, till exempel Fiddler för Windows, kan du diagnostisera problemen på följande sätt:

* Om du måste arbeta igenom din proxy kan du behöva konfigurera nätverksverktyget för att ansluta via proxyn.
* Kontrollera portnumret som används av nätverksverktyget.
* Ange den lokala värd-URL:en och nätverksverktygets portnummer som proxyinställningar i Storage Explorer. När du gör det på rätt sätt börjar nätverksverktyget logga nätverksbegäranden från Storage Explorer till hanterings- och tjänstslutpunkter. Ange `https://cawablobgrs.blob.core.windows.net/` till exempel för blob-slutpunkten i en webbläsare så får du ett svar som liknar följande:

  ![Kodexempel](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Det här svaret tyder på att resursen finns, även om du inte kan komma åt den.

### <a name="contact-proxy-server-admin"></a>Kontakta proxyserveradministratör

Om proxyinställningarna är korrekta kan du behöva kontakta proxyserveradministratören för att:

* Kontrollera att din proxy inte blockerar trafik till Azure-hantering eller resursslutpunkter.
* Kontrollera vilket autentiseringsprotokoll som används av proxyservern. Storage Explorer stöder för närvarande inte NTLM-proxyservrar.

## <a name="unable-to-retrieve-children-error-message"></a>Felmeddelandet "Det gick inte att hämta underordnade"

Om du är ansluten till Azure via en proxy kontrollerar du att proxyinställningarna är korrekta. Om du har behörighet till en resurs från ägaren av prenumerationen eller kontot kontrollerar du att du har läs- eller listbehörigheter för den resursen.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Anslutningssträngen har inte fullständiga konfigurationsinställningar

Om du får det här felmeddelandet är det möjligt att du inte har de behörigheter som krävs för att hämta nycklarna till ditt lagringskonto. För att bekräfta att så är fallet går du till portalen och letar reda på ditt lagringskonto. Du kan göra detta genom att högerklicka på noden för ditt lagringskonto och välja **Öppna i Portal**. Gå sedan till **bladet Access Keys.** Om du inte har behörighet att visa nycklar visas meddelandet "Du har inte åtkomst". För att komma runt det här problemet kan du antingen hämta kontonyckeln från någon annan och bifoga via namn och nyckel, eller så kan du be någon om en SAS till lagringskontot och använda den för att bifoga lagringskontot.

Om du ser kontonycklarna lämnar du in ett problem i GitHub så att vi kan hjälpa dig att lösa problemet.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Fel uppstod när ny anslutning skulle ändras: TypeError: Det går inte att läsa egenskapen "version" av odefinierad

Om det här felmeddelandet visas när du försöker lägga till en anpassad anslutning kan anslutningsdata som lagras i den lokala autentiseringshanteraren vara skadade. Du kan lösa problemet genom att ta bort skadade lokala anslutningar och sedan lägga till dem igen:

1. Starta Lagringsutforskaren. Gå till **Hjälp** > **Växla utvecklarverktyg**på menyn .
2. I det öppnade fönstret går du till **Lokal lagring** (vänster) > **file://** på fliken **Program** .
3. Beroende på vilken typ av anslutning du har problem med letar du efter nyckeln och kopierar sedan dess värde till en textredigerare. Värdet är en matris med dina anpassade anslutningsnamn, till exempel följande:
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
4. När du har sparat dina aktuella anslutningsnamn anger du värdet i Utvecklarverktyg till `[]`.

Om du vill bevara de anslutningar som inte är skadade kan du använda följande steg för att hitta de skadade anslutningarna. Om du inte har något emot att förlora alla befintliga anslutningar kan du hoppa över dessa steg och följa de plattformsspecifika instruktionerna för att rensa anslutningsdata.

1. Lägg till varje anslutningsnamn i Utvecklarverktyg från en textredigerare och kontrollera sedan om anslutningen fortfarande fungerar.
2. Om en anslutning fungerar som den ska är den inte skadad och du kan lämna den säkert där. Om en anslutning inte fungerar tar du bort dess värde från Utvecklarverktyg och spelar in den så att du kan lägga till den igen senare.
3. Upprepa tills du har undersökt alla dina kontakter.

När du har gått igenom alla dina anslutningar, för alla anslutningar namn som inte läggs tillbaka, måste du rensa sina skadade data (om det finns några) och lägga till dem tillbaka med hjälp av standardstegen i Storage Explorer:

# <a name="windows"></a>[Windows](#tab/Windows)

1. Sök efter **Autentiseringsuppgifter och** öppna den på **Start-menyn.**
2. Gå till **Windows-autentiseringsuppgifter**.
3. Under **Allmänna autentiseringsuppgifter**letar du `<connection_type_key>/<corrupted_connection_name>` efter poster `StorageExplorer_CustomConnections_Accounts_v1/account1`som har nyckeln (till exempel ).
4. Ta bort dessa poster och lägg till anslutningarna igen.

# <a name="macos"></a>[Macos](#tab/macOS)

1. Öppna Spotlight (Kommando+Blanksteg) och sök efter **åtkomst till nyckelring**.
2. Leta efter poster som `<connection_type_key>/<corrupted_connection_name>` har nyckeln `StorageExplorer_CustomConnections_Accounts_v1/account1`(till exempel ).
3. Ta bort dessa poster och lägg till anslutningarna igen.

# <a name="linux"></a>[Linux](#tab/Linux)

Lokal hantering av autentiseringsuppgifter varierar beroende på Linux-distribution. Om din Linux-distribution inte tillhandahåller ett inbyggt GUI-verktyg för lokal autentiseringshantering kan du installera ett verktyg från tredje part för att hantera dina lokala autentiseringsuppgifter. Du kan till exempel använda [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), ett guidningsverktyg med öppen källkod för hantering av lokala Linux-autentiseringsuppgifter.

1. Öppna ditt lokala verktyg för hantering av autentiseringsuppgifter och hitta dina sparade autentiseringsuppgifter.
2. Leta efter poster som `<connection_type_key>/<corrupted_connection_name>` har nyckeln `StorageExplorer_CustomConnections_Accounts_v1/account1`(till exempel ).
3. Ta bort dessa poster och lägg till anslutningarna igen.
---

Om du fortfarande stöter på det här felet när du har kört de här stegen, eller om du vill dela det du misstänker har skadat anslutningarna, [öppnar du ett problem](https://github.com/microsoft/AzureStorageExplorer/issues) på vår GitHub-sida.

## <a name="issues-with-sas-url"></a>Problem med SAS-URL

Om du ansluter till en tjänst via en SAS-URL och har ett fel:

* Kontrollera att URL:en innehåller de behörigheter som krävs för att läsa eller lista resurser.
* Kontrollera att URL:en inte har upphört att gälla.
* Om SAS-URL:en baseras på en åtkomstprincip kontrollerar du att åtkomstprincipen inte har återkallats.

Om du av misstag har kopplats med hjälp av en ogiltig SAS-URL och nu inte kan koppla från gör du så här:

1. När du kör Storage Explorer trycker du på F12 för att öppna fönstret Utvecklarverktyg.
2. Välj **Lokal lagring** > **file://** i trädet till vänster på fliken **Program.**
3. Hitta nyckeln som är associerad med tjänsttypen för den problematiska SAS URI. Om den felaktiga SAS-URI:n till exempel är för `StorageExplorer_AddStorageServiceSAS_v1_blob`en blob-behållare letar du efter nyckeln .
4. Värdet på nyckeln ska vara en JSON-matris. Leta reda på objektet som är associerat med den felaktiga URI:n och ta sedan bort det.
5. Tryck på Ctrl+R för att läsa in Storage Explorer igen.

## <a name="linux-dependencies"></a>Linux-beroenden

Lagringsutforskaren 1.10.0 och senare är tillgänglig som ett kick från Snap Store. Snap-snapen Storage Explorer installerar alla dess beroenden automatiskt och uppdateras när en ny version av snapen är tillgänglig. Installera Storage Explorer snap är den rekommenderade metoden för installation.

Storage Explorer kräver användning av en lösenordshanterare, som du kan behöva ansluta manuellt innan Storage Explorer fungerar korrekt. Du kan ansluta Storage Explorer till systemets lösenordshanterare genom att köra följande kommando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Du kan också hämta programmet som en .tar.gz-fil, men du måste installera beroenden manuellt.

> [!IMPORTANT]
> Storage Explorer som anges i .tar.gz-hämtningen stöds endast för Ubuntu-distributioner. Andra distributioner har inte verifierats och kan kräva alternativa eller ytterligare paket.

Dessa paket är de vanligaste kraven för Storage Explorer på Linux:

* [.NET Core 2.2 Runtime](/dotnet/core/install/dependencies?tabs=netcore22&pivots=os-linux)
* `libgconf-2-4`
* `libgnome-keyring0` eller `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Storage Explorer version 1.7.0 och tidigare kräver .NET Core 2.0. Om du har installerat en nyare version av .NET Core måste du [korrigera Storage Explorer](#patching-storage-explorer-for-newer-versions-of-net-core). Om du kör Storage Explorer 1.8.0 eller senare bör du kunna använda upp till .NET Core 2.2. Versioner utöver 2.2 har inte verifierats för att fungera just nu.

# <a name="ubuntu-1904"></a>[Ubuntu 19,04](#tab/1904)

1. Hämta Storage Explorer.
2. Installera [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Kör följande kommando:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Hämta Storage Explorer.
2. Installera [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Kör följande kommando:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16,04](#tab/1604)

1. Hämta Storage Explorer.
2. Installera [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Kör följande kommando:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404"></a>[Ubuntu 14.04](#tab/1404)

1. Hämta Storage Explorer.
2. Installera [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Kör följande kommando:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Korrigering av Lagringsutforskaren för nyare versioner av .NET Core

För Storage Explorer 1.7.0 eller tidigare kan du behöva korrigera den version av .NET Core som används av Storage Explorer:

1. Ladda ner version 1.5.43 av StreamJsonRpc [från NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Leta efter länken "Hämta paket" till höger på sidan.
2. När du har hämtat paketet `.nupkg` ändrar du filändelsen från till `.zip`.
3. Packa upp paketet.
4. Öppna mappen `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopiera `StreamJsonRpc.dll` till följande platser i mappen Lagringsutforskaren:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Öppna i Utforskaren" från Azure-portalen fungerar inte

Om knappen **Öppna i Utforskaren** på Azure-portalen inte fungerar kontrollerar du att du använder en kompatibel webbläsare. Följande webbläsare har testats för kompatibilitet:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Nästa steg

Om ingen av dessa lösningar fungerar för dig [öppnar du ett problem i GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Du kan också göra detta genom att välja **rapportproblemet till GitHub-knappen** i det nedre vänstra hörnet.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)
