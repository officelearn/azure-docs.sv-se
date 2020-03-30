---
title: Använda Azure Storage med en Jenkins-lösning för kontinuerlig integrering
description: Den här självstudien visar hur du använder Azure blob-tjänsten som en lagringsplats för att skapa artefakter som skapats av en Jenkins-lösning för kontinuerlig integrering.
keywords: jenkins, azurblå, devops, lagring, cicd
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: df1d59c40fd09fb055db9d7622d86ff9c82991b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624688"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Använda Azure Storage med en Jenkins-lösning för kontinuerlig integrering

Den här artikeln illustrerar hur du använder Blob-lagring som en lagringsplats för byggartefakter som skapats av en Jenkins-lösning för kontinuerlig integrering (CI) eller som en källa till nedladdningsbara filer som ska användas i en byggprocess. Ett av de scenarier där du skulle finna den här lösningen användbar är när du kodar i en flexibel utvecklingsmiljö (med Java eller andra språk), byggen körs baserat på kontinuerlig integrering och du behöver en databas för dina byggartefakter, så att du behöver en databas för dina byggartefakter, så att du behöver en databas för dina byggartefakter, så att du behöver en databas för dina byggartefakter, så att du behöver en databas för dina byggartefakter, så att du behöver en databas för dina byggartefakter, så att du behöver en databas för dina byggartefakter, så att du behöver en databas för dina byggartefakter, så att du behöver en databas för dina byggartefakter, Du kan till exempel dela dem med andra organisationsmedlemmar, dina kunder eller underhålla ett arkiv. Ett annat scenario är när själva byggjobbet kräver andra filer, till exempel beroenden att hämta som en del av byggindata.

I den här självstudien kommer du att använda Azure Storage Plugin för Jenkins CI som gjorts tillgängliga av Microsoft.

## <a name="jenkins-overview"></a>Jenkins översikt

Jenkins möjliggör kontinuerlig integrering av ett mjukvaruprojekt genom att låta utvecklare enkelt integrera sina kodändringar och få byggen som produceras automatiskt och ofta, vilket ökar utvecklarnas produktivitet. Byggen är versionsversioner och skapa artefakter kan överföras till olika databaser. Den här artikeln visar hur du använder Azure blob storage som lagringsplats för build-artefakter. Det visar också hur du hämtar beroenden från Azure blob storage.

