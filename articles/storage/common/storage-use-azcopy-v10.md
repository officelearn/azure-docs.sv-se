---
title: Kopiera eller flytta data till Azure Storage med hjälp av AzCopy v10 | Microsoft Docs
description: AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera data till, från eller mellan lagringskonton. Den här artikeln får du ladda ned AzCopy, ansluta till ditt storage-konto och sedan överföra filer.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 94aca33b2f12c1c39297221a856296dcca052b0f
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565806"
---
# <a name="get-started-with-azcopy"></a>Kom igång med AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar och filer till eller från ett lagringskonto. Den här artikeln får du ladda ned AzCopy, ansluta till ditt storage-konto och sedan överföra filer.

> [!NOTE]
> AzCopy **V10** är versionen som för närvarande stöds av AzCopy.
>
> Om du vill använda AzCopy **v8.1**, finns i den [den tidigare versionen av AzCopy](#previous-version) i den här artikeln.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Ladda ned AzCopy

Ladda ned den körbara filen med AzCopy V10 först till valfri katalog på datorn. 

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> Om du vill kopiera data till och från din [Azure Table storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) tjänsten och sedan installera [AzCopy version 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Kör AzCopy

Överväg att lägga till katalogplatsen av AzCopy körbara systemsökvägen för enkel användning av praktiska skäl. På så sätt kan du skriva `azcopy` från en katalog på datorn.

Om du väljer att inte lägga till AzCopy-katalogen till sökvägen, måste du ändra sökvägen till platsen för ditt AzCopy körbar fil och ange `azcopy` eller `.\azcopy` i Windows PowerShell-Kommandotolken.

Om du vill se en lista över kommandon skriver `azcopy -h` och tryck sedan på RETUR-tangenten.

Om du vill veta mer om ett visst kommando kan bara innehålla namnet på kommandot (till exempel: `azcopy list -h`).

![Infogad hjälp](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> Ägare för Azure Storage-kontot kan behörigheter du inte automatiskt för att komma åt data. Innan du kan göra något beskrivande med AzCopy, måste du bestämma hur du anger autentiseringsuppgifter till storage-tjänsten. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Välj hur du anger autentiseringsuppgifter

Du kan ange autentiseringsuppgifter med hjälp av Azure Active Directory (AD) eller genom att använda en signatur för delad åtkomst (SAS)-token.

Använd den här tabellen som vägledning:

| Lagringstyp | Metod som för närvarande stöds för auktorisering |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**BLOB-lagring (hierarkiska namnrymden)** | Azure AD & SAS |
|**Fillagring** | Endast SAS |

### <a name="option-1-use-azure-ad"></a>Alternativ 1: Använd Azure AD

Genom att använda Azure AD kan ange du autentiseringsuppgifter för en gång i stället för att lägga till en SAS-token till varje kommando.  

Utbud av auktoriseringar som du behöver baseras på om du planerar att ladda upp filer eller hämta dem bara.

Om du bara vill ladda ned filer, kontrollera att den [Storage Blob Data-läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) har tilldelats till ditt användar-ID eller tjänstens huvudnamn. 

> [!NOTE]
> Användarnas identiteter och tjänstens huvudnamn är var och en typ av *säkerhetsobjekt*, så vi använder termen *säkerhetsobjekt* för resten av den här artikeln.

Om du vill ladda upp filer, kontrollerar du att någon av dessa roller har tilldelats till din säkerhetsobjekt:

- [Storage Blob Data-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Storage Blob Data-ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Dessa roller kan tilldelas till din identitet i någon av dessa scope:

- Behållare (filsystem)
- Lagringskonto
- Resursgrupp
- Prenumeration

Läs hur du kontrollerar och tilldela roller i [bevilja åtkomst till Azure blob och kö data med RBAC i Azure-portalen](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE] 
> Tänk på att RBAC-rolltilldelningar kan ta upp till fem minuter att sprida.

Du behöver inte ha någon av dessa roller som tilldelats din säkerhetsobjekt om din säkerhetsobjekt läggs till i åtkomstkontrollistan (ACL) för målbehållare eller katalog. I ACL måste din säkerhetsobjekt skrivbehörighet för målkatalogen och körbehörighet på behållaren och alla överordnade kataloger.

Mer information finns i [åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Autentisera en användaridentitet

När du har kontrollerat att din användaridentitet har fått den nödvändiga säkerhetsnivån, öppna en kommandotolk, Skriv följande kommando och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login
```

Om du tillhör mer än en organisation kan innehålla klient-ID för den organisation som lagringskontot tillhör.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Ersätt den `<tenant-id>` med klient-ID för den organisation som lagringskontot tillhör. Du hittar klient-ID, Välj **Azure Active Directory > Egenskaper > katalog-ID** i Azure-portalen.

Det här kommandot returnerar en Autentiseringskod och Webbadressen till en webbplats. Öppna webbplatsen, ange koden och välj sedan den **nästa** knappen.

![Skapa en container](media/storage-use-azcopy-v10/azcopy-login.png)

Ett fönster för inloggning visas. Logga in på ditt Azure-konto med hjälp av dina Azure-autentiseringsuppgifter i det aktuella fönstret. När du har loggat in, kan du stänga webbläsaren och börja använda AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Autentisera ett huvudnamn för tjänsten

Detta är ett bra alternativ om du planerar att använda AzCopy inuti ett skript som körs utan användaråtgärder. 

Innan du kör skriptet kan behöva du logga in interaktivt minst en gång så att du kan ange AzCopy med autentiseringsuppgifter för tjänstens huvudnamn.  Dessa autentiseringsuppgifter lagras i en säker och krypterad fil så att skriptet inte behöver ange den känsliga informationen.

Du kan logga in på ditt konto med hjälp av en klienthemlighet eller genom att använda lösenordet för ett certifikat som är associerad med tjänstens huvudnamn appregistreringen. 

Läs mer om att skapa huvudnamn för tjänsten i [så här: Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Läs mer om tjänstens huvudnamn i allmänhet i [program och tjänstobjekt i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Med hjälp av en klienthemlighet

Starta genom att ange den `AZCOPY_SPA_CLIENT_SECRET` miljövariabeln till klienthemlighet för din tjänstens huvudnamn är appregistrering. 

> [!NOTE]
> Se till att ange det här värdet från Kommandotolken och inte i miljön varierande inställningarna för ditt operativsystem. På så sätt kan värdet är bara tillgänglig för den aktuella sessionen.

Det här exemplet visar hur du kan göra detta i PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Överväg att använda en uppmaning som visas i det här exemplet. På så sätt kan klienthemligheten visas inte i kommandohistoriken för din konsol. 

Därefter skriver du följande kommando och tryck på RETUR-tangenten.

```azcopy
azcopy login --service-principal --application-id <application-id>
```

Ersätt den `<application-id>` med program-ID för din tjänstens huvudnamn är appregistrering.

##### <a name="using-a-certificate"></a>Med hjälp av ett certifikat

Om du föredrar att använda dina autentiseringsuppgifter för auktorisering kan du överföra ett certifikat till din appregistrering och använder certifikatet för inloggning.

Förutom laddar upp certifikatet i din appregistrering, måste du också ha en kopia av certifikatet sparas på en dator eller virtuell dator där AzCopy kommer att köras. Den här kopian av certifikatet måste vara i. PFX eller. PEM-format och måste innehålla den privata nyckeln. Den privata nyckeln ska vara lösenordsskyddad. Om du använder Windows, och ditt certifikat finns bara i ett certifikatarkiv, se till att exportera certifikatet till en PFX-fil (inklusive den privata nyckeln). Anvisningar finns i [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Nu ska vi konfigurera den `AZCOPY_SPA_CERT_PASSWORD` miljövariabeln till lösenordet för certifikatet.

> [!NOTE]
> Se till att ange det här värdet från Kommandotolken och inte i miljön varierande inställningarna för ditt operativsystem. På så sätt kan värdet är bara tillgänglig för den aktuella sessionen.

Det här exemplet visar hur du kan göra detta i PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Därefter skriver du följande kommando och tryck på RETUR-tangenten.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file>
```

Ersätt den `<path-to-certificate-file>` med den relativa eller fullständigt kvalificerade sökvägen till certifikatfilen. AzCopy sparar sökvägen till det här certifikatet men den inte spara en kopia av certifikatet, så se till att förvara certifikatet på plats.

> [!NOTE]
> Överväg att använda en uppmaning som visas i det här exemplet. På så sätt kan lösenordet visas inte i kommandohistoriken för din konsol. 

### <a name="option-2-use-a-sas-token"></a>Alternativ 2: Använda en SAS-token

Du kan lägga till en SAS-token till varje källa eller mål-URL som använder i AzCopy-kommandon.

Det här exemplet kommandot rekursivt kopierar data från en lokal katalog på en blobbehållare. En fiktiva SAS-token läggs till i slutet av den av behållarens Webbadress.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Mer information om SAS-token och hur du skaffa en finns [använda signaturer för delad åtkomst (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Överföra filer

När du har autentiserats din identitet eller hämta en SAS-token, kan du börja med att överföra filer.

Exempel på kommandon finns i någon av följande artiklar.

- [Överföra data med AzCopy och blob storage](storage-use-azcopy-blobs.md)

- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

- [Överföra data med AzCopy och Amazon S3 buckets](storage-use-azcopy-s3.md)

- [Överföra data med AzCopy och Azure Stack-lagring](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Använda AzCopy i ett skript

Innan du kör skriptet kan behöva du logga in interaktivt minst en gång så att du kan ange AzCopy med autentiseringsuppgifter för tjänstens huvudnamn.  Dessa autentiseringsuppgifter lagras i en säker och krypterad fil så att skriptet inte behöver ange den känsliga informationen. Exempel finns i den [autentisera tjänstens huvudnamn](#service-principal) i den här artikeln.

Med tiden, AzCopy [nedladdningslänk](#download-and-install-azcopy) pekar till nya versioner av AzCopy. Om ditt skript har hämtats AzCopy kan skriptet sluta fungera om en nyare version av AzCopy ändrar funktioner som är beroende av om ditt skript. 

Undvik dessa problem, skaffar du en statisk (icke föränderliga) länk till den aktuella versionen av AzCopy. På så sätt kan dina skript hämtar exakt samma version av AzCopy varje gång det körs.

Om du vill få en länk, kör du följande kommando:

| Operativsystem  | Kommando |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> För Linux, `--strip-components=1` på den `tar` kommandot tar bort den översta mappen som innehåller versionsnamnet på och i stället extraherar den binära filen direkt till den aktuella mappen. På så sätt kan skriptet som ska uppdateras med en ny version av `azcopy` genom att endast uppdatera den `wget` URL: en.

Webbadressen visas i kommandots utdata. Skriptet kan sedan hämta AzCopy med hjälp av URL: en.

| Operativsystem  | Kommando |
|--------|-----------|
| **Linux** | `wget -O azcopyv10.tar https://azcopyvnext.azureedge.net/release20190301/azcopy_linux_amd64_10.0.8.tar.gz tar -xf azcopyv10.tar --strip-components=1 ./azcopy` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

## <a name="use-azcopy-in-storage-explorer"></a>Använda AzCopy i Storage Explorer

Om du vill utnyttja fördelarna med AzCopy prestanda, men du föredrar att använda Storage Explorer i stället för kommandoraden för att interagera med dina filer, aktiverar du AzCopy i Storage Explorer. 

I Storage Explorer väljer **förhandsversion**->**använda AzCopy för förbättrad Blob ladda upp och ladda ned**.

![Aktivera AzCopy som en motor för överföring i Azure Storage Explorer](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Du behöver aktivera den här inställningen om du har aktiverat ett hierarkiskt namnområde på ditt lagringskonto. Det beror på att Storage Explorer automatiskt använder AzCopy på storage-konton som har ett hierarkiskt namnområde.  

Lagringsutforskaren använder din kontonyckel för att utföra åtgärder, så när du loggar in i Storage Explorer inte behöver du ange ytterligare autentiseringsuppgifter.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Använd den tidigare versionen av AzCopy

Om du vill använda den tidigare versionen av AzCopy (AzCopy v8.1) ser du något av följande länkar:

- [AzCopy i Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy i Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurera, optimera och felsöka AzCopy

Se [konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Nästa steg

Om du har frågor eller problem med allmän feedback kan du skicka dem [på GitHub](https://github.com/Azure/azure-storage-azcopy) sidan.
