---
title: Checklista för Azure service fabric-säkerhet | Microsoft Docs
description: Den här artikeln innehåller en uppsättning checklista för Azure-infrastrukturen security säkerhet.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: d0826d60d766874ddd186bcf406ea987134dab16
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385877"
---
# <a name="azure-service-fabric-security-checklist"></a>Checklista för Azure Service Fabric-säkerhet
Den här artikeln innehåller en enkel att använda checklista som hjälper dig att skydda din Azure Service Fabric-miljö.

## <a name="introduction"></a>Introduktion
Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Service Fabric tar också itu med betydande utmaningar vid utveckling och hantering av molnprogram. Utvecklare och administratörer kan undvika komplexa infrastrukturproblem och fokusera på att implementera verksamhetskritiska, krävande arbetsbelastningar som är skalbara, tillförlitliga och hanterbara.

## <a name="checklist"></a>Checklista
Använd följande checklista för att hjälpa dig att kontrollera att du inte har missat några viktiga problem i hantering och konfiguration av en säker lösning för Azure Service Fabric.


|Checklista för kategori| Beskrivning |
| ------------ | -------- |
|[Rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>Åtkomstkontroll kan du Klusteradministratören att begränsa åtkomsten till vissa klusteråtgärder för olika grupper av användare, vilket gör att klustret säkrare.</li><li>Administratörer har fullständig åtkomst till funktioner för hantering (inklusive Läs-och skrivbehörighet). </li><li> Användare, har som standard bara läsbehörighet till funktioner för hantering (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.</li></ul>|
|[X.509-certifikat och Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>[Certifikat](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) används i kluster som kör produktionsarbetsbelastningar ska skapas med hjälp av en korrekt konfigurerad Certifikattjänsten för Windows Server eller hämtas från en godkänd [certifikatutfärdaren (CA)](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Aldrig använda någon [tillfälliga eller testa certifikat](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) i produktion som har skapats med verktyg som [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Du kan använda en [självsignerat certifikat](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) men du bör bara göra det för testkluster och inte i produktion.</li></ul>|
|[Klustersäkerhet](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>Säkerhetsscenarier för kluster omfattar nod till nod-säkerhet, klient-till-nod-säkerhet, [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Kluster-autentisering](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Autentiserar [kommunikation från nod till nod](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) för klustret. </li></ul>|
|[Server-autentisering](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Autentiserar den [kluster hanteringsslutpunkter](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal) i en Hanteringsklient.</li></ul>|
|[Programsäkerhet](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>Kryptering och dekryptering av konfigurationsvärden för programmet.</li><li>   Kryptering av data över noder under replikeringen.</li></ul>|
|[Klustercertifikat](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Detta certifikat krävs för att skydda kommunikationen mellan noderna i ett kluster.</li><li>    Ange primär certifikatets tumavtryck i avsnittet tumavtryck och som sekundär i ThumbprintSecondary variabler.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Det här certifikatet visas för klienten när den försöker ansluta till det här klustret. Du kan använda två olika servercertifikat, en primär och sekundär för uppgradering.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Det här är en uppsättning certifikat som du vill installera på de autentiserade klienterna. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Ange namnet på det första klientcertifikatet för CertificateCommonName. CertificateIssuerThumbprint är tumavtrycket för den här certifikatutfärdaren. </li></ul>|
|ReverseProxyCertificate| <ul><li>Det här är ett valfritt certifikat som kan anges om du vill skydda din [omvänd Proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). </li></ul>|
|Key Vault| <ul><li>Används för att hantera certifikat för Service Fabric-kluster i Azure.  </li></ul>|


## <a name="next-steps"></a>Nästa steg
- [Uppgraderingsprocessen för Service Fabric-kluster och förväntningar från dig](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [Hantera dina Service Fabric-program i Visual Studio](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio).
- [Service Fabric Health modellen introduktion](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction).
