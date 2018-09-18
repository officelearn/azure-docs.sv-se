---
title: Hur du använder Hudson med Blob storage | Microsoft Docs
description: Beskriver hur du använder Hudson med Azure Blob storage som databas för utvecklingsartefakter.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: 86a14d12f6621524ef26e2a869d4be532105dc77
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732361"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Använda Azure Storage med en Hudson-baserad CI-lösning
## <a name="overview"></a>Översikt
Följande information visar hur du använder Blob storage som databas för utvecklingsartefakter skapade med en Hudson kontinuerlig integrering (CI)-lösning eller som en källa till hämtningsbara filer som ska användas i en build-process. En av de scenarier där det skulle vara praktiskt är när du kodning i en smidig utveckling-miljö (med Java eller andra språk), versioner som körs baserat på kontinuerlig integrering och du behöver en lagringsplats för din byggartefakter, så att du kan till exempel dela dem med andra organisationsmedlemmar i, dina kunder, eller skapa ett arkiv.  Ett annat scenario är när din skapandejobb själva kräver andra filer, till exempel beroenden för att ladda ned som en del av den version som indata.

I den här självstudiekursen kommer du att använda Azure Storage-plugin-programmet för Hudson CI som gjorts tillgängliga av Microsoft.

## <a name="introduction-to-hudson"></a>Introduktion till Hudson
Hudson möjliggör kontinuerlig integrering för ett programvaruprojekt genom att låta utvecklare att enkelt integrera sina ändringar i koden och har producerat versioner automatiskt och ofta, vilket ökar produktiviteten-utvecklare. Versioner är en ny version och byggartefakter som kan överföras till olika databaser. Den här artikeln visar hur du använder Azure Blob storage som lagringsplats för byggartefakterna. Den visas också hur du hämtar beroenden från Azure Blob storage.

