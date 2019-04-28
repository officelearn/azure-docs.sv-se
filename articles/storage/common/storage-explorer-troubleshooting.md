---
title: Felsökningsguide för Azure Storage Explorer | Microsoft Docs
description: Översikt över felsökning tekniker för Azure Storage Explorer
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3e26365c4273611c81682a760695522575f3875d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483392"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Felsökningsguide för Azure Storage Explorer

Microsoft Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, macOS och Linux. Appen kan ansluta till lagringskonton i Azure, nationella moln och Azure Stack.

Den här guiden beskriver lösningar på vanliga problem i Storage Explorer.

## <a name="role-based-access-control-permission-issues"></a>Role-based Access Control behörighetsproblem

[Rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) ger detaljerad åtkomsthantering för Azure-resurser genom att kombinera uppsättningar av behörigheter till _roller_. Här följer några förslag som du kan följa för att få RBAC som arbetar i Storage Explorer.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Vad behöver jag att se mina resurser i Storage Explorer?

Om du har problem med åtkomst till lagringsresurser med RBAC kan vara det eftersom du inte har tilldelats lämpliga roller. I följande avsnitt beskrivs de behörigheter som Storage Explorer för närvarande kräver åtkomst till dina lagringsresurser.

Kontakta administratören för Azure-konto om du är osäker på om du har rätt roller eller behörigheter.

#### <a name="read-listget-storage-accounts"></a>Läs: Lista/hämta lagringskonton

Du måste ha behörighet att lista lagringskonton. Du kan hämta den här behörigheten genom att de tilldelas rollen ”läsare”.

#### <a name="list-storage-account-keys"></a>Lista nycklar för lagringskonto

Lagringsutforskaren kan också använda nycklar för att autentisera begäranden. Du kan få åtkomst till nycklar med mer kraftfulla roller, till exempel rollen ”deltagare”.

