---
title: Felsökningsguide för Azure Storage Explorer | Microsoft Docs
description: Översikt över de två felsökning funktion i Azure
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.component: common
ms.openlocfilehash: ffb355b4471bd8455f67e657d9557c3f372c3f4e
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470328"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Felsökningsguide för Azure Storage Explorer

Microsoft Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, macOS och Linux. Appen kan ansluta till lagringskonton i Azure, nationella moln och Azure Stack.

Den här guiden beskriver lösningar på vanliga problem i Storage Explorer.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fel: Självsignerat certifikat i certifikatkedjan (och liknande fel)

Certifikatfel orsakas av en av de två följande situationer:

1. Appen är ansluten via en ”transparent proxy”, vilket innebär att en server (till exempel företagsservern) avlyssnar HTTPS-trafik, dekrypterar den och sedan krypterar den med hjälp av ett självsignerat certifikat.
2. Du kör ett program som infogar ett självsignerat SSL-certifikat till HTTPS-meddelanden som visas. Exempel på program som att mata in certifikat innehåller programvara som antivirus och nätverket trafik-kontroll.

När Storage Explorer ser ett självsignerat signerade eller ej betrodda certifikat kan inte längre veta om det mottagna meddelandet HTTPS har ändrats. Om du har en kopia av det självsignerade certifikatet kan du instruera Lagringsutforskaren förtroende genom att göra följande:

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
4. Leta efter självsignerade certifikat. Om du är osäker på vilka som är självsignerade, leta efter var som helst ämnet `("s:")` och `("i:")` är desamma.
5. När du har hittat självsignerade certifikat för vart och ett, kopiera och klistra in allt från och med **---BEGIN CERTIFICATE---** till **---END CERTIFICATE---** till en ny .cer-fil.
6. Öppna Storage Explorer, klicka på **redigera** > **SSL-certifikat** > **Importera certifikat**, och Använd filväljaren för att hitta, select, och Öppna CER-filen som du skapade.

Om du inte hittar något självsignerat certifikat med föregående steg kan du kontakta oss med verktyget feedback för mer hjälp. Du kan också välja att starta Lagringsutforskaren från kommandoraden med den `--ignore-certificate-errors` flaggan. När startas med den här flaggan ignorerar Lagringsutforskaren certifikatfel.

## <a name="sign-in-issues"></a>Inloggningsproblem

### <a name="reauthentication-loop-or-upn-change"></a>Återautentisering slinga eller UPN-ändring
Om du är i en loop omautentisering eller har ändrats UPN-namnet för ett av dina konton, kan du prova följande:
1. Ta bort alla konton och stäng sedan Storage Explorer
2. Ta bort den. IdentityService mappen från din dator. På Windows, mappen finns i `C:\users\<username>\AppData\Local`. Du kan hitta mapp i användarkatalogen roten för Mac och Linux.
3. Om du använder Mac- eller Linux, måste du också ta bort posten Microsoft.Developer.IdentityService från ditt operativsystem keystore. På Mac är keystore ”gör väldigt lätt nyckelringar”-program. Programmet kallas vanligtvis ”nyckelringen” för Linux, men namnet kan vara olika beroende på din distribution.

## <a name="mac-keychain-errors"></a>Mac-nyckelringen fel
MacOS nyckelring kan ibland hamna i ett tillstånd som orsakar problem med Storage Explorer-autentiseringsbiblioteket. Hämta nyckelringen utanför det här tillståndet prova följande steg:
1. Stäng Storage Explorer.
2. Öppna nyckelring (**cmd + blanksteg**, skriver i nyckelringen, träffar ange).
3. Välj ”login”-nyckelringen.
4. Klicka på hänglåsikonen om du vill låsa nyckelringen (ett hänglås animera till en låst plats när du är klar, det kan ta några sekunder beroende på vilka appar du har öppna).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Starta Lagringsutforskaren.
6. Ett popup-fönster visas som säger något som ”Service hub vill komma åt nyckelringen”. När den, ange ditt Mac administratörslösenordet och klickar på **Tillåt alltid** (eller **Tillåt** om **Tillåt alltid** är inte tillgänglig).
7. Försök att logga in.

