---
title: "Azure Lagringsutforskaren felsökningsguiden | Microsoft Docs"
description: "Översikt över de två felsökning funktion i Azure"
services: virtual-machines
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: 2f62de428d1915b1e070350a2837f24c3486f8c7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Lagringsutforskaren felsökningsguiden

Microsoft Azure Lagringsutforskaren (förhandsversion) är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, macOS och Linux. Appen kan ansluta till lagringskonton finns i Azure, National moln och Azure-stacken.

Den här guiden beskrivs lösningar på vanliga problem som visas i Lagringsutforskaren.

## <a name="sign-in-issues"></a>Logga in problem

Azure Active Directory (AAD)-konton stöds. Om du använder en AD FS-kontot förväntas det att logga in på Lagringsutforskaren inte fungerar. Innan du fortsätter försök att starta om programmet och se om problemen kan åtgärdas.

### <a name="error-self-signed-certificate-in-certificate-chain"></a>Fel: Självsignerat certifikat i certifikatkedjan

Det finns flera skäl till varför detta fel kan uppstå och de två vanligaste orsakerna är följande:

1. Appen är anslutna via en ”transparent proxy”, vilket innebär att en server (till exempel företagets servern) avlyssna HTTPS-trafik, att dekryptera den och kryptera den med hjälp av ett självsignerat certifikat.

2. Du kör ett program, till exempel antivirusprogram, vilket är att injicera en självsignerat SSL-certifikatet till HTTPS-meddelanden som visas.

När Lagringsutforskaren påträffar ett problem, kan den inte längre vet om det mottagna meddelandet HTTPS har ändrats. Om du har en kopia av det självsignerade certifikatet, kan du låta Lagringsutforskaren litar på den. Om du är osäker på som är att injicera certifikatet, Följ dessa steg för att hitta den:

1. Installera öppna SSL

    - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (någon av de lätta versionerna bör vara tillräckligt)

    - Mac- och Linux: ska ingå i ditt operativsystem

