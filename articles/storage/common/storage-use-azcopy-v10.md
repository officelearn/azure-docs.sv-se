---
title: Kopiera eller flytta data till Azure Storage med AzCopy v10 | Microsoft Docs
description: AzCopy är ett kommando rads verktyg som du kan använda för att kopiera data till, från eller mellan lagrings konton. Den här artikeln hjälper dig att ladda ned AzCopy, ansluta till ditt lagrings konto och sedan överföra filer.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 980156284b27478163760b1f833a91ba7cddec21
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244791"
---
# <a name="get-started-with-azcopy"></a>Kom igång med AzCopy

AzCopy är ett kommando rads verktyg som du kan använda för att kopiera blobbar eller filer till eller från ett lagrings konto. Den här artikeln hjälper dig att ladda ned AzCopy, ansluta till ditt lagrings konto och sedan överföra filer.

> [!NOTE]
> AzCopy **v10** är den version av AzCopy som stöds för tillfället.
>
> Om du behöver använda AzCopy **v 8.1**läser du avsnittet [använda den tidigare versionen av AzCopy](#previous-version) i den här artikeln.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Ladda ned AzCopy

Börja med att ladda ned den körbara filen AzCopy v10 till valfri katalog på datorn. AzCopy v10 är bara en körbar fil, så det finns inget att installera.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Filerna komprimeras som en zip-fil (Windows och Mac) eller en tar-fil (Linux).

Du kan använda dessa kommandon för att ladda ned och expandera tar-filen på Linux.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopy-v10-linux
tar -xf azcopy.tar.gz
```

> [!NOTE]
> Om du vill kopiera data till och från [Azure Table Storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) -tjänsten installerar du [AzCopy version 7,3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Kör AzCopy

För enkelhetens skull bör du överväga att lägga till katalog platsen för den körbara AzCopy i din system Sök väg för enkel användning. På så sätt kan du skriva `azcopy` från valfri katalog i systemet.

Om du väljer att inte lägga till katalogen AzCopy i sökvägen måste du ändra katalogerna till platsen för din AzCopy-körbara fil och skriva `azcopy` eller `.\azcopy` i Windows PowerShell-Kommandotolken.

Om du vill se en lista över kommandon, skriver du `azcopy -h` och trycker sedan på RETUR-tangenten.

Om du vill veta mer om ett speciellt kommando inkluderar du bara namnet på kommandot (till exempel: `azcopy list -h`).

![Infogad hjälp](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> Som ägare till ditt Azure Storage-konto tilldelas du inte automatiskt behörigheter för åtkomst till data. Innan du kan göra allt meningsfullt med AzCopy måste du bestämma hur du ska ange autentiseringsuppgifter för lagrings tjänsten. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Välj hur du ska ange autentiseringsuppgifter för auktorisering

Du kan ange autentiseringsuppgifter för auktorisering genom att använda Azure Active Directory (AD) eller genom att använda en SAS-token (signatur för delad åtkomst).

Använd den här tabellen som en guide:

| Lagringstyp | För närvarande stöds metoden för auktorisering |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**Blob Storage (hierarki-namnrymd)** | Azure AD & SAS |
|**Fil lagring** | Endast SAS |

### <a name="option-1-use-azure-active-directory"></a>Alternativ 1: Använd Azure Active Directory

Med hjälp av Azure Active Directory kan du ange autentiseringsuppgifter en gång i stället för att behöva lägga till en SAS-token i varje kommando.  

> [!NOTE]
> Om du planerar att kopiera blobbar mellan lagrings konton i den aktuella versionen måste du lägga till en SAS-token till varje käll-URL. Du kan bara utelämna SAS-token från mål-URL: en. Exempel finns i [Kopiera blobbar mellan lagrings konton](storage-use-azcopy-blobs.md).

Den behörighets nivå som du behöver baseras på om du planerar att ladda upp filer eller bara hämta dem.

Om du bara vill hämta filer kontrollerar du att [lagrings-BLOB-dataläsaren](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) har tilldelats till din användar identitet, hanterad identitet eller tjänstens huvud namn.

> Användar identiteter, hanterade identiteter och tjänstens huvud namn är varje typ av *säkerhets objekt*, så vi använder termen *säkerhets objekt* för resten av den här artikeln.

Om du vill ladda upp filer kontrollerar du att någon av dessa roller har tilldelats ditt säkerhets objekt:

- [Storage BLOB data-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Storage BLOB data-ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

De här rollerna kan tilldelas till säkerhets objekt i alla dessa omfattningar:

- Behållare (fil system)
- Lagringskonto
- Resursgrupp
- Prenumeration

Information om hur du verifierar och tilldelar roller finns i [bevilja åtkomst till Azure blob och Queue data med RBAC i Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Tänk på att det kan ta upp till fem minuter att sprida RBAC-roll tilldelningar.

Du behöver inte ha någon av dessa roller tilldelade till ditt säkerhets objekt om ditt säkerhets objekt läggs till i åtkomst kontrol listan (ACL) för mål behållaren eller katalogen. I ACL: en måste ditt säkerhets objekt ha Skriv behörighet för mål katalogen och köra behörigheten för behållaren och varje överordnad katalog.

Läs mer i [åtkomst kontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Autentisera en användar identitet

När du har kontrollerat att din användar identitet har fått den behörighet som krävs, öppnar du en kommando tolk, skriver följande kommando och trycker sedan på RETUR-tangenten.

```azcopy
azcopy login
```

Om du tillhör fler än en organisation inkluderar du klient-ID för den organisation som lagrings kontot tillhör.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Ersätt plats hållaren `<tenant-id>` med klient organisations-ID: t för den organisation som lagrings kontot tillhör. Om du vill hitta klient-ID: t väljer du **Azure Active Directory > egenskaper > katalog-ID** i Azure Portal.

Det här kommandot returnerar en autentiseringsnyckel och URL: en för en webbplats. Öppna webbplatsen, ange koden och välj sedan knappen **Nästa** .

![Skapa en container](media/storage-use-azcopy-v10/azcopy-login.png)

Ett inloggnings fönster visas. I det fönstret loggar du in på ditt Azure-konto med hjälp av dina autentiseringsuppgifter för Azure-kontot. När du har loggat in kan du stänga webbläsarfönstret och börja använda AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Autentisera ett huvud namn för tjänsten

Det här är ett bra alternativ om du planerar att använda AzCopy inuti ett skript som körs utan användar åtgärder, särskilt när du kör lokalt. Om du planerar att köra AzCopy på virtuella datorer som körs i Azure är det enklare att administrera en hanterad tjänst identitet. Mer information finns i avsnittet [autentisera en hanterad identitet](#managed-identity) i den här artikeln.

Innan du kör ett skript måste du logga in interaktivt minst en tid så att du kan ange AzCopy med autentiseringsuppgifterna för tjänstens huvud namn.  Dessa autentiseringsuppgifter lagras i en säker och krypterad fil så att ditt skript inte behöver ange den känsliga informationen.

Du kan logga in på ditt konto med hjälp av en klient hemlighet eller genom att använda lösen ordet för ett certifikat som är kopplat till tjänstens huvud namn för appens registrering.

Mer information om hur du skapar tjänstens huvud namn finns i [så här gör du: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Om du vill veta mer om tjänstens huvud namn i allmänhet, se [program-och tjänst huvud objekt i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Använda en klient hemlighet

Börja med att ställa in miljövariabeln `AZCOPY_SPA_CLIENT_SECRET` till klient hemligheten för tjänstens huvud namn för program registrering.

> [!NOTE]
> Se till att ange det här värdet från kommando tolken och inte i miljö variabel inställningarna för ditt operativ system. På så sätt är värdet bara tillgängligt för den aktuella sessionen.

Det här exemplet visar hur du kan göra detta i PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Överväg att använda en prompt som du ser i det här exemplet. På så sätt visas inte lösen ordet i konsolens kommando historik.  

Skriv sedan följande kommando och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Ersätt plats hållaren `<application-id>` med program-ID: t för tjänstens huvud namns registrerings program registrering. Ersätt plats hållaren `<tenant-id>` med klient organisations-ID: t för den organisation som lagrings kontot tillhör. Om du vill hitta klient-ID: t väljer du **Azure Active Directory > egenskaper > katalog-ID** i Azure Portal. 

##### <a name="using-a-certificate"></a>Använda ett certifikat

Om du föredrar att använda dina egna autentiseringsuppgifter för auktorisering kan du ladda upp ett certifikat till appens registrering och sedan använda det certifikatet för att logga in.

Förutom att ladda upp ditt certifikat till din app-registrering måste du också ha en kopia av certifikatet som har sparats på datorn eller den virtuella dator där AzCopy ska köras. Den här kopian av certifikatet bör vara i. PFX eller. PEM-format och måste innehålla den privata nyckeln. Den privata nyckeln bör vara lösenordsskyddad. Om du använder Windows och ditt certifikat bara finns i ett certifikat Arkiv, måste du exportera certifikatet till en PFX-fil (inklusive den privata nyckeln). Vägledning finns i [export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Ange sedan miljövariabeln `AZCOPY_SPA_CERT_PASSWORD` till certifikatets lösen ord.

> [!NOTE]
> Se till att ange det här värdet från kommando tolken och inte i miljö variabel inställningarna för ditt operativ system. På så sätt är värdet bara tillgängligt för den aktuella sessionen.

Det här exemplet visar hur du kan utföra den här uppgiften i PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Skriv sedan följande kommando och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Ersätt plats hållaren `<path-to-certificate-file>` med den relativa eller fullständigt kvalificerade sökvägen till certifikat filen. AzCopy sparar sökvägen till det här certifikatet, men det sparar inte en kopia av certifikatet, så se till att hålla certifikatet på plats. Ersätt plats hållaren `<tenant-id>` med klient organisations-ID: t för den organisation som lagrings kontot tillhör. Om du vill hitta klient-ID: t väljer du **Azure Active Directory > egenskaper > katalog-ID** i Azure Portal.

> [!NOTE]
> Överväg att använda en prompt som du ser i det här exemplet. På så sätt visas inte lösen ordet i konsolens kommando historik. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Autentisera en hanterad identitet

Det här är ett bra alternativ om du planerar att använda AzCopy inuti ett skript som körs utan användar interaktion och skriptet körs från en virtuell Azure-dator (VM). När du använder det här alternativet behöver du inte lagra några autentiseringsuppgifter på den virtuella datorn.

Du kan logga in på ditt konto genom att använda en systemomfattande hanterad identitet som du har aktiverat på den virtuella datorn, eller genom att använda klient-ID, objekt-ID eller resurs-ID för en användardefinierad hanterad identitet som du har tilldelat till den virtuella datorn.

Mer information om hur du aktiverar en systemomfattande hanterad identitet eller skapar en användardefinierad hanterad identitet finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

##### <a name="using-a-system-wide-managed-identity"></a>Använda en systemomfattande hanterad identitet

Kontrol lera först att du har aktiverat en systemomfattande hanterad identitet på den virtuella datorn. Se [systemtilldelad hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Skriv sedan följande kommando i kommando konsolen och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Använda en användardefinierad hanterad identitet

Kontrol lera först att du har aktiverat en användardefinierad hanterad identitet på den virtuella datorn. Se [användarens tilldelade hanterade identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Skriv sedan något av följande kommandon i kommando konsolen och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Ersätt plats hållaren `<client-id>` med klient-ID: t för den användarspecifika hanterade identiteten.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Ersätt plats hållaren `<object-id>` med objekt-ID: t för den användarspecifika hanterade identiteten.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Ersätt plats hållaren `<resource-id>` med resurs-ID för den användare som tilldelats den hanterade identiteten.

### <a name="option-2-use-a-sas-token"></a>Alternativ 2: Använd en SAS-token

Du kan lägga till en SAS-token för varje käll-eller mål-URL som används i dina AzCopy-kommandon.

Det här exempel kommandot kopierar data rekursivt från en lokal katalog till en BLOB-behållare. En fiktiv SAS-token läggs till i slutet av för behållar-URL: en.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Mer information om SAS-token och hur du hämtar en finns i [använda signaturer för delad åtkomst (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview).

## <a name="transfer-files"></a>Överför filer

När du har autentiserat din identitet eller fått en SAS-token kan du börja överföra filer.

Du hittar exempel kommandon i någon av de här artiklarna.

- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)

- [Överföra data med AzCopy och fil lagring](storage-use-azcopy-files.md)

- [Överföra data med AzCopy och Amazon S3-buckets](storage-use-azcopy-s3.md)

- [Överföra data med AzCopy och Azure Stack Storage](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Använda AzCopy i ett skript

### <a name="obtain-a-static-download-link"></a>Hämta en statisk nedladdnings länk

Med tiden kommer [nedladdnings länken](#download-and-install-azcopy) för AzCopy att peka på nya versioner av AzCopy. Om skriptet laddar ned AzCopy kan skriptet sluta fungera om en nyare version av AzCopy ändrar de funktioner som skriptet är beroende av.

Undvik dessa problem genom att hämta en statisk (ändra) länk till den aktuella versionen av AzCopy. På så sätt laddar skriptet ned samma exakta version av AzCopy varje gång den körs.

Hämta länken genom att köra det här kommandot:

| Operativsystem  | Kommando |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> För Linux, `--strip-components=1` på kommandot `tar` tar bort mappen på den översta nivån som innehåller versions namnet, och extraherar i stället binärfilen direkt till den aktuella mappen. Detta gör att skriptet kan uppdateras med en ny version av `azcopy` genom att endast uppdatera `wget`-URL: en.

URL: en visas i kommandots utdata. Skriptet kan sedan hämta AzCopy med hjälp av den URL: en.

| Operativsystem  | Kommando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Escape-specialtecken i SAS-token

I kommandofiler som har tillägget `.cmd` måste du undanta de `%` tecken som visas i SAS-token. Du kan göra det genom att lägga till ett additions `%`-tecken bredvid befintliga `%`-tecken i SAS-token-strängen.

## <a name="use-azcopy-in-storage-explorer"></a>Använd AzCopy i Storage Explorer

Om du vill utnyttja prestanda fördelarna med AzCopy, men vill använda Storage Explorer i stället för kommando raden för att interagera med dina filer, aktiverar du AzCopy i Storage Explorer.

I Storage Explorer väljer du för **hands version**->**Använd AzCopy för att förbättra BLOB-uppladdning och hämta**.

![Aktivera AzCopy som överförings motor i Azure Storage Explorer](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Du behöver inte aktivera den här inställningen om du har aktiverat ett hierarkiskt namn område på ditt lagrings konto. Det beror på att Storage Explorer automatiskt använder AzCopy på lagrings konton som har ett hierarkiskt namn område.  

Storage Explorer använder din konto nyckel för att utföra åtgärder, så när du har loggat in på Storage Explorer behöver du inte ange ytterligare autentiseringsuppgifter för auktorisering.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Använd den tidigare versionen av AzCopy

Om du behöver använda den tidigare versionen av AzCopy (AzCopy v 8.1) kan du läsa någon av följande länkar:

- [AzCopy på Windows (V8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy på Linux (V8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurera, optimera och felsöka AzCopy

Se [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Nästa steg

Om du har frågor, problem eller allmän feedback kan du skicka dem [till GitHub](https://github.com/Azure/azure-storage-azcopy) -sidan.
