---
title: Hur man använder Hudson med Blob lagring | Microsoft-dokument
description: Beskriver hur du använder Hudson med Azure Blob-lagring som en lagringsplats för att skapa artefakter.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: a89439f49dd53f09d5cd40be0bf2e4981e9235d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201393"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Använda Azure Storage med en Hudson-lösning för kontinuerlig integrering
## <a name="overview"></a>Översikt
Följande information visar hur du använder Blob-lagring som en lagringsplats för byggartefakter som skapats av en Hudson Continuous Integration (CI) lösning, eller som en källa till nedladdningsbara filer som ska användas i en byggprocess. Ett av de scenarier där du skulle finna detta användbart är när du kodar i en flexibel utvecklingsmiljö (med Java eller andra språk), bygger körs baserat på kontinuerlig integration, och du behöver en databas för din bygga artefakter, så att du kan, Dela dem till exempel med andra organisationsmedlemmar, dina kunder eller underhålla ett arkiv.  Ett annat scenario är när själva byggjobbet kräver andra filer, till exempel beroenden att hämta som en del av byggindata.

I den här självstudien kommer du att använda Azure Storage-insticksprogrammet för Hudson CI som gjorts tillgänglig av Microsoft.

## <a name="introduction-to-hudson"></a>Introduktion till Hudson
Hudson möjliggör kontinuerlig integrering av ett mjukvaruprojekt genom att låta utvecklare enkelt integrera sina kodändringar och få byggen som produceras automatiskt och ofta, vilket ökar produktiviteten hos utvecklarna. Byggen är versionsversioner och skapa artefakter kan överföras till olika databaser. Den här artikeln visar hur du använder Azure Blob-lagring som lagringsplats för build-artefakter. Det visar också hur du hämtar beroenden från Azure Blob-lagring.

