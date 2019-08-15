---
title: Check lista för Azure Service Fabric-säkerhet | Microsoft Docs
description: Den här artikeln innehåller en uppsättning check lista för säkerhet för Azure Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: c30b70d2fccb7580dcb94c2322c0ad3a52461f34
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927731"
---
# <a name="azure-service-fabric-security-checklist"></a>Check lista för Azure Service Fabric Security
Den här artikeln innehåller en lättanvänd check lista som hjälper dig att skydda din Azure Service Fabric-miljö.

## <a name="introduction"></a>Introduktion
Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Service Fabric tar också itu med betydande utmaningar vid utveckling och hantering av molnprogram. Utvecklare och administratörer kan undvika komplexa infrastrukturproblem och fokusera på att implementera verksamhetskritiska, krävande arbetsbelastningar som är skalbara, tillförlitliga och hanterbara.

## <a name="checklist"></a>Checklista
Använd följande check lista för att se till att du inte har kontrollerat några viktiga problem i hanteringen och konfigurationen av en säker Azure Service Fabric-lösning.


|Check lista kategori| Beskrivning |
| ------------ | -------- |
|[Rollbaserad åtkomst kontroll (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md) | <ul><li>Med åtkomst kontroll kan kluster administratören begränsa åtkomsten till vissa kluster åtgärder för olika grupper av användare, vilket gör klustret säkrare.</li><li>Administratörer har fullständig åtkomst till hanterings funktioner (inklusive Läs-och skriv funktioner). </li><li> Användare har som standard endast Läs behörighet till hanterings funktioner (till exempel fråge funktioner) och möjligheten att lösa program och tjänster.</li></ul>|
|[X. 509-certifikat och Service Fabric](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>[Certifikat](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) som används i kluster som kör produktions arbets belastningar bör skapas med en korrekt konfigurerad Windows Server-certifikatutfärdare eller hämtas från en godkänd [certifikat utfärdare (ca)](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Använd aldrig [temporära eller test certifikat](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) i produktion som skapas med verktyg som [Makecert. exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Du kan använda ett [självsignerat certifikat](../../service-fabric/service-fabric-windows-cluster-x509-security.md) men bör bara göra det för test kluster och inte i produktion.</li></ul>|
|[Kluster säkerhet](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>I kluster säkerhets scenarierna ingår Node-to-Node-säkerhet, klient-till-nod-säkerhet, [rollbaserad åtkomst kontroll (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md).</li></ul>|
|[Klustrad autentisering](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>Autentiserar [nod-till-nod-kommunikation](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) för kluster Federation. </li></ul>|
|[Serverautentisering](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>Autentiserar [kluster hanterings slut punkter](../../service-fabric/service-fabric-cluster-creation-via-portal.md) till en hanterings klient.</li></ul>|
|[Programsäkerhet](../../service-fabric/service-fabric-cluster-creation-via-arm.md)| <ul><li>Kryptering och dekryptering av program konfigurations värden.</li><li>   Kryptering av data mellan noder under replikering.</li></ul>|
|[Kluster certifikat](../../service-fabric/service-fabric-windows-cluster-x509-security.md) | <ul><li>Detta certifikat krävs för att skydda kommunikationen mellan noderna i ett kluster.</li><li>    Ange tumavtrycket för det primära certifikatet i avsnittet tumavtryck och det sekundära i ThumbprintSecondary-variablerna.</li></ul>|
|[ServerCertificate](../../service-fabric/service-fabric-windows-cluster-x509-security.md)| <ul><li>Det här certifikatet presenteras för klienten när det försöker ansluta till det här klustret. Du kan använda två olika Server certifikat, en primär och en sekundär för uppgradering.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Detta är en uppsättning certifikat som du vill installera på de autentiserade klienterna. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Ange ett eget namn för det första klient certifikatet för CertificateCommonName. CertificateIssuerThumbprint är tumavtrycket för utfärdaren av det här certifikatet. </li></ul>|
|ReverseProxyCertificate| <ul><li>Det här är ett valfritt certifikat som kan anges om du vill skydda den [omvända proxyn](../../service-fabric/service-fabric-reverseproxy.md). </li></ul>|
|Key Vault| <ul><li>Används för att hantera certifikat för Service Fabric kluster i Azure.  </li></ul>|


## <a name="next-steps"></a>Nästa steg

- [Rekommenderade säkerhets metoder för Service Fabric](service-fabric-best-practices.md)
- [Service Fabric kluster uppgraderings process och förväntningar från dig](../../service-fabric/service-fabric-cluster-upgrade.md)
- [Hantera dina Service Fabric-program i Visual Studio](../../service-fabric/service-fabric-manage-application-in-visual-studio.md).
- [Introduktion till Service Fabric hälso modell](../../service-fabric/service-fabric-health-introduction.md).