2. Kör öppna SSL

    - Windows: öppna installationskatalogen, klicka på **/bin/**, och dubbelklicka sedan på **openssl.exe**.
    - Mac- och Linux: kör **openssl** från en terminal.

3. Köra s_client - showcerts-ansluta microsoft.com:443

4. Leta efter självsignerade certifikat. Om du inte vet vilket självsignerat leta efter överallt ämne (”s:”) och utfärdare (”i:”) är samma.

5. När du har hittat självsignerade certifikat för var och en, kopiera och klistra in allt från och med **---BEGIN CERTIFICATE---** till **---END CERTIFICATE---** till en ny .cer-fil.

6. Öppna Lagringsutforskaren, klicka på **redigera** > **SSL-certifikat** > **Importera certifikat**, och sedan använda filväljaren för att söka efter och välj Öppna CER-filen som du skapade.

Om du inte hittar någon självsignerade certifikat med föregående steg, kontaktar du oss via verktyget feedback för mer hjälp.

### <a name="unable-to-retrieve-subscriptions"></a>Det gick inte att hämta prenumerationer

Om det inte går att hämta dina prenumerationer när du har loggat in, Följ dessa steg för att felsöka problemet:

- Kontrollera att ditt konto har åtkomst till prenumerationerna genom att logga in på Azure-portalen.

- Kontrollera att du har loggat in med rätt miljön (Azure, Azure Kina, Tyskland Azure, Azure som tillhör amerikanska myndigheter eller anpassad miljö-/ Azure-stacken).

- Om du är bakom en proxyserver, se till att du har konfigurerat korrekt Lagringsutforskaren proxy.

- Försök att ta bort och readding kontot.

- Försök ta bort följande filer från rotkatalogen (det vill säga C:\Users\ContosoUser) och readding kontot:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Titta på utvecklingsverktygen konsolen (genom att trycka på F12) när du loggar in för eventuella felmeddelanden:

![utvecklarverktyg](./media/storage-explorer-troubleshooting/4022501_en_2.png)

### <a name="unable-to-see-the-authentication-page"></a>Det gick inte att visas på autentiseringssidan

Om det inte går att visa autentiseringssidan, Följ dessa steg för att felsöka problemet:

- Beroende på anslutningen, kan det ta en stund för inloggningssidan om du vill läsa in vänta minst en minut innan du stänger dialogrutan autentisering.

- Om du är bakom en proxyserver, se till att du har konfigurerat korrekt Lagringsutforskaren proxy.

- Visa developer-konsolen genom att trycka på F12. Titta på svar från developer-konsolen och se om du hittar en ledtråd för varför autentisering fungerar inte.

### <a name="cannot-remove-account"></a>Det går inte att ta bort kontot

Om det inte går att ta bort ett konto, eller om länken återautentisera inte göra något, Följ dessa steg för att felsöka problemet:

- Försök ta bort följande filer från rotkatalogen och readding kontot:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Om du vill ta bort SAS kopplade lagringsresurser, ta bort följande filer:

    - %AppData%/StorageExplorer mapp för Windows

    - /Users/ < ditt_namn >/bibliotek/Flersvalsstart SUpport/StorageExplorer för Mac

    - ~/.config/StorageExplorer för Linux

> [!NOTE]
>  När du tar bort ovanstående filer, behöver du logga in på dina konton.

## <a name="proxy-issues"></a>Proxy-problem

Kontrollera först att följande information som du angett är korrekta:

- Proxy-URL och portnummer

- Användarnamn och lösenord om det krävs av proxy

### <a name="common-solutions"></a>Vanliga lösningar

Följ stegen nedan för att felsöka dem. Om du fortfarande har problem:

- Om du kan ansluta till Internet utan att använda proxyservern kan du kontrollera att Lagringsutforskaren fungerar utan aktiverade proxyinställningar. Om så är fallet kan det vara ett problem med proxyinställningarna. Arbeta med din proxy-administratör att identifiera problem.

- Kontrollera att andra program med hjälp av proxyservern fungerar som förväntat.

- Kontrollera att du kan ansluta till Microsoft Azure-portalen med hjälp av webbläsaren

- Kontrollera att du kan få svar från dina Tjänsteslutpunkter. Ange en slutpunkt-URL i webbläsaren. Om du kan ansluta bör du få ett InvalidQueryParameterValue eller liknande XML-svaret.

- Om någon annan också använder Lagringsutforskaren med proxyservern, kontrollera att de kan ansluta. Om de kan ansluta kan du behöva kontakta din proxy server-administratör.

### <a name="tools-for-diagnosing-issues"></a>Verktyg för att diagnostisera problem

Om du har nätverk verktyg, till exempel Fiddler för Windows kan du diagnostisera felet på följande sätt:

- Om du behöver gå igenom din proxyserver måste du kanske konfigurera nätverk verktyget för att ansluta till proxyservern.

- Kontrollera portnumret som används av ditt nätverk.

- Ange den lokala värd-URL och portnummer för verktyget nätverk som proxyinställningarna i Lagringsutforskaren. Om det görs startar ditt nätverk verktyget logging nätverksbegäranden av Lagringsutforskaren hanterings- och slutpunkter. Ange till exempel https://cawablobgrs.blob.core.windows.net/ för blob-slutpunkten i en webbläsare och du får ett svar som liknar följande, vilket tyder på resursen finns, även om du inte kommer åt den.

![kodexempel](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Kontakta serveradministratören för proxy

Om proxyinställningarna är korrekta, du kan behöva kontakta administratören proxy-server och

- Se till att proxyservern inte blockerar trafik till Azure hanterings- eller resurs-slutpunkter.

- Kontrollera autentiseringsprotokollet som används av proxyservern. Lagringsutforskaren stöder för närvarande inte NTLM-proxyservrar.

## <a name="unable-to-retrieve-children-error-message"></a>”Det gick inte att hämta underordnade” felmeddelande

Om du är ansluten till Azure via en proxyserver, kontrollerar du att proxyinställningarna är korrekta. Om du har beviljats åtkomst till en resurs från ägaren av prenumerationen eller konto, kontrollera att du har läst eller visa en lista med behörigheter för resursen.

### <a name="issues-with-sas-url"></a>Problem med SAS-URL
Om du ansluter till en tjänst med hjälp av en SAS-URL och det här felet:

- Kontrollera att URL: en ger tillräcklig behörighet för att läsa eller visa en lista med resurser.

- Kontrollera att URL: en inte har gått ut.

- Om SAS-URL är baserat på en åtkomstprincip, kontrollerar du att åtkomstprincipen inte har återkallats.

Följ dessa steg om du av misstag ansluten med en ogiltig SAS-URL och det går inte att koppla från:
1.  När du kör Lagringsutforskaren trycker du på F12 för att öppna fönstret developer tools.
2.  Klicka på fliken program och klicka sedan på lokal lagring > file:// i trädet till vänster.
3.  Hitta nyckeln som associeras med tjänsttypen problematiska SAS-URI. Till exempel om dåligt SAS-URI är för en blob-behållare, leta efter den nyckel som heter `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  Värdet för nyckeln ska vara en JSON-matris. Hitta ett objekt som är associerade med den felaktiga URI och ta bort den.
5.  Tryck på Ctrl + R för att läsa in Lagringsutforskaren.

## <a name="linux-dependencies"></a>Linux-beroenden

Du kan behöva installera manuellt vissa beroenden för Linux-distributioner än Ubuntu 16.04. I allmänhet krävs följande paket:
* libgconf-2-4
* libsecret
* Uppdaterade GCC

Beroende på din distro kan det finnas andra paket som du behöver installera. Lagringsutforskaren [viktig information](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) innehåller särskilda åtgärder för vissa distributioner.

## <a name="next-steps"></a>Nästa steg

Om ingen av lösningarna som fungerar för dig skicka din via verktyget feedback med din e-post och så många detaljer om problem som du kan, så att vi kan kontakta dig om hur du löser problemet.

Gör detta genom att klicka på **hjälp** -menyn och klicka sedan på **skicka Feedback**.

![Feedback](./media/storage-explorer-troubleshooting/4022503_en_1.png)
