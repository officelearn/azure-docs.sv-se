---
title: Kopiera eller flytta data till Azure Storage med AzCopy v10 | Microsoft-dokument
description: AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera data till, från eller mellan lagringskonton. Den här artikeln hjälper dig att ladda ner AzCopy, ansluta till ditt lagringskonto och sedan överföra filer.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8701fe6857e95334a5e1d24bfe70feb130d5512c
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756022"
---
# <a name="get-started-with-azcopy"></a>Kom igång med AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobbar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att ladda ner AzCopy, ansluta till ditt lagringskonto och sedan överföra filer.

> [!NOTE]
> AzCopy **V10** är den version som för närvarande stöds av AzCopy.
>
> Om du behöver använda en tidigare version av AzCopy läser du avsnittet [Använd den tidigare versionen av AzCopy](#previous-version) i den här artikeln.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Ladda ner AzCopy

Hämta först den körbara filen AzCopy V10 till valfri katalog på datorn. AzCopy V10 är bara en körbar fil, så det finns inget att installera.

- [Windows 64-bitars](https://aka.ms/downloadazcopy-v10-windows) (dragkedja)
- [Windows 32-bitars](https://aka.ms/downloadazcopy-v10-windows-32bit) (dragkedja)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tjära)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (dragkedja)

Dessa filer komprimeras som en zip-fil (Windows och Mac) eller en tar-fil (Linux). Om du vill hämta och expandera tjärfilen på Linux läser du dokumentationen för din Linux-distribution.

> [!NOTE]
> Om du vill kopiera data till och från din [Azure Table storage-tjänst](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) installerar du [AzCopy version 7.3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Kör AzCopy

För enkelhetens skull, överväg att lägga till katalogplatsen för AzCopy körbar till din systemsökväg för enkel användning. På så sätt `azcopy` kan du skriva från valfri katalog på ditt system.

Om du väljer att inte lägga till AzCopy-katalogen i sökvägen måste du ändra kataloger `azcopy` `.\azcopy` till platsen för din AzCopy-körbara och typ eller i Windows PowerShell-kommandotolken.

Om du vill visa en `azcopy -h` lista med kommandon skriver du och trycker sedan på RETUR.

Om du vill veta mer om ett visst kommando, `azcopy list -h`bara inkludera namnet på kommandot (Till exempel: ).

![Hjälp i linje](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Information om hur du hittar detaljerad referensdokumentation för varje kommando- och kommandoparameter finns i [azkopia](storage-ref-azcopy.md)

> [!NOTE] 
> Som ägare av ditt Azure Storage-konto tilldelas du inte automatiskt behörigheter för åtkomst till data. Innan du kan göra något meningsfullt med AzCopy måste du bestämma hur du ska tillhandahålla auktoriseringsautentiseringsuppgifter till lagringstjänsten. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Välj hur du ska ange auktoriseringsuppgifter

Du kan ange auktoriseringsbehörighetsbehörighet med hjälp av Azure Active Directory (AD) eller med hjälp av en SAS-token (Shared Access Signature).

Använd den här tabellen som vägledning:

| Lagringstyp | För närvarande stöds tillståndsmetod |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**Blob-lagring (hierarkiskt namnområde)** | Azure AD & SAS |
|**Fillagring** | ENDAST SAS |

### <a name="option-1-use-azure-active-directory"></a>Alternativ 1: Använd Azure Active Directory

Genom att använda Azure Active Directory kan du ange autentiseringsuppgifter en gång i stället för att behöva lägga till en SAS-token till varje kommando.  

> [!NOTE]
> Om du planerar att kopiera blobbar mellan lagringskonton i den aktuella versionen måste du lägga till en SAS-token i varje käll-URL. Du kan endast utelämna SAS-token från mål-URL:en. Exempel finns i [Kopiera blobbar mellan lagringskonton](storage-use-azcopy-blobs.md).

Den behörighetsnivå som du behöver baseras på om du planerar att ladda upp filer eller bara ladda ned dem.

Om du bara vill hämta filer kontrollerar du att [dataläsaren För storage blob har](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) tilldelats din användaridentitet, hanterade identitet eller tjänsthuvudnamn.

> Användaridentiteter, hanterade identiteter och tjänsthuvudnamn är varje typ av *säkerhetsobjekt*, så vi använder termen *säkerhetsobjekt* för resten av den här artikeln.

Om du vill ladda upp filer kontrollerar du att en av dessa roller har tilldelats ditt säkerhetsobjekt:

- [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Ägare av lagringsblobbdata](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Dessa roller kan tilldelas ditt säkerhetsobjekt i något av följande scope:

- Behållare (filsystem)
- Lagringskonto
- Resursgrupp
- Prenumeration

Mer information om hur du verifierar och tilldelar roller finns i [Bevilja åtkomst till Azure-blob och ködata med RBAC i Azure-portalen](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Tänk på att RBAC-rolltilldelningar kan ta upp till fem minuter att sprida.

Du behöver inte ha någon av dessa roller tilldelad ditt säkerhetsobjekt om ditt säkerhetsobjekt läggs till i åtkomstkontrollistan (ACL) för målbehållaren eller katalogen. I åtkomstkontrollistan behöver ditt säkerhetsobjekt skrivbehörighet för målkatalogen och köra behörighet för behållaren och varje överordnad katalog.

Mer information finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Autentisera en användaridentitet

När du har verifierat att din användaridentitet har fått den nödvändiga auktoriseringsnivån öppnar du en kommandotolk, skriver följande kommando och trycker sedan på RETUR.

```azcopy
azcopy login
```

Om du tillhör mer än en organisation, inkludera klient-ID för den organisation som lagringskontot tillhör.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Ersätt `<tenant-id>` platshållaren med klient-ID:t för den organisation som lagringskontot tillhör. Om du vill hitta klient-ID:et väljer du **Azure Active Directory > Properties > Directory ID** i Azure-portalen.

Det här kommandot returnerar en autentiseringskod och webbadressen till en webbplats. Öppna webbplatsen, ange koden och välj sedan knappen **Nästa.**

![Skapa en container](media/storage-use-azcopy-v10/azcopy-login.png)

Ett inloggningsfönster visas. I det fönstret loggar du in på ditt Azure-konto med hjälp av dina Azure-kontouppgifter. När du har loggat in kan du stänga webbläsarfönstret och börja använda AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Autentisera ett tjänsthuvudnamn

Detta är ett bra alternativ om du planerar att använda AzCopy inuti ett skript som körs utan användarinteraktion, särskilt när du kör lokalt. Om du planerar att köra AzCopy på virtuella datorer som körs i Azure är en hanterad tjänstidentitet enklare att administrera. Mer information finns i avsnittet [Autentisera en hanterad identitet i](#managed-identity) den här artikeln.

Innan du kör ett skript måste du logga in interaktivt minst en gång så att du kan förse AzCopy med autentiseringsuppgifterna för tjänstens huvudnamn.  Dessa autentiseringsuppgifter lagras i en skyddad och krypterad fil så att skriptet inte behöver ange den känsliga informationen.

Du kan logga in på ditt konto med hjälp av en klienthemlighet eller genom att använda lösenordet för ett certifikat som är kopplat till tjänstens huvudnamns appregistrering.

Mer information om hur du skapar tjänstens huvudnamn finns i [Så här: Använd portalen för att skapa ett Azure AD-program och tjänsthuvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Mer information om tjänstens huvudnamn i allmänhet finns i [Program- och tjänsthuvudobjekt i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Använda en klienthemlighet

Börja med `AZCOPY_SPA_CLIENT_SECRET` att ställa in miljövariabeln till klienthemligheten för tjänstens huvudnamns appregistrering.

> [!NOTE]
> Se till att ange det här värdet från kommandotolken och inte i miljövariabelinställningarna för operativsystemet. På så sätt är värdet endast tillgängligt för den aktuella sessionen.

Det här exemplet visar hur du kan göra detta i PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Överväg att använda en fråga som visas i det här exemplet. På så sätt visas inte lösenordet i konsolens kommandohistorik.  

Skriv sedan följande kommando och tryck sedan på RETUR.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Ersätt `<application-id>` platshållaren med program-ID:t för tjänsthuvudhuvudets appregistrering. Ersätt `<tenant-id>` platshållaren med klient-ID:t för den organisation som lagringskontot tillhör. Om du vill hitta klient-ID:et väljer du **Azure Active Directory > Properties > Directory ID** i Azure-portalen. 

##### <a name="using-a-certificate"></a>Använda ett certifikat

Om du föredrar att använda dina egna autentiseringsuppgifter för auktorisering kan du ladda upp ett certifikat till din appregistrering och sedan använda det certifikatet för att logga in.

Förutom att ladda upp certifikatet till din appregistrering måste du också ha en kopia av certifikatet sparat på den dator eller dator där AzCopy körs. Denna kopia av intyget ska finnas i . PFX eller . PEM-format och måste innehålla den privata nyckeln. Den privata nyckeln bör vara lösenordsskyddad. Om du använder Windows och certifikatet bara finns i ett certifikatarkiv måste du exportera certifikatet till en PFX-fil (inklusive den privata nyckeln). För vägledning, se [Exportera-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Ange sedan `AZCOPY_SPA_CERT_PASSWORD` miljövariabeln till certifikatlösenordet.

> [!NOTE]
> Se till att ange det här värdet från kommandotolken och inte i miljövariabelinställningarna för operativsystemet. På så sätt är värdet endast tillgängligt för den aktuella sessionen.

Det här exemplet visar hur du kan utföra den här uppgiften i PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Skriv sedan följande kommando och tryck sedan på RETUR.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Ersätt `<path-to-certificate-file>` platshållaren med den relativa eller fullständigt kvalificerade sökvägen till certifikatfilen. AzCopy sparar sökvägen till det här certifikatet, men det sparar inte en kopia av certifikatet, så se till att hålla certifikatet på plats. Ersätt `<tenant-id>` platshållaren med klient-ID:t för den organisation som lagringskontot tillhör. Om du vill hitta klient-ID:et väljer du **Azure Active Directory > Properties > Directory ID** i Azure-portalen.

> [!NOTE]
> Överväg att använda en fråga som visas i det här exemplet. På så sätt visas inte lösenordet i konsolens kommandohistorik. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Autentisera en hanterad identitet

Detta är ett bra alternativ om du planerar att använda AzCopy inuti ett skript som körs utan användarinteraktion och skriptet körs från en virtuell virtuell dator (Virtuell dator) ( Azure Virtual Machine). När du använder det här alternativet behöver du inte lagra några autentiseringsuppgifter på den virtuella datorn.

Du kan logga in på ditt konto med hjälp av den systemomfattande hanterade identitet som du har aktiverat på den virtuella datorn, eller med hjälp av klient-ID, object ID eller Resource ID för en användartilldelad hanterad identitet som du har tilldelat den virtuella datorn.

Mer information om hur du aktiverar en systemomfattande hanterad identitet eller skapar en användartilldelad hanterad identitet finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure-portalen](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

##### <a name="using-a-system-wide-managed-identity"></a>Använda en hanterad systemomfattande identitet

Kontrollera först att du har aktiverat en systemomfattande hanterad identitet på den virtuella datorn. Se [Systemtilldelade hanterade identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Skriv sedan följande kommando i kommandokonsolen och tryck sedan på RETUR.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Använda en användartilldelad hanterad identitet

Kontrollera först att du har aktiverat en användartilldelad hanterad identitet på den virtuella datorn. Se [Användartilldelade hanterade identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Skriv sedan något av följande kommandon i kommandokonsolen och tryck sedan på RETUR.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Ersätt `<client-id>` platshållaren med klient-ID:et för den användartilldelade hanterade identiteten.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Ersätt `<object-id>` platshållaren med objekt-ID:t för den användartilldelade hanterade identiteten.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Ersätt `<resource-id>` platshållaren med resurs-ID:t för den användartilldelade hanterade identiteten.

### <a name="option-2-use-a-sas-token"></a>Alternativ 2: Använd en SAS-token

Du kan lägga till en SAS-token i varje käll- eller mål-URL som används i azcopykommandona.

Det här exempelkommandot kopierar rekursivt data från en lokal katalog till en blob-behållare. En fiktiv SAS-token läggs till i slutet av behållar-URL:en.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Mer information om SAS-token och hur du skaffar en finns i [Använda SIGNATURER för delad åtkomst (SAS).](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

## <a name="transfer-files"></a>Överföra filer

När du har autentiserat din identitet eller fått en SAS-token kan du börja överföra filer.

Information om hur du hittar exempelkommandon finns i någon av dessa artiklar.

- [Överföra data med AzCopy- och bloblagring](storage-use-azcopy-blobs.md)

- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

- [Överför data med AzCopy och Amazon S3 hinkar](storage-use-azcopy-s3.md)

- [Överföra data med Lagring av AzCopy och Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Använda AzCopy i ett skript

### <a name="obtain-a-static-download-link"></a>Skaffa en statisk nedladdningslänk

Med tiden kommer AzCopy [nedladdningslänken](#download-and-install-azcopy) peka på nya versioner av AzCopy. Om skriptet hämtar AzCopy kan skriptet sluta fungera om en nyare version av AzCopy ändrar funktioner som skriptet är beroende av.

Undvik dessa problem genom att skaffa en statisk (obyte) länk till den aktuella versionen av AzCopy. På så sätt hämtar skriptet exakt samma version av AzCopy varje gång det körs.

Om du vill hämta länken kör du det här kommandot:

| Operativsystem  | Kommando |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> För Linux `--strip-components=1` tar `tar` den översta mappen som innehåller versionsnamnet bort på kommandot och extraherar i stället binärfilen direkt i den aktuella mappen. Detta gör att skriptet kan uppdateras med en ny version av `azcopy` genom att bara uppdatera `wget` URL:en.

URL:en visas i utdata från det här kommandot. Skriptet kan sedan hämta AzCopy med hjälp av webbadressen.

| Operativsystem  | Kommando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Escape specialtecken i SAS-token

I batchfiler som `.cmd` har tillägget måste du `%` fly från de tecken som visas i SAS-token. Det kan du göra `%` genom att `%` lägga till ytterligare ett tecken bredvid befintliga tecken i SAS-tokensträngen.

### <a name="run-scripts-by-using-jenkins"></a>Köra skript med Jenkins

Om du planerar att använda [Jenkins](https://jenkins.io/) för att köra skript, se till att placera följande kommando i början av skriptet.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Använda AzCopy i Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) använder AzCopy för att utföra alla dess dataöverföringsåtgärder. Du kan använda [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) om du vill utnyttja prestandafördelarna med AzCopy, men du föredrar att använda ett grafiskt användargränssnitt i stället för kommandoraden för att interagera med dina filer.

Storage Explorer använder din kontonyckel för att utföra åtgärder, så när du har loggat in på Storage Explorer behöver du inte ange ytterligare auktoriseringsautentiseringsuppgifter.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Använda den tidigare versionen av AzCopy

Om du behöver använda den tidigare versionen av AzCopy läser du någon av följande länkar:

- [AzCopy i Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy på Linux (v7)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurera, optimera och felsöka AzCopy

Se [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Nästa steg

Om du har frågor, problem eller allmän feedback skickar du dem [på GitHub-sidan.](https://github.com/Azure/azure-storage-azcopy)