### <a name="general-sign-in-troubleshooting-steps"></a>Allmän inloggning felsökningssteg
* Om du är på macOS och fönstret för inloggning visas aldrig över dialogrutan ”väntar på verifiering...”, försök [de här stegen](#mac-keychain-errors)
* Starta om Lagringsutforskaren
* Om fönstret autentisering är tom, vänta minst en minut innan du stänger dialogrutan för autentisering.
* Se till att proxy- och certifikat som är rätt konfigurerade, inställningar för både din dator och Storage Explorer.
* Om du är på Windows och har tillgång till Visual Studio 2017 på samma dator och logga in, försök att logga in till Visual Studio 2017. Efter en lyckad inloggning till Visual Studio 2017, bör du kunna öppna Storage Explorer och se ditt konto på panelen konto. 

Om ingen av dessa metoder fungerar [öppna ett ärende på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Prenumerationer som saknas och bruten klienter

Om det inte går att hämta dina prenumerationer när du har loggat in kan du prova följande metoder för felsökning:

* Kontrollera att ditt konto har åtkomst till de prenumerationer som du förväntar dig. Du kan kontrollera att du har åtkomst genom att logga in portalen för Azure-miljö du vill använda.
* Se till att du har loggat in med rätt Azure-miljö (Azure, Azure Kina, Azure Germany, Azure US Government eller anpassad miljö).
* Om du är bakom en proxyserver, se till att du har konfigurerat Storage Explorer-proxyservern korrekt.
* Försök att ta bort och lägga till kontot igen.
* Om det finns en ”mer information”-länk, titta och se vilka felmeddelanden rapporteras för klienter som misslyckas. Om du är osäker på hur de felmeddelanden du se sedan gärna [öppna ett ärende på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Det går inte att ta bort bifogade konto eller storage-resurs

Om det inte går att ta bort ett anslutna konto eller en resurs för lagring via Användargränssnittet, kan du manuellt ta bort alla anslutna resurser genom att ta bort följande mappar:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Applicaiton Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  Stäng Lagringsutforskaren innan du tar bort de ovanstående mapparna.

> [!NOTE]
>  Om du någonsin har importerat eventuella SSL-certifikat och sedan säkerhetskopiera innehållet i den `certs` directory. Du kan senare använda säkerhetskopian för att importera SSL-certifikat.

## <a name="proxy-issues"></a>Proxyproblem

Kontrollera först att du har angett följande stämmer:

* Proxy-URL och portnummer
* Användarnamn och lösenord om det behövs av proxyn

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

## <a name="issues-with-sas-url"></a>Problem med SAS-URL
Om du vill ansluta till en tjänst med hjälp av en SAS-URL och upplever det här felet:

* Kontrollera att URL: en ger tillräcklig behörighet för att läsa eller lista resurser.
* Kontrollera att URL: en inte har gått ut.
* Om SAS-Webbadressen är baserad på en åtkomstprincip, kontrollerar du att åtkomstprincipen inte har återkallats.

Följ dessa steg om du av misstag ansluten med hjälp av en ogiltig SAS-URL och kan inte koppla från:
1.  När du kör Lagringsutforskaren, trycker du på F12 för att öppna fönstret med utvecklingsverktyg.
2.  Klicka på fliken program och klicka sedan på lokal lagring > file:// i trädet till vänster.
3.  Hitta nyckeln som associeras med tjänsttypen problematiska SAS-URI. Till exempel om dåligt SAS-URI är för en blob-behållare, letar du efter nyckeln med namnet `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  Värdet för nyckeln ska vara en JSON-matris. Hitta det objekt som är associerade med den felaktiga URI och ta bort den.
5.  Tryck på Ctrl + R för att läsa in Storage Explorer.

## <a name="linux-dependencies"></a>Linux-beroenden

Du kan behöva installera några beroenden manuellt för Linux-distributioner än Ubuntu 16.04. I allmänhet krävs följande paket:
* [.NET core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* Uppdaterade GCC

Beroende på din distribution, kan det finnas andra paket som du behöver installera. Lagringsutforskaren [viktig](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) innehålla specifika åtgärder för vissa distributioner.

## <a name="next-steps"></a>Nästa steg

Om ingen av lösningarna fungerar för dig, sedan [öppna ett ärende på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Du kan också snabbt få till GitHub med hjälp av knappen ”rapportera fel till GitHub” i det nedre vänstra hörnet.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)
