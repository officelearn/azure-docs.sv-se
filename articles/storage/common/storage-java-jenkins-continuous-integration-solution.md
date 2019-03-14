---
title: Använda Azure Storage med en lösning för kontinuerlig integrering av Jenkins
description: Den här självstudien visar hur du använder Azure blob service som en lagringsplats för skapa artefakter som skapats av en lösning för kontinuerlig integrering av Jenkins.
ms.topic: article
ms.author: tarcher
author: tarcher
services: devops
ms.service: storage
custom: jenkins
ms.date: 07/31/2018
ms.subservice: common
ms.openlocfilehash: 2aeab9be28a61f4e185be7192e7965cbf02bfed9
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791995"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Använda Azure Storage med en lösning för kontinuerlig integrering av Jenkins

Den här artikeln visar hur du kan använda Blob storage som databas för utvecklingsartefakter skapade med en Jenkins-lösning för kontinuerlig integrering (CI) eller som en källa för hämtningsbara filer som ska användas i en build-process. En av de scenarier som skulle hittar du den här lösningen användbar är när du kodning i en smidig utveckling-miljö (med Java eller andra språk), versioner som körs baserat på kontinuerlig integrering och du behöver en lagringsplats för din byggartefakter, så att Du kan till exempel dela dem med andra organisationsmedlemmar i, dina kunder, eller skapa ett arkiv. Ett annat scenario är när din skapandejobb själva kräver andra filer, till exempel beroenden för att ladda ned som en del av den version som indata.

I de här självstudierna kommer du att använda Azure Storage-Plugin för Jenkins CI som gjorts tillgängliga av Microsoft.

## <a name="jenkins-overview"></a>Jenkins-översikt
Jenkins möjliggör kontinuerlig integrering för ett programvaruprojekt genom att låta utvecklare att enkelt integrera sina ändringar i koden och har producerat versioner automatiskt och ofta, vilket ökar produktiviteten-utvecklare. Versioner är en ny version och byggartefakter som kan överföras till olika databaser. Den här artikeln visar hur du använder Azure blob storage som lagringsplats för byggartefakterna. Den visas också hur du hämtar beroenden från Azure blob storage.

