<properties
   pageTitle="Skapa ditt första Service Fabric-program i Linux med Java | Microsoft Azure"
   description="Skapa och distribuera ett Service Fabric-program med Java"
   services="service-fabric"
   documentationCenter="java"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="seanmck"/>



# Skapa ditt första Azure Service Fabric-program

> [AZURE.SELECTOR]
- [C# – Windows](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java – Linux](service-fabric-create-your-first-linux-application-with-java.md)
- [C# – Linux](service-fabric-create-your-first-linux-application-with-csharp.md)

Service Fabric innehåller SDK:er för att skapa tjänster i Linux i både .NET Core och Java. I den här självstudiekursen visar vi hur du skapar ett program för Linux och skapar en tjänst med hjälp av Java.

## Krav

Du måste [konfigurera Linux-utvecklingsmiljön](service-fabric-get-started-linux.md) innan du börjar. Om du använder Mac OS X kan du [konfigurera en Linux-miljö på en virtuell dator med hjälp av Vagrant](service-fabric-get-started-mac.md).

## Skapa programmet

Ett Service Fabric-program kan innehålla en eller flera tjänster, som var och en ansvarar för att leverera programmets funktioner. I Service Fabric SDK för Linux finns en [Yeoman](http://yeoman.io/)-generator som gör det enkelt att skapa din första tjänst och lägga till fler senare. Lås oss använda Yeoman för att skapa ett nytt program med en enskild tjänst.

1. Skriv **yo azuresfjava** i en terminal.

2. Namnge ditt program.

3. Välj vilken typ din första tjänst ska ha och namnge den. I den här självstudiekursen väljer vi en Reliable Actor-tjänst.

  ![Service Fabric Yeoman-generator för Java][sf-yeoman]

>[AZURE.NOTE] Mer information om alternativen finns i [Översikt över Service Fabric-programmeringsmodell](service-fabric-choose-framework.md).

## Skapa programmet

I Service Fabric Yeoman-mallarna ingår ett byggskript för [Gradle](https://gradle.org/), som du kan använda för att skapa appen från terminalen.

  ```bash
  cd myapp
  gradle
  ```

## Distribuera programmet

När du har skapat programmet kan du distribuera det till det lokala klustret med Azure CLI.

1. Anslut till det lokala Service Fabric-klustret.

    ```bash
    azure servicefabric cluster connect
    ```

2. Använd installationsskriptet som medföljer mallen för att kopiera programpaketet till klustrets avbildningsarkiv, registrera programtypen och skapa en instans av programmet.

    ```bash
    ./install.sh
    ```

3. Öppna en webbläsare och gå till Service Fabric Explorer på http://localhost:19080/Explorer (ersätt localhost med den virtuella datorns privata IP om du använder Vagrant på Mac OS X).

4. Expandera programnoden och observera att det nu finns en post för din programtyp och en post för den första instansen av den typen.

## Starta testklienten och utför en redundansväxling

Aktörsprojekt gör ingenting på egen hand. Det behövs en annan tjänst eller klient för att skicka meddelanden till dem. Aktörsmallen innehåller ett enkelt testskript som du kan använda för att interagera med aktörstjänsten.

1. Kör skriptet med övervakningsverktyget för att se resultatet av aktörstjänsten.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. I Service Fabric Explorer letar du reda på noden där den primära repliken för aktörstjänsten finns. På skärmbilden nedan är det nod 3.

    ![Hitta den primära repliken i Service Fabric Explorer][sfx-primary]

3. Klicka på noden du hittade i föregående steg och välj sedan **Deactivate (restart)** (Inaktivera (omstart)) på menyn Actions (Åtgärder). Därmed startas en av de fem noderna om i ditt lokala kluster och påtvingar en redundansväxling till en av de sekundära replikerna på en annan nod. Titta på resultatet från testklienten när du gör detta och observera att räknaren fortsätter att räkna upp trots redundansväxlingen.

## Skapa och distribuera ett program med plugin-programmet Eclipse Neon

Om du har installerat plugin-programmet för Eclipse Neon kan du använda det till att skapa och distribuera Service Fabric-program som skapats med Java.  Välj **Eclipse IDE för Java-utvecklare**, när du installerar Eclipse.

### Skapa programmet

Service Fabric-plugin-programmet är tillgängligt via utökningsfunktionen i Eclipse.

1. Välj **File > Other > Service Fabric** (Arkiv > Annat > Service Fabric) i Eclipse. Olika alternativ visas, bland annat aktörer och behållare.

    ![Service Fabric-mallar i Eclipse][sf-eclipse-templates]

2. I det här fallet väljer du Stateless Service (Tillståndslös tjänst).

3. Du får bekräfta att du vill använda Service Fabric-perspektivet, vilket optimerar Eclipse för användning med Service Fabric-projekt. Välj Yes (Ja).

### Distribuera programmet

I Service Fabric-mallarna ingår en uppsättning Gradle-aktiviteter för att skapa och distribuera program, som du kan köra via Eclipse.

1. Välj **Run > Run Configurations** (Kör > Kör konfigurationer).

2. Expandera **Gradle Project** (Gradle-projekt) och välj **ServiceFabricDeployer**.

3. Klicka på **Run** (Kör).

Din app skapas och distribueras på ett par minuter. Du kan övervaka dess status från Service Fabric Explorer.

## Nästa steg

- [Läs mer om Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Interagera med Service Fabric-kluster med Azure CLI](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png



<!--HONumber=Oct16_HO3-->