Mer information om Hudson finns på [uppfyller Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Fördelarna med att använda Blob-tjänsten
Fördelar med att använda Blob service som värd för din smidig utveckling byggartefakter är:

* Hög tillgänglighet för din byggartefakter och/eller nedladdningsbara beroenden.
* Prestanda när din Hudson CI-lösning laddar upp din byggartefakter.
* Prestanda när hämtar din byggartefakter, kunder och partner.
* Kontroll över principer för åtkomst, med olika alternativ anonym åtkomst, upphör att gälla-baserad delad åtkomst signatur åtkomst, privat åtkomst, osv.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att använda Blob-tjänsten med Hudson CI-lösning:

* En hudson-baserad CI-lösning.
  
    Om du för närvarande inte har en Hudson CI-lösning kan du köra en Hudson CI-lösning med hjälp av följande metod:
  
  1. Ladda ned Hudson WAR från på en Java-aktiverad dator <http://hudson-ci.org/>.
  2. I Kommandotolken som öppnas till den mapp som innehåller Hudson WAR, kör du Hudson WAR. Exempel: Om du har hämtat version 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. Öppna i webbläsaren, `http://localhost:8080/`. Hudson-instrumentpanelen öppnas.
  4. Slutföra installationen vid vid första användning av Hudson `http://localhost:8080/`.
  5. När du har den inledande installationen, Avbryt den pågående instansen av Hudson WAR, starta Hudson WAR igen och öppnar instrumentpanelen Hudson `http://localhost:8080/`, som du använder för att installera och konfigurera Azure Storage-plugin-programmet.
     
      När en typisk Hudson CI-lösning skulle ställas in att köras som en tjänst som körs på Hudson war på kommandoraden räcker för den här självstudiekursen.
* Ett Azure-konto. Du kan registrera dig för ett Azure-konto på <http://www.azure.com>.
* Ett Azure-lagringskonto. Om du inte redan har ett lagringskonto kan du skapa en med hjälp av stegen i [skapa ett Lagringskonto](../common/storage-quickstart-create-account.md).
* Liknar processen med Hudson CI-lösning rekommenderas men krävs inte, eftersom följande innehåll kommer att använda ett grundläggande exempel för att visa de steg som krävs när du använder Blob service som databas för Hudson CI skapa artefakter.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Hur du använder Blob-tjänsten med Hudson CI
Om du vill använda Blob-tjänsten med Hudson, måste du installera plugin-programmet Azure Storage, konfigurera plugin-programmet för att använda ditt storage-konto och sedan skapa en post-build-åtgärd som laddar upp din byggartefakter till ditt lagringskonto. De här stegen beskrivs i följande avsnitt.

## <a name="how-to-install-the-azure-storage-plugin"></a>Så här installerar du Azure Storage-plugin-programmet
1. Inom Hudson-instrumentpanelen klickar du på **hantera Hudson**.
2. På den **hantera Hudson** klickar du på **hantera plugin-program**.
3. Klicka på den **tillgänglig** fliken.
4. Klicka på **andra**.
5. I den **artefakt Uppladdare** väljer **Microsoft Azure Storage-plugin-programmet**.
6. Klicka på **Installera**.
7. När installationen är klar, startar du om Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Så här konfigurerar du Azure Storage-plugin-programmet för att använda ditt storage-konto
1. Inom Hudson-instrumentpanelen klickar du på **hantera Hudson**.
2. På den **hantera Hudson** klickar du på **konfigurera System**.
3. I den **konfiguration av Microsoft Azure Lagringskonto** avsnittet:
   
    a. Ange namnet på ditt lagringskonto, som du kan hämta från den [Azure-portalen](https://portal.azure.com).
   
    b. Ange din lagringskontonyckel, även kan erhållas från den [Azure-portalen](https://portal.azure.com).
   
    c. Använd standardvärdet för **slutpunkts-URL för Blob Service** om du använder offentliga Azure-molnet. Om du använder en annan Azure-molnet, Använd slutpunkten som anges i den [Azure-portalen](https://portal.azure.com) för ditt lagringskonto.
   
    d. Klicka på **verifiera autentiseringsuppgifter för lagringskontot** att verifiera ditt storage-konto.
   
    e. [Valfritt] Om du har ytterligare storage-konton som du vill få tillgång till din Hudson CI, klickar du på **lägga till fler lagringskonton**.
   
    f. Klicka på **spara** att spara dina inställningar.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Så här skapar du en efter byggåtgärden som laddar upp din byggartefakter till ditt storage-konto
För anvisningar behöver vi först du skapar ett jobb som kommer att skapa flera filer och sedan lägga till i instruktionen efter skapandet överföra filerna till ditt lagringskonto.

1. Inom Hudson-instrumentpanelen klickar du på **nytt jobb**.
2. Namnge jobbet **MyJob**, klickar du på **skapa ett jobb för freestyle-programvara**, och klicka sedan på **OK**.
3. I den **skapa** avsnittet jobbkonfigurationen, klickar du på **Lägg till byggsteg** och välj **köra Windows batch-kommandot**.
4. I **kommandot**, Använd följande kommandon:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. I den **post-build Actions** avsnittet jobbkonfigurationen, klickar du på **ladda upp artefakter till Microsoft Azure Blob storage**.
6. För **Lagringskontonamn**, Välj lagringskontot som du använder.
7. För **behållarnamn**, ange behållarens namn. (Behållaren skapas om det inte redan finns när byggartefakterna laddas.) Du kan använda miljövariabler, så det här exemplet anger **${JOB_NAME}** som behållarens namn.
   
    **Tips**
   
    Under den **kommandot** avsnitt där du har angett ett skript för **köra Windows batch-kommandot** är en länk till miljövariabler som identifieras av Hudson. Klicka på länken Mer miljövariabelnamn och beskrivningar. Observera att miljövariabler som innehåller särskilda tecken, till exempel den **BUILD_URL** miljövariabeln, är inte tillåtna som namn på behållare eller vanliga virtuell sökväg.
8. Klicka på **offentliggöra ny behållare som standard** i det här exemplet. (Om du vill använda en privat behållare, du måste skapa en signatur för delad åtkomst för att tillåta åtkomst. Som ligger utanför omfånget för den här artikeln. Du kan läsa mer om signaturer för delad åtkomst på [med signaturer för delad åtkomst (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Valfritt] Klicka på **ren behållaren innan du laddar upp** om du vill att behållaren tas bort av innehållet innan byggartefakter överförs (lämna det avmarkerat om du inte vill att rensa innehållet i behållaren).
10. För **lista av artefakter för att ladda upp**, ange **text/*.txt**.
11. För **vanliga virtuella sökvägen för överförda artefakter**, ange **${skapa\_ID} / ${skapa\_NUMBER}**.
12. Klicka på **spara** att spara dina inställningar.
13. I Hudson-instrumentpanelen klickar du på **skapa nu** att köra **MyJob**. Granska konsolutdata för status. Statusmeddelanden för Azure Storage inkluderas i konsolens utdata när instruktionen efter skapandet börjar ladda upp byggartefakter.
14. Du kan undersöka byggartefakterna genom att öppna den offentliga blobben vid slutförande av jobbet.
    
    a. Logga in på [Azure Portal](https://portal.azure.com).
    
    b. Klicka på **Storage**.
    
    c. Klicka på namnet på lagringskontot som du använde för Hudson.
    
    d. Klicka på **behållare**.
    
    e. Klicka på den behållare med namnet **myjob**, vilket är en gemen version av namnet på det jobb som du valde när du skapade Hudson-jobbet. Namn på behållare och blobnamn är gemener (och skiftlägeskänsliga) i Azure Storage. I listan över blobar för behållaren med namnet **myjob** bör du se **hello.txt** och **date.txt**. Kopiera Webbadressen för något av dessa objekt och öppna den i webbläsaren. Textfil som har överförts visas som en byggesartefakt.

Endast en efter byggåtgärden som laddar upp artefakter till Azure Blob storage kan skapas per jobb. Observera att den enda post-build åtgärden att ladda upp artefakter till Azure Blob storage kan ange olika filer (inklusive jokertecken) och sökvägar för filer i **lista av artefakter för att ladda upp** med ett semikolon som avgränsare. Skapar till exempel om ditt Hudson bygger JAR-filerna och TXT-filer i din arbetsyta **skapa** mapp, och du vill ladda upp både till Azure Blob storage, använder du följande för den **lista av artefakter för att ladda upp** värde: **skapa /\*.jar; build /\*.txt**. Du kan också använda dubbla kolon syntax för att ange en sökväg som ska användas i blobnamnet. Exempel: Om du vill att JAR-filer som får överföras med hjälp av **binärfiler** i blobbsökvägen och TXT-filer som får överföras med hjälp av **meddelanden** i blob-sökväg, använder du följande för den **lista av artefakter ladda upp** värde: **skapa /\*. jar::binaries; build /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Så här skapar du ett byggsteg som hämtar från Azure Blob storage
Följande steg visar hur du konfigurerar ett byggsteg för att hämta objekt från Azure Blob storage. Detta är användbart om du vill inkludera objekt i din version, till exempel JAR-filer som du håller i Azure Blob storage.

1. I den **skapa** avsnittet jobbkonfigurationen, klickar du på **Lägg till byggsteg** och välj **ladda ned från Azure Blob storage**.
2. För **lagringskontonamn**, Välj lagringskontot som du använder.
3. För **behållarnamn**, ange namnet på behållaren som innehåller de blobar som du vill hämta. Du kan använda miljövariabler.
4. För **blobnamnet**, ange blobnamnet på. Du kan använda miljövariabler. Du kan också använda en asterisk som jokertecken när du har angett den första bokstaven i blobnamnet. Till exempel **projekt\***  anger alla blobbar vars namn börjar på **projekt**.
5. [Valfritt] För **hämtningssökvägen**, ange sökvägen på den Hudson-datorn där du vill ladda ned filer från Azure Blob storage. Miljövariabler kan också användas. (Om du inte anger ett värde för **hämtningssökvägen**, hämtas filer från Azure Blob storage till arbetsytan för jobbets.)

Om du har ytterligare objekt som du vill ladda ned från Azure Blob storage kan skapa du ytterligare byggsteg.

När du kör en version måste du kontrollera build historik konsolens utdata eller titta på din nedladdningsplatsen, för att se om de blobar som du förväntade dig har laddats ned.

## <a name="components-used-by-the-blob-service"></a>Komponenter som används av Blob-tjänsten
Följande innehåller en översikt över tjänstkomponenter Blob.

* **Storage-konto**: All åtkomst till Azure Storage görs genom ett lagringskonto. Det här är den högsta nivån av namnområdet för att komma åt blobar. Ett konto kan innehålla ett obegränsat antal behållare, så länge som deras totala storlek är under 100 TB.
* **Behållaren**: en behållare grupperar en uppsättning blobbar. Alla blobar måste vara i en container. Ett konto kan innehålla ett obegränsat antal containrar. En container kan lagra ett obegränsat antal blobar.
* **BLOB**: en fil av valfri typ och storlek. Det finns två typer av blobbar som kan lagras i Azure Storage: block- och sidblobbar. De flesta filer som är blockblobar. En enda blockblobb kan vara upp till 200 GB i storlek. Den här självstudien använder blockblobar. Sidblobar, en annan blobtyp kan vara upp till 1 TB i storlek, och är mer effektivt när intervallen för byte i en fil ändras ofta. Läs mer om BLOB-objekt, [förstå Blockblobbar, Tilläggsblobbar och Sidblobbar](http://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL-format**: BLOB-lagring är adresserbara via följande URL-format:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Formatet som ovan gäller till offentliga Azure-molnet. Om du använder en annan Azure-molnet kan använda slutpunkten inom den [Azure-portalen](https://portal.azure.com) att fastställa din URL-slutpunkt.)
  
    I formatet som ovan, `storageaccount` representerar namnet på ditt lagringskonto `container_name` representerar namnet på din behållare och `blob_name` respektive representerar namnet på din blob. Du kan ha flera sökvägar, avgränsade med ett snedstreck inom behållarnamn, **/**. Exempel behållarens namn i den här självstudien har **MyJob**, och **${skapa\_ID} / ${skapa\_NUMBER}** har använts för den vanliga virtuella sökvägen, vilket resulterar i blobben med en URL för den följande format:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Nästa steg
* [Uppfyll Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Azure Storage SDK för Java](https://github.com/azure/azure-storage-java)
* [Azure Storage Client SDK-referens](http://dl.windowsazure.com/storage/javadoc/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage Teamblogg](http://blogs.msdn.com/b/windowsazurestorage/)

Mer information finns i [Azure för Java-utvecklare](/java/azure).