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
ms.openlocfilehash: 722097f1a61a10cd45c0c330e998021cd1abf0c8
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147969"
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
|**BLOB-lagring (hierarkiska namnrymden)** | Azure AD |
|**Fillagring** | Endast SAS |

### <a name="option-1-use-azure-ad"></a>Alternativ 1: Använd Azure AD

Utbud av auktoriseringar som du behöver baseras på om du planerar att ladda upp filer eller hämta dem bara.

Om du bara vill ladda ned filer, kontrollera att den [Storage Blob Data-läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) har tilldelats till din identitet.

Om du vill ladda upp filer, kontrollerar du att någon av dessa roller har tilldelats till din identitet:

- [Storage Blob Data-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Storage Blob Data-ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Dessa roller kan tilldelas till din identitet i någon av dessa scope:

- Behållare (filsystem)
- Lagringskonto
- Resursgrupp
- Prenumeration

Läs hur du kontrollerar och tilldela roller i [bevilja åtkomst till Azure blob och kö data med RBAC i Azure-portalen](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Du behöver inte ha någon av dessa roller som tilldelats din identitet om din identitet har lagts till i åtkomstkontrollistan (ACL) för målbehållare eller katalog. I ACL måste din identitet skrivbehörighet för målkatalogen och körbehörighet på behållaren och alla överordnade kataloger.

Mer information finns i [åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-your-identity"></a>Verifiera din identitet

När du har kontrollerat att din identitet har fått den nödvändiga säkerhetsnivån, öppna en kommandotolk, Skriv följande kommando och tryck sedan på RETUR-tangenten.

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

## <a name="use-azcopy-in-a-script"></a>Använda AzCopy i ett skript

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