Mer information om Jenkins finns på [Meet Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Fördelar med att använda Blob-tjänsten

Fördelarna med att använda Blob-tjänsten för att vara värd för dina agila utvecklingsversionsartefakter är:

* Hög tillgänglighet för dina byggartefakter och/eller nedladdningsbara beroenden.
* Prestanda när din Jenkins CI-lösning laddar upp dina byggartefakter.
* Prestanda när dina kunder och partner hämtar dina byggartefakter.
* Kontroll över principer för användaråtkomst, med ett val mellan anonym åtkomst, åtkomst till signatur för delad åtkomst för delad åtkomst, privat åtkomst osv.

## <a name="prerequisites"></a>Krav

* En Jenkins kontinuerlig integrationslösning.
  
    Om du för närvarande inte har en Jenkins CI-lösning kan du köra en Jenkins CI-lösning med följande teknik:
  
  1. På en Java-aktiverad dator, ladda <https://jenkins-ci.org>ner jenkins.war från .
  2. Kör i en kommandotolk som öppnas till mappen som innehåller jenkins.war:
     
      `java -jar jenkins.war`

  3. Öppna jenkins-instrumentpanelen i webbläsaren, `http://localhost:8080/` som du använder för att installera och konfigurera Azure Storage-insticksprogrammet.
     
      Medan en typisk Jenkins CI-lösning skulle ställas in för att köras som en tjänst, kommer kör Jenkins kriget på kommandoraden vara tillräckligt för den här guiden.
* Ett Azure-konto. Du kan registrera dig för <https://www.azure.com>ett Azure-konto på .
* Ett Azure-lagringskonto. Om du inte redan har ett lagringskonto kan du skapa ett med hjälp av stegen på [Skapa ett lagringskonto](../storage/common/storage-account-create.md).
* Förtrogenhet med Jenkins CI-lösningen rekommenderas men krävs inte, eftersom följande innehåll kommer att använda ett grundläggande exempel för att visa dig de steg som behövs när du använder Blob-tjänsten som en databas för Jenkins CI bygga artefakter.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Så här använder du Blob-tjänsten med Jenkins CI
Om du vill använda Blob-tjänsten med Jenkins måste du installera plugin-programmet Azure Storage, konfigurera plugin-programmet så att det använder ditt lagringskonto och sedan skapa en åtgärd efter build som överför dina byggartefakter till ditt lagringskonto. De här stegen beskrivs i följande avsnitt.

## <a name="how-to-install-the-azure-storage-plugin"></a>Så här installerar du plugin-programmet Azure Storage
1. Välj Hantera Jenkins på **Jenkins-instrumentpanelen**.
2. På sidan **Hantera Jenkins** väljer du **Hantera insticksprogram**.
3. Välj fliken **Tillgängliga**.
4. I avsnittet **Artifact Uploaders** kontrollerar du **plugin Microsoft Azure Storage**.
5. Välj antingen **Installera utan omstart** eller Ladda ner nu och installera efter **omstart**.
6. Starta om Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Konfigurera azure storage-insticksprogrammet för att använda ditt lagringskonto
1. Välj Hantera Jenkins på **Jenkins-instrumentpanelen**.
2. På sidan **Hantera Jenkins** väljer du **Konfigurera system**.
3. I avsnittet Konfiguration av **Microsoft Azure Storage Account:**
   1. Ange ditt lagringskontonamn som du kan hämta från [Azure-portalen](https://portal.azure.com).
   2. Ange din lagringskontonyckel, som också kan hämtas från [Azure-portalen](https://portal.azure.com).
   3. Använd standardvärdet för **slutpunkts-URL för Blob-tjänst** om du använder det globala Azure-molnet. Om du använder ett annat Azure-moln använder du slutpunkten som anges i [Azure-portalen](https://portal.azure.com) för ditt lagringskonto. 
   4. Välj **Validera lagringsreferenser** för att validera ditt lagringskonto. 
   5. [Valfritt] Om du har ytterligare lagringskonton som du vill ska göra tillgängliga för din Jenkins CI väljer du **Lägg till fler lagringskonton**.
   6. Välj **Spara** om du vill spara inställningarna.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Så här skapar du en post-build-åtgärd som överför dina byggartefakter till ditt lagringskonto
I instruktionssyfte måste du först skapa ett jobb som skapar flera filer och sedan lägga till i åtgärden efter build för att ladda upp filerna till ditt lagringskonto.

1. Välj Nytt objekt **på**Jenkins-instrumentpanelen .
2. Namnge jobbet **MyJob**, välj **Bygg ett friformatsprogram och**välj sedan **OK**.
3. I avsnittet **Skapa** i jobbkonfigurationen väljer du **Lägg till byggsteg** och väljer Kommandot Kör **Windows.**
4. I **Kommando**använder du följande kommandon:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. I avsnittet **Efterbyggningsåtgärder** i jobbkonfigurationen väljer du **Lägg till post-build-åtgärd** och väljer **Ladda upp artefakter till Azure Blob-lagring**.
6. För **lagringskontonamn**väljer du det lagringskonto som ska användas.
7. Ange **Container name**behållarnamnet för Behållarnamn. (Behållaren skapas om den inte redan finns när byggartefakterna överförs.) Du kan använda miljövariabler, `${JOB_NAME}` så för det här exemplet ange som behållarnamn.
   
    **Tips**
   
    Under avsnittet **Kommando** där du angav ett skript för **kommandot Kör Windows finns** en länk till de miljövariabler som Jenkins känner igen. Välj den länken om du vill lära dig miljövariabelnamn och beskrivningar. Miljövariabler som innehåller specialtecken, till exempel **BUILD_URL** miljövariabeln, tillåts inte som ett behållarnamn eller en gemensam virtuell sökväg.
8. Välj **Gör ny behållare offentlig som standard** för det här exemplet. (Om du vill använda en privat behållare måste du skapa en signatur för delad åtkomst för att tillåta åtkomst, vilket ligger utanför den här artikelns omfattning. Du kan läsa mer om signaturer för delad åtkomst på [Använda SIGNATURER för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md).)
9. [Valfritt] Välj **Rensa behållare innan** du överför om du vill att behållaren ska rensas från innehållet innan byggartefakter överförs (lämna den avmarkerad om du inte vill rensa innehållet i behållaren).
10. Ange **lista över artefakter som ska överföras** `text/*.txt`.
11. För **vanlig virtuell sökväg för uppladdade artefakter**anger `${BUILD\_ID}/${BUILD\_NUMBER}`du i den här självstudien .
12. Välj **Spara** om du vill spara inställningarna.
13. I Jenkins-instrumentpanelen väljer du **Bygg nu** för att köra **MyJob**. Undersök konsolens utgång för status. Statusmeddelanden för Azure-lagring inkluderas i konsolutdata när åtgärden efter build börjar överföra byggartefakter.
14. När jobbet har slutförts kan du undersöka byggartefakterna genom att öppna den offentliga blobben.
    1. Logga in på [Azure-portalen](https://portal.azure.com).
    2. Välj **Lagring**.
    3. Välj det lagringskontonamn som du använde för Jenkins.
    4. Välj **Behållare**.
    5. Markera behållaren **myjob**, som är den gemena versionen av jobbnamnet som du tilldelade när du skapade Jenkins-jobbet. Behållarnamn och blobnamn är gemener (och skiftlägeskänsliga) i Azure-lagring. I listan över blobbar för behållaren som heter **myjob,** bör du se **hello.txt** och **date.txt**. Kopiera webbadressen för något av dessa objekt och öppna den i webbläsaren. Du kommer att se textfilen som laddades upp som en byggartefakt.

Endast en post-build-åtgärd som överför artefakter till Azure blob-lagring kan skapas per jobb. Den enda åtgärden efter build för att överföra artefakter till Azure blob storage kan ange olika filer (inklusive jokertecken) och sökvägar till filer i **Lista över artefakter att ladda upp** med en semikolon som avgränsare. Om din Jenkins-version till exempel producerar JAR-filer och TXT-filer i arbetsytans **byggmapp** och du vill överföra båda till Azure-bloblagring använder du följande värde för **alternativet Lista över artefakter för att ladda upp:** `build/\*.jar;build/\*.txt`. Du kan också använda dubbelkolonsyntax för att ange en sökväg som ska användas i blobnamnet. Om du till exempel vill att JAR:arna ska överföras med **binärfiler** i blob-sökvägen och TXT-filerna ska **överföras** med hjälp av meddelanden i blob-sökvägen använder du följande värde för **alternativet Lista över artefakter för att ladda upp:** `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Skapa ett byggsteg som hämtar från Azure blob storage
Följande steg illustrerar hur du konfigurerar ett byggsteg för att hämta objekt från Azure blob storage, vilket är användbart om du vill inkludera objekt i din version. Ett exempel på hur du använder det här mönstret är JARs som du kanske vill behålla i Azure blob storage.

1. I avsnittet **Bygg** i jobbkonfigurationen väljer du **Lägg till byggsteg** och väljer Hämta från Azure **Blob storage**.
2. För **lagringskontonamn**väljer du det lagringskonto som ska användas.
3. För **Behållarnamn**anger du namnet på behållaren som har de blobbar som du vill hämta. Du kan använda miljövariabler.
4. För **Blob-namn**anger du blobnamnet. Du kan använda miljövariabler. Du kan också använda en asterisk som ett jokertecken när du har angett de första bokstäverna i blobnamnet. Projekt ***\\**anger till exempel alla blobbar vars namn börjar med **projektet**.
5. [Valfritt] För **sökväg hämta**anger du sökvägen på Jenkins-datorn där du vill hämta filer från Azure blob storage. Miljövariabler kan också användas. (Om du inte anger något värde för **sökväg hämtas**filerna från Azure blob storage hämtas till jobbets arbetsyta.)

Om du har ytterligare objekt som du vill hämta från Azure blob storage kan du skapa ytterligare byggsteg.

När du har kört en version kan du kontrollera utdata för build history-konsolen eller titta på hämtningsplatsen för att se om blobbar som du förväntade dig har hämtats.  

## <a name="components-used-by-the-blob-service"></a>Komponenter som används av Blob-tjänsten
Det här avsnittet innehåller en översikt över Blob-tjänstkomponenterna.

* **Lagringskonto:** All åtkomst till Azure Storage sker via ett lagringskonto. Ett lagringskonto är den högsta nivån i namnområdet för åtkomst till blobbar. Ett konto kan innehålla ett obegränsat antal behållare, så länge deras totala storlek är under 100 TB.
* **Behållare**: En behållare innehåller en gruppering av en uppsättning blobbar. Alla blobar måste vara i en container. Ett konto kan innehålla ett obegränsat antal containrar. En container kan lagra ett obegränsat antal blobar.
* **Blob**: En fil av alla typer och storlekar. Det finns två typer av blobbar som kan lagras i Azure Storage: block- och sidblobar. De flesta filer är blockblobar. En enda blockblob kan vara upp till 200 GB i storlek. Den här självstudien använder blockblobar. Sidblobar, en annan blobtyp, kan vara upp till 1 TB stora och är effektivare när byteintervall i en fil ändras ofta. Mer information om blobbar finns i [Förstå blockblobar, tilläggsblobar och sidblobar](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL-format:** Blobbar kan adresseras med hjälp av följande URL-format:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Formatet ovan gäller för det globala Azure-molnet. Om du använder ett annat Azure-moln använder du slutpunkten i [Azure-portalen](https://portal.azure.com) för att fastställa url-slutpunkten.)
  
    I formatet ovan `storageaccount` representerar namnet på `container_name` ditt lagringskonto, namnet `blob_name` på din behållare och representerar namnet på din blob. I behållarnamnet kan du ha flera sökvägar, **/** avgränsade med ett snedstreck, . Exempelbehållarens namn som användes för den här självstudien var **MyJob**och **\_${BUILD ID}/${BUILD\_NUMBER}** användes för den vanliga virtuella sökvägen, vilket resulterade i att bloben har en URL för följande formulär:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på några buggar med Jenkins plugins, lämna in ett problem i [Jenkins JIRA](https://issues.jenkins-ci.org/) för den specifika komponenten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Jenkins på Azure](/azure/Jenkins/)