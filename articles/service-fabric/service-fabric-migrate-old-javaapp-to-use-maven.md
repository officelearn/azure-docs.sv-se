---
title: "Migrera från Java SDK till Maven – uppdatera gamla Azure Service Fabric Java-program för att använda Maven | Microsoft Docs"
description: "Uppdatera de äldre Java-programmen som använde Service Fabric Java-SDK:n för att hämta Service Fabric Java-beroenden från Maven. När du har slutfört konfigurationen kan de äldre Java-programmen skapa."
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: rapatchi
ms.openlocfilehash: 7eac05c859440e868e1a207f0ba0d1bdf41e6e6b
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Uppdatera det tidigare Service Fabric Java-programmet för att hämta Java-bibliotek från Maven
Vi har nyligen flyttat Service Fabric Java-binärfiler från Service Fabric Java-SDK:n till Maven-lagring. Nu kan du använda **mavencentral** för att hämta de senaste Service Fabric Java-beroendena. I den här snabbstarten får du hjälp att uppdatera dina befintliga Java-program, som du tidigare har skapat för användning med Service Fabric Java-SDK:n, med hjälp av en Yeoman-mall eller Eclipse, för att vara kompatibla med den Maven-baserade versionen.

## <a name="prerequisites"></a>Förutsättningar
1. Först måste du avinstallera den befintliga Java-SDK:n.

  ```bash
  sudo dpkg -r servicefabricsdkjava
  ```
2. Installera senaste Service Fabric CLI enligt instruktionerna [här](service-fabric-cli.md).

3. Om du vill skapa och arbeta med Service Fabric Java-programmen måste du se till att du har JDK 1.8 och Gradle installerade. Om de inte har installerats än kan du köra följande för att installera JDK 1.8 (openjdk-8-jdk) och Gradle -

 ```bash
 sudo apt-get install openjdk-8-jdk-headless
 sudo apt-get install gradle
 ```
4. Uppdatera installations-/avinstallationsskripten för programmet så att de använder nya Service Fabric CLI enligt instruktionerna [här](service-fabric-application-lifecycle-sfctl.md). Du kan använda våra komma igång-[exempel](https://github.com/Azure-Samples/service-fabric-java-getting-started) som referens.

>[!TIP]
> När du har avinstallerat Service Fabric Java-SDK:n fungerar inte Yeoman. Följ kraven [här](service-fabric-create-your-first-linux-application-with-java.md) för att få igång Service Fabric Yeoman Java-mallgeneratorn.

## <a name="service-fabric-java-libraries-on-maven"></a>Service Fabric Java-bibliotek på Maven
Service Fabric Java-bibliotek har lagrats i Maven. Du kan lägga till beroendena i ``pom.xml`` eller ``build.gradle`` för dina projekt så att de använder Service Fabric Java-bibliotek från **mavenCentral**.

### <a name="actors"></a>Aktörer

Service Fabric-stöd för tillförlitliga aktörer för ditt program.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.10.0'
  }
  ```

### <a name="services"></a>Tjänster

Service Fabric-stöd för tillståndslös tjänst för ditt program.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.10.0'
  }
  ```

### <a name="others"></a>Andra
#### <a name="transport"></a>Transport

Transportnivåstöd för Service Fabric Java-program. Du behöver inte uttryckligen lägga till det här beroendet till tillförlitliga aktörer- eller tjänstprogram, om du inte programmerar på transportnivån.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.10.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric-stöd

Systemnivåstöd för Service Fabric, som kommunicerar med ursprunglig Service Fabric-körning. Du behöver inte uttryckligen lägga till det här beroendet till tillförlitliga aktörer- eller tjänstprogram. Det hämtas automatiskt från Maven när du inkluderar de andra beroendena ovan.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.10.0'
  }
  ```


## <a name="migrating-service-fabric-stateless-service"></a>Migrera tillståndslös Service Fabric-tjänst

Om du vill kunna skapa en befintlig tillståndslös Service Fabric Java-tjänst med hjälp av Service Fabric-beroenden som hämtas från Maven, måste du uppdatera filen ``build.gradle`` i tjänsten. Tidigare såg det ut så här -
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```
För att hämta beroendena från Maven skulle **uppdaterade** ``build.gradle`` nu ha motsvarande delar enligt följande -
```
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```
I allmänhet kan du titta på något av våra komma igång-exempel för att få en uppfattning om hur byggskriptet skulle se ut för en tillståndslös Service Fabric Java-tjänst. Här är [build.gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/Services/EchoServer/EchoServer1.0/EchoServerService/build.gradle) för EchoServer-exemplet.

## <a name="migrating-service-fabric-actor-service"></a>Migrera Service Fabric-aktörstjänst

Om du vill kunna skapa ett befintligt Service Fabric Java-aktörsprogram med hjälp av Service Fabric-beroenden som hämtas från Maven, måste du uppdatera filen ``build.gradle`` i gränssnittspaketet och i tjänstpaketet. Om du har ett TestClient-paket måste du uppdatera det också. Så för aktören ``Myactor`` behöver du uppdatera på följande platser -
```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>Uppdatera byggskript för gränssnittsprojektet

Tidigare såg det ut så här -
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```
För att hämta beroendena från Maven skulle **uppdaterade** ``build.gradle`` nu ha motsvarande delar enligt följande -
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>Uppdatera byggskript för aktörsprojektet

Tidigare såg det ut så här -
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```
För att hämta beroendena från Maven skulle **uppdaterade** ``build.gradle`` nu ha motsvarande delar enligt följande -
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>Uppdatera byggskript för testklientprojektet

Ändringar här liknar de ändringar som beskrivs i föregående avsnitt, det vill säga aktörsprojektet. Tidigare såg Gradle-skriptet ut så här -
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
      compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
  destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```
För att hämta beroendena från Maven skulle **uppdaterade** ``build.gradle`` nu ha motsvarande delar enligt följande -
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>Nästa steg

* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Interagera med Service Fabric-kluster med Service Fabric CLI](service-fabric-cli.md)
