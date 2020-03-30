---
title: Använda Azure Storage Explorer med Azure Data Lake Storage Gen2
description: Använd Azure Storage Explorer för att hantera kataloger och ACL (File and Directory Access Control List) i lagringskonton som har aktiverat hierarkiskt namnområde (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: fca9fa8a964c6c9d69ffbb3036bd4774e0d1bd34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255553"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Använda Azure Storage Explorer för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att skapa och hantera kataloger, filer och behörigheter i lagringskonton som har aktiverat hierarkiskt namnområde (HNS).

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagringskonto med hierarkiskt namnområde (HNS) aktiverat. Följ [dessa](data-lake-storage-quickstart-create-account.md) instruktioner för att skapa en.
> * Azure Storage Explorer installerat på din lokala dator. Information om hur du installerar Azure Storage Explorer för Windows, Macintosh och Linux finns i [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Logga in i Storage Explorer

När du först startar Storage Explorer visas fönstret **Microsoft Azure Storage Explorer – anslut**. Storage Explorer erbjuder flera sätt att ansluta till lagringskonton, men för närvarande stöds bara ett sätt för att hantera ACL: er.

|Aktivitet|Syfte|
|---|---|
|Lägga till ett Azure-konto | Omdirigerar dig till organisationens inloggningssida för att autentisera dig till Azure. Det här är för närvarande den enda autentiseringsmetod som stöds om du vill hantera och ange ACL: er.|
|Använda en anslutningssträng eller en signatur-URI för delad åtkomst | Kan användas för direkt åtkomst till en container eller ett lagringskonto med en SAS-token eller en delad anslutningssträng. |
|Använda lagringskontots namn och nyckel| Använd lagringskontonamnet och nyckeln för ditt lagringskonto för att ansluta till Azure Storage.|

Välj **Lägg till ett Azure-konto** och klicka på Logga **in..**. Följ anvisningarna på skärmen för att logga in på ditt Azure-konto.

![Fönstret Microsoft Azure Storage Explorer – anslut](media/storage-quickstart-blobs-storage-explorer/connect.png)

När anslutningen är klar läses Azure Storage Explorer in med fliken **Utforskaren** öppen. I den här vyn får du information om alla dina lagringskonton i Azure samt lokal lagring som konfigurerats via [Azure Storage-emulatorn](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) -konton eller [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)miljöer.

![Fönstret Microsoft Azure Storage Explorer – anslut](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Skapa en container

En behållare innehåller kataloger och filer. Om du vill skapa ett expanderar du lagringskontot som du skapade i ingåendet. Välj **Blob Containers** (Blob-behållare), högerklicka och välj **Create Blob Container** (Skapa blob-behållare). Ange namnet på din behållare. Se avsnittet [Skapa en behållare](storage-quickstart-blobs-dotnet.md#create-a-container) för en lista över regler och begränsningar för namngivning av behållare. När du är klar trycker du på **Retur** för att skapa behållaren. När behållaren har skapats visas den under mappen **Blob Containers** för det valda lagringskontot.

![Microsoft Azure Storage Explorer – Skapa en behållare](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Skapa en katalog

Om du vill skapa en katalog markerar du den behållare som du skapade i ingåendet. Välj knappen **Ny mapp i menyfliksområdet för** behållaren. Ange namnet på katalogen. När du är klar trycker du på **Retur** för att skapa katalogen. När katalogen har skapats visas den i redigeringsfönstret.

![Microsoft Azure Storage Explorer – Skapa en katalog](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Ladda upp blobar till katalogen

Välj knappen **Ladda upp** i katalogfliksområdet. Den här åtgärden ger dig möjlighet att ladda upp en mapp eller fil.

Välj de filer eller mappar du vill ladda upp.

![Microsoft Azure Storage Explorer – ladda upp en blob](media/data-lake-storage-explorer/upload-file.png)

När du väljer **OK** sätts de valda filerna i kö för uppladdning och varje fil laddas upp. När uppladdningen är klar visas resultatet i fönstret **Activities** (Aktiviteter).

## <a name="view-blobs-in-a-directory"></a>Visa blobar i en katalog

I programmet **Azure Storage Explorer** väljer du en katalog under ett lagringskonto. I huvudrutan visas en lista över blobarna i den valda katalogen.

![Microsoft Azure Storage Explorer – lista med blobar i en katalog](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Ladda ned blobbar

Om du vill hämta filer med Hjälp av **Azure Storage Explorer**väljer du **Hämta** från menyfliksområdet. En fildialogruta öppnas och där du kan ange ett filnamn. Välj **Spara** om du vill starta hämtningen av en fil till den lokala platsen.

## <a name="managing-access"></a>Hantera åtkomst

Du kan ange behörigheter i behållarens rot. För att göra det måste du vara inloggad i Azure Storage Explorer med ditt enskilda konto med behörighet att göra det (i motsats till med en anslutningssträng). Högerklicka på behållaren och välj **Hantera behörigheter**och hämta dialogrutan **Hantera behörighet.**

![Microsoft Azure Storage Explorer – Hantera katalogåtkomst](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

I dialogrutan **Hantera behörighet** kan du hantera behörigheter för ägare och ägargruppen. Du kan även lägga till nya användare och grupper i åtkomstkontrollistan, som du sedan kan lägga till behörigheter för.

Du kan lägga till en ny användare eller grupp i åtkomstkontrollistan genom att välja fältet **Lägg till användare eller grupp**.

Ange motsvarande Azure Active Directory-post (AAD) du vill lägga till i listan och välj sedan **Lägg till**.

Användaren eller gruppen visas nu i fältet **Användare och grupper:**, där du kan börja hantera deras behörigheter.

> [!NOTE]
> Vi rekommenderar bästa praxis att skapa en säkerhetsgrupp i AAD och hantera behörigheter för gruppen istället för enskilda användare. Information om den här rekommendationen, samt andra metodtips, finns i [metodtipsen för Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Det finns två kategorier av behörigheter du kan tilldela: åtkomst-ACL och standard-ACL.

* **Åtkomst:** Åtkomstkontrollistor styr åtkomsten till ett objekt. Filer och kataloger har båda åtkomst-ACL:er.

* **Standard**: En mall med ACL:er som är associerad med en katalog som bestämmer åtkomstkontrollistorna för underordnade objekt som skapas under den katalogen. Filer har inte standard-ACL:er.

Inom båda dessa kategorier finns det tre behörigheter som du sedan kan tilldela på filer eller kataloger: **Läs**, **Skriv**och **Kör**.

>[!NOTE]
> När du gör val här anges inte behörigheter för några befintliga objekt i katalogen. Du måste gå till varje enskilt objekt och ange behörigheterna manuellt, om filen redan finns.

Du kan hantera behörigheter för enskilda kataloger, och enskilda filer, vilket är det som ger dig detaljerad åtkomstkontroll. Processen för att hantera behörigheter för både kataloger och filer är samma som beskrivs ovan. Högerklicka på filen eller katalogen du vill hantera behörigheter för och följ samma process.

## <a name="next-steps"></a>Nästa steg

Lär dig åtkomstkontrollistor i Gen2 för lagring av datasjö.

> [!div class="nextstepaction"]
> [Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
