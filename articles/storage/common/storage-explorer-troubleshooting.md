---
title: Azure Lagringsutforskaren felsökningsguiden | Microsoft Docs
description: Översikt över de två felsökning funktion i Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: f58fb5090aba3c5052d1bbdec76225d0ae50e8f2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Lagringsutforskaren felsökningsguiden

Microsoft Azure Lagringsutforskaren är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, macOS och Linux. Appen kan ansluta till lagringskonton finns i Azure, National moln och Azure-stacken.

Den här guiden beskrivs lösningar på vanliga problem som visas i Lagringsutforskaren.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fel: Självsignerat certifikat i certifikatkedjan (och liknande fel)

Certifikatfel orsakas av något av följande två situationer:

1. Appen är anslutna via en ”transparent proxy”, vilket innebär att en server (till exempel företagets servern) avlyssna HTTPS-trafik, att dekryptera den och kryptera den med hjälp av ett självsignerat certifikat.
2. Du kör ett program som är att injicera en självsignerat SSL-certifikatet till HTTPS-meddelanden som visas. Exempel på program som att mata in certifikat innehåller programvara som antivirus- och trafik kontroll.

När Lagringsutforskaren ser ett självsignerat signerade eller inte betrott certifikat kan inte längre vet om det mottagna meddelandet HTTPS har ändrats. Om du har en kopia av det självsignerade certifikatet du instruera Lagringsutforskaren förtroende genom att göra följande:

1. Hämta en Base64-kodad X.509 (.cer) kopia av certifikatet
2. Klicka på **redigera** > **SSL-certifikat** > **Importera certifikat**, och Använd filväljaren att hitta, väljer och öppnar den .cer-fil

Det här problemet kan också vara resultatet av flera certifikat (roten och mellanliggande). Båda certifikaten måste läggas till att lösa felet.

Om du är osäker på om certifikatet kommer från kan du dessa steg för att hitta den:

1. Installera öppen SSL

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (någon av de lätta versionerna bör vara tillräckligt)
    * Mac- och Linux: ska ingå i ditt operativsystem