Mer information om Jenkins kan hittas på [uppfyller Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Fördelarna med att använda Blob-tjänsten
Fördelar med att använda Blob service som värd för din smidig utveckling byggartefakter är:

* Hög tillgänglighet för din byggartefakter och/eller nedladdningsbara beroenden.
* Prestanda när din Jenkins CI-lösning laddar upp din byggartefakter.
* Prestanda när hämtar din byggartefakter, kunder och partner.
* Kontroll över principer för åtkomst, med olika alternativ anonym åtkomst, upphör att gälla-baserad delad åtkomst signatur åtkomst, privat åtkomst, osv.

## <a name="prerequisites"></a>Förutsättningar
* En lösning för kontinuerlig integrering av Jenkins.
  
    Om du för närvarande inte har en Jenkins CI-lösning kan du köra ett Jenkins CI-lösning med hjälp av följande metod:
  
  1. Ladda ned jenkins.war från på en Java-aktiverad dator <http://jenkins-ci.org>.
  2. I Kommandotolken som öppnas till den mapp som innehåller jenkins.war, kör du:
     
      `java -jar jenkins.war`

  3. Öppna i webbläsaren, `http://localhost:8080/` att öppna Jenkins-instrumentpanelen som du använder för att installera och konfigurera Azure Storage-plugin-programmet.
     
      När en typisk Jenkins CI-lösning skulle ställas in att köras som en tjänst som körs på Jenkins-war på kommandoraden räcker för den här självstudiekursen.
* Ett Azure-konto. Du kan registrera dig för ett Azure-konto på <http://www.azure.com>.
* Ett Azure-lagringskonto. Om du inte redan har ett lagringskonto kan du skapa en med hjälp av stegen i [skapa ett Lagringskonto](../common/storage-quickstart-create-account.md).
* Liknar processen med Jenkins CI-lösning rekommenderas men krävs inte, eftersom följande innehåll kommer att använda ett grundläggande exempel för att visa de steg som krävs när du använder Blob service som databas för Jenkins CI skapa artefakter.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Hur du använder Blob-tjänsten med Jenkins CI
Om du vill använda Blob-tjänsten med Jenkins, måste du installera plugin-programmet Azure Storage, konfigurera plugin-programmet för att använda ditt storage-konto och sedan skapa en post-build-åtgärd som laddar upp din byggartefakter till ditt lagringskonto. De här stegen beskrivs i följande avsnitt.

## <a name="how-to-install-the-azure-storage-plugin"></a>Så här installerar du Azure Storage-plugin-programmet
1. Jenkins-instrumentpanelen väljer **hantera Jenkins**.
2. I den **hantera Jenkins** väljer **hantera plugin-program**.
3. Välj fliken **Tillgängliga**.
4. I den **artefakt Uppladdare** avsnittet, kontrollera **Microsoft Azure Storage-plugin-programmet**.
5. Välj antingen **installera utan omstart** eller **ladda ned nu och installera efter omstart**.
6. Starta om Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Så här konfigurerar du Azure Storage-plugin-programmet för att använda ditt storage-konto
1. Jenkins-instrumentpanelen väljer **hantera Jenkins**.
2. I den **hantera Jenkins** väljer **konfigurera System**.
3. I den **konfiguration av Microsoft Azure Lagringskonto** avsnittet:
   1. Ange namnet på ditt lagringskonto, som du kan hämta från den [Azure-portalen](https://portal.azure.com).
   2. Ange din lagringskontonyckel, även kan erhållas från den [Azure-portalen](https://portal.azure.com).
   3. Använd standardvärdet för **slutpunkts-URL för Blob Service** om du använder det globala Azure-molnet. Om du använder en annan Azure-molnet, Använd slutpunkten som anges i den [Azure-portalen](https://portal.azure.com) för ditt lagringskonto. 
   4. Välj **verifiera autentiseringsuppgifter för lagringskontot** att verifiera ditt storage-konto. 
   5. [Valfritt] Om du har ytterligare lagringskonton som ska göras tillgängliga för din Jenkins CI väljer **lägga till fler Lagringskonton**.
   6. Välj **spara** att spara dina inställningar.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Så här skapar du en efter byggåtgärden som laddar upp din byggartefakter till ditt storage-konto
För instruktioner måste du först skapa ett jobb som kommer att skapa flera filer och sedan lägga till i instruktionen efter skapandet överföra filerna till ditt lagringskonto.

1. Jenkins-instrumentpanelen väljer **nytt objekt**.
2. Namnge jobbet **MyJob**väljer **skapar en för freestyle-programvaruprojekt**, och välj sedan **OK**.
3. I den **skapa** avsnittet i väljer jobbkonfigurationen **Lägg till byggsteg** och välj **köra Windows batch-kommandot**.
4. I **kommandot**, Använd följande kommandon:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. I den **post-build Actions** avsnittet i väljer jobbkonfigurationen **Lägg till åtgärd för post-build** och välj **ladda upp artefakter till Azure Blob storage**.
6. För **lagringskontonamn**, Välj lagringskontot som du använder.
7. För **behållarnamn**, ange behållarens namn. (Behållaren skapas om det inte redan finns när byggartefakterna laddas.) Du kan använda miljövariabler, så det här exemplet anger `${JOB_NAME}` som behållarens namn.
   
    **Tips**
   
    Under den **kommandot** avsnitt där du har angett ett skript för **köra Windows batch-kommandot** är en länk till miljövariabler som identifieras av Jenkins. Välj länken miljövariabelnamn och beskrivningar. Miljövariabler som innehåller specialtecken, till exempel den **BUILD_URL** miljövariabeln, är inte tillåtna som namn på behållare eller vanliga virtuell sökväg.
8. Välj **offentliggöra ny behållare som standard** i det här exemplet. (Om du vill använda en privat behållare, du måste skapa en signatur för delad åtkomst för att tillåta åtkomst, vilket är utanför omfattningen för den här artikeln. Du kan läsa mer om signaturer för delad åtkomst på [med signaturer för delad åtkomst (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Valfritt] Välj **ren behållaren innan du laddar upp** om du vill att behållaren tas bort av innehållet innan byggartefakter överförs (lämna det avmarkerat om du inte vill att rensa innehållet i behållaren).
10. För **lista av artefakter för att ladda upp**, ange `text/*.txt`.
11. För **vanliga virtuella sökvägen för överförda artefakter**, för den här självstudiekursen, ange `${BUILD\_ID}/${BUILD\_NUMBER}`.
12. Välj **spara** att spara dina inställningar.
13. I Jenkins-instrumentpanelen väljer **skapa nu** att köra **MyJob**. Granska konsolutdata för status. Statusmeddelanden för Azure storage inkluderas i konsolens utdata när instruktionen efter skapandet börjar ladda upp byggartefakter.
14. Du kan undersöka byggartefakterna genom att öppna den offentliga blobben vid slutförande av jobbet.
    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Välj **Lagring**.
    3. Välj namnet på lagringskontot som du använde för Jenkins.
    4. Välj **behållare**.
    5. Välj behållaren med namnet **myjob**, vilket är en gemen version av namnet på det jobb som du valde när du skapade Jenkins-jobbet. Namn på behållare och blobnamn är gemener (och skiftlägeskänsliga) i Azure storage. I listan över blobar för behållaren med namnet **myjob**, bör du se **hello.txt** och **date.txt**. Kopiera Webbadressen för något av dessa objekt och öppna den i webbläsaren. Textfil som har överförts visas som en byggesartefakt.

Endast en efter byggåtgärden som laddar upp artefakter till Azure blob storage kan skapas per jobb. Den enda post-build åtgärden att ladda upp artefakter till Azure blob storage kan ange olika filer (inklusive jokertecken) och sökvägar till filer i **lista av artefakter för att ladda upp** med ett semikolon som avgränsare. Skapar till exempel om din Jenkins build JAR-filerna och TXT-filer i din arbetsyta **skapa** mapp, och du vill ladda upp både till Azure blob storage använder du följande värde för den **lista av artefakter för att ladda upp** alternativet: `build/\*.jar;build/\*.txt`. Du kan också använda dubbla kolon syntax för att ange en sökväg som ska användas i blobnamnet. Exempel: Om du vill att JAR-filer som får överföras med hjälp av **binärfiler** i blobbsökvägen och TXT-filer som får överföras med hjälp av **meddelanden** i blob-sökväg, använder du följande värde för den **lista över Artefakter att ladda upp** alternativet: `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Så här skapar du ett byggsteg som hämtar från Azure blob storage
Följande steg illustrerar om du vill konfigurera ett byggsteg för att hämta objekt från Azure blob storage, vilket är användbart om du vill inkludera objekt i din version. Ett exempel på hur du använder det här mönstret är JAR-filer som du kanske vill bevara i Azure blob storage.

1. I den **skapa** avsnittet i väljer jobbkonfigurationen **Lägg till byggsteg** och välj **ladda ned från Azure Blob storage**.
2. För **lagringskontonamn**, Välj lagringskontot som du använder.
3. För **behållarnamn**, ange namnet på behållaren som innehåller de blobar som du vill hämta. Du kan använda miljövariabler.
4. För **blobnamnet**, ange blobnamnet på. Du kan använda miljövariabler. Du kan också använda en asterisk som jokertecken när du har angett den första bokstaven i blobnamnet. Till exempel **projekt\***  anger alla blobbar vars namn börjar på **projekt**.
5. [Valfritt] För **hämtningssökvägen**, ange sökvägen på Jenkins-datorn där du vill ladda ned filer från Azure blob storage. Miljövariabler kan också användas. (Om du inte anger ett värde för **hämtningssökvägen**, hämtas filer från Azure blob storage till arbetsytan för jobbets.)

Om du har ytterligare objekt som du vill ladda ned från Azure blob storage kan skapa du ytterligare byggsteg.

När du kör en version måste du kontrollera build historik konsolens utdata eller titta på din nedladdningsplatsen, för att se om de blobar som du förväntade dig har laddats ned.  

## <a name="components-used-by-the-blob-service"></a>Komponenter som används av Blob-tjänsten
Det här avsnittet innehåller en översikt över tjänstkomponenter Blob.

* **Storage-konto**: All åtkomst till Azure Storage görs genom ett lagringskonto. Ett lagringskonto är den högsta nivån av namnområdet för att komma åt blobar. Ett konto kan innehålla ett obegränsat antal behållare, så länge som deras totala storlek är under 100 TB.
* **behållaren**: En behållare grupperar en uppsättning blobbar. Alla blobar måste vara i en container. Ett konto kan innehålla ett obegränsat antal containrar. En container kan lagra ett obegränsat antal blobar.
* **Blob**: En fil av valfri typ och storlek. Det finns två typer av blobbar som kan lagras i Azure Storage: block- och sidblobbar. De flesta filer som är blockblobar. En enda blockblobb kan vara upp till 200 GB i storlek. Den här självstudien använder blockblobar. Sidblobar, en annan blobtyp kan vara upp till 1 TB i storlek, och är mer effektivt när intervallen för byte i en fil ändras ofta. Läs mer om BLOB-objekt, [förstå Blockblobbar, Tilläggsblobbar och Sidblobbar](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL-format**: BLOB-lagring är adresserbara via följande URL-format:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Formatet som ovan gäller globala Azure-molnet. Om du använder en annan Azure-molnet kan använda slutpunkten inom den [Azure-portalen](https://portal.azure.com) att fastställa din URL-slutpunkt.)
  
    I formatet som ovan, `storageaccount` representerar namnet på ditt lagringskonto `container_name` representerar namnet på din behållare och `blob_name` respektive representerar namnet på din blob. Du kan ha flera sökvägar, avgränsade med ett snedstreck inom behållarnamn, **/**. Exempel behållarens namn i den här självstudien har **MyJob**, och **${skapa\_ID} / ${skapa\_NUMBER}** har använts för den vanliga virtuella sökvägen, vilket resulterar i blobben med en URL följande format:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på buggar med Jenkins-plugin-programmet kan du rapportera problemet i [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg
* [Uppfyll Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Azure Storage SDK för Java](https://github.com/azure/azure-storage-java)
* [Azure Storage Client SDK-referens](http://dl.windowsazure.com/storage/javadoc/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage Teamblogg](https://blogs.msdn.com/b/windowsazurestorage/)

Mer information finns i [Azure för Java-utvecklare](/java/azure).