Mer information om Hudson finns på [Meet Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Fördelar med att använda Blob-tjänsten
Fördelarna med att använda Blob-tjänsten för att vara värd för dina agila utvecklingsversionsartefakter är:

* Hög tillgänglighet för dina byggartefakter och/eller nedladdningsbara beroenden.
* Prestanda när din Hudson CI-lösning laddar upp dina byggartefakter.
* Prestanda när dina kunder och partner hämtar dina byggartefakter.
* Kontroll över principer för användaråtkomst, med ett val mellan anonym åtkomst, åtkomst till signatur för delad åtkomst för delad åtkomst, privat åtkomst osv.

## <a name="prerequisites"></a>Krav
Du behöver följande för att använda Blob-tjänsten med din Hudson CI-lösning:

* En Hudson Continuous Integration-lösning.
  
    Om du för närvarande inte har en Hudson CI-lösning kan du köra en Hudson CI-lösning med följande teknik:
  
  1. På en Java-aktiverad dator [laddar du ner Hudson WAR-filen](https://www.eclipse.org/hudson/download.php).
  2. Vid en kommandotolk som öppnas för mappen som innehåller Hudson WAR, kör Hudson WAR. Om du till exempel har laddat ned version 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. Öppna i webbläsaren `http://localhost:8080/`. Detta kommer att öppna Hudson instrumentpanelen.
  4. Vid första användning av Hudson, `http://localhost:8080/`slutföra den första installationen på .
  5. När du har slutfört den första installationen avbryter du den löp förekomsten av `http://localhost:8080/`Hudson WAR, startar Hudson WAR igen och öppnar Hudson-instrumentpanelen igen, som du använder för att installera och konfigurera Azure Storage-insticksprogrammet.
     
      Medan en typisk Hudson CI lösning skulle inrättas för att köra som en tjänst, kör Hudson kriget på kommandoraden kommer att vara tillräckligt för den här guiden.
* Ett Azure-konto. Du kan registrera dig för <https://www.azure.com>ett Azure-konto på .
* Ett Azure-lagringskonto. Om du inte redan har ett lagringskonto kan du skapa ett med hjälp av stegen på [Skapa ett lagringskonto](../common/storage-account-create.md).
* Förtrogenhet med Hudson CI-lösningen rekommenderas men krävs inte, eftersom följande innehåll kommer att använda ett grundläggande exempel för att visa dig de steg som behövs när du använder Blob-tjänsten som en lagringsplats för Hudson CI bygga artefakter.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Så här använder du Blob-tjänsten med Hudson CI
Om du vill använda Blob-tjänsten med Hudson måste du installera plugin-programmet Azure Storage, konfigurera plugin-programmet så att det använder ditt lagringskonto och sedan skapa en åtgärd efter build som överför dina byggartefakter till ditt lagringskonto. De här stegen beskrivs i följande avsnitt.

## <a name="how-to-install-the-azure-storage-plugin"></a>Så här installerar du plugin-programmet Azure Storage
1. I Hudson-instrumentpanelen klickar du på **Hantera Hudson**.
2. På sidan **Hantera Hudson** klickar du på **Hantera plugins**.
3. Klicka på fliken **Tillgänglig.**
4. Klicka på **Andra**.
5. I avsnittet **Artifact Uploaders** väljer du **Plugin Microsoft Azure Storage**.
6. Klicka på **Installera**.
7. Starta om Hudson när installationen är klar.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Konfigurera azure storage-insticksprogrammet för att använda ditt lagringskonto
1. I Hudson-instrumentpanelen klickar du på **Hantera Hudson**.
2. Klicka på **Konfigurera system**på sidan **Hantera Hudson** .
3. I avsnittet Konfiguration av **Microsoft Azure Storage Account:**
   
    a. Ange ditt lagringskontonamn som du kan hämta från [Azure-portalen](https://portal.azure.com).
   
    b. Ange din lagringskontonyckel, som också kan hämtas från [Azure-portalen](https://portal.azure.com).
   
    c. Använd standardvärdet för **slutpunkts-URL för Blob-tjänst** om du använder det globala Azure-molnet. Om du använder ett annat Azure-moln använder du slutpunkten som anges i [Azure-portalen](https://portal.azure.com) för ditt lagringskonto.
   
    d. Klicka på **Validera lagringsautentiseringsuppgifter** för att validera ditt lagringskonto.
   
    e. [Valfritt] Om du har ytterligare lagringskonton som du vill ska göra tillgängliga för din Hudson CI klickar du på **Lägg till fler lagringskonton**.
   
    f. Spara inställningarna genom att klicka på **Spara.**

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Så här skapar du en post-build-åtgärd som överför dina byggartefakter till ditt lagringskonto
I instruktionssyfte måste vi först skapa ett jobb som skapar flera filer och sedan lägga till i åtgärden efter build för att ladda upp filerna till ditt lagringskonto.

1. Klicka på Nytt **jobb**i Hudson-instrumentpanelen.
2. Namnge jobbet **MyJob**, klicka på **Skapa ett kostnadsfritt programjobb**och klicka sedan på **OK**.
3. Klicka på **Lägg till byggsteg** i avsnittet Bygg i avsnittet **Skapa** och välj Kommandot **Kör Windows.**
4. I **Kommando**använder du följande kommandon:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. Klicka på **Ladda upp artefakter till Microsoft Azure Blob-lagring**i avsnittet Åtgärder efter **build** i jobbkonfigurationen .
6. För **Lagringskontonamn**väljer du det lagringskonto som ska användas.
7. Ange behållarnamnet för **Behållarnamn.** (Behållaren skapas om den inte redan finns när byggartefakterna överförs.) Du kan använda miljövariabler, så skriv i det här exemplet **${JOB_NAME}** som behållarnamn.
   
    **Tips**
   
    Under **avsnittet Kommando** där du angav ett skript för **kommandot Kör Windows är** en länk till de miljövariabler som Hudson känner igen. Klicka på länken om du vill lära dig miljövariabelnamn och beskrivningar. Miljövariabler som innehåller specialtecken, till exempel **BUILD_URL** miljövariabeln, tillåts inte som ett behållarnamn eller en gemensam virtuell sökväg.
8. Klicka på **Gör en ny behållare offentlig som standard** för det här exemplet. (Om du vill använda en privat behållare måste du skapa en signatur för delad åtkomst för att tillåta åtkomst. Det är utanför ramen för denna artikel. Du kan läsa mer om signaturer för delad åtkomst på [Använda SIGNATURER för delad åtkomst (SAS)](storage-sas-overview.md).)
9. [Valfritt] Klicka på **Rensa behållare innan** du överför om du vill att behållaren ska rensas från innehållet innan byggartefakter överförs (lämna den avmarkerad om du inte vill rensa innehållet i behållaren).
10. Ange **List of Artifacts to upload** **text/*.txt**.
11. Ange **${BUILD\_ID}/${BUILD\_NUMBER}** för vanliga virtuella **sökväg för uppladdade artefakter**.
12. Spara inställningarna genom att klicka på **Spara.**
13. I Hudson-instrumentpanelen klickar du på **Bygg nu** för att köra **MyJob**. Undersök konsolens utgång för status. Statusmeddelanden för Azure Storage inkluderas i konsolutdata när åtgärden efter build börjar överföra byggartefakter.
14. När jobbet har slutförts kan du undersöka byggartefakterna genom att öppna den offentliga blobben.
    
    a. Logga in på [Azure-portalen](https://portal.azure.com).
    
    b. Klicka på **Lagring**.
    
    c. Klicka på det lagringskontonamn som du använde för Hudson.
    
    d. Klicka på **Containers**.
    
    e. Klicka på behållaren **myjob**, som är den gemena versionen av jobbnamnet som du tilldelade när du skapade Hudson-jobbet. Behållarnamn och blobnamn är gemener (och skiftlägeskänsliga) i Azure Storage. I listan över blobbar för behållaren som heter **myjob** bör du se **hello.txt** och **date.txt**. Kopiera webbadressen för något av dessa objekt och öppna den i webbläsaren. Du kommer att se textfilen som laddades upp som en byggartefakt.

Endast en post-build-åtgärd som överför artefakter till Azure Blob-lagring kan skapas per jobb. Den enda åtgärden efter build för att överföra artefakter till Azure Blob-lagring kan ange olika filer (inklusive jokertecken) och sökvägar till filer i **Lista över artefakter som ska överföras** med hjälp av ett semikolon som avgränsare. Om din Hudson-version till exempel producerar JAR-filer och TXT-filer i arbetsytans **byggmapp** och du vill överföra båda till Azure Blob-lagring använder du följande för **listan över artefakter för att ladda upp** värde: **build/\*.jar;build/\*.txt**. Du kan också använda dubbelkolonsyntax för att ange en sökväg som ska användas i blobnamnet. Om du till exempel vill att JAR:erna ska överföras med **binärfiler** i blob-sökvägen och TXT-filerna ska **överföras** med meddelanden i blob-sökvägen använder du följande för **listan över artefakter för att ladda upp** värde: **build/\*.jar::binaries;build/\*.txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Skapa ett byggsteg som hämtas från Azure Blob-lagring
Följande steg visar hur du konfigurerar ett byggsteg för att hämta objekt från Azure Blob-lagring. Detta skulle vara användbart om du vill inkludera objekt i din version, till exempel JARs som du har i Azure Blob-lagring.

1. Klicka på **Lägg till byggsteg** i avsnittet **Bygg** i projektkonfigurationen och välj Hämta från **Azure Blob storage**.
2. För **lagringskontonamn**väljer du det lagringskonto som ska användas.
3. För **Behållarnamn**anger du namnet på behållaren som har de blobbar som du vill hämta. Du kan använda miljövariabler.
4. För **Blob-namn**anger du blobnamnet. Du kan använda miljövariabler. Du kan också använda en asterisk som ett jokertecken när du har angett de första bokstäverna i blobnamnet. Projekt ***\\**anger till exempel alla blobbar vars namn börjar med **projektet**.
5. [Valfritt] För **sökväg hämta**anger du sökvägen på Hudson-datorn där du vill hämta filer från Azure Blob-lagring. Miljövariabler kan också användas. (Om du inte anger något värde för **sökväg hämtas**filerna från Azure Blob-lagring till jobbets arbetsyta.)

Om du har ytterligare objekt som du vill hämta från Azure Blob-lagring kan du skapa ytterligare byggsteg.

När du har kört en version kan du kontrollera utdata för build history-konsolen eller titta på hämtningsplatsen för att se om blobbar som du förväntade dig har hämtats.

## <a name="components-used-by-the-blob-service"></a>Komponenter som används av Blob-tjänsten
Följande ger en översikt över Blob-tjänstkomponenterna.

* **Lagringskonto**: All åtkomst till Azure Storage sker via ett lagringskonto. Det här är den högsta nivån i namnområdet för åtkomst till blobbar. Ett konto kan innehålla ett obegränsat antal behållare, så länge deras totala storlek är under 100 TB.
* **Behållare**: En behållare innehåller en gruppering av en uppsättning blobbar. Alla blobar måste vara i en container. Ett konto kan innehålla ett obegränsat antal containrar. En container kan lagra ett obegränsat antal blobar.
* **Blob**: En fil av alla typer och storlekar. Det finns två typer av blobbar som kan lagras i Azure Storage: block- och sidblobar. De flesta filer är blockblobar. En enda blockblob kan vara upp till 200 GB i storlek. Den här självstudien använder blockblobar. Sidblobar, en annan blobtyp, kan vara upp till 1 TB stora och är effektivare när byteintervall i en fil ändras ofta. Mer information om blobbar finns i [Förstå blockblobar, tilläggsblobar och sidblobar](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL-format:** Blobbar kan adresseras med hjälp av följande URL-format:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Formatet ovan gäller för det globala Azure-molnet. Om du använder ett annat Azure-moln använder du slutpunkten i [Azure-portalen](https://portal.azure.com) för att fastställa url-slutpunkten.)
  
    I formatet ovan `storageaccount` representerar namnet på `container_name` ditt lagringskonto, namnet `blob_name` på din behållare och representerar namnet på din blob. I behållarnamnet kan du ha flera sökvägar, **/** avgränsade med ett snedstreck, . Exempelbehållarens namn i den här självstudien var **MyJob**och **\_${BUILD ID}/${BUILD\_NUMBER}** användes för den vanliga virtuella sökvägen, vilket resulterade i att bloben hade en URL för följande formulär:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Nästa steg
* [Möt Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Azure Storage SDK för Java](https://github.com/azure/azure-storage-java)
* [Azure Storage Client SDK-referens](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogg för Azure Storage Team](https://blogs.msdn.com/b/windowsazurestorage/)

Mer information finns i [Azure för Java-utvecklare](/java/azure).