2. Kör öppen SSL

    * Windows: öppna installationskatalogen, klicka på **/bin/**, och dubbelklicka sedan på **openssl.exe**.
    * Mac- och Linux: kör **openssl** från en terminal.
3. Kör `s_client -showcerts -connect microsoft.com:443`
4. Leta efter självsignerade certifikat. Om du inte vet vilket är självsignerade leta efter var som helst ämnet `("s:")` och utfärdare `("i:")` är samma.
5. När du har hittat självsignerade certifikat för var och en, kopiera och klistra in allt från och med **---BEGIN CERTIFICATE---** till **---END CERTIFICATE---** till en ny .cer-fil.
6. Öppna Lagringsutforskaren, klicka på **redigera** > **SSL-certifikat** > **Importera certifikat**, och sedan använda filväljaren för att söka efter och välj Öppna CER-filen som du skapade.

Om du inte hittar någon självsignerade certifikat med föregående steg, kontaktar du oss via verktyget feedback för mer hjälp. Du kan också välja att starta Lagringsutforskaren från kommandoraden med den `--ignore-certificate-errors` flaggan. Startades med flaggan ignorerar Lagringsutforskaren certifikatfel.

## <a name="sign-in-issues"></a>Inloggningsproblem

Om det inte går att logga in, kan du prova följande metoder:

* Starta om Lagringsutforskaren
* Om fönstret autentisering är tomt, vänta minst en minut innan du stänger dialogrutan autentisering.
* Se till att proxy- och certifikatet inställningar har konfigurerats korrekt för din dator och ett Lagringsutforskaren
* Om du i Windows och har tillgång till Visual Studio 2017 på samma dator och logga in, försök logga in på Visual Studio 2017

Om ingen av dessa metoder fungerar [öppna ett problem på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="unable-to-retrieve-subscriptions"></a>Det gick inte att hämta prenumerationer

Om det inte går att hämta dina prenumerationer när du har loggat in kan du prova följande metoder för felsökning:

* Kontrollera att ditt konto har åtkomst till de prenumerationer som du förväntar dig. Du kan kontrollera att du har åtkomst genom att logga in på portalen för Azure-miljön du försöker använda.
* Kontrollera att du har loggat in med rätt Azure miljö (Azure, Azure Kina, Tyskland Azure, Azure som tillhör amerikanska myndigheter eller anpassade miljön).
* Om du är bakom en proxyserver, se till att du har konfigurerat korrekt Lagringsutforskaren proxy.
* Försök att ta bort och readding kontot.
* Titta på Verktyg-utvecklarkonsolen (Hjälp > Växla utvecklingsverktyg) medan Lagringsutforskaren laddas prenumerationer. Leta efter felmeddelanden (rött) eller ett meddelande som innehåller texten ”kunde inte ladda prenumerationer för klienten”. Om du ser om meddelanden, [öppna ett problem på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Det går inte att ta bort anslutna konto eller lagring resurs

Om det inte går att ta bort ett bifogade konto eller lagringsresurs via Användargränssnittet, kan du manuellt ta bort alla kopplade resurser genom att ta bort följande mappar:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Applicaiton Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  Stäng Lagringsutforskaren innan du tar bort mapparna ovan.

> [!NOTE]
>  Om du någon gång har importerat alla SSL-certifikat och sedan säkerhetskopiera innehållet i den `certs` directory. Du kan senare använda säkerhetskopian importera SSL-certifikat.

## <a name="proxy-issues"></a>Proxy-problem

Kontrollera först att följande information som du angett är korrekta:

* Proxy-URL och portnummer * användarnamn och lösenord om det krävs av proxy

### <a name="common-solutions"></a>Vanliga lösningar

Om du fortfarande har problem, kan du prova följande metoder:

* Om du kan ansluta till Internet utan att använda proxyservern kan du kontrollera att Lagringsutforskaren fungerar utan aktiverade proxyinställningar. Om så är fallet kan det vara ett problem med proxyinställningarna. Arbeta med din proxy-administratör att identifiera problem.
* Kontrollera att andra program med hjälp av proxyservern fungerar som förväntat.
* Kontrollera att du kan ansluta till portalen för Azure-miljön du försöker använda
* Kontrollera att du kan få svar från dina Tjänsteslutpunkter. Ange en slutpunkt-URL i webbläsaren. Om du kan ansluta bör du få ett InvalidQueryParameterValue eller liknande XML-svaret.
* Om någon annan också använder Lagringsutforskaren med proxyservern, kontrollera att de kan ansluta. Om de kan ansluta kan du behöva kontakta din proxy server-administratör.

### <a name="tools-for-diagnosing-issues"></a>Verktyg för att diagnostisera problem

Om du har nätverk verktyg, till exempel Fiddler för Windows kan du diagnostisera felet på följande sätt:

* Om du behöver gå igenom din proxyserver måste du kanske konfigurera nätverk verktyget för att ansluta till proxyservern.
* Kontrollera portnumret som används av ditt nätverk.
* Ange den lokala värd-URL och portnummer för verktyget nätverk som proxyinställningarna i Lagringsutforskaren. När utförs på rätt sätt, startar ditt nätverk verktyget loggning nätverksbegäranden av Lagringsutforskaren hanterings- och slutpunkter. Ange till exempel https://cawablobgrs.blob.core.windows.net/ för din slutpunkt för blob i en webbläsare och du får ett svar liknar följande, vilket tyder på resursen finns, även om du inte kommer åt den.

![kodexempel](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Kontakta serveradministratören för proxy

Om proxyinställningarna är korrekta, du kan behöva kontakta administratören proxy-server och

* Se till att proxyservern inte blockerar trafik till Azure hanterings- eller resurs-slutpunkter.
* Kontrollera autentiseringsprotokollet som används av proxyservern. Lagringsutforskaren stöder för närvarande inte NTLM-proxyservrar.

## <a name="unable-to-retrieve-children-error-message"></a>”Det gick inte att hämta underordnade” felmeddelande

Om du är ansluten till Azure via en proxyserver, kontrollerar du att proxyinställningarna är korrekta. Om du har beviljats åtkomst till en resurs från ägaren av prenumerationen eller konto, kontrollera att du har läst eller visa en lista med behörigheter för resursen.

### <a name="issues-with-sas-url"></a>Problem med SAS-URL
Om du vill ansluta till en tjänst med hjälp av en SAS-URL och det här felet:

* Kontrollera att URL: en ger tillräcklig behörighet för att läsa eller visa en lista med resurser.
* Kontrollera att URL: en inte har gått ut.
* Om SAS-URL är baserat på en åtkomstprincip, kontrollerar du att åtkomstprincipen inte har återkallats.

Följ dessa steg om du av misstag ansluten med en ogiltig SAS-URL och inte går att koppla från:
1.  När du kör Lagringsutforskaren trycker du på F12 för att öppna fönstret developer tools.
2.  Klicka på fliken program och klicka sedan på lokal lagring > file:// i trädet till vänster.
3.  Hitta nyckeln som associeras med tjänsttypen problematiska SAS-URI. Till exempel om dåligt SAS-URI är för en blob-behållare, leta efter den nyckel som heter `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  Värdet för nyckeln ska vara en JSON-matris. Hitta ett objekt som är associerade med den felaktiga URI och ta bort den.
5.  Tryck på Ctrl + R för att läsa in Lagringsutforskaren.

## <a name="linux-dependencies"></a>Linux-beroenden

Du kan behöva installera manuellt vissa beroenden för Linux-distributioner än Ubuntu 16.04. I allmänhet krävs följande paket:
* [.NET core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* Uppdaterade GCC

Beroende på din distro kan det finnas andra paket som du behöver installera. Lagringsutforskaren [viktig information](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) innehåller särskilda åtgärder för vissa distributioner.

## <a name="next-steps"></a>Nästa steg

Om ingen av lösningarna som fungerar för dig, sedan [öppna ett problem på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Du kan också snabbt få till GitHub med hjälp av knappen ”rapporten problemet till GitHub” i det nedre vänstra hörnet.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)
