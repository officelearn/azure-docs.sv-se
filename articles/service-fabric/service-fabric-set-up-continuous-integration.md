---
title: "Ställ in kontinuerlig integration för Azure mikrotjänster | Microsoft Docs"
description: "Få en översikt över hur du ställer in kontinuerlig integrering och distribution för ett Service Fabric-program med hjälp av Visual Studio Team Services VSTS ()."
services: service-fabric
documentationcenter: na
author: mthalman-msft
manager: timlt
editor: 
ms.assetid: 3e8c2290-9e7a-456a-9b2c-db44d1b3988d
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2016
ms.author: mthalman;mikhegn
ms.openlocfilehash: 76a1e013e824910c7a489e345b6563ae3951378f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="set-up-service-fabric-continuous-integration-and-deployment-with-visual-studio-team-services"></a>Konfigurera Service Fabric kontinuerlig integrering och distribution med Visual Studio Team Services
Den här artikeln beskriver stegen för att konfigurera kontinuerlig integrering och distribution för ett Azure Service Fabric-program med hjälp av Visual Studio Team Services VSTS ().

Det här dokumentet visar den aktuella proceduren och förväntas ändras med tiden.

## <a name="prerequisites"></a>Krav
Följ dessa steg för att komma igång:

1. Kontrollera att du har åtkomst till ett Team Services-konto eller [skapar du en](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) själv.
2. Kontrollera att du har åtkomst till ett Team Services grupprojekt eller [skapar du en](https://www.visualstudio.com/docs/setup-admin/create-team-project) själv.
3. Kontrollera att du har ett Service Fabric-kluster som du kan distribuera ditt program eller skapa en med hjälp av den [Azure-portalen](service-fabric-cluster-creation-via-portal.md), en [Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md), eller [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).
4. Se till att du redan har skapat ett Service Fabric-program (.sfproj)-projekt. Du måste ha ett projekt som har skapats eller uppgraderas med Service Fabric SDK 2.1 eller högre (.sfproj-filen ska innehålla ett egenskapsvärde för ProjectVersion 1.1 eller högre).

> [!NOTE]
> Anpassade build agenter inte längre behövs. Team Services finns agenter som nu levereras förinstallerade med Service Fabric klusterhantering för distribution av dina program direkt från dessa agenter.
> 
> 

## <a name="configure-and-share-your-source-files"></a>Konfigurera och dela dina källfiler
Det första som du vill göra är att förbereda en publiceringsprofil för användning av distributionsprocessen som körs i Team Services.  Profilen som ska konfigureras för att rikta det kluster som du tidigare har skapat:

1. Välj en publiceringsprofil inom det projektet som du vill använda för kontinuerlig integration arbetsflödet. Följ den [publicera instruktioner](service-fabric-publish-app-remote-cluster.md) om hur du publicerar ett program till ett kluster. Du behöver inte faktiskt publicera programmet även om. Du kan klicka på den **spara** hyperlänk i dialogrutan Publicera när du har konfigurerat saker på lämpligt sätt.
2. Om du vill att programmet ska uppgraderas för varje distribution sker inom Team Services som du vill konfigurera profilen som du vill aktivera uppgradering. I dialogrutan där du samma publicera används i steg 1 se till att den **uppgradera programmet** är markerad.  Lär dig mer om [konfigurera ytterligare inställningar](service-fabric-visualstudio-configure-upgrade.md). Klicka på den **spara** hyperlink att spara inställningarna i profilen.
3. Se till att du har sparat ändringarna på profilen och avbryter dialogrutan Publicera.
4. Nu är det dags att [dela källfilerna för programmet projektet](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#vs) med Team Services. När källfilerna är tillgängliga i Team Services, kan du nu gå vidare till nästa steg för att generera versioner. 

## <a name="create-a-build-definition"></a>Skapa en build-definition
En definition av Team Services build beskriver ett arbetsflöde som består av en uppsättning build-åtgärder som utförs i tur och ordning. Målet med build-definition som du skapar är att skapa ett Service Fabric-programpaket och andra artefakter som kan användas för att distribuera programmet. Mer information om Team Services [skapa definitioner](https://www.visualstudio.com/docs/build/define/create).

### <a name="create-a-definition-from-the-build-template"></a>Skapa en definition från build-mall
1. Öppna team projekt i Visual Studio Team Services.
2. Välj den **skapa** fliken.
3. Välj gröna  **+**  logga för att skapa en ny build-definition.
4. I den dialogruta som öppnas väljer du **Azure Service Fabric-programmet** inom den **skapa** mallkategori.
5. Välj **nästa**.
6. Välj databasen och gren kopplad till Service Fabric-programmet.
7. Välj agent kön som du vill använda. Värdbaserade agenter stöds.
8. Välj **Skapa**.
9. Spara build-definition och ange ett namn.
10. Följande punkt är en beskrivning byggsteg som genereras av mallen:

| Skapa steg | Beskrivning |
| --- | --- |
| NuGet-återställning |Återställer NuGet-paket för lösningen. |
| Skapa lösningen \*SLN |Skapar hela lösningen. |
| Skapa lösningen \*.sfproj |Skapar Service Fabric-programpaket som används för att distribuera programmet. Programmet paketplatsen har angetts ska vara i den versionen artefakt katalog. |
| Uppdatera Appversioner för Service Fabric |Uppdaterar version värdena i det programpaketet manifestfiler för stöd för uppgradering. Finns det [dokumentationssidan för aktiviteten](https://go.microsoft.com/fwlink/?LinkId=820529) för mer information. |
| Kopiera filer |Kopierar filerna för Publicera profil och programmet parametrar till den versionen artefakter som ska förbrukas för distribution. |
| Publicera artefakt |Publicerar den versionen artefakter. Gör en definition av versionen att använda den versionen artefakter. |

### <a name="verify-the-default-set-of-tasks"></a>Kontrollera en standarduppsättning av uppgifter
1. Kontrollera den **lösning** inmatningsfältet för den **NuGet återställning** och **skapa lösning** skapa steg.  Som standard build steg köras på alla lösningsfiler som finns i den associerade databasen.  Om du bara vill build-definition ska fungera på en av filerna lösningen måste du uttryckligen uppdatera sökvägen till filen.
2. Kontrollera den **lösning** inmatningsfältet för den **paketera program** skapa steg.  Det här build-steget förutsätter som standard endast ett tjänstprogram för Fabric-projekt (.sfproj) finns i databasen.  Om du har flera filer i databasen och vill använda endast en av dem för denna build-definition, måste du uttryckligen uppdatera sökvägen till filen.  Om du vill paketera flera projekt i databasen måste du skapa ytterligare **Visual Studio skapa** steg i build-definitionen varje mål Application-projekt.  Du kan sedan också behöva uppdatera de **MSBuild-argument** fält för var och en av de skapa steg så att paketplatsen är unika för dem.
3. Kontrollera beteendet versionshantering som definierats i den **App Uppdateringsversioner Service Fabric** skapa steg.  Som standard lägger det här steget build till build-nummer på alla Versionsvärden i det programpaketet manifestfiler. Finns det [dokumentationssidan för aktiviteten](https://go.microsoft.com/fwlink/?LinkId=820529) för mer information. Detta är användbart för att stödja uppgradering av ditt program eftersom varje uppgradera distributionen kräver olika Versionsvärden från den tidigare distribueringen. Om du inte avsikten är att använda programmet uppgradering i arbetsflödet, kan du inaktivera det här build-steget. Det måste inaktiveras om din avsikt är att skapa en version som kan användas för att skriva över en befintlig Service Fabric-program. Distributionen misslyckas om versionen av programmet som produceras av versionen inte matchar versionen av programmet i klustret.
4. Om din lösning innehåller ett .NET Core-projekt, måste du kontrollera att din build-definition innehåller ett build-steg som återställer beroenden:
   1. Välj **Lägg till build steg...** .
   2. Leta upp den **kommandoradsverktyget** aktiviteten inom fliken Verktyg och klicka på knappen Lägg till.
   3. För aktivitetens inmatningsfält, använder du följande värden:
   4. Verktyget: dotnet
   5. Argument: återställa
   6. Dra aktiviteten så att den är omedelbart efter den **NuGet återställning** steg.
5. Spara alla ändringar du har gjort i build-definition.

### <a name="try-it"></a>Prova
Välj **kön skapa** manuellt starta en version. Bygger också utlösare på push eller incheckning. När du har kontrollerat att bygga körs korrekt kan gå du nu vidare till att definiera en definition av versionen som distribuerar programmet till ett kluster.

## <a name="create-a-release-definition"></a>Skapa en definition för versionen
En definition av Team Services versionen beskriver ett arbetsflöde som består av en uppsättning uppgifter som utförs i tur och ordning. Målet med definitionen versionen som du skapar är ett programpaket och distribuera den till ett kluster. När de används tillsammans köra build definitionen och versionen definition i hela arbetsflödet från att starta med källfiler som slutar med ett program som körs i klustret. Mer information om Team Services [viktiga definitioner](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-definition-from-the-release-template"></a>Skapa en definition från versionsmall
1. Öppna projektet i Visual Studio Team Services.
2. Välj den **versionen** fliken.
3. Välj gröna  **+**  logga för att skapa en ny version definition och välj **skapa versionen definition** på menyn.
4. I den dialogruta som öppnas väljer du **Azure Service Fabric-distribution** inom den **distribution** mallkategori.
5. Välj **nästa**.
6. Välj build-definition som du vill använda som källa för den här versionen definitionen.  Definitionen för versionen refererar till artefakter av den valda build-definitionen.
7. Kontrollera den **kontinuerlig distribution** kryssrutan om du vill ha Team Services automatiskt skapa en ny version och distribuera Service Fabric-programmet varje gång en version har slutförts.
8. Välj agent kön som du vill använda. Värdbaserade agenter stöds.
9. Välj **Skapa**.
10. Redigera definition namnet genom att klicka på pennikonen längst upp på sidan.
11. Markerar det kluster som programmet ska distribueras från den **klustret anslutning** inmatningsfältet för aktiviteten. Kluster-anslutningen innehåller nödvändig information som gör att distributionen aktiviteten för att ansluta till klustret. Om du ännu inte har en kluster-anslutning för klustret, väljer du den **hantera** hyperlink bredvid fältet om du vill lägga till en. Utför följande steg på sidan som öppnas:
    1. Välj **nya tjänstslutpunkten** och välj sedan **Azure Service Fabric** på menyn.
    2. Välj typ av autentisering som används av klustret mål för den här slutpunkten.
    3. Ange ett namn för anslutningen i den **anslutningsnamn** fältet.  Normalt använder du namnet på klustret.
    4. Definiera klienten anslutning slutpunkts-URL i den **Klusterslutpunkten** fältet.  Exempel: tcp://contoso.westus.cloudapp.azure.com:19000.
    5. Definiera de autentiseringsuppgifter som du vill använda för att ansluta till klustret i Azure Active Directory-autentiseringsuppgifter för den **användarnamn** och **lösenord** fält.
    6. Definiera för certifikatbaserad autentisering Base64-kodning av certifikatfilen klient i den **klientcertifikat** fältet.  Hjälpen popup på fältet för information om hur du hämtar det värdet.  Om certifikatet är lösenordsskyddad, ange lösenordet i den **lösenord** fältet.
    7. Bekräfta ändringarna genom att klicka på **OK**. Efter att gå tillbaka till din versionen definition, klicka på Uppdatera på den **klustret anslutning** fältet för att se den slutpunkt som du just lagt till.
12. Spara definition för versionen.

> [!NOTE]
> Microsoft Accounts (till exempel @hotmail.com eller @outlook.com) stöds inte med Azure Active Directory-autentisering.
> 
> 

Definition som skapas består av en aktivitet av typen **Service Fabric-programdistribution**. Finns det [dokumentationssidan för aktiviteten](https://go.microsoft.com/fwlink/?LinkId=820528) för mer information om den här uppgiften.

### <a name="verify-the-template-defaults"></a>Kontrollera mallen standardvärden
1. Kontrollera den **Publicera profil** inmatningsfältet för den **distribuera Fabric tjänstprogrammet** aktivitet. Det här fältet refererar till en profil med namnet Cloud.xml som ingår i den versionen artefakter som standard. Om du vill referera till en annan profil eller om versionen innehåller flera programpaket i dess artefakter, måste du uppdatera sökvägen på lämpligt sätt.
2. Kontrollera den **programpaket** inmatningsfältet för den **distribuera Fabric tjänstprogrammet** aktivitet. Som standard refererar detta fält till programmet paketet standardsökvägen används i mallen för build-definition.  Om du har ändrat standardsökvägen för program som paketet i build-definition, måste du uppdatera sökvägen korrekt här samt.

### <a name="try-it"></a>Prova
Välj **skapa släpper** från den **släpper** knappmenyn för att manuellt skapa en version. I dialogrutan som öppnas väljer du den version som du vill basera versionen på och klicka sedan på **skapa**. Om du har aktiverat kontinuerlig distribution kommer versioner att skapas automatiskt när den associera build-definitionen är slutförd en version.

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om kontinuerlig integration med Service Fabric-program kan läsa följande artiklar:

* [Team Services dokumentationen hemnätverk](https://www.visualstudio.com/docs/overview)
* [Skapa hantering i Team Services](https://www.visualstudio.com/docs/build/overview)
* [Versionshantering i Team Services](https://www.visualstudio.com/docs/release/overview)