> [!NOTE]
> Åtkomstnycklar ge obegränsad behörighet till någon som innehar dem. Därför vanligtvis rekommenderas inte de lämnas till användare av konton. Om du vill återkalla åtkomstnycklar kan du återskapa dem från den [Azure-portalen](https://portal.azure.com/).

#### <a name="data-roles"></a>Data-roller

Du måste tilldelas minst en roll som ger åtkomst läsa data från resurser. Till exempel om du vill visa eller ladda ned blobar behöver du minst rollen ”Storage Blob Data Reader”.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Varför behöver jag en ledningsroll layer se mina resurser i Storage Explorer?

Azure Storage har två nivåer av åtkomst: _management_ och _data_. Prenumerationer och lagringskonton nås via hanteringslagret. Behållare, blobar och andra dataresurser som kan nås via data-lagret. Till exempel om du vill hämta en lista över dina lagringskonton från Azure kan skicka du en begäran till hanteringsslutpunkten. Om du vill en lista över blob-behållare i ett konto som kan skicka en begäran till lämplig tjänsteslutpunkt.

RBAC-roller kan innehålla behörigheter för layer management eller data. Rollen ”läsare” ger till exempel skrivskyddad åtkomst management layer-resurser.

Strikt sett rollen ”läsare” innehåller inga data layer behörigheter och är inte nödvändiga för att komma åt data-lagret.

Lagringsutforskaren gör det enkelt att komma åt dina resurser genom att samla in nödvändig information för att ansluta till dina Azure-resurser åt dig. Om du vill visa din blob-behållare, skickar Lagringsutforskaren exempelvis en begäran om hanteringspaketlista behållare till blob-tjänsteslutpunkt. För att få att slutpunkten kan Lagringsutforskaren söker i listan över prenumerationer och lagringskonton som du har åtkomst till. Men för att hitta dina prenumerationer och lagringskonton, Lagringsutforskaren också behöver åtkomst till hanteringslagret.

Om du inte har en roll som tillståndsbeviljande ett lager, kan inte Storage Explorer hämta information som behövs för att ansluta till data-lagret.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Vad händer om jag kan inte hämta hanteringen layer behörigheter måste från min administratör?

Vi har ännu inte en RBAC-relaterade lösning just nu. Som en lösning kan du begära en SAS-URI att [ansluta till din resurs](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#attach-a-service-by-using-a-shared-access-signature-sas).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fel: Självsignerat certifikat i certifikatkedjan (och liknande fel)

Certifikatfel orsakas av en av de två följande situationer:

1. Appen är ansluten via en ”transparent proxy”, vilket innebär att en server (till exempel företagsservern) avlyssnar HTTPS-trafik, dekrypterar den och sedan krypterar den med hjälp av ett självsignerat certifikat.
2. Du kör ett program som infogar ett självsignerat SSL-certifikat till HTTPS-meddelanden som visas. Exempel på program som att mata in certifikat innehåller programvara som antivirus och nätverket trafik-kontroll.

När Storage Explorer ser ett självsignerat eller ej betrodda certifikat, kan den inte längre veta om det mottagna meddelandet HTTPS har ändrats. Om du har en kopia av det självsignerade certifikatet kan du instruera Lagringsutforskaren förtroende genom att göra följande:

1. Hämta en Base64-kodad X.509 (.cer) kopia av certifikatet
2. Klicka på **redigera** > **SSL-certifikat** > **Importera certifikat**, och Använd filväljaren för att hitta, markera och öppna CER-filen

Det här problemet kan också vara resultatet av flera certifikat (rot och mellanliggande). Båda certifikaten måste läggas till att lösa felet.

Om du är osäker på var certifikaten som kommer från, kan du dessa steg för att hitta den:

1. Installera öppen SSL
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (någon av de enklare versionerna bör vara tillräckligt med)
    * Mac och Linux: ska ingå i ditt operativsystem
2. Kör öppen SSL
    * Windows: öppna installationskatalogen, klicka på **/bin/**, och dubbelklicka sedan på **openssl.exe**.
    * Mac och Linux: kör **openssl** från en terminal.
3. Kör `s_client -showcerts -connect microsoft.com:443`
4. Leta efter självsignerade certifikat. Om du är osäker på vilka certifikat som är självsignerade, leta efter var som helst ämnet `("s:")` och `("i:")` är desamma.
5. När du har hittat självsignerade certifikat för vart och ett, kopiera och klistra in allt från och med **---BEGIN CERTIFICATE---** till **---END CERTIFICATE---** till en ny .cer-fil.
6. Öppna Storage Explorer, klicka på **redigera** > **SSL-certifikat** > **Importera certifikat**, och Använd filväljaren för att hitta, select, och Öppna CER-filen som du skapade.

Om du inte hittar något självsignerat certifikat med föregående steg kan du kontakta oss med verktyget feedback för mer hjälp. Du kan också välja att starta Lagringsutforskaren från kommandoraden med den `--ignore-certificate-errors` flaggan. När startas med den här flaggan ignorerar Lagringsutforskaren certifikatfel.

## <a name="sign-in-issues"></a>Inloggningsproblem

### <a name="blank-sign-in-dialog"></a>Tom inloggningsrutan

Tom inloggning dialogrutor orsakas oftast av AD FS ber Storage Explorer att utföra en omdirigering, som inte stöds av Electron. Du kan försöka att använda enheten kod Flow för att logga in för att lösa problemet. Det gör du på följande sätt:

1. ”Gå till förhandsversion” -> ”använda kod Enhetsinloggning”.
2. Öppna dialogrutan Anslut (antingen via ikonen plugin på den vänstra vertikalstreck eller ”Lägg till konto” på panelen konto).
3. Välj vilken miljö som du vill logga in på.
4. Klicka på ”signera” i knappen.
5. Följ anvisningarna på nästa panel.

Om du har problem med att logga in på kontot som du vill använda eftersom din standardwebbläsare redan är inloggad på ett annat konto, kan du antingen:

1. Manuellt kopiera länken och koden i en privat session i webbläsaren.
2. Manuellt kopiera länken och koden i en annan webbläsare.

### <a name="reauthentication-loop-or-upn-change"></a>Återautentisering slinga eller UPN-ändring

Om du är i en loop omautentisering eller har ändrats UPN-namnet för ett av dina konton, kan du prova följande:

1. Ta bort alla konton och stäng sedan Storage Explorer
2. Ta bort den. IdentityService mappen från din dator. På Windows, mappen finns i `C:\users\<username>\AppData\Local`. Du kan hitta mapp i användarkatalogen roten för Mac och Linux.
3. Om du använder Mac- eller Linux, måste du också ta bort posten Microsoft.Developer.IdentityService från ditt operativsystem keystore. På Mac är keystore ”gör väldigt lätt nyckelringar”-program. Programmet kallas vanligtvis ”nyckelringen” för Linux, men namnet kan vara olika beroende på din distribution.

### <a name="conditional-access"></a>Villkorlig åtkomst

Villkorlig åtkomst stöds inte när Lagringsutforskaren används på Windows 10, Linux eller macOS. Detta beror på en begränsning i AAD-biblioteket som används av Storage Explorer.

## <a name="mac-keychain-errors"></a>Mac-nyckelringen fel

MacOS nyckelring kan ibland hamna i ett tillstånd som orsakar problem med Storage Explorer-autentiseringsbiblioteket. För att få nyckelringen utanför det här tillståndet kan du prova följande steg:

1. Stäng Storage Explorer.
2. Öppna nyckelring (**cmd + blanksteg**, skriver i nyckelringen, träffar ange).
3. Välj ”login”-nyckelringen.
4. Klicka på hänglåsikonen om du vill låsa nyckelringen (ett hänglås animera till en låst plats när du är klar, det kan ta några sekunder beroende på vilka appar du har öppna).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Starta Lagringsutforskaren.
6. Ett popup-fönster visas som säger något som ”Service hub vill komma åt nyckelringen”. När den, ange ditt Mac administratörslösenordet och klickar på **Tillåt alltid** (eller **Tillåt** om **Tillåt alltid** är inte tillgänglig).
7. Försök att logga in.

### <a name="general-sign-in-troubleshooting-steps"></a>Allmän inloggning felsökningssteg

* Om du är på macOS och fönstret för inloggning visas aldrig över den ”väntar på verifiering...” dialogrutan försök [de här stegen](#mac-keychain-errors)
* Starta om Lagringsutforskaren
* Om fönstret autentisering är tom, vänta minst en minut innan du stänger dialogrutan för autentisering.
* Se till att proxy- och certifikat som är rätt konfigurerade, inställningar för både din dator och Storage Explorer.
* Om du är på Windows och har tillgång till Visual Studio 2017 på samma dator och logga in, försök att logga in till Visual Studio 2017. Efter en lyckad inloggning till Visual Studio 2017, bör du kunna öppna Storage Explorer och se ditt konto på panelen konto.

Om ingen av dessa metoder fungerar [öppna ett ärende på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Prenumerationer som saknas och bruten klienter

Om det inte går att hämta dina prenumerationer när du har loggat in kan du prova följande metoder för felsökning:

* Kontrollera att ditt konto har åtkomst till de prenumerationer som du förväntar dig. Du kan verifiera din åtkomst genom att logga in portalen för Azure-miljö du vill använda.
* Se till att du har loggat in med rätt Azure miljö (Azure, Azure Kina 21Vianet, Azure Germany, Azure US Government eller anpassad miljö).
* Om du är bakom en proxyserver, se till att du har konfigurerat Storage Explorer-proxyservern korrekt.
* Försök att ta bort och lägga till kontot igen.
* Om det finns en ”mer information”-länk, titta och se vilka felmeddelanden rapporteras för klienter som misslyckas. Om du är osäker på hur de felmeddelanden du se sedan gärna [öppna ett ärende på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Det går inte att ta bort bifogade konto eller storage-resurs

Om det inte går att ta bort ett anslutna konto eller en resurs för lagring via Användargränssnittet, kan du manuellt ta bort alla anslutna resurser genom att ta bort följande mappar:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Stäng Lagringsutforskaren innan du tar bort de ovanstående mapparna.

> [!NOTE]
> Om du någonsin har importerat eventuella SSL-certifikat och sedan säkerhetskopiera innehållet i den `certs` directory. Du kan senare använda säkerhetskopian för att importera SSL-certifikat.

## <a name="proxy-issues"></a>Proxyproblem

Kontrollera först att du har angett följande stämmer:

* Proxy-URL och portnummer
* Användarnamn och lösenord om det behövs av proxyn

> [!NOTE]
> Lagringsutforskaren stöder inte filer för automatisk konfiguration av proxy för att konfigurera proxy-inställningar.

### <a name="common-solutions"></a>Vanliga lösningar

Om du fortfarande har problem, kan du prova följande metoder:

* Om du kan ansluta till Internet utan att använda proxyservern kan du kontrollera att Storage Explorer fungerar utan proxy-inställningar som är aktiverad. Om så är fallet kan det finnas ett problem med proxyinställningarna. Arbeta med din proxy-administratör att identifiera problem.
* Kontrollera att andra program med proxyservern fungerar som förväntat.
* Kontrollera att du kan ansluta till Azure-miljön du försöker använda portalen
* Kontrollera att du kan ta emot svar från dina tjänstslutpunkter. Ange någon av slutpunkt-webbadresser i din webbläsare. Om du kan ansluta, får du en InvalidQueryParameterValue eller liknande XML-svaret.
* Om någon annan också använder Storage Explorer med proxyservern, kontrollerar du att de kan ansluta. Om de kan ansluta kan du behöva kontakta serveradministratören proxy.

### <a name="tools-for-diagnosing-issues"></a>Verktyg för att diagnostisera problem

Om du har verktyg för nätverk, till exempel Fiddler för Windows kan du diagnostisera problem på följande sätt:

* Om du behöver gå igenom din proxyserver kan du behöva konfigurera ditt nätverk verktyg för att ansluta via proxy.
* Kontrollera det portnummer som används av ditt verktyg för nätverk.
* Ange lokal värd-URL och portnummer för verktyget nätverk som proxyinställningar i Storage Explorer. När görs på rätt sätt startar ditt verktyg för nätverk loggning nätverksbegäranden gjorda av Storage Explorer till hanterings- och slutpunkter. Ange till exempel https://cawablobgrs.blob.core.windows.net/ för blob-slutpunkt i en webbläsare och du får ett svar liknar följande, vilket tyder på resursen finns, även om du inte kommer åt den.

![Kodexempel](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Kontakta serveradministratören för proxy

Om proxyinställningarna är korrekta, du kan behöva kontakta din serveradministratör för proxy och

* Se till att proxyservern inte blockerar trafik till Azure management eller resurs-slutpunkter.
* Kontrollera autentiseringsprotokoll som används av proxyservern. Lagringsutforskaren stöder för närvarande inte NTLM-proxyservrar.

## <a name="unable-to-retrieve-children-error-message"></a>”Det gick inte att hämta underordnade” felmeddelande

Om du är ansluten till Azure via en proxyserver, kontrollerar du att proxyinställningarna är korrekta. Om du har beviljats åtkomst till en resurs från ägaren av prenumerationen eller konto, kontrollera att du har läst eller lista över behörigheter för den resursen.

## <a name="connection-string-does-not-have-complete-configuration-settings"></a>Anslutningssträngen har inte slutförts konfigurationsinställningar

Om du får detta felmeddelande är det möjligt att du inte har behörigheten som krävs för att få nycklarna för ditt lagringskonto. Gå till portalen för att bekräfta om så är fallet, och leta upp ditt Storage-konto. Du kan snabbt göra detta genom att högerklicka på noden för ditt lagringskonto och klicka på ”Öppna i portalen”. När du gör det, går du till bladet ”åtkomstnycklar”. Om du inte har behörighet att visa nycklar, sedan visas en sida med meddelandet ”du inte har åtkomst”. Undvik problemet genom du antingen hämta kontonyckeln från någon annan och bifoga med namn och nyckel, eller du kan be någon för en SAS för lagringskontot och använda den för att ansluta till Storage-kontot.

Om du ser nycklar för kontot kan du rapportera problemet på GitHub så att vi kan hjälpa dig att lösa problemet.

## <a name="issues-with-sas-url"></a>Problem med SAS-URL

Om du vill ansluta till en tjänst med hjälp av en SAS-URL och upplever det här felet:

* Kontrollera att URL: en ger tillräcklig behörighet för att läsa eller lista resurser.
* Kontrollera att URL: en inte har gått ut.
* Om SAS-Webbadressen är baserad på en åtkomstprincip, kontrollerar du att åtkomstprincipen inte har återkallats.

Följ dessa steg om du av misstag ansluten med hjälp av en ogiltig SAS-URL och kan inte koppla från:

1. När du kör Lagringsutforskaren, trycker du på F12 för att öppna fönstret med utvecklingsverktyg.
2. Klicka på fliken program och klicka sedan på lokal lagring > file:// i trädet till vänster.
3. Hitta nyckeln som associeras med tjänsttypen problematiska SAS-URI. Till exempel om dåligt SAS-URI är för en blob-behållare, letar du efter nyckeln med namnet `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. Värdet för nyckeln ska vara en JSON-matris. Hitta det objekt som är associerade med den felaktiga URI och ta bort den.
5. Tryck på Ctrl + R för att läsa in Storage Explorer.

## <a name="linux-dependencies"></a>Linux-beroenden

I allmänhet krävs följande paket för att köra Storage Explorer på Linux:

* [.NET Core 2.0 Runtime](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgnome-keyring-common` och `libgnome-keyring-dev`
* `libgconf-2-4`

Beroende på din distribution kan det finnas olika eller paket måste du installera.

Lagringsutforskaren stöds officiellt på Ubuntu 18.04, 16.04 och 14.04. Installationssteg för en ren dator är följande:

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. Ladda ned Storage Explorer
2. Installera .NET Core Runtime, senaste verifierade versionen är: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-2.0.8) (om du redan har installerat en senare version, du kan behöva uppdatera Lagringsutforskaren, se nedan)
3. Kör `sudo apt-get install libgconf-2-4`
4. Kör `sudo apt install libgnome-keyring-common libgnome-keyring-dev`

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Ladda ned Storage Explorer
2. Installera .NET Core Runtime, senaste verifierade versionen är: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-2.0.8) (om du redan har installerat en senare version, du kan behöva uppdatera Lagringsutforskaren, se nedan)
3. Kör `sudo apt install libgnome-keyring-dev`

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Ladda ned Storage Explorer
2. Installera .NET Core Runtime, senaste verifierade versionen är: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-2.0.8) (om du redan har installerat en senare version, du kan behöva uppdatera Lagringsutforskaren, se nedan)
3. Kör `sudo apt install libgnome-keyring-dev`

---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Korrigeringar Storage Explorer för nyare versioner av .NET Core 
Om du har en version av .NET Core är större än 2.0 installerat och som kör Lagringsutforskaren version 1.7.0 eller äldre, behöver du troligen att korrigera Storage Explorer genom att utföra följande steg:
1. Hämta versionen 1.5.43 av StreamJsonRpc [från nuget](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Leta efter länken ”Hämta paketet” höger på sidan.
2. När du laddar ned paketet, byter du dess filnamnstillägg från `.nupkg` till `.zip`
3. Packa upp paketet
4. Gå till `streamjsonrpc.1.5.43/lib/netstandard1.1/`
5. Kopiera `StreamJsonRpc.dll` på följande platser i mappen Storage Explorer:
    1. `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
    2. `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Öppna i Explorer från Azure portal fungerar inte

Om knappen ”Öppna i Explorer” i Azure-portalen inte fungerar för dig kan du kontrollera att du använder en kompatibel webbläsare. Följande webbläsare har testats för kompatibilitet.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Nästa steg

Om ingen av lösningarna fungerar för dig, sedan [öppna ett ärende på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Du kan också snabbt få till GitHub med hjälp av knappen ”rapportera fel till GitHub” i det nedre vänstra hörnet.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)
