---
title: Java och bas-OS för Azure våren Cloud mikroservice-appar
description: Principer för att underhålla felfria Java-och bas operativ system för Azure våren Cloud mikroservice-appar
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 1a8f48ff365ffae4a7013d2bfd537721ba2bc381
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172700"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>Java och bas-OS för våren mikroservice-appar
Följande är principer för att underhålla felfria Java-och bas operativ system för våren mikroservice-appar.
## <a name="principles-for-healthy-java-and-base-os"></a>Principer för felfritt Java och bas-OS
* Måste vara samma grundläggande operativ system mellan nivåer – Basic | Standard | Denaturering.
    * För närvarande använder appar i Azure våren Cloud en blandning av Debian 10 och Ubuntu 18,04.
    * VMware build-tjänsten använder Ubuntu 18,04.
* Måste vara samma grundläggande operativ system oavsett distributionens start punkter-källa | JAR
    * För närvarande använder appar i Azure våren Cloud en blandning av Debian 10 och Ubuntu 18,04.
* Det grundläggande operativ systemet skall vara fritt från säkerhets risker.
    * Debian 10 Base Opera ting system har 147 öppna CVEs.
    * Ubuntu 18,04 bas operativ system har 132 öppna CVEs.
* Ska använda JRE.
    * För närvarande använder appar i Azure våren Cloud JDK. JRE-konsol löst är en mindre bild.
* Ska använda de senaste versionerna av Java.
    * För närvarande använder appar i molnet Azure våren Java 8 build 242. Det här är en inaktuell version.
 
Azul Systems söker kontinuerligt efter ändringar i bas operativ system och behåller de senaste inbyggda avbildningarna aktuella. Azure våren Cloud söker efter ändringar i bilder och uppdaterar dem kontinuerligt över distributioner.
 
## <a name="faq-for-azure-spring-cloud"></a>Vanliga frågor och svar om Azure Spring Cloud

* Vilka versioner av Java stöds? Huvud version och build-nummer.
    * Stöd för LTS-versioner – Java 8 och 11.
    * Använder den senaste versionen, till exempel, nu, Java 8 build 252 och Java 11 version 7.
* Vem har skapat dessa Java-körningar?
    * Azul-system.
* Vad är det grundläggande operativ systemet för avbildningar?
    * Ubuntu 20,04 LTS (fokus fossa). Apparna fortsätter att stanna kvar på den senaste LTS-versionen av Ubuntu.
    * Se [Ubuntu 20,04 LTS (fokus fossa)](http://releases.ubuntu.com/focal/)
* Hur kan jag hämta en Java-körning som stöds för lokal utveckling? 
    * Se [installera JDK för Azure och Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)
* Hur kan jag få support för problem på Java Runtime-nivån?
    * Öppna ett support ärende med support för Azure.
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Standard distribution i Azure våren Cloud

> ![Standard distribution](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>Nästa steg
* [Snabb start: starta ett befintligt Azure våren Cloud-program med hjälp av Azure Portal](spring-cloud-quickstart-launch-app-portal.md)
* [Java-långsiktigt stöd för Azure och Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)
